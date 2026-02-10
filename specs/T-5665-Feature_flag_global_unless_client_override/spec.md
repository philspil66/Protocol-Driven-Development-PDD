# Technical Specification: Feature Flags – Use Global Unless Client Override Exists (No Auto-Creation)

**Task ID:** T-5665  
**Title:** Feature flags: use global record unless a client override exists; do not auto-create client records  
**Date:** 2026-02-05  
**Author:** Phil  
**Status:** Implemented  
**Last updated:** 2026-02-05

---

## Executive Summary

Client-scoped feature flags (e.g. `issue_notifications`, `issue_project_permissions`) should **use the global feature record** for all clients unless an **explicit client override** exists. The application must **not** auto-create client-scoped rows when checking a feature for a client; client records are created only when an admin explicitly sets a different value for that client. This specification defines the required behaviour and the change from Laravel Pennant’s scoped API to the existing “effective value” logic in `App\Models\Feature`, so that global-by-default with optional client overrides is consistent and no lazy persistence occurs.

**Scope:** Client-scoped feature flag resolution only (`issue_notifications`, `issue_project_permissions`); global and non-client features unchanged  
**Estimated Impact:** Low – Replace Pennant scoped checks with model-based reads in ~7 files, 10 call sites  
**Risk Level:** Low – Read-only resolution change; reversible

---

## Background

### Problem Statement

- **Current behaviour:** When the application checks a client-scoped feature (e.g. `Feature::for($client)->active('issue_notifications')`), Laravel Pennant resolves the value via a **define callback**. If no row exists for that client scope, Pennant runs the callback and then **persists** the result into the `features` table. The define callback for `issue_notifications` (and `issue_project_permissions`) returns `false`, so the first check for any client creates a client row with `false`—even when the **global** flag is `true`.
- **User impact:** Admins set the global flag to `true` expecting all clients to get the feature unless they add a client override. Instead, the first time the app checks the feature for a client, a client row is auto-created with `false`, overriding the global setting.
- **Desired behaviour:** Use the **global** feature value for every client unless a **client-scoped row already exists** (explicitly created by an admin). The app must **not** create client-scoped rows when resolving the feature; only reading from existing rows.

### Relationship to Existing Behaviour

- **Global feature record:** Stored with scope `_laravel_null` (or null). Set via admin UI or seeders/commands. This is the default for all clients when no client override exists.
- **Client override:** A row with scope `App\Models\Client|{id}`. Created only when an admin explicitly sets a different value for that client. The app should never create such a row as a side effect of checking the feature.
- **`App\Models\Feature`:** The application already has `Feature::getEffectiveValue($featureName, $client)` and `Feature::isEnabled($featureName, $client)` which implement “client row if exists, else global row” by **reading** from the `features` table only. They do not invoke Pennant’s define callback and do not persist. These are not currently used for the client-scoped feature checks in the app.

### Out of Scope

- **Global-only feature flags** (e.g. `dashboard`, `issue_block`, `scoping_block`): No change; they do not use client scope.
- **How client overrides are created:** Admin UI or existing seeders/commands; no change to that flow.
- **Pennant define callbacks:** May be left as-is for `issue_notifications` and `issue_project_permissions`; after this change the app will not trigger them for client scope when resolving “is this feature on for this client?”. Optional follow-up: adjust or remove those callbacks for clarity.
- **Other client-scoped features:** If additional client-scoped features are added later, they should follow the same pattern (use `App\Models\Feature::isEnabled()` / `getEffectiveValue()` and do not use Pennant’s `Feature::for($client)->active()` for resolution).

---

## Current System Analysis

### Where Client-Scoped Features Are Checked

| File | Usage | Feature name |
|------|--------|---------------|
| `app/Services/AccountService.php` | `Feature::for($this->user->client)->active('issue_notifications')` | issue_notifications |
| `app/Services/AccountService.php` | `Feature::for($this->user->client)->active('issue_notifications')` | issue_notifications |
| `app/Services/AlertService.php` | `Feature::for($client)->active('issue_notifications')` | issue_notifications |
| `app/Http/Controllers/AccountController.php` | `Feature::for($user->client)->active('issue_notifications')` | issue_notifications |
| `app/Http/Controllers/Issue/ProjectController.php` | `Feature::for($this->client)->active('issue_project_permissions')` | issue_project_permissions |
| `app/Http/Controllers/Issue/ProjectPermissionController.php` | `Feature::for($client)->active('issue_project_permissions')` (2 places) | issue_project_permissions |
| `app/Services/Issue/RttvService.php` | `Feature::for($client)->active('issue_project_permissions')` (3 places) | issue_project_permissions |

