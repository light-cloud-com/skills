# Light Cloud skills

Agent skills for [Light Cloud](https://light-cloud.com), the hosting platform you can drive from an AI agent.

| Skill | What it does |
|---|---|
| [light-cloud-deploy](skills/light-cloud-deploy/SKILL.md) | Deploys the current project to Light Cloud (sign-up included) and manages it afterwards: logs, environment variables, databases, rollbacks, custom domains. Uses the [Light Cloud MCP server](https://github.com/light-cloud-com/mcp-server) when it is connected, otherwise the [`lc` CLI](https://github.com/light-cloud-com/cli). |

## Install

**Claude Code**

```bash
git clone https://github.com/light-cloud-com/skills
mkdir -p ~/.claude/skills && cp -r skills/skills/light-cloud-deploy ~/.claude/skills/
```

For the MCP tools as well (recommended):

```bash
claude mcp add light-cloud -- npx @light-cloud/mcp-server
```

**Cursor, Codex and other agents that read `SKILL.md`**

Copy `skills/light-cloud-deploy` into the agent's skills folder (for Cursor, `.cursor/skills/` in the project; for Codex, `~/.codex/skills/`).

Then ask: "deploy this project to Light Cloud".

## License

MIT
