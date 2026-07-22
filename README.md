# AI Policy

AI Policy is a model-agnostic contract repository for AI-assisted software development.

This repository contains shared AI role contracts only. It does not contain project-specific source code or automation runtime logic.

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
```

- `*.md`: canonical English contracts for AI systems.
- `*-kr.md`: Korean reference translations for human maintenance.

Each canonical contract is self-contained and must work without loading additional policy files.

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
