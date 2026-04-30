# Project Rules Overlay

This file contains repository-specific rules that are not general enough for
Cursor rules and not broad enough for a reusable pack.

For coding style, prefer `CODING_CONVENTIONS.md`.
For folder/module/ownership structure, prefer `STRUCTURE.md`.
For build/test commands, prefer `TOOLING.md`.

## Hard Rules

These block completion if violated:

- `[rule]`

Examples:

- Do not edit generated files manually.
- Do not commit credentials or local machine config.
- Do not change public API without tests and migration notes.

## Soft Rules

These guide implementation but may be waived with a reason:

- `[rule]`

Examples:

- Prefer existing project patterns over new abstractions.
- Keep changes scoped to the task.
- Avoid broad formatting-only rewrites during feature work.

## Architecture Boundaries

Allowed dependency directions:

```text
[Layer A] -> [Layer B]
[Layer B] -> [Layer C]
```

Forbidden dependencies:

- `[forbidden dependency]`

## API And Contract Rules

- `[rule]`

Examples:

- Public interfaces require tests.
- Breaking changes require migration notes.
- External API behavior must be documented.

## Data And Migration Rules

- `[rule]`

Examples:

- Schema/config changes require compatibility notes.
- Serialized field renames require migration handling.
- Existing user data must not be silently reset.

## Security And Secrets

- Do not commit credentials, tokens, private keys, or local machine config.
- Do not print secrets into logs.
- Do not add real production credentials to examples.
- `[project-specific secret rule]`

## Performance Rules

- `[rule]`

Examples:

- Hot paths must avoid unnecessary allocation.
- Expensive operations should be cached or measured.
- Performance-sensitive changes need before/after notes.

## Documentation Rules

- `[rule]`

Examples:

- Update README or docs when public workflows change.
- Add comments only for non-obvious decisions.
- Keep generated docs separate from handwritten docs.

## Review Rules

Reviewer must fail work when:

- Acceptance criteria are not met.
- Required verification was not run and no reason was given.
- Generated or owned files were edited against policy.
- A hard rule above was violated.
- `[blocking review condition]`

