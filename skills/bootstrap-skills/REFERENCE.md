# Reference — Bootstrap Skills

Lookup tables and details that don't need to be in `SKILL.md`'s main flow. Load this when you reach the search/install steps (Section 6) — and earlier if you need to look up a bare-topic query for Section 5.5 "anything else?" items.

## Search strategy

For each section the user opts into, search skills.sh and **verify install counts before recommending**.

**Search with bare topic terms, not verbose phrases.** Skills are usually named for what they *are* (e.g. `vercel-react-best-practices`), not what they do (e.g. "react component design"). A bare-topic search (`react`, `pytest`, `postgres`) lets the platform's install-count ranking surface the popular skill, rather than fighting it with over-specified queries.

**Three-step process:**

1. **Run a bare-topic search.** Use `npx skills find <topic>` with the shortest meaningful term — `react`, not `react components`. Take the top ~5 results.
2. **Verify install counts on skills.sh.** For each candidate, fetch its skills.sh page (e.g. `https://www.skills.sh/<owner>/<repo>/<skill>`) or web-search `site:skills.sh <skill-name>` to see install count, GitHub stars, and source. CLI output alone is not enough.
3. **Sanity-check if your pick is small.** If the candidate you'd recommend has fewer than 10K installs, run a broader search (an even shorter term, or just the bare topic by itself) and scan for a skill with at least 10x more installs that still fits. Often there's a popular skill the narrow search missed.

Only after all three steps, pick a winner. If a candidate looks promising but you can't verify its metrics, say so to the user rather than guessing.

## Bare-topic queries by section and project type

Queries are project-type- and language-aware. Look up the right query based on what was picked in Section 1 and what the user selected in the current section.

### Section 2 — Working style

| Pick | Query |
|---|---|
| Plan before coding | `grill` |
| Compressed responses | `caveman`, `compressed` |
| TDD discipline | `tdd` |
| Disciplined debugging | `debug`, `diagnose` |

### Section 3 — Building it

Project type determines the query.

**Web app:**

| Pick | Query |
|---|---|
| Frontend (React/Next) | `react`, `nextjs` |
| Frontend (Vue/Nuxt) | `vue`, `nuxt` |
| Frontend (Svelte) | `svelte` |
| Backend (Node) | `express`, `fastify`, `node` |
| Backend (Python) | `fastapi`, `django` |
| Database | `postgres`, `mongo`, `redis` |
| ORM specific | `drizzle`, `prisma`, `sqlalchemy` |
| UI / styling | `tailwind`, `shadcn` |

**Backend service:**

| Pick | Query |
|---|---|
| Node/TS framework | `express`, `fastify` |
| Python framework | `fastapi`, `django` |
| Go framework | `gin`, `go` |
| Rust framework | `actix`, `axum`, `rust` |
| Database / ORM | `postgres`, `mongo`, `drizzle`, `prisma`, `sqlalchemy` |
| API style | `graphql`, `grpc`, `rest` |
| Message queue | `kafka`, `rabbitmq`, `redis` |

**Mobile app:**

| Pick | Query |
|---|---|
| iOS (SwiftUI/UIKit) | `swift`, `swiftui`, `ios` |
| Android (Compose/Kotlin) | `kotlin`, `android`, `compose` |
| React Native | `react-native`, `expo` |
| Flutter | `flutter`, `dart` |
| Backend integration | `mobile api`, `offline-first` |

**CLI tool or library:**

| Pick | Query |
|---|---|
| Arg parsing (JS) | `commander`, `oclif` |
| Arg parsing (Python) | `click`, `typer` |
| Arg parsing (Rust) | `clap` |
| Arg parsing (Go) | `cobra` |
| Distribution (npm) | `npm publish` |
| Distribution (crates.io) | `cargo publish`, `crates` |
| Distribution (PyPI) | `pypi`, `poetry` |
| Library API design | `semver`, `library design` |

### Section 4 — Quality & correctness

Query depends on Section 1's language.

