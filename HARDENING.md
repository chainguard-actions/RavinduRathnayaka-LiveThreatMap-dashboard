<!-- markdownlint-disable -->

# Hardening Report: RavinduRathnayaka--LiveThreatMap-dashboard/profile

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **RavinduRathnayaka--LiveThreatMap-dashboard/profile** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action uses `actions/setup-python@v5`, which is pinned to a mutable version tag rather than an immutable 40-character commit SHA. A tag can be moved to point to a different (potentially malicious) commit, enabling supply-chain attacks.

Locations:

- `action.yml:33`

### script-injection (severity: high)

Multiple `run:` blocks directly interpolate GitHub Actions expressions (`${{ ... }}`) into shell command strings (sub-rule a), allowing an attacker-controlled value to inject arbitrary shell commands before the shell ever parses the string.

1. Line 43: `run: python ${{ github.action_path }}/generate_threat_map.py` — `github.action_path` is interpolated directly into the shell command.
2. Line 51: `git add ${{ inputs.OUTPUT_FILE }}` — the user-controlled `inputs.OUTPUT_FILE` is interpolated unquoted into the shell.
3. Line 52: `if [ -f "${{ inputs.MAP_FILE }}" ]; then git add ${{ inputs.MAP_FILE }}; fi` — `inputs.MAP_FILE` is interpolated twice, once inside quotes and once unquoted.
4. Line 55: `git commit -m "${{ inputs.COMMIT_MSG }}"` — `inputs.COMMIT_MSG` is interpolated directly into the commit message argument, enabling shell metacharacter injection.

Fix: Move all expression values into `env:` variables and reference them as properly double-quoted shell variables (e.g., `"$OUTPUT_FILE"`).

Locations:

- `action.yml:43`
- `action.yml:51`
- `action.yml:52`
- `action.yml:55`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.OUTPUT_FILE }}" appears directly in run: block of step "Commit and push the updated map"; move to env: map

Locations:

- `action.yml:55`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.MAP_FILE }}" appears directly in run: block of step "Commit and push the updated map"; move to env: map

Locations:

- `action.yml:56`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.MAP_FILE }}" appears directly in run: block of step "Commit and push the updated map"; move to env: map

Locations:

- `action.yml:56`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.COMMIT_MSG }}" appears directly in run: block of step "Commit and push the updated map"; move to env: map

Locations:

- `action.yml:60`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, static-inline-injection

**Notes:**

Fixed all findings in action.yml: (1) Pinned actions/setup-python@v5 to full SHA a26af69be951a213d495a4c3e4e4022e16d87065 with # v5 comment. (2) Moved ${{ github.action_path }} into env var ACTION_PATH in the 'Run threat map generator script' step, referencing it as "$ACTION_PATH/generate_threat_map.py". (3) Moved ${{ inputs.OUTPUT_FILE }}, ${{ inputs.MAP_FILE }}, and ${{ inputs.COMMIT_MSG }} into env: variables in the 'Commit and push the updated map' step, referencing them as properly double-quoted shell variables "$OUTPUT_FILE", "$MAP_FILE", and "$COMMIT_MSG".