**Total:** 7 files, 10 call sites. All use `Laravel\Pennant\Feature` (Pennant), which triggers define callback and persist on first resolution for a client scope.

### Existing “Effective Value” Logic (Not Yet Used for These Checks)

- **`App\Models\Feature::getEffectiveValue(string $featureName, ?Client $client = null): ?bool`**
  - If `$client` is set: look up row for scope `App\Models\Client|{client->id}`. If found, return its value (as bool).
  - Otherwise (or if no client row): look up global row (scope null or `__laravel_null`). If found, return its value.
  - Returns `null` if neither exists. Does **not** call Pennant and does **not** write to the database.
- **`App\Models\Feature::isEnabled(string $featureName, ?Client $client = null): bool`**
  - Calls `getEffectiveValue()` and returns `true` only when the value is `true` (treats `false` and `null` as disabled).

### Gap

- The application uses Pennant’s scoped API for client-scoped checks, which causes auto-creation of client rows. The desired behaviour (“global unless client override exists; no auto-creation”) is already implemented in `App\Models\Feature` but is not used at these call sites.

---

## Requirements

### Functional Requirements

1. **Use global value when no client override exists**
   - For client-scoped feature flags, the effective value for a client must be the **global** feature value when no row exists for scope `App\Models\Client|{client->id}`.

2. **Use client value only when a client override exists**
   - When a row exists for scope `App\Models\Client|{client->id}` for that feature, the effective value must be that row’s value (explicit client override).

3. **No auto-creation of client records**
   - Checking “is feature X enabled for client C?” must **not** create a row in the `features` table for that client. Client-scoped rows are created only by explicit admin (or seeder/command) actions.

4. **Affected features**
   - At least: `issue_notifications`, `issue_project_permissions`. Any other client-scoped feature that is resolved via `Feature::for($client)->active(...)` should follow the same pattern for consistency.

5. **Backward compatibility**
   - Existing client overrides (rows that were already created) must continue to be respected. Behaviour for clients that already have a row is unchanged; only the “no row” case changes from “callback result + persist” to “use global”.

### Non-Functional Requirements

1. **Performance**
   - Resolution is a direct read from the `features` table (one or two queries: client row then global row if needed). Comparable to or lighter than Pennant’s resolve-and-persist path.

2. **Security & multi-tenant**
   - No change to authorization or client isolation. We only change how the effective value is computed; callers still pass the same client context.

3. **Observability**
   - Existing Log usage at call sites can remain. No new logging required for this change unless desired (e.g. debug when falling back to global).

---

## Technical Architecture

### Resolution Strategy

- **For “is feature X enabled for client C?”:** Use `App\Models\Feature::isEnabled('X', $client)`.
- **For “what is the effective value (true/false/null)?”:** Use `App\Models\Feature::getEffectiveValue('X', $client)`.
- **Do not use** `Laravel\Pennant\Feature::for($client)->active('X')` for client-scoped features at resolution time, so that Pennant never runs the define callback for that scope and never persists a client row.

### Files to Touch

| Area | File(s) | Change |
|------|---------|--------|
| Account | `app/Services/AccountService.php` | Replace 2× `Feature::for($this->user->client)->active('issue_notifications')` with `\App\Models\Feature::isEnabled('issue_notifications', $this->user->client)`. Ensure `Feature` import refers to `App\Models\Feature` where used for this check (or use fully qualified name). |
| Alerts | `app/Services/AlertService.php` | Replace 1× `Feature::for($client)->active('issue_notifications')` with `\App\Models\Feature::isEnabled('issue_notifications', $client)`. |
| Account UI | `app/Http/Controllers/AccountController.php` | Replace 1× `Feature::for($user->client)->active('issue_notifications')` with `\App\Models\Feature::isEnabled('issue_notifications', $user->client)`. |
| ISSUE Project | `app/Http/Controllers/Issue/ProjectController.php` | Replace 1× `Feature::for($this->client)->active('issue_project_permissions')` with `\App\Models\Feature::isEnabled('issue_project_permissions', $this->client)`. |
| ISSUE Permissions | `app/Http/Controllers/Issue/ProjectPermissionController.php` | Replace 2× `Feature::for($client)->active('issue_project_permissions')` with `\App\Models\Feature::isEnabled('issue_project_permissions', $client)`. |
| ISSUE Service | `app/Services/Issue/RttvService.php` | Replace 3× `Feature::for($client)->active('issue_project_permissions')` with `\App\Models\Feature::isEnabled('issue_project_permissions', $client)`. |
| Tests | Unit/Feature tests that mock or assert on these feature checks | Update any tests that depend on Pennant creating client rows or on the old define callback behaviour; assert instead on effective value (global vs client override). |
| Docs | `docs/app-docs/ISSUE_NOTIFICATIONS.md` (if it describes feature behaviour) | Optional: note that client-scoped resolution uses global unless a client override exists and that the app does not auto-create client rows. |
| Spec | `docs/specs/DPRTL-5665_FEATURE_FLAG_GLOBAL_UNLESS_CLIENT_OVERRIDE.md` | Update status to Implemented when done. |

