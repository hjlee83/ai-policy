# Changelog

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
