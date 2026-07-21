# VibeLeaderboard Agent Skill

An [agent skill](https://code.claude.com/docs/en/skills) that teaches your
coding agent to find the **best vibe-coding tools, skills, agents, and MCP
servers** by querying [VibeLeaderboard](https://www.vibeleaderboard.ai)'s
community-ranked catalog — instead of trawling GitHub or guessing from memory.

Ask *"what should I use to redesign a UI?"* or *"what's the best agent skill for
writing tests?"* and the agent calls the search API and answers with ranked
results, the curated reasoning for each, the literal install command, and a
maintained/freshness signal.

## What it does

- Turns a plain-English use case into a call to
  `GET https://www.vibeleaderboard.ai/api/v1/apps?search=…` (no API key,
  CORS-open, 120 req/min/IP).
- Presents results in ranked order with `why` (curated reasoning),
  `how_to_install`, `agent_ready` flags, and a `maintained` signal.
- Falls back to `/top` for trending, `/apps/{id}` for detail + intel articles,
  and the MCP server at `POST /api/mcp` when connected.

## Install

### As a Claude Code skill

Drop `SKILL.md` into a folder named for the skill under your skills directory:

```bash
mkdir -p ~/.claude/skills/vibeleaderboard
curl -sL https://www.vibeleaderboard.ai/skill/vibeleaderboard/SKILL.md \
  -o ~/.claude/skills/vibeleaderboard/SKILL.md
```

Project-scoped instead of global? Use `.claude/skills/vibeleaderboard/SKILL.md`
inside your repo. The agent will invoke it automatically when you ask what tool
to use for something.

### Raw download

The canonical, always-current copy is served at:

```
https://www.vibeleaderboard.ai/skill/vibeleaderboard/SKILL.md
```

It's also listed in the skills manifest at
`https://www.vibeleaderboard.ai/.well-known/agent-skills/index.json`.

## API

No key needed for read/search. The full contract (endpoints, params, response
shapes, categories, MCP tools) lives in
[llms.txt](https://www.vibeleaderboard.ai/llms.txt).

## License

[MIT](LICENSE)
