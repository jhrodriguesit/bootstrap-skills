# bootstrap-skills

An agent skill that runs a short structured interview about your project, then installs the best-matching skills from [skills.sh](https://www.skills.sh) ranked by install count, stars, and source reputation.

Works across any agent that supports the SKILL.md standard — Claude Code, Codex, OpenCode, Cursor, Gemini CLI, GitHub Copilot, and others.

All skills install into the project's local skills folder (the path depends on the agent — `./.claude/skills/` for Claude Code, `./.codex/skills/` for Codex, etc.) so they're committable alongside the project.

## What it does

- Detects your stack from manifest files (`package.json`, `pyproject.toml`, etc.) before asking anything
- Asks **at most 6 structured questions** about working style, frontend, backend, data, deployment
- Offers one open-ended "anything else?" step so you can add niche needs
- Searches skills.sh for each affirmative answer
- Ranks candidates by popularity and quality signals
- Shows you the plan, then installs on confirm

## Install

```bash
npx skills add <your-username>/bootstrap-skills
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

## License

MIT
