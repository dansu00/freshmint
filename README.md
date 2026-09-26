# Freshmint — CA Scanner (prototype)

A single-page dashboard UI for a meme-coin contract-address scanner: a live
feed of newly-minted tokens (0–60s old) with a technical + fundamental read
on each, plus a separate panel for KOL activity.

**The feed and KOL panel are simulated.** They're generated in the browser
(see the `<script>` at the bottom of `index.html`) — no live connection to
X or Telegram yet.

**The wallet and buy execution are real.** Each card has a "Buy" row that:
connects to whatever Solana wallet extension is installed (Phantom,
Solflare, Backpack — anything that injects `window.solana`), gets a quote
and a swap transaction from the [Jupiter](https://jup.ag) aggregator API,
and asks the wallet to sign and send it on Solana mainnet. Nothing here
holds or sees your private key — the wallet extension does the signing.

Two things to know before using it for real:
- **The demo tokens aren't real mints.** Their contract addresses are
  randomly generated for the mock feed, so clicking "Buy" on one will fail
  at the quote step (no route found). Buy execution only works against a
  real token's mint address — once the scanner is wired to a live feed, or
  if you paste a real mint in for testing.
- **The public RPC endpoint is rate-limited.** The RPC field in the header
  defaults to Solana's public endpoint, which is fine for occasional use but
  will throttle under load. For real trading, get a private RPC URL from
  Helius, QuickNode, or similar and paste it in (it's saved in your browser
  via `localStorage`, never sent anywhere else).

## What a real version needs

- **New-mint detection** — a listener on the relevant chain's program logs
  (e.g. pump.fun / Raydium on Solana) or a webhook from a service like
  Helius, to catch mints in near real time.
- **On-chain data** — DexScreener, Birdeye, or Helius APIs for liquidity,
  LP-lock/burn status, holder counts, and holder concentration.
- **X / Twitter coverage** — the X API (filtered stream or search) to catch
  contract addresses posted by tracked accounts.
- **Telegram coverage** — a Telegram bot or MTProto client (e.g. Telethon)
  watching specified groups/channels for CA mentions.
- **A backend** — something to poll/stream the above, score each token, and
  push updates to this frontend (WebSocket or server-sent events instead of
  the current `setInterval` mock).

None of that can run as a static site alone — it needs server-side code and
API keys. This repo is just the frontend shell.

## Deploy as-is (static prototype)

### GitHub Pages
1. Push this folder to a GitHub repo.
2. Repo Settings → Pages → Deploy from branch → select `main` and `/root`.
3. Your page will be live at `https://<username>.github.io/<repo>/`.

### Vercel
1. Push this folder to a GitHub repo.
2. On [vercel.com](https://vercel.com), "Add New Project" → import the repo.
3. Leave the framework preset as "Other" — no build step is needed, since
   this is a plain static `index.html`.
4. Deploy.

## Next step

To make this real, the natural path is: pick a chain (Solana is the obvious
one for 0–60s meme launches), pick a data provider (Birdeye or Helius), and
stand up a small backend (Node or Python) that streams detections to this
page over WebSocket. Happy to help build that next.
