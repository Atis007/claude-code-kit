# claude-code-kit

Modular configuration files for AI coding agents — [Claude Code](https://docs.anthropic.com/en/docs/claude-code), [OpenAI Codex](https://developers.openai.com/codex/), [Cursor](https://cursor.com/docs/rules), and [OpenCode](https://opencode.ai/docs/rules/). A single behavior file (`CLAUDE.md`) defaults to advisor mode. Drop a `context/` folder into any project to activate build mode. Stack-specific standards and project scaffolding templates.

`CLAUDE.md` is the canonical behavior file — the exact same content drives every tool; only its filename and location differ per tool (see [Using with Codex, Cursor, and OpenCode](#using-with-codex-cursor-and-opencode)).

Ships with C, C#, Express, Go, Java, JavaScript, PHP, Python, React, React Native, Rust, and TypeScript. Designed to grow — add any stack by dropping in two files.

Works alongside the [Karpathy behavioral guidelines](https://github.com/multica-ai/andrej-karpathy-skills) plugin.

## How It Works

A single behavior file installed globally for your tool of choice applies to every project:

- **No `context/` directory in the project** → **Advisor Mode**: the agent reads code and answers questions. No file modifications unless explicitly asked.
- **`context/` directory exists in the project** → **Build Mode**: the agent reads `build-rules.md` and all spec files, then implements against them. You lead, the agent builds.

No per-project behavior files. No switching. The presence of `context/` is the switch. This works identically across Claude Code, Codex, Cursor, and OpenCode — only the global file's name/location differs per tool.

## One-Time Setup

1. Install the behavior file at your tool's global path — see
   [Using with Codex, Cursor, and OpenCode](#using-with-codex-cursor-and-opencode)
   for the exact name/location. For Claude Code: place `CLAUDE.md` at
   `~/.claude/CLAUDE.md`.
2. Install the [Karpathy guidelines](https://github.com/multica-ai/andrej-karpathy-skills) plugin (recommended, Claude Code only)

That's it. Every project now runs in advisor mode by default.

## Using with Codex, Cursor, and OpenCode

`CLAUDE.md` is the single source of truth for agent behavior. The content is
identical for every tool — you just install it under the name and location that
tool expects. The `context/` folder and all spec files are copied **unchanged**
regardless of tool.

| Tool | Where the behavior file goes | How |
| ---- | ---------------------------- | --- |
| Claude Code | `~/.claude/CLAUDE.md` | copy as-is |
| OpenAI Codex | `~/.codex/AGENTS.md` | rename `CLAUDE.md` → `AGENTS.md` |
| OpenCode | `~/.config/opencode/AGENTS.md` | rename `CLAUDE.md` → `AGENTS.md` |
| Cursor | project `AGENTS.md`, User Rules, or `.cursor/rules/*.mdc` | see below |

**Codex** and **OpenCode** read `AGENTS.md` as their instructions file (Codex
does not read `CLAUDE.md` at all, so the rename is required). Both also support a
project-level `AGENTS.md` if you prefer per-repo behavior over a global file.

**Cursor** has three options, pick one:

- **Project file** — drop the renamed `AGENTS.md` in the project root. Cursor
  reads it as plain-markdown instructions.
- **User Rules** — paste the `CLAUDE.md` contents into Settings → Rules → User
  Rules to apply it globally across all projects (closest to the other tools'
  global file).
- **Native project rule** — create `.cursor/rules/code-advisor.mdc` and prepend
  this YAML frontmatter above the `CLAUDE.md` body:
  ```
  ---
  description: Code advisor / build-mode behavior
  alwaysApply: true
  ---
  ```

The advisor-vs-build-mode switch (presence of `context/`) works the same in
every case — it is behavior described inside the file, not tool-specific config.

## Repo Structure

```
├── CLAUDE.md                              ← Behavior file; install per tool (once) — see "Using with Codex, Cursor, and OpenCode"
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

- One of [Claude Code](https://docs.anthropic.com/en/docs/claude-code), [OpenAI Codex](https://developers.openai.com/codex/), [Cursor](https://cursor.com/docs/rules), or [OpenCode](https://opencode.ai/docs/rules/) installed
- [Karpathy guidelines](https://github.com/multica-ai/andrej-karpathy-skills) plugin installed (recommended, Claude Code only)

## License

MIT
