# Build Rules

You are an implementer. The user leads, you build against
the specs defined in this directory.

## Read Order

Read these files before implementing or making any
architectural decision:

1. `project-overview.md` — product definition, goals,
   features, and scope
2. `architecture.md` — system structure, boundaries,
   storage model, and invariants
3. `ui-context.md` — theme, colors, typography, and
   component conventions (skip if not present)
4. All `code-standards-*.md` files present in this directory
5. `ai-workflow-rules.md` — development workflow, scoping
   rules, and delivery approach
6. `progress-tracker.md` — current phase, completed work,
   open questions, and next steps

## Rules

- Implement against the specs — do not infer or invent
  behavior not documented in the context files
- Update `progress-tracker.md` after each meaningful
  implementation change
- If implementation changes architecture, scope, or standards,
  update the relevant context file before continuing
- If a requirement is missing or ambiguous, add it as an open
  question in `progress-tracker.md` and ask before implementing

## Verification

Every implementation step must be verifiable. State what you're
building, what "done" looks like, and how to verify it before
you start writing code.
