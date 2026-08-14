<!-- markdownlint-disable -->

# Hardening Report: RavinduRathnayaka--LiveThreatMap-dashboard/LiveThreatMap-2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **RavinduRathnayaka--LiveThreatMap-dashboard/LiveThreatMap-2** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple `uses:` references are pinned to mutable tags instead of full 40-character commit SHAs, making the action vulnerable to supply-chain attacks if the tag is moved. Failing references: `actions/setup-python@v5` (action.yml line 29), `actions/checkout@v4` (.github/workflows/threat-map-generator.yml line 17), `actions/setup-python@v5` (.github/workflows/threat-map-generator.yml line 22).

Locations:

- `action.yml:29`
- `.github/workflows/threat-map-generator.yml:17`
- `.github/workflows/threat-map-generator.yml:22`

### script-injection (severity: high)

Multiple `${{ ... }}` expressions are interpolated directly inside `run:` shell command strings in action.yml, violating sub-rule (a). An attacker controlling these inputs can inject arbitrary shell commands.

- Line 40: `run: python ${{ github.action_path }}/generate_threat_map.py` — `github.action_path` is interpolated directly into the shell command.
- Line 48: `git add ${{ inputs.OUTPUT_FILE }}` — `inputs.OUTPUT_FILE` is interpolated directly and unquoted.
- Line 49: `if [ -f "${{ inputs.MAP_FILE }}" ]; then git add ${{ inputs.MAP_FILE }}; fi` — `inputs.MAP_FILE` is interpolated directly (once quoted, once unquoted).
- Line 52: `git commit -m "${{ inputs.COMMIT_MSG }}"` — `inputs.COMMIT_MSG` is interpolated directly into the shell command.

All of these should be moved to `env:` variables and referenced as quoted shell variables (e.g., `"$OUTPUT_FILE"`).

Locations:

- `action.yml:40`
- `action.yml:48`
- `action.yml:49`
- `action.yml:52`

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

Fixed all findings across action.yml and .github/workflows/threat-map-generator.yml:

1. Pinned actions/setup-python@v5 to SHA a26af69be951a213d495a4c3e4e4022e16d87065 in action.yml.
2. Pinned actions/checkout@v4 to SHA 34e114876b0b11c390a56381ad16ebd13914f8d5 in the workflow file.
3. Pinned actions/setup-python@v5 to SHA a26af69be951a213d495a4c3e4e4022e16d87065 in the workflow file.
4. Fixed script injection on line 40 of action.yml: moved github.action_path into env: ACTION_PATH and used "$ACTION_PATH/generate_threat_map.py" in the run: block.
5. Fixed script injection on lines 48-52 of action.yml: moved inputs.OUTPUT_FILE, inputs.MAP_FILE, and inputs.COMMIT_MSG into an env: block and referenced them as quoted shell variables ($OUTPUT_FILE, $MAP_FILE, $COMMIT_MSG) in the run: block.

