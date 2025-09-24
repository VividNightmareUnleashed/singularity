

Core Writing & AI Interaction

Card Sidebar for AI Outputs

Outputs from AI appear as individual, dismissible cards.

Configurable context actions linked to custom prompts (e.g., Rephrase, Summarize, Expand).

Ability to:

Copy text from cards.

Insert directly at the cursor position.

Replace highlighted text with AI output.

Option to pin or favorite specific cards for later use.

Context Menu Enhancements: Expandable section to adjust thinking effort and verbosity per request.

Scoped selections: Toggle whether actions use selection, paragraph, scene/chapter, or full document.

Safe apply: Side‑by‑side diff, partial accept (per sentence/paragraph), and conflict resolution if the document changed since generation.

Queue & status: Pending/running/complete/error states; cancel/redo; keyboard shortcuts.

Traceability: Each card shows prompt name, model, parameters, token/cost usage, and a permalink to the originating selection.

Audit & rollback: Every apply action writes a snapshot and a one‑click revert point.

Configurable Context Actions

Prompt library + templating: Named prompts with variables (e.g., {selection}, {tone}, {style}), previews, JSON import/export.

Per‑action execution settings: Model selector, temperature/top‑p, thinking effort and verbosity overrides, max tokens, seed, and retry policy.

Access via right‑click, toolbar, or hotkeys; optional per‑action context scope defaults.

Structural Planning & Organization

Dedicated Planning Tab

Hierarchical scene/act management with stable scene IDs bound to manuscript ranges (reordering updates anchors).

Metadata & templates for scenes (POV, goal–conflict–outcome, beats, word count targets, tags, status).

Operations: split/merge scenes, convert selection → scene, bulk reorder, duplicate.

AI assists: generate beat sheets, fill missing beats, propose act breaks, enforce pacing constraints.

Views: outline, corkboard (index cards), and timeline; filters by plotline/character/theme.

Storyboard / Timeline View (Optional Extension)

Visual timeline for acts and scenes with tags and color coding for plotlines, characters, or themes.

AI Chat Integration

Dedicated Chat Tab

Chat with AI for brainstorming, problem‑solving, or freeform writing; optional permissioned “write directly to manuscript” mode.

Customizable AI tools (e.g., Write next paragraph, Suggest dialogue, Check pacing).

Save chat excerpts into notes or insert into manuscript.

Integration with OpenAI API: Persist reasoning tokens for continuity across sessions; controls for thinking effort and verbosity; adjustable creativity/temperature.

Context window management: Live bar (e.g., 0/400k tokens for GPT‑5), soft/hard thresholds, overflow warnings.

Per‑message metrics: Token count with a toggle to display estimated cost based on a configurable pricing table and currency.

Prompt caching: Cache keys (system + tools + pinned context), TTL, manual invalidate, and indicators when a response reused cache.

Reasoning budget controls: Persist a numeric “reasoning tokens/effort” budget per thread; show consumption; escalate on demand; graceful fallback if a model lacks this feature.

Context composition: Pin/unpin messages; include/exclude planner and notes; autosummarize long histories with caps.

Modes & tools: “Draft‑to‑doc” streaming vs “Chat‑only”; per‑thread system messages; toggle available tools/functions. Branch conversation and a tree-style graph option.

Reliability: Streaming with cancel, retry/backoff, offline queue, and actionable error surfaces.

Autonomous AI Agents

Scope & capability model: Read‑only, draft‑write, or apply‑write; allowed sections; allowed tools.

Budgets: Token/time/cost caps with per‑run and per‑day limits.

Execution plan: Agents produce an approval plan (tasks + diffs); dry‑run mode to preview diffs without edits.

Live telemetry: Task queue, status, logs, token/cost meter, and per‑task diffs.

Controls: Pause/resume/stop, step‑through, “redo with higher effort,” and one‑click rollback via snapshots.

Scheduling & triggers: On‑demand, cron (e.g., nightly cleanup), or event‑based (e.g., after chapter finished).

Review & Insights

Dedicated Review Tab

Metrics: readability indices, sentence variety, passive voice, repetition, adverb density, dialogue/narration ratio, sentiment/tone arcs.

Continuity checks: character/setting canon vs manuscript, timeline consistency, viewpoint slips.

Rule packs: project/genre style guides; threshold tuning; false‑positive suppression.

Navigation: click from issue → location; batch “apply suggestions.”

Comparisons: version‑to‑version deltas and progress over time.

Exports: shareable report (PDF/MD) with issue lists and deep links into the manuscript.

Quality‑of‑Life Enhancements

Version Control & Snapshots

Auto‑save versions; restore or compare.

Branching: Experimental branches per chapter/arc; three‑way merge and conflict resolver.

Commit UX: Named snapshots with notes, authorship (you vs. agent), and tags (e.g., “pre‑revamp”).

Searchable history: Full‑text search across versions and AI cards.

Research & Notes Integration

Side panel for notes, worldbuilding, and references.

Linked notes: Bi‑directional links between notes ↔ scenes; quick insert of citations.

Local RAG: On‑device embedding index for notes/lore; per‑query include/exclude controls; citation trails.

Cross‑Project AI Memory (Optional)

Persistent memory of recurring characters, settings, or themes; AI recalls details for continuity.

Export & Publishing Tools

Export to DOCX, PDF, EPUB, Markdown.

Templates: House styles; front/back matter, ToC, scene breaks.

Validation: Pre‑flight checks (widows/orphans, orphaned footnotes, metadata).

Change‑log export: Optional appendix of edits.

Personal‑Use Mode (Global)

Local‑first storage: All manuscripts, plans, caches, and logs stored locally; optional encrypted backups.

Key management: API keys in OS keychain; redaction in logs; no accidental sharing.

Privacy guardrails: Optional redaction of sensitive snippets before API calls; option to store only summaries/metrics of model reasoning.

Cost governance: Monthly budget, alerts, and per‑feature caps (e.g., agents cannot exceed X €/day).

Backups & recovery: Automatic snapshots, restore points, and portable project bundles.

Accessibility, Performance, and UX

Accessibility: Full keyboard control, screen‑reader labels, scalable UI.

Performance: Accurate token estimation aligned with provider tokenizer; incremental rendering; background indexing.

Theming: Light/dark, distraction‑free focus mode, adjustable type scale.

