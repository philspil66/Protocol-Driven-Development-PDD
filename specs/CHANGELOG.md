# Specs folder changelog

Tweaks and process changes to the spec workflow, tooling, and conventions. Newest first.

---

## 2026-02-18

- **Spec status flow** — Builder and reviewer behaviour updated so the spec **Status** is progressed through the pipeline:
  - **Draft** → (builder completes implementation) → **Implemented**
  - **Implemented** → (reviewer completes review) → **Reviewed - Passed** or **Reviewed - Failed**
  So the builder updates status to *Implemented* when done; the reviewer sets *Reviewed - Passed* or *Reviewed - Failed* after review.
