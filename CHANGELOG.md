# Changelog

## v4.0.0 - 2026-08-08

Replaces the GitHub Issue/Pull Request workflow with a local task-folder workflow.

### Changed

- All work is now tracked in a local task folder, `~/task/<project-name>/task-NNN/`, containing `spec.md` (replaces the Issue), `status.md` (replaces GitHub workflow labels), `report.md` (replaces the Pull Request), `review.md`, `merge.md`, and `deploy.md`.
- Implementation still uses a git branch per task (`task/<project-name>/task-NNN`), merged locally by the Merger with `git merge`/squash instead of a GitHub PR merge; no GitHub PR object is created.
- Required workflow renamed: `Spec -> Contract -> ADR -> Implementation -> Report -> Review -> Merge` (previously `Issue -> Contract -> ADR -> Implementation -> PR -> Review -> Merge`).
- `docs/templates/issue.md` and `docs/templates/pull-request.md` replaced by `docs/templates/spec.md`, `docs/templates/report.md`, and `docs/templates/status.md`.
- `docs/workflow-labels.md` replaced by `docs/task-status.md`; GitHub labels replaced by a `State` field (plus supplementary `Review-Round`/`Followup` fields) in each task's `status.md`.
- Product Owner, Developer, Reviewer, Merger, and Deployer contracts (and their `-kr.md` translations) updated to v4 to reference the task folder instead of GitHub Issues/PRs/labels.
- Use cases renamed `docs/use-cases/autonomous-delivery-v4.md` and updated to the task-folder model.
- The gh-relay-specific shared-state `summary.md` (`~/.gh-relay/<project>/summary.md`) is replaced by a project-level `~/task/<project-name>/summary.md`, maintained directly by the roles instead of an external dispatch tool.

## v3.1.0 - 2026-08-04

Adds shared per-project context to reduce repeated discovery cost across roles, backed by gh-relay's local, non-git dispatch-mirror shared-state directory (default `~/.gh-relay/<project>/`) rather than a git-committed file in the Target Repository.

### Added

- Developer reads the project's shared-state `summary.md`, when present, before analyzing the codebase, and updates it (or creates a minimal version) after a structurally meaningful change (module composition, data flow, or component responsibilities). Developer also records progress notes in that project's `issue-N`/`pr-N` folder while implementing.
- Reviewer checks, as a RECOMMENDED finding, whether a structurally meaningful PR left the project's shared-state `summary.md` unupdated, while treating the document only as an orientation starting point and verifying accuracy against the actual diff and code.
- Product Owner reads the project's existing shared-state `summary.md` first, when present, to inform the Design Confidence assessment before writing an Issue.

### Note

An earlier same-day revision of this release used a git-committed `ARCHITECTURE.md` in the Target Repository instead. That approach was superseded before any Target Repository adopted it (this policy repository was the only place it was ever recorded), so this entry documents the shared-state approach directly rather than layering a separate release on top.

## v3.0.0 - 2026-08-02

Workflow-complete policy release for autonomous Issue-to-deployment execution.

### Changed

- Product Owner v3 adds clarification handoff, concise numbered user decisions, Source Issue decision records, and the narrowly scoped post-merge E2E follow-up exception.
- Developer v3 requires PR-comment evidence for review-fix disposition and verification, non-draft PRs by default, and Product Owner handoff for ambiguity.
- Reviewer v3 distinguishes pre-merge from post-merge verification, records actionable owner handoffs, and re-reviews only new commits.
- Merger v3 automatically merges once all merge gates pass; the approved Source Issue is the user authorization for merge.
- Added Deployer v3 and workflow use cases for deployment, post-merge E2E, and failure follow-up.
- Added stage-oriented `develop:*`, `review:*`, `merge:*`, `deploy:*`, and `e2e:*` labels, with bounded review rounds and follow-up context labels.

## v1.0.0 - 2026-07-26

Initial stable policy release for Issue-driven AI-assisted development.

### Added

- Standard GitHub Issue template at `docs/templates/issue.md`.
- Standard Pull Request template at `docs/templates/pull-request.md`.
- Required workflow declaration: Issue -> Contract -> ADR -> Implementation -> PR -> Review -> Merge.
- Product Owner contract guidance requiring approved Issues before implementation work.

### Policy Status

Policy v1.0.0 defines the first stable baseline for shared AI role contracts across participating repositories.
