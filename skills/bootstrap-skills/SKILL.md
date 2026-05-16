---
name: bootstrap-skills
description: Run a short structured interview about a software project (planning style, frontend, backend, data, testing, deployment), then search skills.sh for the highest-quality matching skills and install them into the project's local .claude/skills/ folder. Use when the user runs /bootstrap-skills, says "bootstrap this project", "set up skills for this project", "what skills should I install", or starts a new project and wants a recommended skill set.
---

# Bootstrap Skills

Run a short structured interview about a software project, then install the best-matching skills from skills.sh ranked by popularity and quality signals.

This skill is **proactive** — it asks what the project needs at setup time, rather than waiting for the user to ask later. All skills are installed at the project level (`.claude/skills/`), never globally.

## Operating principles

Read these before starting the interview. They govern every decision below.

1. **Detect before asking factual stack questions.** Look at the repo first (`package.json`, `pyproject.toml`, `Cargo.toml`, `go.mod`, `Gemfile`, etc.). The repo scan only answers *factual* questions like "what framework is this?" — it never answers *preference* questions like "how do you want the agent to behave?" or "do you want testing skills?" Always ask preference questions, even when the repo is fully detected. Confirm stack assumptions rather than asking blind.
2. **One question at a time.** Wait for the answer. Never bundle multiple questions in one turn.
3. **Always offer a recommended answer.** Based on what's in the repo and what's been said so far. Make the user's job to confirm or correct, not to think from scratch.
4. **Skip sections only when they truly don't apply.** "Docs-only repo" skips frontend, backend, data. "No tests planned" skips testing. But never skip Section 2 (working style) — preferences always need to be asked. When in doubt, ask the section's question rather than guessing.
5. **Keep the running tally visible.** After each section, show a one-line summary of what's been picked.
6. **Cap the structured interview at ~6 questions.** After the structured sections, always offer one open-ended "anything else?" question (Section 5.5) as an escape hatch for niche needs. Don't expand the structured sections beyond 6.
7. **Never install without confirmation.** Show the final list, get explicit yes, then install.
8. **Always install to the project, never globally.** Use `npx skills add <repo> --skill <name>` without the `-g` flag. Skills land in `./.claude/skills/`.

## The interview

Run sections in this order. Skip any section that doesn't apply.

### Section 1 — Project shape (always ask, 1 question)

Ask: *"In one sentence, what is this project? (e.g. 'Next.js dashboard with a Postgres backend', 'CLI tool in Rust', 'design docs and ADRs')"*

Use the answer to decide which downstream sections matter. If the user says "just docs" or "no code," skip to Section 6.

Also run a quick repo scan in parallel: list top-level files, read any manifest files, check for `.github/`, `docs/`, `tests/`. Form a mental model of the stack before continuing.

### Section 2 — Working style (always ask, 1 question)

Ask as a multi-select: *"How do you want the agent to behave on this project? Pick any that fit:"*

- **Plan before coding** — agent grills you on the design before writing code
- **Compressed responses** — agent uses terse, minimal-token output
- **Disciplined testing** — agent insists on red-green-refactor
- **Disciplined debugging** — agent uses a structured diagnosis loop for hard bugs

Each pick maps to a skills.sh search (see "Search strategy" below).

### Section 3 — Frontend (skip if Section 1 made it irrelevant, 1 question)

Only ask if the project has or will have a UI. If the repo already shows a framework (React, Vue, Svelte, Solid, Angular), skip to confirmation: *"I see [framework] in the repo. Want frontend skills for it?"*

If no framework yet: *"Will this project have a frontend? If yes, which framework?"*

Map the answer to a search query. If the user says "not sure yet," skip — they can run this skill again later.

### Section 4 — Backend & data (skip if irrelevant, max 1 question)

Detect from manifest files first. Look for: web frameworks (Express, Fastify, FastAPI, Django, Rails, Gin), ORMs (Prisma, Drizzle, SQLAlchemy, ActiveRecord), database drivers (pg, mysql2, redis).

If detected: *"I see [stack]. Want skills for [framework] and [database]?"*

If not detected and the project sounds like it has a backend: *"What's the backend stack? (e.g. 'FastAPI + Postgres', 'Express + Mongo', 'none')"*

