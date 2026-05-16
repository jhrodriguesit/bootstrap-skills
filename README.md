# bootstrap-skills

An agent skill that runs a short structured interview about your project, then installs the best-matching skills from [skills.sh](https://www.skills.sh) ranked by install count, stars, and source reputation.

Works across any agent that supports the SKILL.md standard — Claude Code, Codex, OpenCode, Cursor, Gemini CLI, GitHub Copilot, Cline, Continue, Windsurf, Kiro, Goose, Roo, Amp, and others.

All skills install into the project's local skills folder — `./.claude/skills/` for Claude Code, `./.agents/skills/` for everything else (the default created by `npx skills add`) — so they're committable alongside the project.

## What it does

- Detects your stack from manifest files (`package.json`, `pyproject.toml`, `Cargo.toml`, `go.mod`, `Package.swift`, etc.) before asking anything
- Asks **at most 6 structured questions** organized by software-development concern, not stack layer:
  1. **Project shape & language** — what kind of project, what language
  2. **Working style** — how the agent should behave moment-to-moment
  3. **Building it** — frameworks and libraries (options adapt to project type)
  4. **Quality & correctness** — testing, types, linting, code review, accessibility
  5. **Running it in production** — deployment, observability, security, performance
  6. **Anything else** — open-ended escape hatch (loops until you opt out)
- Every question is a numbered list — reply with a number (or comma-separated numbers for multi-select), no free-text required
- Handles web apps, backend services, mobile apps, and CLI tools/libraries — the questions stay the same, the options shown adapt
- Searches skills.sh with language- and project-type-aware queries
- Ranks candidates by popularity and quality signals
- Shows you the plan, then installs on confirm
- Detects which agent you're running and installs to the right folder automatically

## Install

```bash
npx skills add jhrodriguesit/bootstrap-skills
```

Then in your agent (Claude Code, Codex, OpenCode, etc.):

```
/bootstrap-skills
```

## When to use

- Starting a new project
- Picking up an unfamiliar codebase
- Refreshing skills on a project you haven't touched in a while
- Helping a teammate onboard

## Files

- `SKILL.md` — the interview flow and operating principles (loaded by the agent on every run)
- `REFERENCE.md` — lookup tables for search queries, ranking rubric, agent flags, the HOW_TO_USE.md template, and an example session (loaded on demand when needed)

## License

MIT
