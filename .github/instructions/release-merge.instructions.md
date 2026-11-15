---
description: Read this when merging an upstream release branch (e.g. release/1.x) into this fork.
---

# Merging upstream release branches

Before merging, ensure the working tree is clean: run `git status --short`
and remove any untracked/scratch files (e.g. temp diff dumps, stray `.ts`/`.css`
copies used for 3-way comparisons) with `git clean -fd` (review the dry run
`git clean -ndd` first) so they don't get accidentally staged into the merge
commit.

This fork excludes the `extensions/copilot` directory (the built-in Copilot
CLI/agent extension and its build/CI integration). When merging an upstream
`release/x.y` branch into this fork:

- Do not bring in `extensions/copilot/**`. If the merge adds this directory,
  remove it (`git rm -r --cached extensions/copilot` + delete the working
  copy) rather than keeping it.
- Check for and revert any other files that reference `extensions/copilot`
  as part of the same upstream change, including:
  - Build scripts: `build/filters.ts`, `build/hygiene.ts`, `build/npm/dirs.ts`,
    `build/lib/extensions.ts`, `build/lib/copilot.ts`, `build/gulpfile*.ts`
  - Editor/test config: `.eslint-ignore`, `.vscode-test.js`, `.vscode/launch.json`
  - CI: `.github/workflows/*`, `build/azure-pipelines/**` (including any new
    `product-copilot*.yml`, `build/azure-pipelines/copilot/**`, and
    `downloadCopilotVsix.ts`-style scripts)
  - `package.json` scripts/dependencies that are copilot-extension-specific
    (e.g. `watch-copilot`, `copilot:setup`)
- Do NOT revert changes unrelated to `extensions/copilot`, such as the
  `@github/copilot`/`@github/copilot-sdk` npm package used by
  `src/vs/platform/agentHost/**`, or the `src/vs/sessions/contrib/copilotChatSessions/**`
  feature area — these are separate from the excluded extension.
- After resolving conflicts, run `npm run compile-check-ts-native` to confirm
  no dangling references remain (deleted/renamed exports, missing enum
  members, etc.) from the upstream refactor.
- Also run `npm run compile` once, since the merge can touch `build/gulpfile*.ts`
  and `build/lib/**` directly — `compile-check-ts-native` only type-checks
  `src/`, it won't catch build-script breakage from the exclusion cleanup.
- This fork does not adopt "vibe coding" defaults: the settings
  `chat.defaultToCopilotHarness` and `chat.editor.preferCopilotHarness` (or
  any equivalently-named setting that defaults chat to an external Copilot
  harness instead of the built-in experience) must keep a default value of
  `false` whenever they appear, even if an upstream release branch changes
  their default to `true`. If a merge introduces or flips these defaults,
  revert just the default value (not unrelated code in the same file).