### Import / Namespace Notes

- Call sites currently import `Laravel\Pennant\Feature`. After the change, for the replaced calls use `App\Models\Feature::isEnabled(...)`. Options: (a) add `use App\Models\Feature as FeatureModel` and call `FeatureModel::isEnabled(...)`, or (b) use fully qualified `\App\Models\Feature::isEnabled(...)` at each call site. Keep Pennant `Feature` import where it is still used (e.g. global checks elsewhere in the same file, if any).

---

## Implementation Plan

### Phase 1: Replace client-scoped Pennant checks with App\Models\Feature

1. **AccountService.php**  
   Replace both `Feature::for($this->user->client)->active('issue_notifications')` with `\App\Models\Feature::isEnabled('issue_notifications', $this->user->client)`. Add or adjust import so `App\Models\Feature` is available; avoid name clash with Pennant `Feature`.

2. **AlertService.php**  
   Replace `Feature::for($client)->active('issue_notifications')` with `\App\Models\Feature::isEnabled('issue_notifications', $client)`.

3. **AccountController.php**  
   Replace `Feature::for($user->client)->active('issue_notifications')` with `\App\Models\Feature::isEnabled('issue_notifications', $user->client)`.

4. **Issue\\ProjectController.php**  
   Replace `Feature::for($this->client)->active('issue_project_permissions')` with `\App\Models\Feature::isEnabled('issue_project_permissions', $this->client)`.

5. **Issue\\ProjectPermissionController.php**  
   Replace both occurrences of `Feature::for($client)->active('issue_project_permissions')` with `\App\Models\Feature::isEnabled('issue_project_permissions', $client)`.

6. **Issue\\RttvService.php**  
   Replace all three occurrences of `Feature::for($client)->active('issue_project_permissions')` with `\App\Models\Feature::isEnabled('issue_project_permissions', $client)`.

7. **Imports**  
   In each file, ensure `App\Models\Feature` is used only for `isEnabled` (client-scoped). If the file still uses Pennant for other features, keep `use Laravel\Pennant\Feature` and use `\App\Models\Feature::isEnabled(...)` fully qualified to avoid conflict.

### Phase 2: Tests

1. **Unit / Feature tests**  
   - Add or update tests that assert: when no client row exists for `issue_notifications` (or `issue_project_permissions`), the effective value is the **global** value (e.g. global `true` → client sees enabled).  
   - Assert that no new row is created in `features` for the client when checking the feature.  
   - Assert that when a client override exists, that value is used.

2. **Regression**  
   - Existing tests that rely on these feature checks should pass; update expectations if they currently depend on Pennant creating a client row with `false`.

### Phase 3: Verification and docs

1. **Manual**  
   - Follow the **Manual re-test steps** section below.

2. **Docs**  
   - Update this spec status to Implemented. Optionally update `docs/app-docs/ISSUE_NOTIFICATIONS.md` to describe “global unless client override; no auto-creation”.

---

## Manual re-test steps

Use these steps after implementation to confirm behaviour and that no client rows are auto-created.

### Prerequisites

- Access to the application (e.g. UAT) as a Internal user who can switch clients, and as (or with) a client user for at least two clients (e.g. Client A, Client B).
- Access to the database (e.g. HeidiSQL) to inspect the `features` table.
- Optional: delete any existing client-scoped rows for `issue_notifications` and `issue_project_permissions` for the test clients so you start from a clean "no override" state.

### 1. Global on, no client row – issue_notifications

1. **DB:** Ensure global `issue_notifications` is **true** (scope `_laravel_null` or null). For one test client (e.g. Client A), ensure there is **no** row with scope `App\Models\Client|{client_id}` for `issue_notifications`. Note the current row count:  
   `SELECT COUNT(*) FROM features WHERE name = 'issue_notifications' AND scope LIKE 'App\Models\Client|%';`