Cap at one question for this whole section. Don't drill into "which ORM" — let popularity ranking handle it.

### Section 5 — Deployment & ops (optional, skip by default)

Only ask if the user explicitly mentioned deployment, infra, or CI in Section 1. Otherwise skip — most projects don't need this on day one and a skill can always be added later.

If asked: *"Any deployment or CI specifics worth installing skills for? (e.g. Vercel, Docker, GitHub Actions, none)"*

### Section 5.5 — Anything else? (always ask, open-ended)

Before showing the final tally, give the user one chance to add anything the structured sections missed.

Ask: *"Before I show you the final list — anything specific you want skills for that I didn't cover? (e.g. a particular library, a workflow, or 'no')"*

Treat this as free-form, not a multiple-choice. If the user mentions something concrete (e.g. "Drizzle migrations", "writing ADRs", "OpenAPI specs"), search skills.sh for it and add the best match to the tally. If they say "no" or similar, move straight to confirmation.

If they mention multiple things, handle them one at a time and surface what you found for each before adding.

This is the escape hatch for niche needs. Keep it light — don't turn it into a second interview. If the user starts listing many things, stop after a couple and suggest running `/bootstrap-skills` again later for the rest, or using `find-skills` for one-offs.

### Section 6 — Confirm and install (always run)

Synthesize everything into a final list. For each skill picked:

1. Search skills.sh via `npx skills find <query>` to get candidates.
2. Rank candidates by the criteria in "Ranking" below.
3. Pick the top result per category. If two are very close in score, surface both and let the user choose.

Present the final list to the user:

> "Based on your answers, I'll install these skills into `./.claude/skills/`:
>
> - **[skill-name]** by [author] — [one-line description] ([N] installs, [M] stars)
> - ...
>
> Confirm to install, or tell me what to swap."

After confirmation, run `npx skills add <repo> --skill <name>` (no `-g` flag) for each one. Report what was installed and any follow-up the user should know about.

## Search strategy

For each section the user opts into, search skills.sh and **verify install counts before recommending**. Default trust in CLI ordering is wrong — the CLI does not necessarily return results ranked by installs.

**Two-step process, do both:**

1. **Run the search to get candidates.** Use `npx skills find <keywords>`. Take the top ~5 results, not just the first.
2. **Verify install counts and reputation on skills.sh.** For each candidate, fetch its skills.sh page (e.g. `https://www.skills.sh/<owner>/<repo>/<skill>`) or run a web search like `site:skills.sh <skill-name>` to see the actual install count, GitHub stars, and source. CLI output alone is not enough.

Only after both steps, pick a winner. If a candidate looks promising but you can't verify its metrics, say so to the user rather than guessing.

**Sanity check — known popular sources.** Before finalizing, ask yourself: is there a well-known skill from a reputable source that the CLI didn't surface? For example: Matt Pocock's `mattpocock/skills` (engineering & productivity, ~84k stars), Anthropic's official skills, Vercel Labs' `vercel-labs/skills` and `vercel-labs/agent-skills`, framework-team skills (Angular, Next.js, etc.). If the top CLI result has 41 installs and you haven't checked these known sources, **check them explicitly**. A search like `npx skills find mattpocock tdd` or fetching `https://www.skills.sh/mattpocock/skills/tdd` will surface the popular alternative.

