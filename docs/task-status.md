# Task Status v4

Task status is durable, machine-readable lifecycle state stored in each task's own `status.md`
(`task/task-NNN/status.md`), not in a GitHub label. The active task has one current
`State` value. `Review-Round` and `Followup` are the only supplementary fields and may coexist with
that state. `status.md` describes the present action, not history; the file's `History` section and
the task folder's other files (`spec.md`, `report.md`, `review.md`, `merge.md`, `deploy.md`) record
the detailed history.

## States

| Scope | State | Meaning |
|---|---|---|
| Task | `develop:ready` | Approved new work is ready for a Developer. |
| Task | `develop:working` | A Developer is actively working. |
| Task | `develop:clarify` | Development is paused for a Product Owner/user decision. |
| Task | `develop:resume` | A Developer must resume an existing branch. |
| Task | `review:ready` | A report is queued for review. |
| Task | `review:working` | A Reviewer is actively reviewing. |
| Task | `review:clarify` | Review is paused for a Product Owner/user decision. |
| Task | `review:resume` | A recorded decision requires re-review of the same commit. |
| Task | `review:round-1`, `review:round-2`, `review:round-3` | Supplementary count of completed review-fix cycles. |
| Task | `merge:ready` | All pre-merge gates passed. |
| Task | `merge:working` | Automatic local merge is executing or being confirmed. |
| Task | `deploy:working` | The merged change is deploying. |
| Task | `deploy:failed` | Deployment failed. |
| Task | `e2e:working` | Post-merge E2E verification is running. |
| Task | `e2e:failed` | Post-merge E2E verification failed. |
| Task | `followup:deploy` | Supplementary origin for an automatically created deployment-recovery task. |
| Task | `followup:e2e` | Supplementary origin for an automatically created E2E-recovery task. |
| Task | `work:blocked` | A technical, access, or external blocker prevents safe progress. |
| Task | `work:done` | Deployment and all required post-merge E2E gates succeeded. |

## Trigger and Claim Rules

- A new Developer worker starts only from a task whose `status.md` has `State: develop:ready`.
- A resume Developer worker starts only from `develop:resume`. It resumes the existing task branch;
  it never creates another task folder or branch for the same spec.
- A Reviewer worker starts only from `review:ready` or `review:resume` on a task.
- A worker atomically claims its target by rewriting `State` to `develop:working` or
  `review:working` in `status.md` and appending a `History` line. It preserves any `Review-Round`
  value already set.
- The runtime must also enforce one active worker lease per task folder and check for an existing
  task branch before creating a new worktree. `status.md` alone is not a concurrency lock.

## Transition Rules

```text
Task develop:ready
  -> develop:working
  -> review:ready
  -> review:working
  -> merge:ready
  -> merge:working
  -> deploy:working
  -> e2e:working
  -> work:done
```

- Developer ambiguity: `develop:working -> develop:clarify -> develop:resume -> develop:working`.
- Reviewer ambiguity without code change:
  `review:working -> review:clarify -> review:resume -> review:working`.
- Reviewer-required or clarification-required code change:
  `review:working -> develop:resume + review:round-N -> develop:working + review:round-N
  -> review:ready + review:round-N`.
- A reviewer increments `Review-Round` only when its decision requires a code change. It replaces
  the previous round value before applying the next one.
- At `review:round-3`, another required code change becomes
  `review:clarify + review:round-3`; the Product Owner asks the user whether to permit another
  correction cycle, revise the plan, or stop. Automatic code-change looping stops there.
- A deployment failure becomes `deploy:failed`; an E2E failure becomes `e2e:failed`. The Product
  Owner creates a new task folder with `develop:ready` and respectively `followup:deploy` or
  `followup:e2e`. The merged task never returns to a development or review state.
