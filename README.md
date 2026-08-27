# EduNova — Deployment Guide

This is a real, deployable version of the EduNova prototype: same app, same features,
but now structured as a proper project instead of a single Claude-artifact file.

## What's different from the artifact version

The Claude artifact version could call Anthropic's API for free with no setup, because
Claude's sandbox quietly handled the API key for you. That trick only works inside
claude.ai — it does not work on the open internet.

So this version adds:
- A real project structure (Vite + React)
- A serverless function (`api/claude.js`) that holds your Anthropic API key
  **on the server, never in the browser** — this is required; API keys must never
  be exposed in frontend code, or anyone could steal and use yours.
- The app now calls `/api/claude` (your own backend) instead of Anthropic directly.

## One-time setup

### 1. Get an Anthropic API key
Go to https://console.anthropic.com/settings/keys and create a key.
Note: API usage costs money based on how much you use it (unlike claude.ai chat,
which is a separate product). Check current pricing at https://www.anthropic.com/pricing
before a big demo day so you're not surprised by a bill. For a school demo with a
handful of judges clicking through features, the cost will be a few cents to a
couple of dollars at most — but keep an eye on it.

### 2. Create a free Vercel account
Go to https://vercel.com and sign up (GitHub login is easiest).

### 3. Get this project onto GitHub
The simplest path:
1. Create a new empty repository on GitHub (e.g. "edunova").
2. Upload all the files in this folder to that repository
   (GitHub's web UI lets you drag-and-drop files directly — you don't need
   git installed for this step).

### 4. Import the project into Vercel
1. In Vercel, click **Add New → Project**.
2. Select the GitHub repo you just created.
3. Vercel will auto-detect this as a Vite project — leave the default settings.
4. Before clicking Deploy, open **Environment Variables** and add:
   - Key: `ANTHROPIC_API_KEY`
   - Value: the key you copied in step 1
5. Click **Deploy**.

That's it — Vercel builds and hosts it for you, and gives you a live URL like
`edunova-yourname.vercel.app` that works on any device, no Claude account needed.

## Testing locally first (optional, but recommended)

If you or someone on your team has Node.js installed:

```bash
npm install
```

Create a file called `.env.local` (copy `.env.example` and fill in your real key),
then run:

```bash
vercel dev
```

(Requires the Vercel CLI: `npm install -g vercel`, then `vercel login` once.)
Plain `npm run dev` will start the frontend but the `/api/claude` route won't work
locally without `vercel dev`, since that route needs Vercel's serverless runtime.

## Updating the app later

Any time you want to change something: edit the files, push the change to your
GitHub repo, and Vercel automatically redeploys within about a minute. No manual
redeploy step needed once it's connected.

## Files in this project

- `src/App.jsx` — the entire app (same code as the Claude artifact version, with
  the API calls redirected to `/api/claude`)
- `api/claude.js` — the serverless proxy that holds your API key safely
- `index.html`, `src/main.jsx`, `vite.config.js`, `package.json` — standard Vite
  project scaffolding
- `.env.example` — template for your local API key (never commit your real key)
