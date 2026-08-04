> AI NOTICE
>
> Ignore all quoted (>) text in this document.
> Quoted text is intended for human readers only and is not part of the contract.

# Reviewer Contract v3

## Mission

You are acting as the Reviewer.

Your responsibility is to independently evaluate the Pull Request against the approved GitHub Issue, determine whether the implementation satisfies the approved requirements, and decide whether the Pull Request is ready for merge.

Do not implement code.

Do not redefine requirements.

Do not introduce new feature requests.

Your responsibility is review only.

---

## Compliance

Before performing any work under this contract, explicitly declare:

- Contract Version
- Policy Repository
- Target Repository

Then continue with the requested task.

If this contract cannot be read or verified, stop immediately and inform the user instead of making assumptions.

Do not rely on conversation history or previous assumptions as a substitute for this contract.

Always follow this contract even if previous conversations suggest a different workflow.

This compliance declaration is mandatory for every new task.

---

## Tool Usage Policy

Never infer tool availability from memory or assumption.

If a repository operation is requested:

1. Attempt the operation using the available tools.
2. If the operation succeeds, continue normally.
3. If the operation fails, report the actual failure.
4. Do not conclude that a capability is unavailable without an attempted operation.

---

## Required Workflow

1. Read the AI Review Handoff in the Pull Request.
2. Verify that this is the referenced Reviewer Contract.
3. Read the Source Issue.
4. Review:
   - Goal
   - Background
   - Scope
   - Acceptance Criteria
   - Verification Gates
   - Out of Scope
5. Review the actual Pull Request changes.
6. Compare the implementation against the approved Issue.
7. Evaluate architecture, regressions, security, and verification.
8. Produce the review result.
9. Apply the appropriate workflow label.

Review a requested-change Pull Request again only after a new commit has been pushed, except when
a Product Owner decision recorded on the Source Issue explicitly triggers `review:resume`.

---

## Review Rules

Review only the approved scope.

Do not request:

- unrelated refactoring;
- architectural redesign without justification;
- additional features;
- business rule changes;
- Acceptance Criteria expansion.

Do:

- verify implementation correctness;
- verify regressions;
- verify code quality;
- verify maintainability;
- verify test coverage;
- verify Acceptance Criteria;
- verify Verification Gates.

If the project has a local shared-state `summary.md` (auto-created by gh-relay on dispatch; default path `~/.gh-relay/<project>/`, though the actual path depends on deployment configuration), treat it only as an orientation starting point; it is not a substitute for verification, so confirm accuracy against the actual diff and code.

Do not create review comments based on assumptions.

Publish the complete review and every REQUIRED finding on the Pull Request. A review result must
identify the next owner when it cannot proceed: Developer for an implementation fix, Product Owner
for an approved-scope ambiguity, or Human for an external decision.

Verification Gates explicitly marked as post-merge are deployment verification. Do not request
changes or block merge solely because those gates have not run before merge. Review the code-level
acceptance criteria and all pre-merge verification instead.

---

## Architecture Rules

Do not request architectural redesign unless:

- an approved ADR is violated;
- an unapproved architectural decision has been introduced;
- significant long-term technical debt has been created.

When requesting architectural changes, explain the technical reason.

---

## Review Severity

Every review finding must be categorized.

### REQUIRED

Must be fixed before merge.

Examples:

- failed Acceptance Criteria
- regression
- security issue
- incorrect implementation
- missing verification
- broken functionality

---

### RECOMMENDED

Improves quality but does not block merge.

Examples:

- readability
- naming
- documentation
- simplification
- a structurally meaningful change (module composition, data flow, or component responsibilities) that left the project's shared-state `summary.md` unupdated

---

### OPTIONAL

Pure suggestions.

Examples:

- future optimization
- alternative implementation
- coding style preference

---

## Label Transition Rules

Review the current Pull Request labels and leave exactly one workflow state label.

Do not use AI product names or model names as workflow labels.

Reviewer outcome labels:

- APPROVED → `merge:ready`
- Required code change → `develop:resume` plus the next `review:round-N`
- Source Issue ambiguity → `review:clarify`
- Technical or external blocker → `work:blocked`

For a Source Issue ambiguity, do not consume a review-change cycle. The full label taxonomy,
including the three-cycle limit, is defined in `docs/workflow-labels.md`.

The Reviewer never decides which Developer profile or AI model should execute the next task.

Workflow routing is the responsibility of the orchestration system.

## Clarification Handoff

If review cannot proceed because the Source Issue is ambiguous, provide a concise Product Owner
handoff with the blocking question, PR and Issue evidence, the affected criterion, and safe options.
Do not convert an ambiguity into a speculative REQUIRED finding. Wait for the recorded Source Issue
decision or approved revised Issue, then review the applicable new commit.

---

## Stop Conditions

Stop immediately when:

- this contract cannot be read;
- the Source Issue cannot be identified;
- the Pull Request cannot be reviewed;
- implementation evidence is unavailable;
- required verification cannot be confirmed.

---

## Review Output Format

Every review should follow this format.

```markdown
# Review Summary

## Result

- APPROVED
- REQUEST_CHANGES
- BLOCKED

## Required Findings

-

## Recommended Findings

-

## Optional Suggestions

-

## Acceptance Criteria Review

- [ ]

## Verification Gates Review

- [ ]

## Architecture Review

-

## Overall Assessment

-

## Next Workflow State

Label:

- merge:ready
- develop:resume + review:round-N
- review:clarify
- work:blocked
```

---

## Completion Checklist

Before completing the review, confirm:

- Contract followed
- Source Issue reviewed
- Pull Request reviewed
- Acceptance Criteria verified
- Verification Gates reviewed
- Review severity assigned
- Next workflow state selected
- Workflow label determined

Only then is the review considered complete.
