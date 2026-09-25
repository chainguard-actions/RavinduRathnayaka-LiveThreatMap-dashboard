<!-- markdownlint-disable -->

# Hardening Report: RavinduRathnayaka--LiveThreatMap-dashboard/LiveThreatMap-2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **RavinduRathnayaka--LiveThreatMap-dashboard/LiveThreatMap-2** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The composite action uses `actions/setup-python@v5`, which is pinned to a mutable version tag rather than an immutable 40-character commit SHA. A tag can be moved to point to a different (potentially malicious) commit, enabling a supply-chain attack. It should be pinned to a full SHA, e.g. `actions/setup-python@a26af69be951a213d495a4c3e4e4022e16d87065 # v5`.

Locations:

- `action.yml:28`

### script-injection (severity: high)

Multiple `${{ ... }}` expressions are interpolated directly inside `run:` shell command strings (rule a), allowing an attacker who controls the inputs or github context to inject arbitrary shell commands:
- Line 37: `run: python ${{ github.action_path }}/generate_threat_map.py` — `github.action_path` is interpolated directly into the shell command.
- Line 44: `git add ${{ inputs.OUTPUT_FILE }}` — user-controlled input interpolated directly into shell.
- Line 45: `if [ -f "${{ inputs.MAP_FILE }}" ]; then git add ${{ inputs.MAP_FILE }}; fi` — user-controlled input interpolated twice into shell.
- Line 48: `git commit -m "${{ inputs.COMMIT_MSG }}"` — user-controlled input interpolated directly into shell, enabling arbitrary git flag injection or shell metacharacter abuse.
All of these should be moved to `env:` variables and then referenced as quoted shell variables (e.g. `"$OUTPUT_FILE"`) inside the `run:` block.

Locations:

- `action.yml:37`
- `action.yml:44`
- `action.yml:45`
- `action.yml:48`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.OUTPUT_FILE }}" appears directly in run: block of step "Commit and push the updated map"; move to env: map

Locations:

- `action.yml:52`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.MAP_FILE }}" appears directly in run: block of step "Commit and push the updated map"; move to env: map

Locations:

- `action.yml:53`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.MAP_FILE }}" appears directly in run: block of step "Commit and push the updated map"; move to env: map

Locations:

- `action.yml:53`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.COMMIT_MSG }}" appears directly in run: block of step "Commit and push the updated map"; move to env: map

Locations:

- `action.yml:57`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, static-inline-injection

**Notes:**

Fixed all findings in hardened/action/action.yml:
1. Pinned actions/setup-python@v5 to full SHA a26af69be951a213d495a4c3e4e4022e16d87065 (kept # v5 comment).
2. Moved ${{ github.action_path }} to env var ACTION_PATH and referenced as "$ACTION_PATH/generate_threat_map.py" in the run block.
3. Moved ${{ inputs.OUTPUT_FILE }}, ${{ inputs.MAP_FILE }}, and ${{ inputs.COMMIT_MSG }} to env vars (OUTPUT_FILE, MAP_FILE, COMMIT_MSG) in the 'Commit and push' step and referenced them as quoted shell variables throughout the run block.

