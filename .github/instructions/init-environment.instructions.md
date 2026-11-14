---
description: Read this before running npm/node/gulp commands in a terminal for this repo.
---

# Node environment setup

This repo pins an exact Node.js version in `.nvmrc`, enforced by
`build/npm/preinstall.ts`. Before running `npm install`, `npm run <script>`,
or any `gulp`/`node` build command in a new terminal session, switch to the
pinned version with [fnm](https://github.com/Schniz/fnm):

```powershell
fnm env --use-on-cd --shell powershell | Out-String | Invoke-Expression
fnm use
```

If `fnm use` reports the pinned version isn't installed, install it first:

```powershell
fnm install
fnm use
```

Verify with `node --version` before proceeding — it must match `.nvmrc`
exactly on the major version (and be `>=` on minor/patch). Skipping this step
causes `build/npm/preinstall.ts` to abort `npm install` with a Node version
mismatch error.

Yarn is pinned as a devDependency; install the matching global CLI with
`npm run install-yarn` (runs `npm install --global yarn@1`) before using
`yarn`-based scripts.
