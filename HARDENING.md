<!-- markdownlint-disable -->

# Hardening Report: RavinduRathnayaka--LiveThreatMap-dashboard/profile

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **RavinduRathnayaka--LiveThreatMap-dashboard/profile** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The composite action uses `actions/setup-python@v5`, which is pinned to a mutable version tag rather than an immutable 40-character commit SHA. This means the action could be silently updated to a different (potentially malicious) version without any change to this file. It should be pinned to a full SHA, e.g. `actions/setup-python@a26af69be951a213d495a4c3e4e4022e16d87065 # v5`.

Locations:

- `action.yml:32`

### script-injection (severity: high)

Multiple `${{ ... }}` expressions are interpolated directly inside `run:` shell command strings (rule a), allowing script injection. (1) `python ${{ github.action_path }}/generate_threat_map.py` — the `github.action_path` context is substituted directly into the shell command before the shell parses it. (2) `git add ${{ inputs.OUTPUT_FILE }}` — the `inputs.OUTPUT_FILE` value is injected unquoted into the shell, allowing an attacker-controlled path with shell metacharacters to execute arbitrary commands. (3) `if [ -f "${{ inputs.MAP_FILE }}" ]; then git add ${{ inputs.MAP_FILE }}; fi` — `inputs.MAP_FILE` is interpolated twice, once inside quotes and once unquoted. (4) `git commit -m "${{ inputs.COMMIT_MSG }}"` — `inputs.COMMIT_MSG` is injected directly into the commit message argument, allowing shell metacharacter injection. All inputs should be passed via `env:` variables and then referenced as properly double-quoted shell variables (e.g. `"$OUTPUT_FILE"`).

Locations:

- `action.yml:36`
- `action.yml:43`
- `action.yml:44`
- `action.yml:47`

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

1. Pinned actions/setup-python@v5 to full commit SHA a26af69be951a213d495a4c3e4e4022e16d87065. 2. Moved ${{ github.action_path }} into an ACTION_PATH env var in the 'Run threat map generator script' step, and referenced it as "$ACTION_PATH/generate_threat_map.py" in the run command. 3. Moved ${{ inputs.OUTPUT_FILE }}, ${{ inputs.MAP_FILE }}, and ${{ inputs.COMMIT_MSG }} into an env: block in the 'Commit and push the updated map' step, and replaced all inline ${{ }} expressions with properly double-quoted shell variable references ($OUTPUT_FILE, $MAP_FILE, $COMMIT_MSG).

