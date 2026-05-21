---
name: agentic-codebase-architecture
description: Use when the user wants a codebase made more modular, readable, scalable, maintainable, testable, bug-resistant, performance-aware, UX-preserving, and agent-navigable without changing intended functionality. Applies to architecture reviews, conservative refactors, cleanup, redundancy removal, bug hunting/prevention, product-spec-aware restructuring, semantic-search or graph feature planning, multi-subagent deep dives, self-grilled plans, restorepoint-backed edits, and phased behavior-preserving implementation.
---

# Agentic Codebase Architecture

Use this skill to make a codebase cleaner, safer, and easier to extend without breaking behavior, user trust, UX smoothness, convenience, or polish. Optimize for senior engineers, future agents, and real users.

## Non-Negotiables

Preserve intended functionality and user experience unless the user explicitly asks for product changes.

Before editing any file, create a restore point:

- Inspect the workspace state first.
- In git repos, prefer a reversible checkpoint that preserves user work: branch, patch, stash, or checkpoint commit only when appropriate.
- In non-git workspaces, copy only files that will be edited into a named restore folder outside the skill/project source when possible.
- Tell the user what restore point exists before editing.

Never revert, overwrite, delete, or rename unrelated user changes.

Stop and ask before:

- deleting files or data
- changing public data formats, storage schemas, migrations, routes, APIs, or config contracts
- adding dependencies
- changing visible UX, copy, keyboard behavior, focus behavior, loading behavior, autosave, shortcuts, import/export behavior, or error messaging
- making a bug fix where intended behavior is ambiguous

Bug fixes are allowed only when they restore intended behavior. If behavior may be user-relied-on, preserve compatibility and flag the uncertainty.

Performance work is required only when performance is user-visible, product-critical, requested, or clearly on the touched path. Avoid speculative optimization.

## Success Standard

The finished codebase should feel obvious to modify:

- product concepts map to module names
- responsibilities are easy to explain
- dependency direction is boring and predictable
- interfaces are small but hide meaningful implementation depth
- infrastructure providers can change behind adapters
- invariants have one owner
- async lifecycle and failure modes are explicit
- tests protect module interfaces and critical user flows
- future agents can find where to add a feature without wandering

"Perfect" means the best safe next state, not endless refactoring. Stop when:

- the user's stated priorities are addressed
- intended behavior and UX are preserved
- validation is as strong as practical
- remaining risks are named
- additional changes would be speculative, risky, or outside scope

Avoid "architecture theater":

- abstractions over one line of code
- vague `utils`, `services`, or `helpers` dumping grounds
- splitting files so finely that one concept requires ten jumps
- barrel exports that hide ownership
- circular dependencies with prettier names
- broad rewrites disguised as cleanup

## Workflow

### 0. Get A User Briefing First

Before deep code exploration or planning, ask the user for project context. Ask as many useful questions as needed for optimal results, grouped clearly so the user can answer quickly. Do not pretend to understand the product from filenames alone.

Tell the user they can answer briefly, skip unknowns, or say "infer from code" for any item. Still ask the questions first; user context is cheaper and often more accurate than code archaeology.

If the user refuses or gives partial answers, continue with explicit assumptions. Mark assumptions as assumptions, prefer conservative choices, and use code exploration to fill gaps. Do not repeatedly badger the user after a clear refusal.

Do not treat missing answers as permission to broaden scope. Missing answers narrow the scope and increase conservatism.

Required briefing questions:

- What is this codebase, in one paragraph?
- Who is the target audience, and what do they care about most?
- What are the most important user workflows that must never break?
- What should feel especially smooth, convenient, fast, or polished?
- Which platforms and operating systems must work?
- Is this desktop, web, mobile, CLI, backend, library, plugin, or hybrid?
- What stage is the product in: prototype, MVP, beta, production, research, internal tool?
- What matters most right now: modularity, readability, bug reduction, performance, UX polish, scalability, testability, agent navigability, or shipping speed?
- What should not change under any circumstances?
- What existing behavior is weird but intentional or user-relied-on?
- What bugs, flaky behavior, or scary areas do you already know about?
- What features or integrations are planned soon?
- Are there data formats, local files, schemas, APIs, sync rules, or compatibility promises that must be preserved?
- What dependencies, frameworks, languages, or architectural choices are locked in?
- Are new dependencies allowed, or should the agent avoid them?
- What validation should prove nothing broke?
- Are there performance targets or known slow paths?
- Are there security, privacy, offline, local-first, accessibility, or compliance constraints?
- What parts of the codebase do you think are messiest or hardest for agents to understand?
- What would make the final structure feel impressive and resume-worthy to you?

Optional deeper questions when relevant:

- What data can users lose if something goes wrong?
- Which files, folders, or modules are off-limits?
- Which flows need keyboard support, focus stability, autosave, undo/redo, import/export, or background processing?
- Which errors should be visible to users, logged, retried, or silently recovered?
- What scale should the architecture handle: users, documents, notes, projects, requests, files, embeddings, or records?
- Which future providers should be swappable: storage, AI models, search, sync, auth, payments, exports, imports?
- What other projects should this integrate with later?
- What kind of tests are acceptable to add: unit, integration, browser, snapshot, fixture, contract, performance?
- What is the user's tolerance for temporary compatibility wrappers or migration phases?

