# workflows

Reusable GitHub Actions workflows for agentic-workbench plugins.

## plugin-release.yml

Versions and publishes a Claude Code plugin. A pull request from `develop` into `main` gets its version bump
and CHANGELOG section pushed to `develop` (job `version`); merging it tags the merge commit and creates the
GitHub release (job `publish`).

Caller, e.g. `.github/workflows/release.yml`:

```yaml
name: Release
on:
  pull_request:
    branches: [main]
  push:
    branches: [main]
permissions:
  contents: write
concurrency:
  group: release-${{ github.event_name }}-${{ github.ref }}
jobs:
  release:
    uses: agentic-workbench/workflows/.github/workflows/plugin-release.yml@<full commit SHA>
    secrets: inherit
```

Requirements:

- `scripts/release.mjs` with the `prepare` and `notes <version>` commands, and `.claude-plugin/plugin.json`, in the caller repository.
- Secret `RELEASE_DEPLOY_KEY`: a deploy key with write access, used to push the version commit to `develop`.
- `permissions: contents: write` in the caller.
- The check names become `release / version` and `release / publish`.
