# AI Policy

AI Policy is a model-agnostic contract repository for AI-assisted software development.

This repository contains shared AI role contracts only. It does not contain project-specific source code or automation runtime logic.

## Policy Version

Current stable policy: `v4.0.0`

See `CHANGELOG.md` for release notes.

## Structure

```text
contracts/
    product-owner.md
    product-owner-kr.md
    developer.md
    developer-kr.md
    reviewer.md
    reviewer-kr.md
    merger.md
    merger-kr.md
    deployer.md
    deployer-kr.md
docs/
    templates/
        spec.md
        report.md
        status.md
    task-status.md
    use-cases/
```

- `*.md`: canonical English contracts for AI systems.
- `*-kr.md`: Korean reference translations for human maintenance.

Each canonical contract is self-contained and must work without loading additional policy files.

## Task Folder

Work is tracked in a local task folder instead of a GitHub Issue or Pull Request. The task folder
lives at the root of the Target Repository being worked on (not under the user's home directory),
and is committed to git like any other file in that repository:

```text
<Target Repository root>/
    task/
        summary.md       (shared, project-level architecture summary; optional, maintained across tasks)
        task-NNN/
            spec.md       (Product Owner's approved requirements)
            status.md     (current lifecycle state)
            report.md     (Developer's implementation report)
            review.md     (Reviewer's output)
            merge.md      (Merger's output)
            deploy.md     (Deployer's output)
```

`task-NNN` is a sequential, zero-padded, per-repository counter.

## Required Workflow

All implementation work must follow this workflow:

```text
Spec -> Contract -> ADR -> Implementation -> Report -> Review -> Merge
```

- `Spec`: define the approved goal, acceptance criteria, verification gates, and out-of-scope work in the task folder's `spec.md`.
- `Contract`: the acting AI role reads and declares the relevant contract before work starts.
- `ADR`: record architectural decisions when required by the spec or by material architecture changes.
- `Implementation`: complete only the approved scope on the task branch.
- `Report`: submit the work using the standard task report template in `report.md`.
- `Review`: review against the spec, contract, acceptance criteria, and verification gates in `review.md`.
- `Merge`: merge the task branch locally only after review is complete.

Use `docs/templates/spec.md`, `docs/templates/report.md`, and `docs/templates/status.md` as the standard templates for repositories that adopt this policy.
Use `docs/task-status.md` for the primary task lifecycle states.

## Repository Boundaries

| Repository | Responsibility |
|---|---|
| `ai-policy` | Defines what AI roles must do |
| Target Repository | Contains application source code |

## Compatibility

Compatibility is listed only after an AI system has been tested against the contracts.

| AI system | Status |
|---|---|
| ChatGPT | Not yet verified |
| Claude | Not yet verified |
| Gemini | Not yet verified |
