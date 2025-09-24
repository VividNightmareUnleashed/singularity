# Implementation Plan

Goal: build a web-only, local-first writing workspace that extends the Novel editor with AI-assisted drafting, structured planning, chat, review analytics, and (post-MVP) optional autonomous agents, all tuned for a personal authoring workflow.

## Phase 0 – Baseline Alignment & Architecture Foundations
- **Audit & cleanup**: Review existing Novel fork (`apps/web`, `packages/headless`) to identify leftover branding, legacy assumptions (localStorage persistence, Vercel-only endpoints) and document gaps to close before extending.
- **Target architecture definition**: Confirm the web-only, local-first model; choose the browser storage stack (e.g., IndexedDB + File System Access for backups), and outline how local-only AI flows will execute. Produce ADRs covering storage, AI integration boundaries, and sandboxing.
- **Design system inventory**: Catalogue existing Tailwind UI primitives, typography, icons, and Tiptap extensions. Decide on additions needed for card sidebar, planning timelines, review dashboards, etc.
- **Tooling upgrades**: Configure workspace-wide linting, formatting, testing (Vitest/Playwright) and Storybook or Ladle for component work. Set up Changesets for publishing updated `packages/headless` builds and ensure Turbo pipelines cover new packages/apps.
- **Scaffolding new workspaces**: Create new package stubs (e.g., `packages/data`, `packages/ai`, `packages/ui`) to decouple domain logic from the web shell.
- **Data model blueprint**: Draft TypeScript interfaces for Manuscript, Scene, AIAction, Snapshot, AgentRun, ReviewIssue, Note, etc., and decide how they map to persistence.

### Phase 0 Task List
- [ ] Catalog remaining Novel-specific branding/config; note removals in `docs/audit.md`.
- [ ] Inventory persistence touchpoints (localStorage, API routes) and record migration notes.
- [ ] Draft ADR-001: local storage stack (IndexedDB wrapper + File System Access backup flow).
- [ ] Draft ADR-002: OpenAI integration boundary (personal-use, no server proxy) and sandbox assumptions.
- [ ] Audit Tailwind components/Tiptap extensions; log gaps for cards/planner/review.
- [ ] Configure Vitest + Playwright in root `package.json` via Turbo; ensure CI scripts run locally.
- [ ] Add Storybook/Ladle setup for `packages/ui` components scaffold.
- [ ] Create package scaffolds `packages/data`, `packages/ai`, `packages/ui` with tsconfig, build scripts, and placeholder exports.
- [ ] Establish initial TypeScript interfaces in `packages/data/src/models.ts` covering Manuscript, Scene, Snapshot, AIAction, ReviewIssue, Note.
- [ ] Update `/docs/` with architecture overview referencing new ADRs and package layout.

## Phase 1 – Core Writer Experience Modernization
- **Editor state refactor** (`packages/headless`): Extend Tiptap schema to support stable block IDs, scene anchors, diff metadata, and snapshots. Introduce transaction hooks emitting change events for autosave, AI references, and audit trail.
- **Document persistence service** (`packages/data`): Implement storage abstraction targeting local IndexedDB/File System Access drivers. Provide optimistic lock handling and expose CRUD hooks for `apps/web`.
- **Versioning & snapshots**: Build background autosave with named snapshots, restore, compare, and branching. Store diff metadata to support safe apply workflows later.
- **UI shell updates** (`apps/web`): Replace single-editor page with workspace layout (sidebar, tabbed panels for AI cards, planning, chat, notes). Implement responsive design and keyboard accessibility.
- **API key handling**: Keep minimal OpenAI key entry with local persistence (e.g., encrypted localStorage) suitable for personal-use scenarios.
- **Testing**: Add integration tests for editor persistence, snapshot creation, and key handling flows.

### Phase 1 Task List
- [ ] Add persistent block/scene IDs to Tiptap schema in `packages/headless/src/extensions` with migration for existing content.
- [ ] Emit transaction events from `EditorRoot` via a new event bus (e.g., jotai/store) for autosave hooks.
- [ ] Implement `packages/data/src/storage/indexeddb-driver.ts` handling manuscripts/scenes/snapshots CRUD.
- [ ] Add File System Access backup/export utility in `packages/data/src/storage/fs-access.ts`.
- [ ] Build persistence hooks (`useManuscript`, `useSnapshotHistory`) in `packages/data` and wire into `apps/web` providers.
- [ ] Implement autosave daemon in `apps/web/providers.tsx` listening to editor events and writing snapshots with diff metadata.
- [ ] Replace landing layout with new workspace shell (sidebar + tabbed panels) in `apps/web/app/page.tsx` using Tailwind layout primitives.
- [ ] Create placeholder tabs for AI Cards, Planning, Chat, Notes with routing/state scaffolding.
- [ ] Build OpenAI key modal storing encrypted key in localStorage (Web Crypto) under `apps/web/components/settings/openai-key.tsx`.
- [ ] Write Vitest tests for storage drivers and Playwright tests for autosave + workspace shell keyboard navigation.

