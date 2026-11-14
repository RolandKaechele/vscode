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

Windows signing tools (`signtool.exe`) live under the Windows SDK bin folder,
which isn't on `PATH` by default. In every PowerShell terminal, run this
before any build step that needs it:

```powershell
$env:Path = 'C:\Program Files (x86)\Windows Kits\10\bin\10.0.26100.0\x64;' + $env:Path
Get-Command signtool.exe
```

## Packaging a Windows build

To produce a win32 x64 desktop package, run these in order (requires `signtool.exe`
on `PATH` and the matching `yarn` CLI installed — see above):

```powershell
npm run gulp vscode-win32-x64
yarn gulp vscode-win32-x64-inno-updater
yarn gulp vscode-win32-x64-system-setup
```
