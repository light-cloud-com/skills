---
name: light-cloud-deploy
description: Deploy the current project to Light Cloud and manage it afterwards (logs, environment variables, databases, rollbacks, custom domains). Use when the user asks to deploy, host, ship or put online an app, website or API, or mentions Light Cloud. Works through the Light Cloud MCP server when it is connected, otherwise through the `lc` CLI.
---

# Deploy to Light Cloud

Light Cloud hosts static sites, server-rendered apps, APIs and databases. It reads the project, picks the build, and gives back a live `*.light-cloud.io` URL. Static builds go to a CDN; everything else runs as a container that scales to zero when idle.

## Pick the tool

1. If the `light-cloud` MCP tools are available (`connect`, `upload-and-deploy`, `wait-for-deployment`, ...), use them.
2. Otherwise use the CLI: `npx @light-cloud/cli <command>` (or `lc <command>` if it is installed globally). Add `--json` when you need to read the output, and `--yes` to skip prompts.

Don't ask the user to open the web console. Everything below works from here.

## 1. Sign in (or sign up)

Check first: MCP `whoami`, or `lc whoami`.

If not signed in, ask for the user's email, then:
- MCP: `connect` with the email, then poll `connect-status` until it is approved.
- CLI: `lc login --device --email <email>`.

Both print an 8-character code. Tell the user to type it at `console.light-cloud.com/device` on any device. A new email gets an account on the Free plan: no card, no password. Never ask for a password.

## 2. Look at the project

- MCP: `detect-local-framework` on the project folder. CLI: check for a `.lightcloud` file (the folder is already linked to an app) and read `package.json`, `requirements.txt`, `go.mod`, `Dockerfile` and so on.
- Detection covers the common frameworks (Next.js, Nuxt, SvelteKit, Astro, React, Vue, Angular, Django, FastAPI, Flask, Rails, Laravel, Express, NestJS, Go, .NET, Spring Boot and more) and any project with a `Dockerfile`. Full list: https://docs.light-cloud.com/platform/frameworks
- A server must listen on the `PORT` environment variable.

Ask the user only what the project can't tell you, in one message: which workspace (only if they have several) and whether the site should be public or password-protected.

## 3. Deploy

Local folder:
- MCP: `upload-and-deploy` (pass `password` to gate the site), then `wait-for-deployment`. It blocks until the build finishes and returns the URL.
- CLI: `lc init` (link or create the app), then `lc deploy`. Use `lc create --upload` to create from the folder without a Git repository.

From a GitHub, GitLab or Bitbucket repository:
- MCP: `create-application` with the repository, then `wait-for-deployment`. If access is missing, `get-github-install-url` (GitHub) or `connect-git-provider` (GitLab, Bitbucket) returns the link the user must open.
- CLI: `lc create`, then `lc deploy`.

Hand over the live URL, plus the password if you set one. Nothing else is needed.

## 4. If the build fails

Read the build log (MCP `get-build-logs`, CLI `lc deployment <id>`), fix the cause in the project (missing start script, wrong port, missing dependency), and deploy again. Don't change build settings to hide an error.

## After the deploy

| Task | MCP tool | CLI |
|---|---|---|
| Runtime logs | `get-environment-logs` | `lc logs -f` |
| Environment variables | `set-environment-variables` | `lc env vars set KEY=value --redeploy` |
| Database | `create-database`, `get-database-connection-string` | `lc db create`, `lc db url` |
| Roll back | `rollback-deployment` | `lc rollback` |
| Custom domain | `add-custom-domain`, `get-custom-domain-status` | `lc domains add <domain>` |
| Status | `get-application-status` | `lc status` |

To use a database, create it, get its connection string, and set it as `DATABASE_URL` (or what the app reads) with a redeploy.

## Plans

Every workspace starts on Free: $1 of usage a month, unlimited static sites, 3 server apps, no card. When the $1 is used up, server apps pause until next month and static sites stay up; a free workspace is never billed. Custom domains and databases need a paid plan.

If a tool or command is refused by a plan limit, the error names the plan and the next step. Tell the user the plan and its price and get a yes before calling `choose-plan` / `lc billing upgrade <plan>`: that takes payment, through a Stripe page the user opens. Card numbers never pass through the agent.

## Refusals

- `AGENT_ACCESS_DISABLED` or `AGENT_ACTION_BLOCKED`: the account owner switched this off under Settings → Security → Agents & CLI. Tell the user; don't retry or look for a way around it.
- An error ending in `Next step: call X`: call X, then retry.