## Phase 2 – AI Interaction Overhaul
- **Card sidebar architecture** (`packages/ui` + `apps/web`): Create card store/context tracking prompts, AI metadata (model, temperature), states (pending/running/complete/error), and actions (copy/insert/replace/pin). Render dismissible card components with diff previews.
- **Context actions engine** (`packages/ai`): Define prompt templates with variables, scope options (selection, paragraph, scene, manuscript), and execution policies (model, temperature, verbosity). Surface via right-click, toolbar, or hotkeys in the editor.
- **Safe apply workflow**: Implement side-by-side diff viewer with per-sentence accept/reject, conflict detection if document changed, and snapshot rollback integration.
- **Queue & status controls**: Add job queue with concurrency limits, cancel/retry, keyboard shortcuts, and persistent history referencing originating selection.
- **Tracing**: Attach prompt name, model, and permalink to selection; allow exporting prompt runs and pinned cards for personal records.
- **AI provider integration**: Integrate OpenAI via Vercel AI SDK with resilient retry and rate-limit handling; no multi-provider abstraction needed initially.
- **Testing**: Mock OpenAI responses for deterministic tests covering prompt execution, card pinning, and safe apply flows.

### Phase 2 Task List
- [ ] Implement jotai/zustand store in `packages/ui` for AI cards with lifecycle states and history persistence.
- [ ] Build `AICard` UI component suite (`packages/ui/src/ai-card/`) with actions for copy/insert/replace/pin and metadata badges.
- [ ] Integrate card sidebar into `apps/web` workspace shell; ensure it syncs with editor selection.
- [ ] Create context action registry in `packages/ai/src/actions.ts` defining template schema and scope resolution helpers.
- [ ] Wire editor toolbar/right-click/slash command to invoke `packages/ai` actions with selection payloads.
- [ ] Implement safe apply diff viewer in `packages/ui/src/ai-card/diff-viewer.tsx` using Tiptap doc comparisons.
- [ ] Build OpenAI client wrapper in `packages/ai/src/openai-client.ts` handling retries/rate limits and returning streaming iterables.
- [ ] Add job queue manager in `packages/ai/src/job-queue.ts` coordinating prompt execution, cancel, retry.
- [ ] Persist prompt traces (prompt name, model, selection permalink) in local history store for export.
- [ ] Write Vitest unit tests for action templating, job queue, OpenAI wrapper; Playwright test covering safe apply flow end-to-end.

## Phase 3 – Structural Planning & Research Workspace
- **Planning tab infrastructure**: Build scene/act tree view with draggable ordering, stable scene IDs synchronized to editor anchors, metadata editing (POV, beats, tags, targets). Persist structure via `packages/data`.
- **Outline & corkboard views**: Implement list, board, and timeline representations. Use virtualization for large manuscripts and ensure reordering updates anchors and snapshots.
- **Scene operations**: Add split/merge/duplicate actions with conflict protection. Allow converting selection into scene entry and update metadata accordingly.
- **Planning AI tools**: Integrate prompt templates for generating beat sheets, suggesting act breaks, and enforcing pacing; surface controls for thinking effort/verbosity per request.
- **Research & notes integration**: Add side panel for notes/worldbuilding with bi-directional links to scenes; implement basic local RAG pipeline (embedding index per project using local vector store, e.g., `@xenova/transformers`).
- **Storyboard/timeline optional module**: Prototype timeline visualization using D3/Visx with color-coded plotlines; gate behind feature flag until performance tuned.
- **Testing**: Cover drag/drop ordering, anchor syncing, and note linkage with integration tests.

