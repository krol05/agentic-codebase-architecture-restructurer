# Agentic Codebase Architecture Restructurer

A universal agent skill/workflow for safely restructuring codebases into cleaner, more modular, more maintainable architectures without changing intended functionality or degrading UX.

This is not tied to one coding agent. It is written as a portable `SKILL.md` instruction package that can be used by Codex, Claude Code, Cursor-style agents, OpenCode-style agents, or any agent that can load Markdown workflow instructions.

## Why This Exists

Most architecture cleanup fails in one of two ways:

- it moves code around without making the system easier to change
- it accidentally changes behavior, UX smoothness, or user expectations

This skill pushes the agent toward the opposite: conservative, reversible restructuring that makes the project easier for humans and future agents to understand.

## Important: Good User Input Matters

This skill relies on strong user input for optimal results.

Before deep code exploration, the agent is instructed to ask a structured project briefing: what the product is, who uses it, what must never break, which UX details matter, which platforms must work, what performance means, what bugs are already known, and what future features are planned.

That matters because code can reveal implementation, but the user often knows intent:

- weird behavior that is intentional
- workflows users rely on
- future integrations that should shape module seams
- performance or UX expectations that are not obvious from files
- parts of the product that matter more than others

If the user gives partial answers, the skill tells the agent to proceed with explicit assumptions and be more conservative, not more reckless.

## What It Does

- Front-loads a user briefing before expensive code archaeology
- Creates restore points before edits
- Preserves intended functionality and UX polish
- Uses focused subagents for broad architecture work when the agent supports delegation
- Hunts current bugs and future architecture-induced bug risks
- Classifies findings as confirmed bugs, likely bugs, architecture risks, or ambiguous behavior
- Embeds a self-contained grilling protocol, so no separate grilling skill is required
- Plans reversible phases instead of broad rewrites
- Treats performance intelligently, only when critical or requested
- Verifies with the strongest practical validation available

## Repository Layout

```text
agentic-codebase-architecture-restructurer/
├── README.md
└── agentic-codebase-architecture/
    ├── SKILL.md
    └── agents/
        └── openai.yaml
```

`SKILL.md` is the portable instruction file. `agents/openai.yaml` is optional metadata for OpenAI/Codex-style skill UIs; other agents can ignore it.

## Installation

Use the installation location expected by your agent.

### Codex / OpenAI Skills

Copy the skill folder into your Codex skills directory:

```powershell
Copy-Item -Recurse .\agentic-codebase-architecture "$env:USERPROFILE\.codex\skills\"
```

Then restart Codex so it discovers the skill.

Manual target:

```text
~/.codex/skills/agentic-codebase-architecture/
```

### Other Agent Systems

Copy the `agentic-codebase-architecture/` folder into the directory your agent scans for skills or reusable instructions. The important file is:

```text
agentic-codebase-architecture/SKILL.md
```

If your agent does not support skill folders, paste or reference `SKILL.md` as a reusable architecture workflow instruction.

## Usage

Ask your coding agent to use:

```text
Agentic Codebase Architecture Restructurer
```

Example request:

```text
Use Agentic Codebase Architecture Restructurer to make this codebase modular, readable, scalable, and bug-resistant without changing functionality or degrading UX. Ask me the project briefing first, then create a restore point, use subagents where useful, self-grill the plan, and only edit after the plan is safe.
```

## Core Guardrails

- Do not change intended functionality unless explicitly asked.
- Do not degrade UX smoothness, convenience, polish, shortcuts, focus behavior, loading states, autosave, or user trust.
- Do not treat ambiguous behavior as a bug.
- Do not add dependencies, delete files, change schemas, or change public contracts without asking.
- Do not optimize performance unless it is critical, requested, or clearly on the touched path.
- Do not do architecture theater: no pointless abstractions, vague dumping grounds, or broad rewrites.

## Agent Compatibility

The workflow is intentionally agent-neutral:

- Subagents are used only when the host agent supports delegation.
- Restore points adapt to the workspace: git branch/patch/stash/checkpoint where appropriate, file backups otherwise.
- Validation adapts to the project: typecheck, lint, tests, build, smoke checks, UX checks, and performance checks only when relevant.
- The embedded grilling protocol does not require any separate skill.

## Inspired By

The structure follows common Agent Skills conventions used by public skill repositories:

- a discoverable skill folder
- required `SKILL.md` with YAML frontmatter
- optional UI metadata, such as `agents/openai.yaml`
- concise README with purpose, layout, install, and usage
- progressive disclosure: the agent loads the workflow only when relevant

## Validation

The skill package was validated with the OpenAI/Codex skill validator for `SKILL.md` compatibility:

```text
Skill is valid!
```
