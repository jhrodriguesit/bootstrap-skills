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

### Section 5.5 — Anything else? (loop until user opts out)

Before showing the final tally, give the user repeated chances to add anything the structured sections missed. This is a loop, not a single ask.

**First time, ask:** *"Before I show you the final list — anything specific you want skills for that I didn't cover? (e.g. a particular library, a workflow, or 'no')"*

**After each item they add, ask again:** *"Anything else, or are we good?"*

Continue looping until the user signals they're done with phrases like "no", "that's all", "we're good", "nothing else", "done". Then move to Section 6.

For each item the user mentions:
1. Search skills.sh for it using the bare-topic approach (Search Strategy above).
2. Verify install counts.
3. Briefly tell the user what you found — name, source, install count, one-line fit — before adding to the tally.
4. If you can't find a good match, say so honestly. Don't add a low-quality skill just to satisfy the request.

**Guardrails:**
- If the user is on a roll listing many things (5+ items), pause and ask: *"Want to keep going, or save the rest for another run?"* The interview shouldn't balloon indefinitely.
- If a user-suggested item duplicates something already in the tally, point that out instead of adding a second one.
- Treat ambiguous answers ("maybe", "I dunno", "what would you recommend?") as a request for help, not as "yes" or "no". Ask a clarifying follow-up or suggest a specific skill they can accept or reject.

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

### Section 7 — How to use these skills (offer after install)

After successful install, offer to generate a short usage guide so the user knows what they just got and how to trigger each skill.

Ask: *"Want me to drop a quick 'How to use these skills' guide into the repo? It'll list each installed skill, what it does, and how to invoke it."*

If yes, create `.claude/skills/HOW_TO_USE.md` (or `<agent>/skills/HOW_TO_USE.md` for whichever agent path was used) with the following structure:

```markdown
# How to use these skills

This project has [N] skills installed via /bootstrap-skills. Each one extends what the agent can do — some trigger automatically, others you invoke explicitly.

## Installed skills

### [skill-name]
**What it does:** [one-line summary from the skill's description]
**How to trigger:** [slash command if applicable, e.g. `/grill-me` — or natural-language phrases that activate it, e.g. "say 'grill me' or 'stress-test this plan'"]
**When to use it:** [one-line guidance on the moment in your workflow when this helps]

[repeat per skill]

## Tips
- Skills with a slash command (e.g. `/tdd`) are explicit — type the command to run them.
- Skills without one trigger automatically when the agent recognizes the task from the description.
- Re-run `/bootstrap-skills` anytime to add more skills as the project grows.
```

To populate each entry accurately:
1. Read each installed skill's `SKILL.md` frontmatter (name, description).
2. Extract the trigger phrases from the description (the "Use when..." phrases tell you the natural-language triggers).
3. Check if the skill is invoked as a slash command in its body — most are; if so, the command is `/<skill-name>`.
4. Keep each entry to 3 short lines. Don't pad. The user can read the full SKILL.md if they want more.

After writing the file, tell the user where it landed and suggest they commit it alongside the skills so the team has the same reference.

## Search strategy

For each section the user opts into, search skills.sh and **verify install counts before recommending**.

**Search with bare topic terms, not verbose phrases.** Skills are usually named for what they *are* (e.g. `vercel-react-best-practices`), not what they do (e.g. "react component design"). A bare-topic search (`react`, `tdd`, `postgres`) lets the platform's install-count ranking surface the popular skill, rather than fighting it with over-specified queries.

**Three-step process:**

1. **Run a bare-topic search.** Use `npx skills find <topic>` with the shortest meaningful term — `react`, not `react components`. Take the top ~5 results.
2. **Verify install counts on skills.sh.** For each candidate, fetch its skills.sh page (e.g. `https://www.skills.sh/<owner>/<repo>/<skill>`) or web-search `site:skills.sh <skill-name>` to see install count, GitHub stars, and source. CLI output alone is not enough.
3. **Sanity-check if your pick is small.** If the candidate you'd recommend has fewer than 10K installs, run a broader search (an even shorter term, or just the bare topic by itself) and scan for a skill with at least 10x more installs that still fits. Often there's a popular skill the narrow search missed.

Only after all three steps, pick a winner. If a candidate looks promising but you can't verify its metrics, say so to the user rather than guessing.

