# AI Policy

AI Policy is a model-agnostic contract repository for AI-assisted software development.

This repository contains shared AI role contracts only. It does not contain project-specific source code or automation runtime logic.

## Policy Version

Current stable policy: `v1.0.0`

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
docs/
    templates/
        issue.md
        pull-request.md
```

- `*.md`: canonical English contracts for AI systems.
- `*-kr.md`: Korean reference translations for human maintenance.

Each canonical contract is self-contained and must work without loading additional policy files.

## Required Workflow

All implementation work must follow this workflow:

```text
Issue -> Contract -> ADR -> Implementation -> PR -> Review -> Merge
```

- `Issue`: define the approved goal, acceptance criteria, verification gates, and out-of-scope work.
- `Contract`: the acting AI role reads and declares the relevant contract before work starts.
- `ADR`: record architectural decisions when required by the Issue or by material architecture changes.
- `Implementation`: complete only the approved scope.
- `PR`: submit the work using the standard Pull Request template.
- `Review`: review against the source Issue, contract, acceptance criteria, and verification gates.
- `Merge`: merge only after review is complete.

Use `docs/templates/issue.md` and `docs/templates/pull-request.md` as the standard templates for repositories that adopt this policy.

## Repository Boundaries

| Repository | Responsibility |
|---|---|
| `ai-policy` | Defines what AI roles must do |
| `ai-automation` | Executes workflows and automation runtime |
| Target Repository | Contains application source code |

## Compatibility

Compatibility is listed only after an AI system has been tested against the contracts.

| AI system | Status |
|---|---|
| ChatGPT | Not yet verified |
| Claude | Not yet verified |
| Gemini | Not yet verified |
