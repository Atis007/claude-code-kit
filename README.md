# claude-code-kit

Modular configuration files for [Claude Code](https://docs.anthropic.com/en/docs/claude-code). Global CLAUDE.md defaults to advisor mode. Drop a `context/` folder into any project to activate build mode. Stack-specific standards and project scaffolding templates.

Ships with C, C#, Express, Go, Java, JavaScript, PHP, Python, React, React Native, Rust, and TypeScript. Designed to grow — add any stack by dropping in two files.

Works alongside the [Karpathy behavioral guidelines](https://github.com/multica-ai/andrej-karpathy-skills) plugin.

## How It Works

A single `CLAUDE.md` at `~/.claude/CLAUDE.md` applies globally to every project:

- **No `context/` directory in the project** → **Advisor Mode**: Claude reads code and answers questions. No file modifications unless explicitly asked.
- **`context/` directory exists in the project** → **Build Mode**: Claude reads `build-rules.md` and all spec files, then implements against them. You lead, Claude builds.

No per-project CLAUDE.md files. No switching. The presence of `context/` is the switch.

## One-Time Setup

1. Place `CLAUDE.md` at `~/.claude/CLAUDE.md`
2. Install the [Karpathy guidelines](https://github.com/multica-ai/andrej-karpathy-skills) plugin (recommended)

That's it. Every project now runs in advisor mode by default.

## Repo Structure

```
├── CLAUDE.md                              ← Goes to ~/.claude/CLAUDE.md (once)
└── context/
    ├── build-rules.md                     ← Build mode instructions
    ├── templates/                         ← Universal, fill per project
    │   ├── ai-workflow-rules.md
    │   ├── progress-tracker.md
    │   ├── project-overview.md
    │   └── ui-context.md
    ├── standards/                         ← Stack-specific, reusable as-is
    │   ├── code-standards-c.md
    │   ├── code-standards-csharp.md
    │   ├── code-standards-express.md
    │   ├── code-standards-go.md
    │   ├── code-standards-java.md
    │   ├── code-standards-js.md
    │   ├── code-standards-php.md
    │   ├── code-standards-python.md
    │   ├── code-standards-react.md
    │   ├── code-standards-rn.md
    │   ├── code-standards-rust.md
    │   └── code-standards-typescript.md
    └── architecture/                      ← Stack starters, pick + customize
        ├── architecture-c.md
        ├── architecture-csharp.md
        ├── architecture-express.md
        ├── architecture-go.md
        ├── architecture-java.md
        ├── architecture-js.md
        ├── architecture-php.md
        ├── architecture-python.md
        ├── architecture-react.md
        ├── architecture-rn.md
        └── architecture-rust.md
```

**Templates** are language-agnostic — same files for every project, filled in with your specifics.

**Standards and architecture** are stack-specific — pick the ones matching your project, or create new ones for any stack.

## Activating Build Mode for a Project

1. Create a `context/` folder in your project root
2. Copy `build-rules.md` into `context/`
3. Copy the templates you need from `templates/` into `context/` — **flat, as siblings to `build-rules.md`**, not inside a subfolder
4. Pick your architecture starter from `architecture/`, copy into `context/` and rename to `architecture.md`
5. Pick your code standards from `standards/`, copy into `context/`
6. Fill in the template placeholders

**Important:** In your project, all files must be flat inside `context/`. The subdirectories (`templates/`, `standards/`, `architecture/`) only exist in this repo for organization. Your project should look like this:

```
my-project/
├── context/
│   ├── build-rules.md
│   ├── project-overview.md
│   ├── architecture.md              ← renamed from architecture-{stack}.md
│   ├── ui-context.md
│   ├── code-standards-typescript.md
│   ├── code-standards-rn.md
│   ├── ai-workflow-rules.md
│   └── progress-tracker.md
├── src/
└── ...
```

## Example Stack Combos

Files to copy flat into your project's `context/` (alongside `build-rules.md`):

**C (systems)**
```
build-rules.md
project-overview.md
architecture.md              ← from architecture/architecture-c.md
code-standards-c.md
ai-workflow-rules.md
progress-tracker.md
```

**C# / .NET (backend)**
```
build-rules.md
project-overview.md
architecture.md              ← from architecture/architecture-csharp.md
code-standards-csharp.md
ai-workflow-rules.md
progress-tracker.md
```

**Express.js + TypeScript (backend)**
```
build-rules.md
project-overview.md
architecture.md              ← from architecture/architecture-express.md
code-standards-typescript.md
code-standards-express.md
ai-workflow-rules.md
progress-tracker.md
```

**Go (backend / systems)**
```
build-rules.md
project-overview.md
architecture.md              ← from architecture/architecture-go.md
code-standards-go.md
ai-workflow-rules.md
progress-tracker.md
```

**Java (backend)**
```
build-rules.md
project-overview.md
architecture.md              ← from architecture/architecture-java.md
code-standards-java.md
ai-workflow-rules.md
progress-tracker.md
```

**JavaScript (vanilla / Node.js)**
```
build-rules.md
project-overview.md
architecture.md              ← from architecture/architecture-js.md
code-standards-js.md
ai-workflow-rules.md
progress-tracker.md
```

**PHP REST API (backend, no framework)**
```
build-rules.md
project-overview.md
architecture.md              ← from architecture/architecture-php.md
code-standards-php.md
ai-workflow-rules.md
progress-tracker.md
```

**Python (backend / scripting)**
```
build-rules.md
project-overview.md
architecture.md              ← from architecture/architecture-python.md
code-standards-python.md
ai-workflow-rules.md
progress-tracker.md
```

**React + Tailwind (web frontend)**
```
build-rules.md
project-overview.md
architecture.md              ← from architecture/architecture-react.md
ui-context.md
code-standards-typescript.md
code-standards-react.md
ai-workflow-rules.md
progress-tracker.md
```

**React Native + Expo (mobile)**
```
build-rules.md
project-overview.md
architecture.md              ← from architecture/architecture-rn.md
ui-context.md
code-standards-typescript.md
code-standards-rn.md
ai-workflow-rules.md
progress-tracker.md
```

**Rust (backend / systems)**
```
build-rules.md
project-overview.md
architecture.md              ← from architecture/architecture-rust.md
code-standards-rust.md
ai-workflow-rules.md
progress-tracker.md
```

**Full-stack:** Combine frontend and backend files. Use one `architecture.md` covering both layers, or split into `architecture-frontend.md` and `architecture-backend.md`.

## Adding a New Stack

The kit grows with you. To add Kotlin, Swift, Zig, or anything else:

1. Create `context/standards/code-standards-{stack}.md` — your coding conventions for that stack
2. Create `context/architecture/architecture-{stack}.md` — typical system structure and boundaries
3. Add the combo to this README

That's it. The templates, workflow rules, and CLAUDE.md work with any stack unchanged.

## Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) installed
- [Karpathy guidelines](https://github.com/multica-ai/andrej-karpathy-skills) plugin installed (recommended, not required)

## License

MIT