| Pick | TS/JS | Python | Rust | Go | Swift / Kotlin |
|---|---|---|---|---|---|
| Testing | `vitest`, `jest`, `playwright` | `pytest` | `cargo test` | `go test` | `xctest`, `swift testing` / `kotlin test` |
| Type safety | `typescript strict` | `mypy`, `pydantic` | `rust ownership` | `go generics` | `swift types` / `kotlin null safety` |
| Linting / formatting | `eslint`, `prettier`, `biome` | `ruff`, `black` | `clippy`, `rustfmt` | `golangci-lint` | `swiftlint`, `ktlint` |
| Code review | `code review`, `pr review` | (same) | (same) | (same) | (same) |
| Accessibility | `a11y`, `accessibility`, `wcag` | — | — | — | `accessibility ios`, `accessibility android` |

### Section 5 — Running it in production

Query depends on what the user picked.

| Pick | Query |
|---|---|
| Deployment (Docker) | `docker` |
| Deployment (CI) | `ci`, `github actions` |
| Deployment (Vercel) | `vercel` |
| Deployment (AWS) | `aws`, `terraform` |
| Deployment (mobile) | `app store`, `play store`, `fastlane` |
| Release (npm/crates/PyPI) | `release`, `publish` |
| Observability | `sentry`, `opentelemetry`, `logging` |
| Security | `security`, `owasp`, `secrets management` |
| Performance | `performance`, `profiling`, `caching` |

If the user said something specific ("we use Drizzle", "we use Sentry"), search for that directly with the bare term.

## Ranking

After you have verified install counts and stars for the top candidates, score them:

1. **Install count** — primary signal. >100K installs is dominant; 10K-100K is strong; 1K-10K is solid; 100-1K is plausible; <100 is weak.
2. **GitHub stars on the source repo** — secondary signal. A skill in a 50K-star repo from a known author beats an unknown 100-install standalone.
3. **Source reputation** — official authors (Anthropic, Vercel Labs, framework teams) get a boost.
4. **Recency** — skills updated in the last 6 months beat stale ones, all else equal.
5. **Description fit** — does the skill description actually match what we want, or is it tangentially related? A weak fit with high installs is worse than a strong fit with moderate installs.

Pick the top-ranked candidate per concern. Don't install multiple skills for the same job — one well-chosen skill per concern.

**Hard rule — sanity check on low picks.** If the top candidate has fewer than 10K installs, run the broader sanity search before recommending. Don't ship a 2.6K-install pick when a 400K-install alternative exists one broader search away.

If after sanity-checking, the top candidate still has fewer than 100 installs and no clear reputational signal, flag it to the user as a numbered choice:
> The best match for [category] is new and unproven ([N] installs).
>
> Reply with the number:
>   1) Install anyway
>   2) Skip it for now
>   3) Look at alternatives

## Agent detection

Most agents read skills from `.agents/skills/`, which `npx skills add` creates by default — so for those, no `-a` flag is needed. Claude Code is the exception: it reads from `.claude/skills/`, which requires `-a claude-code`.

**Detection rule — Claude Code or not:**

1. **Is this Claude Code?** Check for `.claude/` directory in the project root, or the `CLAUDE_CODE` environment variable. If yes → use `-a claude-code`.
2. **Anything else** (Codex, Cursor, OpenCode, Gemini CLI, GitHub Copilot, Cline, Continue, Windsurf, Kiro, Goose, Roo, Amp, etc.) → no `-a` flag. The default `.agents/skills/` folder works.
3. **Unsure?** Ask once:
   > Are you running Claude Code?
   >
   > Reply with the number:
   >   1) Yes — Claude Code
   >   2) No — something else (Codex, Cursor, OpenCode, etc.)

### Install command pattern

**Claude Code:**
```bash
npx skills add <repo> --skill <name> -a claude-code -y
```

**Everything else:**
```bash
npx skills add <repo> --skill <name> -y
```

The `-y` flag suppresses interactive prompts so installs don't stall mid-session.

## HOW_TO_USE.md template

