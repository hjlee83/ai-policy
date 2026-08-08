# Autonomous Delivery Use Cases v4

These use cases exercise the role contracts without prescribing a particular runtime. Slack is the
concise decision surface; each task's local folder (`~/task/<project>/task-NNN/`) is the durable
record. `status.md` describes the current action and uses `Review-Round` or `Followup` only as
supplementary context.

## Common Invariants

- A new Developer starts only from an approved task whose `status.md` has `develop:ready`.
- A resume Developer works on the existing task branch; it never creates a second branch or task
  folder for the same spec.
- A user decision is never inferred from silence. Questions, answers, and decisions are recorded
  in the task folder.
- Every review result and REQUIRED finding is recorded in `review.md`.
- Only pre-merge gates determine `merge:ready`.
- A merged task is never reopened to repair a post-merge failure; recovery uses a follow-up task
  folder.

## UC-01: Normal autonomous delivery

```text
Slack request
  -> task folder created [develop:ready]
  -> Developer claim [develop:working]
  -> report.md created [review:ready]
  -> Reviewer claim [review:working]
  -> approved [merge:ready]
  -> automatic local merge [merge:working]
  -> deployment [deploy:working]
  -> post-merge E2E [e2e:working]
  -> complete [work:done]
```

## UC-02: Developer clarification

```text
task [develop:working]
  -> ambiguity [develop:clarify]
  -> Slack question: 1 option A / 2 option B / 3 free text / 4 ask again
  -> decision recorded in the task folder
  -> resume trigger [develop:resume]
  -> existing task branch resumed [develop:working]
```

If the decision materially changes approved scope, the Product Owner obtains approval for the
revised full Spec Preview before applying `develop:resume`.

## UC-03: Reviewer-required code change

```text
task [review:ready]
  -> Reviewer claim [review:working]
  -> REQUIRED code fix [develop:resume, review:round-1]
  -> Developer claim [develop:working, review:round-1]
  -> same branch push [review:ready, review:round-1]
  -> Reviewer claim [review:working, review:round-1]
  -> approved [merge:ready]
```

For another required code change, replace `review:round-1` with `review:round-2` and repeat.

## UC-04: Reviewer clarification without a code change

```text
task [review:working]
  -> ambiguous approved scope [review:clarify]
  -> Slack question and task folder decision
  -> same-commit re-review trigger [review:resume]
  -> Reviewer claim [review:working]
  -> approved [merge:ready]
```

The runtime deduplicates this re-review by the recorded decision ID as well as the task branch
commit SHA.

## UC-05: Reviewer clarification requiring a code change

```text
task [review:working]
  -> ambiguous approved scope [review:clarify]
  -> Slack question and spec.md update
  -> code change [develop:resume, review:round-1]
  -> same task branch resumed [develop:working, review:round-1]
  -> push [review:ready, review:round-1]
```

## UC-06: Review repetition limit

```text
task [review:working, review:round-2]
  -> another REQUIRED code fix [develop:resume, review:round-3]
  -> push and re-review [review:working, review:round-3]
  -> another REQUIRED code fix [review:clarify, review:round-3]
  -> Slack asks: 1 one more correction / 2 revise plan / 3 stop / 4 ask again
```

Automatic code-fix looping stops at that final clarification. If the user permits another
correction, the Product Owner records the decision and applies `develop:resume` while preserving
`review:round-3`.

## UC-07: Deployment failure

```text
Merged task [deploy:working]
  -> deployment failure [deploy:failed]
  -> follow-up task folder [develop:ready, followup:deploy]
  -> new recovery flow begins
```

## UC-08: Post-merge E2E failure

```text
Merged task [e2e:working]
  -> E2E failure [e2e:failed]
  -> follow-up task folder [develop:ready, followup:e2e]
  -> new recovery flow begins
```

## UC-09: Duplicate and delayed events

| Event | Expected outcome |
|---|---|
| Duplicate Slack request, answer, or local filesystem event | One durable action is taken; later duplicates observe the same `status.md` state record. |
| Duplicate worker claim | The lease permits only one active Developer or Reviewer per task folder. |
| Slack notification failure after a task-folder mutation | The task folder remains authoritative; the concise Slack notification retries without repeating the mutation. |
| Local tooling temporary failure | The state does not advance; retry follows the existing state. |
