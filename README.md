# agametool landing page

Static landing page for [agametool.com](https://agametool.com/), served by a **Cloudflare Worker with static assets**. There is no Worker script and no build step: Cloudflare serves the files in `public/` exactly as they are.

## Repository layout

- `public/`: everything that gets published (`index.html`, `styles.css`, icons, `robots.txt`, `sitemap.xml`)
- `wrangler.jsonc`: Worker configuration (`agametool`, assets served from `./public`)

Only the contents of `public/` are uploaded. Files at the repository root, including the config, are never served.

## Local preview

**Prerequisites:** Node.js. Wrangler runs through `npx`, so you don't need to install anything first.

```
npx wrangler dev
```

Open `http://localhost:8787`. Because nothing needs building, you can also open `public/index.html` straight in a browser for quick checks. Absolute paths such as `/styles.css` only resolve correctly through `wrangler dev`, though.

## Deployment & Release Process

The site is deployed with **Wrangler** (Cloudflare's CLI). The `agametool` Worker is linked to this GitHub repository through **Cloudflare's Git integration** (Workers Builds). There is no GitHub Actions workflow: Cloudflare watches the repository and runs the deploy itself.

### Production URL

- `https://agametool.com/`

### How Releases Work

1. **Merge into `main`**: open a pull request and merge it, or push to `main` directly. Cloudflare detects the push and starts a build.
2. Cloudflare runs the deploy command set in the Worker's build settings (`npx wrangler deploy`), which uploads `public/` as the Worker's assets.
3. The new version is live on the production URL within seconds. No manual approval is needed.

You can see each build's status in the GitHub commit/PR checks and in the Cloudflare dashboard under *Workers & Pages → agametool → Deployments*.

### Branch Deployments

- **Production**: every push to `main` is deployed to the production URL.
- **Preview**: pushes to any other branch get their own preview URL (shown in the PR checks), so you can review a change before merging it.

### Manual Deploy (Fallback)

To deploy without going through Git:

```
npx wrangler login   # first time only
npx wrangler deploy
```

### Rollback

If a deployment causes a problem, roll back to an earlier version in the Cloudflare dashboard under *Workers & Pages → agametool → Deployments*. You can also revert the commit on `main`, which triggers a new deployment of the known-good state.
