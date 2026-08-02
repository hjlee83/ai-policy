# Workflow Labels v3

Workflow labels are durable, machine-readable lifecycle state. The active work item has one current
stage label. `review:round-*` and `followup:*` are the only supplementary labels and may coexist
with that stage label. Labels describe the present action, not history; GitHub comments and the
runtime state store the detailed history.

## Labels

| Scope | Label | Meaning |
|---|---|---|
| Issue | `develop:ready` | Approved new work is ready for a Developer. |
| Issue or PR | `develop:working` | A Developer is actively working. |
| Issue | `develop:clarify` | Development is paused for a Product Owner/user decision. |
| Issue or PR | `develop:resume` | A Developer must resume an existing worktree or PR branch. |
| PR | `review:ready` | A PR commit is queued for review. |
| PR | `review:working` | A Reviewer is actively reviewing. |
| PR | `review:clarify` | Review is paused for a Product Owner/user decision. |
| PR | `review:resume` | A recorded decision requires re-review of the same commit. |
| PR | `review:round-1`, `review:round-2`, `review:round-3` | Supplementary count of completed review-fix cycles. |
| PR | `merge:ready` | All pre-merge gates passed. |
| PR | `merge:working` | Automatic merge is executing or being confirmed. |
| PR | `deploy:working` | The merged change is deploying. |
| PR | `deploy:failed` | Deployment failed. |
| PR | `e2e:working` | Post-merge E2E verification is running. |
| PR | `e2e:failed` | Post-merge E2E verification failed. |
| Issue | `followup:deploy` | Supplementary origin for an automatically created deployment-recovery Issue. |
| Issue | `followup:e2e` | Supplementary origin for an automatically created E2E-recovery Issue. |
| Issue or PR | `work:blocked` | A technical, access, or external blocker prevents safe progress. |
| PR | `work:done` | Deployment and all required post-merge E2E gates succeeded. |

## Trigger and Claim Rules

- A new Developer worker starts only from an Issue labelled `develop:ready`.
- A resume Developer worker starts only from `develop:resume`. If the target is a PR, it resumes
  that PR's existing branch; it never creates another PR for the Source Issue.
- A Reviewer worker starts only from `review:ready` or `review:resume` on a PR.
- A worker atomically claims its target by replacing its trigger label with `develop:working` or
  `review:working`. It preserves any `review:round-*` supplementary label.
- The runtime must also enforce one active worker lease per Source Issue and check for a linked open
  PR before creating a new Developer worktree. Labels alone are not a concurrency lock.

## Transition Rules

```text
Issue develop:ready
  -> develop:working
  -> PR review:ready
  -> PR review:working
  -> PR merge:ready
  -> PR merge:working
  -> PR deploy:working
  -> PR e2e:working
  -> PR work:done
```

- Developer ambiguity: `develop:working -> develop:clarify -> develop:resume -> develop:working`.
- Reviewer ambiguity without code change:
  `review:working -> review:clarify -> review:resume -> review:working`.
- Reviewer-required or clarification-required code change:
  `review:working -> develop:resume + review:round-N -> develop:working + review:round-N
  -> review:ready + review:round-N`.
- A reviewer increments the review-round label only when its decision requires a code change. It
  removes the previous round label before applying the next one.
- At `review:round-3`, another required code change becomes
  `review:clarify + review:round-3`; the Product Owner asks the user whether to permit another
  correction cycle, revise the plan, or stop. Automatic code-change looping stops there.
- A deployment failure becomes `deploy:failed`; an E2E failure becomes `e2e:failed`. The Product
  Owner creates a new `develop:ready` Issue with respectively `followup:deploy` or `followup:e2e`.
  The merged PR never returns to a development or review state.
