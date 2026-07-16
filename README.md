# beta-tools

The incubator overlay for [mcp-tools](https://github.com/wnkinc/claude-custom-connector-server):
tools that run on the live stack but haven't (or won't) graduate to the main
repo. Main stays the curated production set; experiments land here first.
Graduation is a folder move — every tool dir is self-contained — gated by
main's CI. Loose by design: no CI here.

## The tools

| Tool | What it is | Built on |
|---|---|---|
| `xmcp` | The full X API surface — reads, and OAuth1 user-context writes acting as your account | [xdevplatform/xmcp](https://github.com/xdevplatform/xmcp) |
| `data` | Crypto market data into a local parquet lake, exportable to the backtester | [OpenBB](https://openbb.co) (Tiingo provider) |
| `lean` | Self-hosted backtests of agent-authored algorithms over the lake's data | [QuantConnect Lean](https://github.com/QuantConnect/Lean), pinned engine image |

## Running

Clone inside the main checkout (the image builds need main's `./security` in
their context) and pin the file list:

```
git clone https://github.com/wnkinc/beta-tools <mcp-tools>/beta
```

In the main repo's root `.env`:

```
COMPOSE_FILE=docker-compose.yml:docker-compose.tunnel.yml:beta/docker-compose.beta.yml
```

Then fill each `tools/<name>/.env` from its `env.example`, add the tools to
`COMPOSE_PROFILES`, and `docker compose up -d --build` from the main repo root.
A changed `egress/` file needs `docker compose restart egress`; a route change
needs a cloudflared `--force-recreate`.

Each tool keeps the same layout as a main-repo tool (`server.py`, `Dockerfile`,
`requirements.lock`, `deploy.json`, `env.example`); the compose/egress/ingress
wiring lives in `docker-compose.beta.yml` and `egress/beta.conf` instead of
main's files. The ingress route list in the overlay restates main's routes
(compose replaces config content wholesale) — keep it in sync when main's
routes change.
