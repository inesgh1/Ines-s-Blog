# ines-bot — AI backend (Cloudflare Worker)

This tiny Cloudflare Worker powers the blog chatbot using **Cloudflare Workers AI**.
The AI model runs on Cloudflare's free daily allowance, and your setup never exposes
a secret key to the browser — the page just calls this Worker's URL.

## What you need
- A free [Cloudflare account](https://dash.cloudflare.com/sign-up)
- [Node.js](https://nodejs.org) installed (for the `wrangler` CLI)

## Deploy in ~5 steps

```bash
# 1. from this folder
cd worker

# 2. install the Cloudflare CLI (one time)
npm install -g wrangler

# 3. log in (opens your browser)
wrangler login

# 4. deploy
wrangler deploy
```

After `wrangler deploy` finishes, it prints a URL like:

```
https://ines-bot.<your-subdomain>.workers.dev
```

## Connect it to the blog
1. Copy that Worker URL.
2. Open `_layouts/default.html`, find the line:
   ```js
   var AI_ENDPOINT = ""; // <-- paste your Cloudflare Worker URL here
   ```
3. Paste your URL between the quotes and save:
   ```js
   var AI_ENDPOINT = "https://ines-bot.your-subdomain.workers.dev";
   ```
4. Commit + push. Done — the bot now uses real AI.

## Good to know
- **Cost:** $0 at normal blog traffic. Workers AI has a free daily neuron allowance; the
  Worker itself is on the free 100k-requests/day plan.
- **Before you connect it** (or if the endpoint is ever down), the bot automatically falls
  back to its built-in witty rule-based replies — so it never looks broken.
- **Change the model:** edit `MODEL` in `src/index.js` (e.g. a larger Llama variant).
- **Change the personality:** edit `SYSTEM_PROMPT` in `src/index.js`.
- **Rate limiting:** for a personal blog the free tier is plenty. If you ever get heavy
  traffic, add Cloudflare's rate-limiting rules in the dashboard.
