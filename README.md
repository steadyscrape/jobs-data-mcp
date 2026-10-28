# Jobs Data MCP: live job postings for AI agents

A remote MCP server that gives Claude, Cursor, VS Code, ChatGPT and other MCP clients live job data, collected straight from employers' own career sites rather than job-board reposts.

**Endpoint (Streamable HTTP):**

```
https://mcp.apify.com/?tools=steady-scrape/ats-job-feed,steady-scrape/career-jobs-search,steady-scrape/remote-jobs-api
```

The server runs on Apify's hosted MCP gateway ([mcp.apify.com](https://mcp.apify.com)) with three [SteadyScrape](https://apify.com/steady-scrape) Actors preselected. Nothing to install or host.

## Tools

| Tool | What it does | Price |
|---|---|---|
| `steady-scrape--ats-job-feed` | Every open job from the companies you name, across Greenhouse, Lever, Ashby, Workday, Oracle HCM, SmartRecruiters, Workable, BambooHR, Personio, Rippling, Teamtailor and more. Optional "only new since last run" mode. ([Store](https://apify.com/steady-scrape/ats-job-feed)) | $0.0015 / job |
| `steady-scrape--career-jobs-search` | Keyword search ("data engineer", "nurse") across company career sites, with location, remote and date filters. ([Store](https://apify.com/steady-scrape/career-jobs-search)) | $0.002 / job |
| `steady-scrape--remote-jobs-api` | Remote jobs from employer career sites, filterable by keyword, region and recency. ([Store](https://apify.com/steady-scrape/remote-jobs-api)) | $0.0015 / job |
| `get-actor-run`, `get-dataset-items`, `get-key-value-store-record`, `abort-actor-run` | Apify helpers for fetching larger results and managing runs | free |

You pay per job returned, billed to your own Apify account. New accounts get free monthly credit ([sign up](https://console.apify.com/sign-up)).

## Authentication

Either:

- **OAuth**: clients that support MCP OAuth (Claude.ai, VS Code, Cursor) open an Apify sign-in the first time, or
- **API token**: send `Authorization: Bearer <APIFY_TOKEN>`. Get the token from [Console → Settings → API & Integrations](https://console.apify.com/settings/integrations).

## Client setup

**Cursor** (`~/.cursor/mcp.json`), **Windsurf**, and other clients that use `mcpServers`:

```json
{
  "mcpServers": {
    "jobs-data": {
      "url": "https://mcp.apify.com/?tools=steady-scrape/ats-job-feed,steady-scrape/career-jobs-search,steady-scrape/remote-jobs-api",
      "headers": { "Authorization": "Bearer <YOUR_APIFY_TOKEN>" }
    }
  }
}
```

**VS Code** (`.vscode/mcp.json`):

```json
{
  "servers": {
    "jobs-data": {
      "type": "http",
      "url": "https://mcp.apify.com/?tools=steady-scrape/ats-job-feed,steady-scrape/career-jobs-search,steady-scrape/remote-jobs-api"
    }
  }
}
```

**Claude Code:**

```bash
claude mcp add --transport http jobs-data "https://mcp.apify.com/?tools=steady-scrape/ats-job-feed,steady-scrape/career-jobs-search,steady-scrape/remote-jobs-api" \
  --header "Authorization: Bearer $APIFY_TOKEN"
```

**Claude.ai / Claude Desktop:** Settings → Connectors → *Add custom connector* → paste the endpoint URL, then sign in to Apify.

**Clients that only speak stdio:**

```json
{
  "mcpServers": {
    "jobs-data": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://mcp.apify.com/?tools=steady-scrape/ats-job-feed,steady-scrape/career-jobs-search,steady-scrape/remote-jobs-api", "--header", "Authorization:${AUTH_HEADER}"],
      "env": { "AUTH_HEADER": "Bearer <YOUR_APIFY_TOKEN>" }
    }
  }
}
```

## Example prompts

- "Which of Stripe, Ramp and Notion are hiring data engineers right now? Give me links."
- "Find remote senior Python roles posted in the last 7 days, Europe-friendly."
- "Compare how many open engineering roles Figma and Canva have, by team."

Agents should keep requests small (for example `maxJobsPerCompany` or `maxResults` of 20–50) and use `get-dataset-items` to page through larger results.

## Registry files

- [`server.json`](server.json): official MCP Registry entry (`io.github.steadyscrape/jobs-data-mcp`)
- [`glama.json`](glama.json): Glama maintainer claim

More examples (Python, JavaScript, LangChain, CrewAI, n8n): [steadyscrape-examples](https://github.com/steadyscrape/steadyscrape-examples).

## License

MIT. This repo contains configuration and docs only; the tools are paid services on Apify.