**Suggested bare-topic queries** (these are starting points; refine based on the user's actual answer):

| Section | Pick | Bare topic to search |
|---|---|---|
| Working style | Plan before coding | `grill` |
| Working style | Compressed responses | `caveman` or `compressed` |
| Working style | Disciplined testing | `tdd` |
| Working style | Disciplined debugging | `debug` or `diagnose` |
| Frontend | React | `react` |
| Frontend | Vue | `vue` |
| Frontend | Svelte | `svelte` |
| Frontend | Generic UI | `frontend` |
| Backend | Node/TS API | `node` or `express` |
| Backend | Python API | `python` or `fastapi` |
| Backend | Rust | `rust` |
| Data | Postgres | `postgres` |
| Data | Mongo | `mongo` |
| Data | Redis | `redis` |
| Ops | Docker | `docker` |
| Ops | CI | `ci` or `github actions` |
| Ops | Vercel | `vercel` |

If the user said something specific ("we use Drizzle"), search for that directly with the bare term (`drizzle`).

## Ranking

After you have **verified install counts and stars** for the top candidates (not just the CLI output), score them:

1. **Install count** — primary signal. >100K installs is dominant; 10K-100K is strong; 1K-10K is solid; 100-1K is plausible; <100 is weak.
2. **GitHub stars on the source repo** — secondary signal. A skill in a 50K-star repo from a known author beats an unknown 100-install standalone.
3. **Source reputation** — official authors (Anthropic, Vercel Labs, framework teams) get a boost.
4. **Recency** — skills updated in the last 6 months beat stale ones, all else equal.
5. **Description fit** — does the skill description actually match what we want, or is it tangentially related? A weak fit with high installs is worse than a strong fit with moderate installs.

Pick the top-ranked candidate per category. Don't install multiple skills for the same job — one well-chosen skill per concern.

**Hard rule — sanity check on low picks.** If the top candidate has fewer than 10K installs, run the broader sanity search from Step 3 above before recommending. Don't ship a 2.6K-install pick when a 400K-install alternative exists one broader search away.

If after sanity-checking, the top candidate still has fewer than 100 installs and no clear reputational signal, flag it: *"The best match for [category] is new and unproven ([N] installs). Install anyway, skip it, or want me to look at alternatives?"*

## Notes for Claude running this skill

- **The interview is the product.** Don't pad it. A user with a clear project should be done in under 2 minutes (excluding any "anything else?" rounds the user chooses to extend).
- **Show your work on rankings.** When picking between candidates, briefly say why (e.g. "Picked X over Y because 30k vs 800 installs and X is from the framework team").
- **Respect existing installs.** Before installing, run `npx skills list` to see what's already there. Don't reinstall, don't override user-edited skills without asking.
- **Project-scoped only.** All `npx skills add` commands run without `-g`. Skills land in `./.claude/skills/` and are committable with the project.
- **Loop "anything else?" until the user opts out.** Don't ask once and move on — keep offering until they say they're done.
- **Always offer the usage guide.** After install, ask if the user wants a HOW_TO_USE.md generated. It's a small step that makes the installed skills discoverable to the team.
- **One-off discovery is fine mid-interview.** If the user asks "is there a skill for X" mid-flow, run a quick `npx skills find` for that, answer briefly, then resume the interview where you left off.
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

Claude: [searches skills.sh for "drizzle"]
        Found one — [top drizzle skill] (N installs).
        Adding it to the list. Anything else, or are we good?

User: Also writing ADRs.

Claude: [searches skills.sh for "adr"]
        Found [top ADR skill] (M installs). Adding.
        Anything else, or are we good?

User: That's all.

Claude: Here's the plan — all installed into ./.claude/skills/:
        - [top "grill" skill] — plan before coding
        - [top tdd skill] — disciplined testing
        - [top frontend-design skill] — Next.js/Tailwind UI
        - [top nextjs skill] — Next.js best practices
        - [top postgres skill] — Drizzle + Postgres schema patterns
        - [top drizzle skill] — Drizzle migrations
        - [top ADR skill] — ADR authoring

        Confirm to install?

User: Confirm.

Claude: [runs npx skills add for each without -g, reports results]
        Done — 7 skills installed.
        Want me to drop a quick 'How to use these skills' guide
        into the repo?

User: Yes.

Claude: [reads each SKILL.md, writes .claude/skills/HOW_TO_USE.md]
        Wrote HOW_TO_USE.md. Commit it alongside the skills so
        the team has the same reference.

Claude: [runs npx skills add for each without -g, reports results]
```
