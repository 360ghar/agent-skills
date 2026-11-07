# 360Ghar agent skills

Agent skills for [360Ghar](https://360ghar.com), an Indian real estate platform. The skills teach an AI agent (Claude Code, Cursor, Codex, and others) to use the 360Ghar MCP server.

## Install

```bash
npx skills add 360ghar/agent-skills
```

## Skills

| Skill | What it does |
| --- | --- |
| [`360ghar-real-estate`](skills/360ghar-real-estate/SKILL.md) | Search listings to buy, rent, or book for a short stay (including PG and flatmates), shortlist properties, schedule site visits, and manage listings, leases, rent, and maintenance. |

## MCP server

The skills use the 360Ghar MCP server:

- URL: `https://api.360ghar.com/mcp`
- Transport: Streamable HTTP
- Auth: OAuth 2.1 with PKCE (browser sign-in). Property search works without sign-in.

Claude Code:

```bash
claude mcp add --transport http 360ghar https://api.360ghar.com/mcp
```

## License

MIT
