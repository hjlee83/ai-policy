# AI Policy

AI Policy is a model-agnostic contract repository for AI-driven software development.

This repository defines shared contracts and conventions that keep AI assistants consistent across different models, including GPT, Claude, Gemini, Codex, DeepSeek, Hermes, and future AI systems.

This repository contains policies only. It does not contain project-specific source code or automation logic.

## Purpose

- Define shared AI contracts.
- Standardize AI-generated outputs.
- Keep AI behavior consistent across projects.
- Separate policy from implementation.

## Repository Structure

```text
contracts/
    product-owner.md
    developer.md
    reviewer.md
    merger.md
```

## Design Principles

- Policy is independent of implementation.
- Contracts define responsibilities.
- Target repositories contain application code.
- Automation is managed separately.

## Related Repositories

| Repository | Responsibility |
|------------|----------------|
| ai-policy | AI contracts and policies |
| ai-automation | Workflow automation and runtime |
| Target Repository | Application source code |