### Phase 3 Task List
- [ ] Build planning data structures in `packages/data/src/planning.ts` linking scenes to manuscript anchors.
- [ ] Implement drag-and-drop scene tree (`apps/web/components/planning/scene-tree.tsx`) with virtualization.
- [ ] Sync scene ordering updates to manuscript anchors via editor transactions.
- [ ] Create metadata editor drawer (POV, beats, tags, targets) and persist via `packages/data` APIs.
- [ ] Implement outline view list and corkboard view using card components in `packages/ui`.
- [ ] Add scene split/merge/duplicate helpers in `packages/data/src/scene-operations.ts` with conflict checks.
- [ ] Implement selection-to-scene conversion action in editor context menu.
- [ ] Integrate planning AI prompts (beat sheet, act break) via `packages/ai` action templates with adjustable verbosity controls.
- [ ] Build Notes sidebar (`apps/web/components/notes/`) with bi-directional linking to scenes and world entries.
- [ ] Implement local embeddings service using `@xenova/transformers` in `packages/ai/src/local-rag.ts` with opt-in indexing controls.
- [ ] Prototype timeline view (`apps/web/components/planning/timeline.tsx`) behind feature flag; measure performance.
- [ ] Write integration tests for drag/drop ordering, metadata persistence, notes linking, and AI-assisted planning workflows.

## Phase 4 – Chat Workspace & Context Management
- **Chat tab implementation**: Create threaded chat UI with message pinning, branchable conversations, system prompt controls, and optional “write directly to manuscript” mode gated by permissions.
- **Context window management**: Show live token usage bar, soft/hard thresholds, auto-summarization for long histories, and alerts when approaching limits.
- **Reasoning controls**: Allow per-thread reasoning budget (tokens/effort), escalate on demand, and gracefully degrade if the selected model lacks reasoning knobs.
- **Prompt caching layer**: Cache responses based on prompt signature (system/tools/pinned context); support TTL, manual invalidation, and reuse indicators.
- **Message exports**: Display token counts per message and allow saving excerpts to notes/manuscript without pricing overlays.
- **Reliability features**: Implement streaming with cancel, retry/backoff, offline queue for pending requests, and robust error messaging.
- **Testing**: Simulate large threads and offline mode to verify caching, budgeting, and autosummarization logic.

### Phase 4 Task List
- [ ] Architect chat state in `packages/data/src/chat.ts` including threads, branches, message metadata, and pinned context.
- [ ] Build chat UI (`apps/web/components/chat/`) with message list virtualization, pinning, branch controls, and system prompt editor.
- [ ] Implement optional “write to manuscript” action gated by permission toggle in chat settings.
- [ ] Add token usage tracker UI showing live counts and thresholds per thread.
- [ ] Implement autosummarization pipeline in `packages/ai/src/chat-summarizer.ts` used when context window exceeds threshold.
- [ ] Build prompt cache layer keyed by signature with TTL controls in `packages/ai/src/prompt-cache.ts`.
- [ ] Hook caching into chat send pipeline with reuse indicators on messages.
- [ ] Add offline queue persistence (IndexedDB) for pending chat requests with retry/backoff logic.
- [ ] Implement export-to-notes/manuscript feature for selected chat excerpts.
- [ ] Write tests covering prompt caching, autosummarization triggers, offline queue recovery, and write-to-manuscript safeguards.

## Phase 5 – Review, Insights & Quality Governance
- **Analysis engine** (`packages/review`): Create pipeline to compute readability, sentence variety, passive voice, repetition, dialogue ratios, sentiment arcs, etc. Use incremental analysis to avoid full recompute.
- **Continuity checks**: Introduce knowledge base for characters/settings; detect timeline inconsistencies and POV slips by scanning scene metadata and text.
- **Rule packs & thresholds**: Allow configurable rule sets per project/genre, false-positive suppression, and severity levels; surface as structured issues.
- **Review tab UI**: Display sortable/filterable issue lists, navigation to manuscript locations, batch apply suggestions, and comparison views between snapshots.
- **Exports & reporting**: Generate PDF/Markdown reports with metrics, issue lists, and deep links; integrate with snapshot history for progress over time.
- **Testing**: Add regression suite for analysis calculations and ensure false-positive suppression persists across sessions.

