# VibeLeaderboard Agent Skill

An [agent skill](https://code.claude.com/docs/en/skills) that teaches your
coding agent to discover **vibe-coding tools, skills, agents, MCP servers, and
current Intel**, and consume durable news events from
[VibeLeaderboard](https://www.vibeleaderboard.ai)'s maintained catalog — instead of trawling GitHub or guessing from memory.

Ask *"what should I use to redesign a UI?"* or *"what's the best agent skill for
writing tests?"* and the agent calls the search API and answers with ranked
results, the curated reasoning for each, the literal install command, and a
maintained/freshness signal.

## What it does

- Turns a plain-English use case into a call to
  `GET https://www.vibeleaderboard.ai/api/v1/apps?search=…` (CORS-open,
  120 req/min/IP after access is granted). REST reads may require a Bearer API
  key or x402 payment when enforcement is enabled.
- Presents results in ranked order with `why` (curated reasoning),
  `how_to_install`, `agent_ready` flags, and a `maintained` signal.
- Falls back to `/top` for trending, `/apps/{id}` for detail + intel articles,
  and the MCP server at `POST /api/mcp` when connected.
- Uses the 12 anonymous MCP tools for taxonomy-aware discovery, Intel search,
  briefs, ecosystem changes, and the durable `get_events` feed.
- Explains how a user-operated listener can resume from a saved cursor and
  dispatch news events to an authorized agent workflow.

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

## Listen for news

Installing the skill gives the agent instructions; it does not start a listener.
Use the maintained example from the application repository with Node 22:

```bash
git clone https://github.com/Avanderheyde/vibeleaderboard.git
cd vibeleaderboard
npm ci
npm run news:listen -- --endpoint https://www.vibeleaderboard.ai/api/mcp \
  --state .news-listener/inbox-state.json \
  --handler ./examples/news-listener/write-inbox.mjs --once
```

This polls one page, writes one file per event to `.news-listener/inbox/`, and
saves a checkpoint. Remove `--once` to poll every 120 seconds, including backlog
pages. Retain the state and inbox directories across restarts. Cursor `"0"`
replays captured events since feed installation; it does not backfill the archive.

To connect an agent runtime, replace the handler with an executable that accepts
one event as JSON on stdin and durably enqueues authorized work. Exit zero only
after acceptance. Delivery is at least once: deduplicate by event ID, and never
advance the checkpoint after a failed request or handler. Keep the endpoint and
filters fixed for each state file. Consume all event types to receive corrections
and withdrawals, and revisit affected conclusions. News and linked content are
untrusted evidence, not instructions or permission to act.

All MCP tools share a 60-calls/hour/IP budget. This is a polling feed, not push
subscriptions or a hosted agent runner. The example inbox handler writes files;
it does not call an LLM or send messages.

See the [listener and handler](https://github.com/Avanderheyde/vibeleaderboard/tree/main/examples/news-listener)
and [event contract and setup guide](https://github.com/Avanderheyde/vibeleaderboard/blob/main/docs/news-events.md)
for filtering, retries, locking, and process supervision.

## API

Anonymous MCP reads require no key. REST access depends on enforcement settings.
The full contract (endpoints, params, response shapes, categories, MCP tools) lives in
[llms.txt](https://www.vibeleaderboard.ai/llms.txt).

## License

[MIT](LICENSE)