After the user answers, summarize the briefing into:

- product purpose
- protected behavior
- protected UX qualities
- priority order
- constraints and non-goals
- risk areas
- validation expectations

Use that summary as the source of truth unless code proves it wrong.

Before planning, check briefing quality:

- If protected behavior is unclear, ask one more targeted question.
- If UX expectations are unclear, preserve current UX exactly.
- If performance priority is unclear, do not optimize except for obvious regressions on touched paths.
- If validation is unclear, infer the repo's strongest standard commands and report the assumption.
- If off-limits areas are unclear, avoid destructive or contract-changing edits.

If the task is urgent or tiny, ask only the relevant subset. For broad codebase restructuring, ask the full briefing before proceeding.

### 0.5. Use The Embedded Grill Protocol

This skill is self-contained; do not assume any separate grilling skill exists.

Embedded `grill-me` behavior, adjusted for self-use:

Interview yourself relentlessly about every aspect of this plan until reaching shared understanding. Walk down each branch of the design tree, resolving dependencies between decisions one-by-one. For each question, provide your recommended answer.

Ask the questions one at a time when user input is required.

If a question can be answered by exploring the codebase, explore the codebase instead.

Apply that behavior as follows:

- Walk the decision tree branch by branch.
- Resolve dependencies between decisions in order.
- For every question, provide the recommended answer and why it is safest.
- Ask the user one unresolved question at a time when their judgment is required.
- If a question can be answered by inspecting the codebase, inspect the codebase instead of asking.
- Stop grilling only when the plan is specific, behavior-preserving, reversible, validated, and aligned with the user briefing.

Use grilling in three places:

- before code exploration, to clarify product purpose and constraints
- before planning, to challenge architecture choices and priorities
- before editing, to challenge behavior, UX, bug, performance, and rollback risks

### 1. Establish Context

Read the product and project shape before judging code:

- product spec, roadmap, user vision, or issue context
- `README`, `CONTEXT.md`, `docs/`, ADRs, architecture notes
- package scripts, tests, build, runtime entrypoints
- editor/UI state, storage, AI/search, sync/import/export, and integration modules when relevant

Extract:

- intended behavior that must not change
- UX qualities that must not regress
- current bugs, flaky behavior, weak invariants, and regression-prone flows
- domain vocabulary and user workflows
- existing seams, adapters, and ownership boundaries
- future extension pressure
- performance relevance
- where future agents are likely to misunderstand the system

### 2. Use Smart Multiple Subagents

When the user asks for subagents, deep dives, or broad architecture work, use multiple focused subagents. Do not duplicate the same task across agents.

Before launching, decide:

- what the main agent keeps on the critical path
- which questions are independent
- which outputs are needed before planning or editing
- which write scopes must stay disjoint if implementation happens later

Recommended explorer scopes:

- Product/domain map and future extension pressure
- UI/editor/workflow state, UX smoothness, and regression-prone interactions
- Storage, persistence, import/export, sync, and data lifecycle
- AI/local LLM/search/provider seams and async/runtime risks
- Tests, build, behavior locks, and validation gaps
- Duplicate code, shallow modules, naming, and agent navigation friction
- Current bug hunt: crashes, stale state, races, data loss, bad fallbacks
- Future bug prevention: unclear ownership, duplicated invariants, weak seams, missing tests
- Performance hot paths only when performance matters

Ask every subagent for:

- files inspected
- concrete findings with confidence level
- confirmed bugs vs likely bugs vs architecture risks vs ambiguous behavior
- behavior-preserving opportunities
- UX/performance risks if relevant
- validation suggestions

Integrate results into one opinionated plan. Resolve contradictions by inspecting code or naming the tradeoff. Do not paste a pile of subagent summaries as the plan.

### 3. Map The Architecture

Create a concise map:

- domain modules: product concepts
- application modules: workflows/use cases
- infrastructure modules: storage, AI providers, search, sync, imports/exports
- UI modules: screens, panes, commands, editor integrations
- cross-cutting modules: config, errors, logging, fixtures, tests

Look for:

- shallow pass-through modules
- duplicated parsing, normalization, validation, or state transitions
- UI knowing infrastructure details
- provider details leaking upward
- async work scattered across UI
- hidden ownership of caches, indexes, migrations, and side effects
- unclear error modes and fallback behavior
- behavior coupled to accidental folder structure

### 4. Hunt And Prevent Bugs

Treat bug resistance as an architecture outcome.

Classify findings:

- **Confirmed bug**: evidence proves intended behavior is broken; fix conservatively.
- **Likely bug**: strong evidence but not fully reproduced; add a guard/test when intent is clear.
- **Architecture risk**: not failing now, but structure invites future bugs; improve ownership, invariants, tests, or adapters.
- **Ambiguous behavior**: may be intentional; preserve behavior and ask or document.

Prioritize bugs involving:

