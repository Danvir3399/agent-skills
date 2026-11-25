# Onlypult agent skills

Skills and a ready MCP connection that teach AI agents (Claude Code, Cursor, Codex and others) how to work with [Onlypult](https://onlypult.com): schedule and publish posts, Stories, Reels and Shorts, fix failed posts, and read analytics.

The MCP server is hosted at `https://mcp.onlypult.com/mcp`. This repository does not contain server code. It contains the **skills** that explain to an agent how to use its tools well, plus the plugin manifests that install both in one step.

- MCP docs: https://onlypult.com/dev/mcp.html
- REST API: https://onlypult.com/dev/api.html

[![Onlypult MCP connector](https://glama.ai/mcp/connectors/com.onlypult/mcp/badges/score.svg)](https://glama.ai/mcp/connectors/com.onlypult/mcp)

## Skills

| Skill | What it does |
|---|---|
| [`schedule-post`](skills/schedule-post/SKILL.md) | Create, schedule, publish or draft a post; edit and delete |
| [`cross-post`](skills/cross-post/SKILL.md) | Same content to several profiles, within each platform's limits |
| [`short-form-video`](skills/short-form-video/SKILL.md) | Reels, Shorts and Stories |
| [`upload-media`](skills/upload-media/SKILL.md) | Public URL, browser upload or local file upload |
| [`fix-failed-posts`](skills/fix-failed-posts/SKILL.md) | Find failures, explain, fix, retry |
| [`analytics-report`](skills/analytics-report/SKILL.md) | Followers, reach, engagement, top posts, period comparison |
| [`best-time-to-post`](skills/best-time-to-post/SKILL.md) | Best days and hours from the engagement heatmap |
| [`content-calendar`](skills/content-calendar/SKILL.md) | Plan and fill a week or month |
| [`hashtag-sets`](skills/hashtag-sets/SKILL.md) | Manage and reuse saved hashtag sets |

## Install

### Claude Code

```bash
/plugin marketplace add onlypult/agent-skills
/plugin install onlypult@onlypult
```

The first call to an Onlypult tool opens the OAuth sign-in. To use an API key instead, add the server by hand with a header:

```bash
claude mcp add --transport http onlypult https://mcp.onlypult.com/mcp --header "Authorization: Bearer op_your_api_key"
```

### Cursor

Install the plugin from this repository, or add the server to `.cursor/mcp.json` and copy `skills/` into `.cursor/skills/`. Hosted config:

```json
{
  "mcpServers": {
    "onlypult": {
      "type": "http",
      "url": "https://mcp.onlypult.com/mcp",
      "headers": { "Authorization": "Bearer op_your_api_key" }
    }
  }
}
```

### Codex and other agents

The skills are plain `SKILL.md` files. Point your agent at the `skills/` folder and connect the MCP server from `.mcp.json`.

### Uploading files from disk

The hosted server cannot read your files. To post a local image or video by path, use the local package instead:

```json
{
  "mcpServers": {
    "onlypult": {
      "command": "npx",
      "args": ["-y", "@onlypult/mcp@latest"],
      "env": { "ONLYPULT_API_KEY": "op_your_api_key" }
    }
  }
}
```

The `upload-media` skill picks the right method for the connection in use.

## API key

Create one in Onlypult under Settings → API Keys. It starts with `op_` and is shown once.

## Repository layout

```
skills/                    single source of truth for every agent
.claude-plugin/            Claude Code manifest and marketplace
.cursor-plugin/            Cursor manifest
.codex-plugin/             Codex manifest
.mcp.json, mcp.json        MCP connection (same content)
```

All manifests point at the same `skills/` folder. Edit a skill once and every agent gets it.

## Contributing

One folder per skill, with a `SKILL.md` that has `name` and `description` in its front matter. The description should say **when** to use the skill, because that is what the agent matches on. Use real tool names and parameters from the MCP docs.

## License

MIT
