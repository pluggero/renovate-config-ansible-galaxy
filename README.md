# Renovate Config - Ansible Galaxy Role Management

Centralized Renovate preset for controlling Ansible Galaxy role automerge across multiple repositories.

## Usage

Add to `.github/renovate.json`:

```json
{
  "extends": ["github>rplugge/renovate-config-ansible-galaxy"]
}
```

**Important**: Do **not** set `"automerge": false` at the root level in your consuming repository's `renovate.json`, as this will override the preset's automerge settings for approved Galaxy roles. Instead, control automerge behavior through individual `packageRules`.

## How It Works

- Versions matching `matchNewValue` regex → **auto-merge**
- Versions not matching pattern → **PR created, manual review required**
- Unknown roles → **PR created, no automerge**

## Approving Versions

Edit `default.json`:

```json
"matchNewValue": "/^(1\\.2\\.0|1\\.3\\.0)$/"  // Add new version to regex alternation
```

Push to central repo → all repos pick up changes on next Renovate run.

## Preventing Version Skipping

**Problem**: If version 1.1.0 needs manual changes, don't auto-merge 1.2.0 until all repos are on 1.1.0.

**Solution - Phased Rollout**:

1. Version 1.1.0 released (needs manual intervention)
   - Manually fix issues in this version across all repos
   - Merge 1.1.0 manually

2. After merging this version across all repos, approve this version:
   - Set: `"matchNewValue": "/^(1\\.0\\.0|1\\.1\\.0)$/"`

3. You can now proceed with the next version (e.g. 1.2.0)

**Key**: Only add new version to `matchNewValue` regex after all repos have the prerequisite version.

## Version Syntax

`matchNewValue` uses regex patterns.

```json
"matchNewValue": "/^1\\.2\\.0$/"              // Single version
"matchNewValue": "/^(1\\.2\\.0|1\\.3\\.0)$/"  // Multiple versions (recommended)
"matchNewValue": "/^1\\.(2|3)\\.0$/"          // Pattern matching (less explicit)
```

**Important**:
- Regex pattern must be enclosed in forward slashes: `/pattern/`
- Use `^` and `$` anchors to match exact versions
- Escape dots with `\\.` (backslash-dot)
- Use `|` (pipe) for alternation between versions
