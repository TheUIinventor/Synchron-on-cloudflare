Overview

This project now targets Cloudflare Workers using the Next-on-Pages toolchain. To make CI builds deterministic you must add the build tools to the lockfile and commit it. Follow the steps below.

Local steps (required)

1. Install dev dependencies and update your lockfile:

```powershell
pnpm install
```

This will add `@cloudflare/next-on-pages` and `wrangler` to `pnpm-lock.yaml`.

2. Commit the updated lockfile and package.json:

```powershell
git add package.json pnpm-lock.yaml
git commit -m "chore: add Cloudflare next-on-pages and wrangler for CF deployment"
git push
```

How CI (Cloudflare) should run

Cloudflare build settings must run the `build` script (which performs `next build`), then `postbuild` will generate the `.next-on-pages` output. Finally Wrangler deploys the worker.

Example build commands (Cloudflare Build Step):

```powershell
pnpm install --frozen-lockfile
pnpm run build
npx wrangler deploy
```

Notes and troubleshooting

- If your CI environment restricts `npx` network installs, adding the dev deps and committing `pnpm-lock.yaml` avoids network installs because the packages will be fetched from the lockfile.
- If `wrangler` errors about the worker entry point, inspect `.next-on-pages` after `pnpm run build` to find the worker file (for example `.next-on-pages/worker.mjs` or `.next-on-pages/worker/index.mjs`) and update `wrangler.toml` `main` to that exact path.
- The current setup preserves SSR/API routes via Next-on-Pages. If you prefer the newer OpenNext adapter we can migrate; that requires different packages and minor script changes.

If you want, I can:
- Run a local build here to inspect `.next-on-pages` and update `wrangler.toml` automatically (I cannot run `pnpm install` in this environment, so you'll need to run `pnpm install` locally and push the lockfile), or
- Prepare a PR that adds dev deps and includes an updated `pnpm-lock.yaml` (you must allow me to run `pnpm install` locally — I can instead provide the exact commands to run).
