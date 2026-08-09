# Task Status Template

Save as `task/task-NNN/status.md` at the Target Repository root, and commit it to git. This file is the single source of truth for
the task's current lifecycle state. See `docs/task-status.md` for the full state list and
transition rules.

```markdown
# Task Status

Task: task-NNN

State: develop:ready
Review-Round:
Followup:

## History

- <timestamp> develop:ready — spec approved
```

Keep exactly one current `State` value. `Review-Round` and `Followup` are supplementary fields and
may be blank. Append a line to `History` on every state change instead of deleting prior lines.