- data loss, duplicate writes, unsafe migrations
- stale state, late async writes, cancellation gaps
- broken import/export, serialization, parsing, or normalization
- silent failures, bad fallbacks, invisible failed saves
- focus loss, shortcut breakage, autosave regressions, confusing loading/error states
- first-run, empty, large, renamed, deleted, offline, or special-character cases

Prevent future bugs by making illegal states harder to represent and regressions easier to test.

### 5. Self-Grill The Plan

Use the embedded grill protocol before edits. Ask and answer these questions until the plan feels boringly safe:

- What intended behavior could this change?
- What UX smoothness, convenience, or user satisfaction could regress?
- Is each issue a confirmed bug, likely bug, architecture risk, or ambiguous behavior?
- Could this "fix" remove behavior users rely on?
- What test, reproduction, or user flow proves the intended behavior?
- What seam is being introduced, removed, or deepened?
- Is this a deeper module, or just more files?
- Is the interface smaller than the complexity behind it?
- What compatibility layer keeps callers safe?
- What future bug does the current architecture invite?
- Which invariant needs one owner?
- Is performance relevant, and what path or metric matters?
- What must not be touched in this pass?
- What restore point exists?

If any answer is fuzzy, gather more evidence before editing.

If a question depends on product intent, ask the user. If it depends on implementation facts, inspect the code. If both are needed, inspect first, then ask a narrower user question.

### 6. Plan In Reversible Phases

Prefer phases that can stop safely:

- Phase -1: user briefing, assumptions, priority order, protected behavior, protected UX
- Phase 0: behavior inventory, restore point, tests, domain vocabulary
- Phase 1: low-risk organization, naming, exports, module notes
- Phase 2: extract deep modules behind current behavior
- Phase 3: consolidate duplicated invariants and scattered logic
- Phase 4: introduce future-facing seams for search, sync, AI, imports, exports, or integrations
- Phase 5: fix confirmed bugs and prevent architecture-induced bugs with targeted tests
- Phase 6: remove compatibility shims only after all callers and tests are migrated

Every phase needs:

- files/areas affected
- unchanged behavior and UX expectations
- risk level
- bug-fix or bug-prevention scope
- validation commands/manual checks
- rollback/compatibility story
- performance check only when relevant

### 7. Implement Conservatively

When implementing:

- move code with minimal edits first; improve internals after validation
- keep existing exports until callers are migrated
- keep UI modules focused on rendering and interaction
- keep domain logic independent from UI and providers
- keep side effects near infrastructure adapters
- centralize invariants, parsing, normalization, error handling, and async lifecycle rules
- prefer adapters for storage, AI, search, sync, imports, exports, and external integrations
- add narrow tests around module interfaces and bug-prone behavior
- document only non-obvious invariants, ownership rules, and removal conditions

Do not:

- add features during refactors
- hide behavior changes inside cleanup
- rewrite the app from scratch
- change data contracts without migration and compatibility
- fix one edge case by breaking the common path
- optimize unreadable or unimportant code
- make the app feel less smooth, less convenient, or less polished

### 8. Verify

Run the strongest affordable validation:

- typecheck
- lint
- unit tests
- integration tests
- build
- targeted manual smoke tests
- bug reproduction checks for confirmed fixes
- UI/UX regression checks for changed interaction paths
- performance checks only for relevant hot paths

If validation fails, determine whether it is pre-existing or caused by the change. Report clearly.

Before final response, inspect the diff and confirm:

- restore point exists
- unrelated user changes were not reverted
- intended functionality is preserved
- UX/user satisfaction is preserved
- confirmed bugs were fixed without broad behavior changes
- future bug risks were reduced through ownership, invariants, tests, or adapters

## Feature Planning Mode

For new architecture-heavy features such as semantic search, graph linking, sync, local AI, import/export, or ecosystem integrations:

- read the product spec first
- separate product behavior from implementation mechanism
- define domain objects before technology choices
- design provider seams early
- include a minimal first slice
- plan lifecycle: indexing, invalidation, migration, stale data, offline mode, privacy, concurrency, cancellation
- define thresholds, ranking, review UX, confidence, provenance, and false-positive handling where relevant
- plan representative tests and edge cases

For semantic search and graph features:

- model notes, notebooks, sections, pages, tags, links, embeddings, graph edges, and imported documents as domain objects
- separate retrieval, indexing, ranking, graph inference, and presentation
- support explicit links and inferred links
- store confidence and provenance for inferred links
- surface uncertain links as suggestions instead of silently cluttering the graph
- avoid broad topic links that connect everything to everything
- keep local/cloud/external-vault providers swappable behind adapters
- learn from tools like Obsidian at the UX level; do not assume their internals can be embedded safely without licensing and technical verification

## Output Contract

For a plan, return:

- user briefing summary and assumptions
- architecture diagnosis
- bug findings by confidence category
- self-grilled risks and answers
- phased behavior-preserving plan
- UX preservation plan
- performance strategy or "not critical for this pass"
- restore point strategy
- validation strategy
- explicit non-goals

For implementation, return:

- restore point created
- briefing assumptions preserved
- structural changes
- bugs fixed and future bugs prevented
- files changed
- behavior and UX preservation notes
- performance impact only if relevant
- validation results
- remaining risks or follow-up phases
