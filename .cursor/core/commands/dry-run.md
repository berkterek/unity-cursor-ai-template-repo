# Dry Run

Preview an orchestration plan without modifying source files or assigning
agent work.

## Inputs

Read:

- `.cursor/project/WORKFLOW.md`
- `.cursor/project/PROJECT.md`
- `.cursor/project/STRUCTURE.md`
- `.cursor/project/TOOLING.md`
- Enabled pack instructions if they affect agent types.

If no workflow exists, stop and ask for one.

## Process

Analyze the workflow and report:

- Total phases.
- Total tasks.
- Tasks by type.
- Parallel batches.
- Potential file conflicts.
- Critical path.
- Expected agent-role invocations.
- Review gates.
- Verification commands.
- Commit gates.

## Output

```markdown
## Orchestration Dry Run

Execution summary:
- Total phases: [count]
- Total tasks: [count]
- Estimated agent-role invocations: [count]
- Max parallel tasks: [count]
- Commit mode: [per-phase/none/manual]

Phase breakdown:

### Phase [n]: [name]
- Tasks: [count]
- Parallel batches: [count]
- Verification: [commands]

| Batch | Task | Agent | Outputs | Risk |
|-------|------|-------|---------|------|
| 1 | P1.T1 | coder | path | low |

Risk points:
- [conflicts, bottlenecks, missing inputs]

Proceed by running orchestrate.
```

## Rules

- Do not assign agent work.
- Do not edit project files.
- Do not update progress or events.
- Be realistic about likely re-review and conflict points.
