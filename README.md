# Update Floating Version Tags

A GitHub Action that automatically creates and updates major and minor floating tags when a semantic version release is published.

## Why Floating Tags?

When publishing GitHub Actions or other versioned software, it's common practice to maintain "floating" tags that always point to the latest release within a major or minor version. This allows users to:

- Use `v1` to always get the latest `v1.x.x` release (recommended for most users)
- Use `v1.2` to always get the latest `v1.2.x` release (for users who want minor version stability)
- Use `v1.2.3` to pin to an exact version (for maximum stability)

This action automates the management of these floating tags, so you don't have to manually update them with each release.

## Usage

Add this action to a workflow that triggers on release publication:

```yaml
name: Update Floating Tags

on:
  release:
    types: [published]

permissions:
  contents: write

jobs:
  update-tags:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Update floating tags
        uses: SomoneIT/update-floating-tags@v1
```

### Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `version` | Version to create floating tags for | No | `github.ref_name` |

### Outputs

| Output | Description | Example |
|--------|-------------|---------|
| `major-tag` | The major floating tag that was created/updated | `v1` |
| `minor-tag` | The minor floating tag that was created/updated | `v1.2` |

### Example with Custom Version

```yaml
- name: Update floating tags
  uses: SomoneIT/update-floating-tags@v1
  with:
    version: "v2.0.0"
```

### Example Using Outputs

```yaml
- name: Update floating tags
  id: tags
  uses: SomoneIT/update-floating-tags@v1

- name: Print tags
  run: |
    echo "Major tag: ${{ steps.tags.outputs.major-tag }}"
    echo "Minor tag: ${{ steps.tags.outputs.minor-tag }}"
```

## How It Works

1. **Validates** the version string matches semantic versioning format (`v1.2.3` or `1.2.3`)
2. **Parses** the version to extract major and minor components
3. **Creates or updates** the major floating tag (e.g., `v1` for `v1.2.3`)
4. **Creates or updates** the minor floating tag (e.g., `v1.2` for `v1.2.3`)

The action preserves the version prefix style—if your release tag uses `v1.2.3`, the floating tags will be `v1` and `v1.2`. If you use `1.2.3` (without the `v`), the floating tags will be `1` and `1.2`.

## Requirements

- The workflow must have `contents: write` permission to create/update tags
- The checkout step should use `fetch-depth: 0` to ensure all tags are available
- The release tag must follow semantic versioning format

## Non-Semver Versions

If the version doesn't match the expected semantic version format, the action will skip tag creation gracefully without failing the workflow. This allows you to have non-semver tags (like `latest` or `beta`) without causing errors.

## License

[MIT](LICENSE)