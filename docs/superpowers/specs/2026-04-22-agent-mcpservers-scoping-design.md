# Design: Scope ArcKit agents with `mcpServers` frontmatter

**Date:** 2026-04-22
**Issue:** [#215](https://github.com/tractorjuice/arc-kit/issues/215) — Cluster A, item #24
**Status:** **ABORTED** — verification spike (2026-04-22) confirmed the feature does not apply to Task-tool-spawned agents. See "Spike findings" at the bottom.

## Goal

Apply Claude Code v2.1.117's `mcpServers` agent frontmatter to all 10 ArcKit agents so each sees only the MCP servers it needs. Reduces per-agent context bloat, limits blast radius under malicious-MCP scenarios (aligns with [#273](https://github.com/tractorjuice/arc-kit/issues/273)), and improves tool-choice accuracy.

## Scope

**In scope:**

- `mcpServers` frontmatter on all 10 agents in `arckit-claude/agents/`
- Converter strip of the new field for non-Claude targets
- Hook consistency fix: add `mcp__govreposcrape__` to `allow-mcp-tools.mjs` allow-list (pre-existing gap surfaced by this work)
- Matrix smoke testing across all 5 bundled MCP servers
- Docs + close-out (CLAUDE.md, memory, issue comment)

**Out of scope:**

- `initialPrompt` frontmatter (separate future spec; different failure mode)
- Any other Cluster B–E items from #215
- Bumping minimum Claude Code version (keeping v2.1.112; `mcpServers` is additive and unknown frontmatter is ignored)
- Test-repo `.claude/settings.json` changes (not affected)
- Documentation edits in `docs/guides/` for research commands (MCP usage guides are accurate regardless of scoping)

## Phase 1 — Verification spike *(gate, ~30 min)*

**Question to resolve:** Does Claude Code apply `mcpServers` frontmatter when an agent is spawned via the **Task tool** (ArcKit's thin-wrapper pattern), or only via `--agent` main-thread invocation? And: does an empty `mcpServers: []` mean "allow-list of none" or "default = all"?

The v2.1.117 changelog is worded as "`mcpServers` now loaded for main-thread agent sessions via `--agent`", which implies behavior on Task-tool invocations may differ or may have already existed. The spike answers this before rollout.

**Method:**

1. Create a feature branch `feat/agent-mcpservers-scoping`.
2. On `arckit-aws-research` only, set `mcpServers: ["aws-knowledge"]` (deliberately omit `govreposcrape`).
3. Invoke `/arckit:aws-research` via the slash command in a test repo (suggest `arckit-test-project-v17`) — this goes through the Task tool.
4. Observe:
   - Does the agent run successfully?
   - Does it attempt any `mcp__govreposcrape__*` calls? (If allow-list honored: no resolving tools. If ignored: calls still succeed.)
   - Capture the agent's tool list from its transcript.
5. On the same branch, run a second test with `mcpServers: []` — observe whether all MCPs are lost or all MCPs are retained.

**Gate outcomes:**

| Spike finding | Plan response |
|---|---|
| Allow-list honored on Task-tool spawns; `[]` = deny-all | Proceed to Phase 2 using the mapping table as-is |
| Allow-list honored; `[]` = default (all) | Proceed to Phase 2 but omit the field entirely for `arckit-framework` and `arckit-grants` (the `[]` vs omission distinction matters only for those two) |
| Feature ignored on Task-tool spawns | Abort rollout. File an upstream feature request for Task-tool support. Close #24 on issue #215 as "not applicable to ArcKit's invocation pattern". Document findings in memory. Stop. |
| Mixed / unclear behavior | Document the finding, choose the safest interpretation (omit field when uncertain), proceed |

## Phase 2 — Agent frontmatter rollout

Add `mcpServers:` to each of the 10 agent files using this mapping (derived from grep of actual MCP tool usage per agent):

| Agent | `mcpServers` |
|---|---|
| `arckit-aws-research` | `[aws-knowledge, govreposcrape]` |
| `arckit-azure-research` | `[microsoft-learn, govreposcrape]` |
| `arckit-gcp-research` | `[google-developer-knowledge, govreposcrape]` |
| `arckit-datascout` | `[datacommons-mcp, govreposcrape]` |
| `arckit-gov-reuse` | `[govreposcrape]` |
| `arckit-gov-code-search` | `[govreposcrape]` |
| `arckit-gov-landscape` | `[govreposcrape]` |
| `arckit-research` | `[aws-knowledge, microsoft-learn, google-developer-knowledge, datacommons-mcp, govreposcrape]` |
| `arckit-framework` | `[]` (or field omitted — see spike outcome) |
| `arckit-grants` | `[]` (or field omitted — see spike outcome) |

Rationale:

- Cloud-research agents keep their cloud's domain MCP plus `govreposcrape` for cross-referencing UK Gov code.
- `arckit-datascout` pairs DataCommons with `govreposcrape` for gov data-source discovery.
- Three `arckit-gov-*` agents restrict to `govreposcrape` only.
- `arckit-research` (cross-cloud build-vs-buy / TCO) keeps all 5 MCPs — the looser option chosen during brainstorming.
- `arckit-framework` (pure artifact transformation) and `arckit-grants` (WebSearch/WebFetch only) have no MCP needs.

## Phase 3 — Converter update

File: `scripts/converter.py` line 15 (`CLAUDE_ONLY_AGENT_FIELDS` tuple).

Add `"mcpServers"` to the tuple so Codex/Gemini/OpenCode/Copilot targets strip the field. The existing `copy_agent_stripped` function at line 83 already does the stripping; no code-path changes required.

After the edit, run `python scripts/converter.py` to regenerate all extension formats and commit the output.

## Phase 3b — Hook consistency fix

File: `arckit-claude/hooks/allow-mcp-tools.mjs` line 17.

The `ALLOWED_PREFIXES` array currently lists 4 MCP servers but omits `mcp__govreposcrape__`. This is a pre-existing gap surfaced by this work: 7 of the 10 scoped agents use `govreposcrape` and currently hit a permission dialog on every call.

Changes:

1. Add `'mcp__govreposcrape__'` to `ALLOWED_PREFIXES`.
2. Update the file's top-of-file JSDoc comment to enumerate all 5 bundled MCPs.

No hook registration changes in `hooks.json` — the existing `mcp__.*` matcher covers the new prefix.

## Phase 4 — Matrix smoke test

Target test repo: `arckit-test-project-v17` (UK Government Fuel Price Transparency Service — public, non-sensitive, has populated artifacts).

Five canary runs, one per bundled MCP server:

| Agent | MCP under test | Pass criteria |
|---|---|---|
| `arckit-aws-research` | `aws-knowledge` | Completes; produces AWS research doc; no MCP connection errors in transcript |
| `arckit-azure-research` | `microsoft-learn` | Completes; produces Azure research doc |
| `arckit-gcp-research` | `google-developer-knowledge` | Completes; produces GCP research doc |
| `arckit-datascout` | `datacommons-mcp` | Completes; produces data-source report |
| `arckit-gov-reuse` | `govreposcrape` | Completes; produces reuse assessment; **no `PermissionRequest` dialog fires for `mcp__govreposcrape__*`** (regression test for Phase 3b) |

Skipped (covered transitively or no MCPs):

- `arckit-research`, `arckit-gov-code-search`, `arckit-gov-landscape` — share MCPs with tested agents
- `arckit-framework`, `arckit-grants` — no MCP dependencies to test

Any failure reverts that agent's `mcpServers` entry, triage, re-run.

## Phase 5 — Docs & close-out

- Update `CLAUDE.md` Agent System section to list `mcpServers:` as a supported plugin-agent frontmatter field with v2.1.117+ note.
- Update `project_claude_code_adoption.md` memory with spike outcome (honored / not honored) and rollout completion.
- Post closing comment on issue #215 #24 with: spike finding, final mapping, allow-list fix.
- PR title: `feat: scope agents with mcpServers frontmatter (#24)`
- PR body: reference #215 and #273 (MCP hardening alignment).

## Risk register

| Risk | Likelihood | Mitigation |
|---|---|---|
| Spike shows feature doesn't apply to Task-tool spawns | Medium — changelog wording is ambiguous | Abort early per Phase 1 gate; file upstream issue; close #24 with findings |
| Smoke test fails for one agent (e.g., MCP connection regression) | Low | Revert that agent's `mcpServers` entry on the branch; investigate; re-run matrix |
| Empty-list semantics removes all MCPs unexpectedly | Low — spike resolves | Fallback: omit the field entirely for `arckit-framework` and `arckit-grants` |
| Older Claude Code clients (< v2.1.117) error on unknown field | Low — Claude Code historically ignores unknown frontmatter | If confirmed in spike, bump minimum version; otherwise no action |
| `allow-mcp-tools.mjs` gap fix causes an unintended auto-allow | Very low — prefix is specific and narrow | Scope of change is additive to a specific prefix; no existing flow changes |

## Files touched

| Category | Paths | Count |
|---|---|---|
| Agents | `arckit-claude/agents/arckit-*.md` | 10 |
| Converter | `scripts/converter.py` | 1 |
| Hooks | `arckit-claude/hooks/allow-mcp-tools.mjs` | 1 |
| Docs | `CLAUDE.md` | 1 |
| Memory | `~/.claude/projects/-workspaces-arc-kit/memory/project_claude_code_adoption.md` | 1 (local) |
| Generated | `.codex/prompts/*.md`, `arckit-codex/skills/*`, `arckit-gemini/commands/**/*.toml`, `arckit-opencode/commands/*.md`, `arckit-copilot/prompts/*.prompt.md` | regenerated by converter |

Single PR.

## Acceptance criteria

1. Spike documented with a clear verdict before any agent-file edits land.
2. All 10 agents have a definitive `mcpServers` declaration (list or intentionally-omitted per spike outcome).
3. `scripts/converter.py` strips `mcpServers` for non-Claude targets; generated extension files contain no `mcpServers` field.
4. `allow-mcp-tools.mjs` allow-list includes `mcp__govreposcrape__`.
5. All 5 canary smoke tests pass.
6. No permission dialog fires for `mcp__govreposcrape__*` during gov-* agent runs.
7. Issue #215 item #24 ticked with closing comment; memory updated.

---

## Spike findings (2026-04-22)

**Verdict:** ABORT — `mcpServers` agent frontmatter is **not applied** when the agent is spawned via Claude Code's Task tool (ArcKit's invocation pattern). Matches the v2.1.117 changelog wording: "loaded for main-thread agent sessions via `--agent`".

### Method

Feature branch `feat/agent-mcpservers-scoping` with plugin version bumped each iteration so the marketplace cache invalidated. Two canary variants tested against `arckit-test-project-v17` in a live Claude Code session, invoking `/arckit:aws-research`:

| Variant | Plugin version | `mcpServers` on `arckit-aws-research` | Observed behavior |
|---|---|---|---|
| 1 (inverted) | 4.9.4 | `["govreposcrape"]` (aws-knowledge excluded) | Agent still called `mcp__aws-knowledge__*` tools successfully |
| 2 (deny-all) | 4.9.5 | `[]` | Agent still called `mcp__aws-knowledge__*` tools successfully |

Either variant should have blocked AWS MCP access if the allow-list were enforced. Both ran normally, proving the frontmatter is fully inert for Task-tool-spawned agents.

### Decision

- **Close item #24 of issue #215** as "not applicable to ArcKit's invocation pattern; requires upstream feature support for Task-tool spawns".
- **File an upstream feature request** on the Claude Code repo asking for `mcpServers` to apply to Task-tool invocations.
- **Drop Tasks 2–6, 8–12, 14** from the execution plan.
- **Retain Task 7** (hook fix for `mcp__govreposcrape__` auto-allow) as it is independent of the aborted feature and valuable on its own.
- **Reshape PR** from `feat: scope agents with mcpServers frontmatter` to `fix(hooks): auto-allow govreposcrape MCP tools`.

### Follow-up candidate (not this PR)

Explore whether `disallowedTools` frontmatter accepts glob patterns (e.g. `"mcp__aws-knowledge__*"`). If so, it could provide a workaround for per-agent MCP scoping that works on Task-tool spawns. A 15-minute follow-up spike could answer this.
