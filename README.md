# Hedera AI Agent Watch

A live, single-page dashboard showing on-chain AI agent activity on Hedera.

**Live demo:** https://jacobjohnson2-cmyk.github.io/hedera-agent-watch

## What it shows

- **SDK downloads/week** — npm installs of the Hedera Agent Kit + core SDK (proxy for builders picking up the SDK)
- **HCS-10 + MCP installs/week** — npm installs of `@hashgraphonline/standards-sdk` and friends (proxy for HCS-10 / agent adoption)
- **HCS messages/day** — `CONSENSUSSUBMITMESSAGE` volume on the selected network
- **Cost per 1,000 messages** — live HBAR/USD cost computed from current network fees and the Mirror Node exchange rate
- **HCS Activity Trend** — all HCS messages vs HCS-10 agent ops over 24 hours, 7 days, 8 weeks or **12 calendar months** (the current month shows "so far" with a dashed full-month projection). Hover/tap any bar for exact numbers and change vs the previous period; a summary row shows period-over-period change, the busiest period, totals and HCS-10 share.
- **x402 Agent Payments** — settlements of pay-per-request x402 payments, tracked by watching transfers whose fee is paid by a known facilitator. Shows settlements, USDC and HBAR volume, unique buyers/sellers, a trend chart over the same ranges, and the latest settlements.
- **Agents Leaderboard** — who's posting to HCS, filterable to **All posters → HCS-10 → ✓ Certified** (accounts whose memo points to an HCS-11 agent profile). Sort by messages, recency or topics; choose a 300 / 1,000 / 2,000-message sample; search by account, topic or memo; click a row for its topics, memos and profile.
- **Active topics** — top HCS topics with memos; click one to filter the leaderboard to it.
- **How to read this page** — an expandable glossary explaining every metric.

Mainnet / testnet toggle included — most agents currently live on testnet, so flip the network to see the agent layer.

## Shareable views & X

Every control is mirrored into the URL, so any view can be linked to:

| Param | Values | Default |
|---|---|---|
| `net` | `mainnet`, `testnet` | `mainnet` |
| `range` | `hours`, `days`, `weeks`, `months` | `hours` |
| `agents` | `all`, `hcs10`, `certified` | `all` |
| `sample` | `300`, `1000`, `2000` | `300` |
| `sort` | `msgs`, `recent`, `topics` | `msgs` |
| `q` | search text | — |
| `x402` | comma-separated facilitator fee-payer accounts, e.g. `0.0.123,0.0.456` | built-in list |

Add `#trend`, `#x402` or `#agents` to jump to a panel, e.g.
`https://jacobjohnson2-cmyk.github.io/hedera-agent-watch/?range=months&net=testnet#x402`

- **Share on X** (header) and **Share chart** (each chart) open an X post pre-filled with the live numbers and a link to the exact view.
- **Copy link** copies the current view's URL.
- Open Graph / Twitter Card tags + `og-card.png` give links a large preview card on X, LinkedIn, Slack and Discord.
- Set `X_HANDLE` at the top of the script to append "via @yourhandle" to share posts.

## How it works

100% static HTML. No backend, no build step, no dependencies.

Data sources (all public, no auth):
- **Hedera Mirror Node REST API** — `mainnet.mirrornode.hedera.com/api/v1` and `testnet.mirrornode.hedera.com/api/v1` (50 RPS limit; the page queues requests to stay well under it)
- **npm public downloads API** — `api.npmjs.org/downloads/point/last-week/{pkg}`

Detection rules:
- **HCS-10**: the transaction memo contains the `hcs-10` protocol marker.
- **Certified**: the payer account's memo starts with `hcs-11:` (an HCS-11 profile reference, e.g. `hcs-11:hcs://1/0.0.x`). The top 150 posters in the sample are checked.
- **x402 settlement**: a successful `CRYPTOTRANSFER` whose payer is a facilitator fee-payer account, with at least one buyer and one seller (system/fee accounts below `0.0.1000` are ignored). Built-in facilitators: Blocky402 (mainnet `0.0.10571514`, testnet `0.0.7162784`) and x402.org (testnet `0.0.9185802`). Fee payers can rotate — check each facilitator's `/supported` endpoint and update `X402_FACILITATORS`, or override with `?x402=`.

## Deploy

1. Fork or clone this repo
2. Replace `jacobjohnson2-cmyk` in `index.html` with your GitHub username (currently only used in the footer link back to the awesome list)
3. Push to GitHub
4. Settings → Pages → Build and deployment → Source: **Deploy from a branch** → Branch: `main` / `(root)` → Save
5. Wait ~1 minute. Site goes live at `https://jacobjohnson2-cmyk.github.io/hedera-agent-watch`

That's it. No environment variables, no API keys.

## Local preview

Just open `index.html` in a browser. Or run any static server:

```bash
python3 -m http.server 8000
# → http://localhost:8000
```

## Limitations

- The Mirror Node has no count endpoint. Busy periods are **estimated** (marked `~`) by sampling the message rate at several points across each bar; quiet periods are counted exactly. Completed calendar months are cached for the session.
- x402 bars in very busy periods are scaled up from the most recent pages of settlements; unique buyer/seller counts are "seen in sampled transactions".
- HCS-10 detection is best-effort — some agent traffic doesn't tag its memo. "Certified" only means an HCS-11 profile is referenced on-chain; the profile itself isn't validated.
- npm download numbers are the published weekly totals; they update daily on a lag.

## License

MIT. Do whatever you want with it.
