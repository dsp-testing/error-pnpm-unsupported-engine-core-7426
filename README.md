# Reproduction: pnpm ignores the project's Node.js requirement

Issue: https://github.com/dependabot/dependabot-core/issues/7426

The project requires Node.js 18 through `engines.node` and enables
`engine-strict=true` in `.npmrc`. Dependabot should select that runtime before
updating dependencies, but the affected updater uses its image's Node.js version.

`pnpm-lock.yaml` and the dependency declarations come from Dependabot Core's
[`pnpm/simple` fixture at `2483131868d88c02db546c2a38cc3cd2caf8f912`](https://github.com/dependabot/dependabot-core/tree/2483131868d88c02db546c2a38cc3cd2caf8f912/npm_and_yarn/spec/fixtures/projects/pnpm/simple).
The strict Node 18 requirement matches the original report and Core's
`pnpm/unsupported_engine` regression fixture. pnpm is pinned to 8.3.1, the version
used by the source lockfile fixture.

## Reproduction

1. Keep `package.json`, `pnpm-lock.yaml`, `.npmrc`, and `.github/dependabot.yml` on the default branch.
2. Let Dependabot run its initial version update, or request a new check under Insights > Dependency graph > Dependabot.
3. Inspect the npm/pnpm update job under Actions.

Expected: Dependabot uses Node.js 18 and opens an update PR for `fetch-factory`.

Reported failure: `ERR_PNPM_UNSUPPORTED_ENGINE`, with `Expected version: 18` and
the updater image's incompatible Node.js version. The original report used Node
16.20.0; a current hosted run can have a newer incompatible version instead.
