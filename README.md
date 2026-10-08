# intent

Run every feature as a **living intent**: one folder per feature, written as you go, that any later session (or teammate) can resume from the files alone.

```
docs/intent/<feature>/
  prompt.md     the spec: goal, non-goals, decisions, acceptance ids with their proofs (Rev, Status)
  log.md        every prompt you gave, verbatim, and what it changed
  progress.md   the worker's record: Acceptance met/open with evidence, steps, the PR
  findings.md   what the worker found that may change the spec, with a proposed amendment
```

You talk to the **orchestrator** (your session). It writes the spec, logs each prompt, and starts a **background worker** in its own worktree and branch. The worker works until every acceptance item is met, with proof read from tool output (an exit code, a pass count, a deployment state), never from prose. When the spec turns out wrong, the worker writes a finding and you decide.

Works with Claude Code (as a plugin) and with any agent that reads `SKILL.md` skills (Codex, Cursor, Gemini CLI, and others).

## Install

### Claude Code

```bash
claude plugin marketplace add AskTinNguyen/intent
```

```bash
claude plugin install intent@intent --scope user
```

Use `--scope project` instead to install it for one repository only, recorded in its `.claude/settings.json`.

To offer it to everyone who opens a repository, commit this to that repository's `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "intent": { "source": { "source": "github", "repo": "AskTinNguyen/intent" } }
  },
  "enabledPlugins": { "intent@intent": true }
}
```

### Other agents

With the [skills CLI](https://github.com/vercel-labs/skills):

```bash
npx skills add AskTinNguyen/intent
```

Or copy [`skills/intent/`](skills/intent/) into the project's `.agents/skills/` (or `.claude/skills/`) folder.

## Use

1. **Set the project up once:** `/intent init`. The agent reads your repository (scripts, CI, deploy config, `AGENTS.md`) and drafts `docs/intent/README.md`, the project contract: your areas, the proofs that exist here (`gate: npm test`, `build`, `prod`, …), what workers read first and must never do, and whether the agent may merge. You confirm it. Merge authority is `none` unless you grant it.
2. **Start a feature:** `/intent add CSV export to the report page`. You get the slug, area and acceptance list back, and the worker starts.
3. **Keep talking.** Each later message is logged, then treated as an amendment (new rev), a decision on a finding, a question (answered from the files) or an aside.
4. **Resume anywhere:** `/intent` alone lists open intents and resumes one.
5. **Finish:** "we're done" or "park it". With merge authority the agent merges after proof; without it, it hands you a ready PR with the evidence.

In Claude Code the skill shows as `/intent:intent`; it also triggers when you ask another skill to build, change or fix a feature (auto-capture).

## What lives where

| | Same everywhere (this repo) | Per project (`docs/intent/README.md`) |
| --- | --- | --- |
| Files and who writes them | ✓ | |
| Orchestrator loop, worker loop, stop rules | ✓ | |
| Areas, statuses | | ✓ |
| Proof kinds and the real commands | `gate:`, `review` | the rest |
| Read-first docs, never-do list, calls reserved for you | baseline | additions |
| What `main` triggers, merge authority, close steps | | ✓ |

## Layout

```
.claude-plugin/        plugin.json and marketplace.json (this repo is its own marketplace)
skills/intent/
  SKILL.md             the procedure
  references/setup.md  /intent init
  assets/contract.md   template for a project's docs/intent/README.md
  assets/worker-brief.md
  assets/templates/    prompt, progress, log, findings
```

## Update

```bash
claude plugin marketplace update intent
```

```bash
claude plugin update intent@intent
```

## Changes

- **0.1.0** First shared release, generalized from the per-project copies in han-viet, storey and thinh-live-mac: project specifics moved into a `docs/intent/README.md` contract, `/intent init` drafts it, merge authority defaults to none.

## License

MIT
