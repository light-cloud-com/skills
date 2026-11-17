# Light Cloud

Deploy websites, web apps and APIs to [Light Cloud](https://light-cloud.com) from a conversation, and run them afterwards. Claude writes the code into a repository in your own GitHub, GitLab or Bitbucket account, Light Cloud builds it and gives back a live `light-cloud.io` URL, and every later change is a commit that redeploys. The plugin also covers databases, environment variables, logs, rollbacks, visitor passwords and custom domains.

It contains three skills and the Light Cloud connector (`https://mcp.light-cloud.com/mcp`, the same server as the Light Cloud connector in Claude's directory, so you get one set of tools if you have both).

| Skill | What it does |
|---|---|
| [light-cloud-deploy](skills/light-cloud-deploy/SKILL.md) | Builds and deploys a site or app (new code, an existing repository, a folder in Claude Code, or a template), hands over the URL, fixes failed builds, and manages the app afterwards |
| [light-cloud-databases](skills/light-cloud-databases/SKILL.md) | Creates PostgreSQL or MySQL databases, connects them to an app, shows tables, runs read-only queries and, after you agree, changes |
| [light-cloud-domains](skills/light-cloud-domains/SKILL.md) | Puts an app on your own domain: the DNS records to add or delete, verification, and certificate retries |

## Use it

1. Install the plugin. On claude.ai and in Cowork, add **Light Cloud** from the directory. In Claude Code:

   ```bash
   claude plugin marketplace add light-cloud-com/skills
   claude plugin install light-cloud@light-cloud
   ```

2. Connect the connector and sign in. On claude.ai and in Cowork, open the plugin's **Connectors** tab and connect Light Cloud. In Claude Code, run `/mcp`, pick `light-cloud` and sign in. A Light Cloud page opens and names Claude: sign in and choose **Allow**. If you have no account, the same page creates one (Continue with Google or GitHub, Free plan, no card).
3. Ask, for example:
   - "What's the status of my Light Cloud workspace?"
   - "Build a one-page site for my bakery with the opening hours and put it live."
   - "Change the opening hours to 8 to 4 on weekdays."
   - "Add a Postgres database for the shop and show me its tables."
   - "Put the bakery site on www.example.com."

Claude asks before it deletes anything, overwrites your files, runs SQL that changes data, or starts a paid plan. Every workspace starts on the Free plan; custom domains and databases need a paid plan, and Claude tells you the plan and its price before it changes anything.

## What it connects to and what it sends

The plugin is Markdown skills plus one MCP server address. It runs no code of its own on your machine and asks for no keys.

- **Light Cloud connector** at `mcp.light-cloud.com`. Claude's tool calls go there, and the server carries them out on your Light Cloud account through `api.light-cloud.com`. They contain what the task needs: the files of a site Claude writes, file changes to commit, environment variable values you give it, SQL to run, a domain name. The server sees only those tool calls, not your conversation, and keeps nothing between requests.
- **Your git provider.** Files Claude writes go into a repository in your GitHub, GitLab or Bitbucket account (private unless you ask for a public one), through the connection your Light Cloud account has to that provider.
- **Uploads, Claude Code only.** For a folder on your machine that is too large for one commit, Claude may zip the source and upload it to `api.light-cloud.com`. It leaves out `node_modules`, build output and `.env` files.
- **Without the connector**, the deploy skill can use Light Cloud's local MCP server (`@light-cloud/mcp-server`) or command-line tool (`@light-cloud/cli`) from npm. Both talk to the same API.

Database connection strings and API keys never pass through the connector: you copy a connection string in the Light Cloud console. To end access, disconnect the connector in Claude, or in the Light Cloud console under **Settings → Security → Connected devices**. **Settings → Security → Agents & CLI** turns off agent access for your account, or for groups of actions.

## Privacy

Light Cloud's [privacy policy](https://help.light-cloud.com/legal/privacy-policy) covers the data Light Cloud processes when you use this plugin. Use of Light Cloud is subject to the [terms of service](https://help.light-cloud.com/legal/terms-of-service).

## Support

- Contact: [light-cloud.com/contact](https://light-cloud.com/contact?subject=support)
- Documentation: [docs.light-cloud.com/deploy-with-ai/mcp-server](https://docs.light-cloud.com/deploy-with-ai/mcp-server)
- Issues with the skills: [github.com/light-cloud-com/skills/issues](https://github.com/light-cloud-com/skills/issues)

## Other agents

Cursor, Codex and other agents that read `SKILL.md` can use the skills on their own. Copy the folders under `skills/` into the agent's skills folder (for Cursor, `.cursor/skills/` in the project; for Codex, `~/.codex/skills/`), and add the Light Cloud MCP server or use the `lc` CLI.

## License

MIT, see [LICENSE](LICENSE).
