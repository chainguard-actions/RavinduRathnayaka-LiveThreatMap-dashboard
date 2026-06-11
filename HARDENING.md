<!-- markdownlint-disable -->

# Hardening Report: RavinduRathnayaka--LiveThreatMap-dashboard/LiveThreatMap-2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **RavinduRathnayaka--LiveThreatMap-dashboard/LiveThreatMap-2** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple `${{ }}` expressions are interpolated directly inside `run:` shell command strings (sub-rule a), allowing an attacker to inject arbitrary shell commands via controlled inputs or context values.

1. Line 33: `run: python ${{ github.action_path }}/generate_threat_map.py` — `github.action_path` is interpolated directly into the shell command.
2. Line 42: `git add ${{ inputs.OUTPUT_FILE }}` — `inputs.OUTPUT_FILE` is interpolated directly; an attacker-controlled value could inject shell metacharacters.
3. Line 43: `if [ -f "${{ inputs.MAP_FILE }}" ]; then git add ${{ inputs.MAP_FILE }}; fi` — `inputs.MAP_FILE` is interpolated twice, once unquoted.
4. Line 46: `git commit -m "${{ inputs.COMMIT_MSG }}"` — `inputs.COMMIT_MSG` is interpolated directly into the commit message argument.

Fix: Move all expression values into `env:` variables and reference them as double-quoted shell variables (e.g., `"$OUTPUT_FILE"`) inside the `run:` block.

Locations:

- `action.yml:33`
- `action.yml:42`
- `action.yml:43`
- `action.yml:46`

### unpinned-uses (severity: high)

The composite action uses `actions/setup-python@v5`, which is pinned to a mutable version tag rather than an immutable 40-character commit SHA. This means the action could be silently updated to a different (potentially malicious) version without any change to this file.

Failing reference: `uses: actions/setup-python@v5`

Fix: Pin to a full SHA, e.g. `uses: actions/setup-python@a26af69be951a213d495a4c3e4e4022e16d87065 # v5`

Locations:

- `action.yml:27`

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

Fixed all findings in action.yml: (1) Pinned actions/setup-python@v5 to full SHA a26af69be951a213d495a4c3e4e4022e16d87065. (2) Moved ${{ github.action_path }} into env: ACTION_PATH and referenced as "$ACTION_PATH/generate_threat_map.py" in the run block. (3) Moved ${{ inputs.OUTPUT_FILE }}, ${{ inputs.MAP_FILE }}, and ${{ inputs.COMMIT_MSG }} into an env: block on the 'Commit and push' step, referencing them as double-quoted shell variables "$OUTPUT_FILE", "$MAP_FILE", and "$COMMIT_MSG" throughout the run script.