Suggested query templates (refine based on the user's actual answers):

| Section | Pick | Search keywords | Known popular source to check |
|---|---|---|---|
| Working style | Plan before coding | `grill plan design interview` | `mattpocock/skills/grill-me` |
| Working style | Compressed responses | `compressed terse minimal tokens` | `mattpocock/skills/caveman` |
| Working style | Disciplined testing | `tdd red green refactor` | `mattpocock/skills/tdd` |
| Working style | Disciplined debugging | `debug diagnosis loop bug` | `mattpocock/skills/diagnose` |
| Frontend | React | `react component design` | `anthropics/skills/frontend-design`, `vercel-labs/agent-skills` |
| Frontend | Vue | `vue component` | — |
| Frontend | Svelte | `svelte` | — |
| Frontend | Generic UI | `frontend design accessibility` | `anthropics/skills/frontend-design` |
| Backend | Node/TS API | `express fastify node api` | `vercel-labs/agent-skills` |
| Backend | Python API | `fastapi django python api` | — |
| Backend | Rust | `rust axum api` | — |
| Data | Postgres | `postgres sql schema` | — |
| Data | Mongo | `mongo document` | — |
| Data | Redis | `redis cache` | — |
| Ops | Docker | `docker container` | — |
| Ops | CI | `github actions ci` | — |
| Ops | Vercel | `vercel deployment` | `vercel-labs/agent-skills` |

Treat these as starting points. If the user said something specific ("we use Drizzle"), search for that directly. The "known popular source" column is a backstop, not a prescription — verify the metrics, and only pick it if it actually fits and ranks well.

## Ranking

After you have **verified install counts and stars** for the top candidates (not just the CLI output), score them:

1. **Install count** — primary signal. >10k installs is strong; 1k-10k is solid; 100-1k is plausible; <100 is weak and needs a strong secondary signal.
2. **GitHub stars on the source repo** — secondary signal. A skill in a 50k-star repo from a known author beats an unknown 100-install standalone.
3. **Source reputation** — official authors (Anthropic, Vercel Labs, framework teams) get a boost.
4. **Recency** — skills updated in the last 6 months beat stale ones, all else equal.
5. **Description fit** — does the skill description actually match what we want, or is it tangentially related? A weak fit with high installs is worse than a strong fit with moderate installs.

Pick the top-ranked candidate per category. Don't install multiple skills for the same job — one well-chosen skill per concern.

**Hard rule:** If the top candidate has fewer than 100 installs **and** you haven't checked the "known popular source" column for that category, go check before recommending. Don't ship a 41-install pick when a 30k-install alternative exists one search away.

If after checking, the top candidate still has fewer than 100 installs and no clear reputational signal, flag it: *"The best match for [category] is new and unproven ([N] installs). Install anyway, skip it, or want me to look at alternatives?"*

## Notes for Claude running this skill

- **The interview is the product.** Don't pad it. A user with a clear project should be done in under 2 minutes.
- **Show your work on rankings.** When picking between candidates, briefly say why (e.g. "Picked X over Y because 30k vs 800 installs and X is from the framework team").
- **Respect existing installs.** Before installing, run `npx skills list` to see what's already there. Don't reinstall, don't override user-edited skills without asking.
- **Project-scoped only.** All `npx skills add` commands run without `-g`. Skills land in `./.claude/skills/` and are committable with the project.
- **One-off discovery is fine mid-interview.** If the user asks "is there a skill for X" mid-flow, run a quick `npx skills find` for that, answer briefly, then resume the interview where you left off. No need to defer to other tools.
- **When in doubt, skip the install.** Recommending a skill the user adds manually is fine; installing a wrong skill that pollutes `.claude/skills/` is annoying. Lean conservative.

## Example session (illustrative)

```
User: /bootstrap-skills

Claude: [reads package.json, sees Next.js + Tailwind + Drizzle + Postgres]
        In one sentence, what is this project?

User: SaaS dashboard, internal tool for our team.

Claude: Got it. How do you want the agent to behave here?
        Pick any: plan-before-coding, compressed responses,
        disciplined testing, disciplined debugging.

User: Plan before coding, disciplined testing.

Claude: I see Next.js + Tailwind + Drizzle + Postgres in the repo.
        Install skills for that stack?

User: Yes.

Claude: Skip deployment skills for now?

User: Yes.

Claude: Anything specific you want skills for that I didn't cover?
        (e.g. a particular library, a workflow, or "no")

User: Yes — Drizzle migrations specifically.

Claude: [searches skills.sh for "drizzle migrations"]
        Found one — [top drizzle migration skill] (N installs).
        Adding it to the list.

Claude: Here's the plan — all installed into ./.claude/skills/:
        - [top "grill" skill] — plan before coding
        - [top tdd skill] — disciplined testing
        - [top frontend-design skill] — Next.js/Tailwind UI
        - [top nextjs skill] — Next.js best practices
        - [top postgres skill] — Drizzle + Postgres schema patterns

        Confirm to install?

User: Confirm.

Claude: [runs npx skills add for each without -g, reports results]
```