2. **App – Account preferences:** Log in as a **client user** for Client A (or as Internal user with Client A selected). Go to **Account** / **Account preferences**.
   - **Expect:** ISSUE notification preference section (delivery schedule, severity levels, etc.) is **visible** and usable.
3. **App – Alerts (optional):** If you can trigger an ISSUE issue/host–issue event for a project under Client A, confirm an alert is queued/processed (per existing ISSUE alert behaviour). This confirms `AlertService` sees the feature as enabled.
4. **DB – No auto-creation:** Run the same `SELECT COUNT(*) ...` as in step 1.  
   - **Expect:** Count unchanged (no new client-scoped row was created).

### 2. Client override false – issue_notifications

1. **DB:** For Client A, insert (or use admin UI to set) a **client override** for `issue_notifications` with value **false** (scope `App\Models\Client|{client_a_id}`).
2. **App:** Log in as a client user for Client A. Go to **Account** / **Account preferences**.
   - **Expect:** ISSUE notification preference section is **not** shown (or is hidden/disabled), because the feature is off for this client.
3. **App:** Log in as a client user for **Client B** (no override). Go to **Account** / **Account preferences**.
   - **Expect:** ISSUE notification section **is** visible (global still applies where no override).
4. **Optional:** For Client A, trigger ISSUE alert flow; expect alert processing to be skipped for that client.

### 3. Global on, no client row – issue_project_permissions

1. **DB:** Ensure global `issue_project_permissions` is **true** (or **false**—your choice). For a test client with **no** client row for this feature, note client row count:  
   `SELECT COUNT(*) FROM features WHERE name = 'issue_project_permissions' AND scope LIKE 'App\Models\Client|%';`
2. **App:** As Internal user, select that client and open an ISSUE project (or ISSUE project permissions area, depending on how the flag is used in the UI).
   - **Expect:** Behaviour matches the **global** value (e.g. if global is true, project-permission behaviour is on).
3. **DB:** Run the same `SELECT COUNT(*) ...` again.  
   - **Expect:** Count unchanged (no new client row).

### 4. Client override – issue_project_permissions

1. **DB:** For one client, add a client override for `issue_project_permissions` with value **false**.
2. **App:** Use the app as that client (or Internal user with that client selected); exercise ISSUE project / project permissions.
   - **Expect:** Behaviour matches the override (e.g. permission feature off for that client).
3. **App:** Use another client with no override.  
   - **Expect:** Behaviour matches global.

### 5. Summary checklist

- [ ] With global **true** and **no** client row, client sees feature **on** (issue_notifications: prefs visible; issue_project_permissions: permission behaviour on).
- [ ] With a client override **false**, that client sees feature **off**; other clients still see global.
- [ ] After using the app for clients that have **no** client row, **no** new rows appear in `features` for scope `App\Models\Client|*` for these two features (no auto-creation).

---

## Testing Strategy

- **Effective value (no client row):** For a feature with global row set (e.g. `true`), call `Feature::isEnabled('issue_notifications', $client)` for a client that has no client-scoped row. Expect `true`. Query `features` before and after; no new row for that client.
- **Effective value (client override):** Create a client row with value `false`. Call `Feature::isEnabled('issue_notifications', $client)`. Expect `false`.
- **Regression:** All existing feature tests and flows that use `issue_notifications` or `issue_project_permissions` (account preferences, ISSUE alerts, ISSUE project permissions) continue to pass with the new resolution behaviour.

---

## Success Criteria

- For client-scoped features `issue_notifications` and `issue_project_permissions`, the effective value for a client is the **global** value when no client-scoped row exists.
- When a client-scoped row exists, its value is used (explicit override).
- Checking the feature for a client **does not** create a row in the `features` table; client rows are created only by explicit admin (or seeder/command) actions.
- All 10 call sites use `App\Models\Feature::isEnabled(...)` (or equivalent) instead of Pennant’s `Feature::for($client)->active(...)` for these two features.
- Existing client overrides continue to be respected. No unintended change to global-only or other feature flags.

---

## Conclusion

T-5665 is addressed by using the existing “effective value” logic in `App\Models\Feature` (getEffectiveValue / isEnabled) for client-scoped feature checks instead of Laravel Pennant’s scoped API. Resolution becomes “client row if exists, else global row,” with no persistence on read, so the app no longer auto-creates client records. Implementation is limited to replacing the 10 call sites across 7 files and updating tests and docs as needed.