When the user opts in at Section 7, write this file to the agent's skills folder — `.claude/skills/HOW_TO_USE.md` for Claude Code, `.agents/skills/HOW_TO_USE.md` for everything else:

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

To populate each entry:
1. Read each installed skill's `SKILL.md` frontmatter (name, description).
2. Extract trigger phrases from the description (the "Use when..." phrases tell you the natural-language triggers).
3. Check if the skill is invoked as a slash command in its body — most are; if so, the command is `/<skill-name>`.
4. Keep each entry to 3 short lines. The user can read the full SKILL.md if they want more.

After writing, tell the user where it landed and suggest committing it alongside the skills.

## Example session (illustrative)

```
User: /bootstrap-skills

Agent: [reads package.json — sees Next.js + Tailwind + Drizzle + Postgres]

       What kind of project is this?

       Reply with the number:
         1) Web app — UI + backend
         2) Backend service or API — no UI
         3) Mobile app
         4) CLI tool or library
         5) Docs / ADRs / planning repo
         6) Other — describe in one sentence

User: 1 — internal SaaS dashboard for our team

Agent: [language detected as TypeScript from package.json — skip language question]

       How should the agent behave on this project?
       Reply with comma-separated numbers:
         1) Plan before coding
         2) Compressed responses
         3) TDD discipline
         4) Disciplined debugging

User: 1, 3

Agent: I see Next.js, Tailwind, Drizzle, and Postgres in the repo.

       What's the build stack? Reply with comma-separated numbers:
         1) Frontend framework (Next.js detected)  ← recommended
         2) Backend framework
         3) Database / ORM (Drizzle + Postgres detected)  ← recommended
         4) UI / styling (Tailwind detected)  ← recommended
         5) Skip

User: 1, 3, 4

Agent: Which quality concerns matter for this project?
       Reply with comma-separated numbers:
         1) Testing
         2) Type safety
         3) Linting & formatting
         4) Code review
         5) Accessibility
         6) Skip

User: 1, 2, 5

Agent: What production concerns matter for this project?
       Reply with comma-separated numbers:
         1) Deployment & CI
         2) Observability
         3) Security
         4) Performance
         5) Not in production yet
         6) Skip

User: 1, 3

Agent: Anything specific you want skills for that I didn't cover?
       Reply with the number, or describe what you need:
         1) No, I'm good
         2) Yes — I'll describe it

User: 2 — Drizzle migrations specifically

Agent: [searches skills.sh for "drizzle"]
       Found [top drizzle skill] (12K installs). Adding it.

       Anything else, or are we good? (1 = good, 2 = add more)

User: 1

Agent: Here's the plan — install into .claude/skills/:
         1) [top "grill" skill] — plan before coding (150K installs)
         2) [top tdd skill] — disciplined testing workflow (400K installs)
         3) [top nextjs skill] — Next.js best practices (185K installs)
         4) [top drizzle+postgres skill] — Drizzle + Postgres patterns (45K installs)
         5) [top tailwind skill] — Tailwind patterns (90K installs)
         6) [top vitest skill] — testing for TS/JS projects (220K installs)
         7) [top typescript skill] — strict-mode type safety (310K installs)
         8) [top a11y skill] — accessibility for web (60K installs)
         9) [top docker skill] — deployment & CI (180K installs)
         10) [top security skill] — secrets & dependency scanning (40K installs)
         11) [top drizzle migrations skill] — migration patterns (12K installs)

       Reply with the number:
         1) Install all  ← recommended
         2) Install all except specific ones
         3) Swap one out
         4) Cancel

User: 1

Agent: [detects .claude/ directory → agent is claude-code]
       [runs npx skills add <repo> --skill <name> -a claude-code -y for each]
       Done — 11 skills installed into .claude/skills/.

       Want me to drop a 'How to use these skills' guide into the repo?
         1) Yes, write HOW_TO_USE.md  ← recommended
         2) Skip

User: 1

Agent: [reads each SKILL.md, writes .claude/skills/HOW_TO_USE.md]
       Wrote HOW_TO_USE.md. Commit it alongside the skills so the team
       has the same reference.
```
