# Hedera AI Agent Watch

A live, single-page dashboard showing on-chain AI agent activity on Hedera.

**Live demo:** https://YOUR_USERNAME.github.io/hedera-agent-watch

## What it shows

- **SDK downloads/week** — npm installs of `hedera-agent-kit` (proxy for builders picking up the SDK)
- **MCP installs/week** — npm installs of `@hashgraphonline/standards-sdk` (proxy for HCS-10 / agent adoption)
- **HCS messages/day** — real-time count of `CONSENSUSSUBMITMESSAGE` transactions on the selected network
- **Cost per 1,000 messages** — live HBAR/USD cost computed from current network fees and the Mirror Node exchange rate
- **Activity chart** — dual-series bar chart (all HCS messages + HCS-10 agent ops) across hours / days / weeks / month
- **Live feed** — most recent submit-message transactions
- **Active topics** — top HCS topics by activity, with memos pulled from `/topics/{id}` and clickable HashScan links

Mainnet / testnet toggle included — most agents currently live on testnet via Moonscape (~300+), so flip the network to see the agent layer.

## How it works

100% static HTML. No backend, no build step, no dependencies.

Data sources (all public, no auth):
- **Hedera Mirror Node REST API** — `mainnet.mirrornode.hedera.com/api/v1` and `testnet.mirrornode.hedera.com/api/v1` (50 RPS limit)
- **npm public downloads API** — `api.npmjs.org/downloads/point/last-week/{pkg}`

HCS-10 detection: scans transaction memos and decoded message payloads for the `hcs-10` protocol marker.

## Deploy

1. Fork or clone this repo
2. Replace `YOUR_USERNAME` in `index.html` with your GitHub username (currently only used in the footer link back to the awesome list)
3. Push to GitHub
4. Settings → Pages → Build and deployment → Source: **Deploy from a branch** → Branch: `main` / `(root)` → Save
5. Wait ~1 minute. Site goes live at `https://YOUR_USERNAME.github.io/hedera-agent-watch`

That's it. No environment variables, no API keys.

## Local preview

Just open `index.html` in a browser. Or run any static server:

```bash
python3 -m http.server 8000
# → http://localhost:8000
```

## Limitations

- Mirror Node rate-limits at 50 RPS. The chart paginates per bucket up to a cap (5k–10k transactions depending on range) to keep load reasonable.
- HCS-10 detection is best-effort — counts transactions whose memo or decoded message contains the `hcs-10` marker. Some agent traffic on testnet doesn't tag itself this way.
- npm download numbers are the published weekly totals; they update daily on a lag.

## License

MIT. Do whatever you want with it.