### Phase 5 Task List
- [ ] Scaffold `packages/review` with incremental analysis pipeline architecture and plugin interface.
- [ ] Implement baseline metrics (readability, sentence variety, passive voice, repetition) with incremental updates per editor change event.
- [ ] Add dialogue ratio and sentiment arc analyzers using local NLP libraries (e.g., compromise.js, `@xenova` models).
- [ ] Build continuity knowledge base linking characters/settings to scenes; implement timeline and POV consistency checks.
- [ ] Create rule pack configuration format stored in project config; support overrides and false-positive suppression tagging.
- [ ] Develop Review tab UI (`apps/web/components/review/`) with filterable issue list and navigation into manuscript.
- [ ] Implement batch apply/ignore actions writing back to manuscript snapshots.
- [ ] Generate PDF/Markdown export templates with metrics and deep links referencing snapshot IDs.
- [ ] Write regression tests for analysis calculations and ensure suppression state persists via storage layer.

## Phase 6 – Autonomous Agents & Automation *(Backlog)*
- **Scope**: Treat agent features as post-MVP experimentation; defer implementation until core writing, planning, chat, and review flows are stable.
- **Agent scope model**: Define capability profiles (read-only, draft-write, apply-write) with allowed sections/tools and enforce via middleware before executing agent actions.
- **Execution planner**: Require agents to propose plans (tasks + prospective diffs) before editing; support dry-run preview diff mode and human approval workflow.
- **Telemetry dashboard**: Build UI for task queue, status, logs, token usage, and per-task diff previews with rollback shortcuts.
- **Scheduling & triggers**: Implement cron-style scheduling and event hooks (e.g., after chapter completion).
- **Sandboxing & limits**: Provide simple per-run token/step caps and controls for “redo with higher effort” plus global stop/pause.
- **Rollback integration**: Ensure each agent apply action records snapshots and one-click reverts leveraging Phase 1 versioning.
- **Testing**: Create simulation harness for agent runs validating permissions, limits, and rollback pathways.

### Phase 6 Task List (Backlog)
- [ ] Draft backlog brief summarizing desired agent capabilities and dependencies.
- [ ] Define agent permission model and serialize rules in `packages/data`.
- [ ] Prototype dry-run diff planner using existing diff infrastructure.
- [ ] Outline telemetry dashboard requirements and data schema.
- [ ] Document scheduling trigger scenarios and safeguards.

## Phase 7 – Quality-of-Life, Performance & Release Readiness
- **Searchable history**: Implement full-text search across manuscripts, AI cards, notes, and snapshots with quick navigation.
- **Export & publishing**: Add DOCX/PDF/EPUB/Markdown exports with templates, pre-flight checks (widows/orphans, metadata), and optional change-log appendix.
- **Personal-use mode**: Guarantee local-first mode with straightforward backup options and optional redaction of sensitive snippets before API calls.
- **Accessibility & theming**: Audit keyboard navigation, screen-reader labels, high-contrast themes, focus mode, and adjustable type scale.
- **Performance optimizations**: Tune tokenizer alignment, incremental rendering, background indexing, and add profiling benchmarks.
- **Stabilization**: Run full regression suite (unit, integration, E2E), document deployment instructions, and prepare marketing/demo builds.

### Phase 7 Task List
- [ ] Implement local full-text index (`packages/data/src/search.ts`) spanning manuscripts, cards, notes, snapshots.
- [ ] Build search UI overlay with keyboard shortcut integration.
- [ ] Implement export pipeline leveraging client-side libraries (e.g., `docx`, `pdf-lib`, `epub-gen`) with template selection UI.
- [ ] Add pre-flight validation checks for exports (widows/orphans, metadata completeness).
- [ ] Enhance personal backup workflow (manual export + scheduled reminders) with optional redaction filters.
- [ ] Conduct accessibility audit; fix keyboard focus traps, aria labels, and provide theme controls.
- [ ] Profile editor and AI flows; implement throttling/lazy loading where needed and document benchmarks.
- [ ] Prepare release notes, update documentation, and create demo recordings.

## Cross-Cutting Considerations
- **Documentation**: Maintain living docs in `/docs` covering architecture decisions, data model, AI prompt catalog, and contribution guidelines.
- **DevOps**: Configure CI (GitHub Actions) for lint/test/build across packages and monitoring for the personal-use web deployment.
- **Backup hygiene**: Offer clear guidance for local backup/export workflows aligned with the local-first design.

### Cross-Cutting Task List
- [ ] Set up GitHub Actions workflow running lint/typecheck/test/e2e across workspaces.
- [ ] Publish ADRs and keep `/docs` index updated per phase completion.
- [ ] Document backup and restore guides, including File System Access flow and manual export instructions.
- [ ] Establish contribution guidelines covering code style, testing expectations, and release process.
