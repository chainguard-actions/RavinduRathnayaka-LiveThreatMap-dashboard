<!-- markdownlint-disable -->

# Hardening Report: RavinduRathnayaka--LiveThreatMap-dashboard/LiveThreatMap-2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **RavinduRathnayaka--LiveThreatMap-dashboard/LiveThreatMap-2** was hardened automatically. 8 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml composite step pins its action to a mutable tag instead of a full 40-character commit SHA: `uses: actions/setup-python@v5`.

Locations:

- `action.yml:30`

### script-injection (severity: high)

Rule (a): an expression is interpolated straight into a run: script: `run: python ${{ github.action_path }}/generate_threat_map.py`. Pass it through env (or use $GITHUB_ACTION_PATH) and double-quote it.

Locations:

- `action.yml:42`

### script-injection (severity: high)

Rule (a): workflow-controlled inputs are interpolated straight into the 'Commit and push the updated map' run: script: `git add ${{ inputs.OUTPUT_FILE }}`, `if [ -f "${{ inputs.MAP_FILE }}" ]; then git add ${{ inputs.MAP_FILE }}; fi`, and `git commit -m "${{ inputs.COMMIT_MSG }}"`. A value containing shell metacharacters, $(...) or quotes would run arbitrary commands. Move these into env: variables and expand them double-quoted.

Locations:

- `action.yml:53`
- `action.yml:54`
- `action.yml:58`

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

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index b91c169..0f5c7a3 100644
--- a/action.yml
+++ b/action.yml
@@ -1,7 +1,6 @@
 name: 'Live Threat Map Generator'
 description: 'Generates and commits a dynamic SVG threat map using real-time data from Shodan.'
 author: 'RavinduRathnayaka'
-
 inputs:
   SHODAN_API_KEY:
     description: 'Your Shodan API key. This is required.'
@@ -22,42 +21,59 @@ inputs:
     description: 'The path to your custom background map.svg file.'
     required: false
     default: 'map.svg'
-
 runs:
   using: "composite"
   steps:
     - name: Set up Python
       uses: actions/setup-python@v5
       with:
         python-version: '3.10'
-
     - name: Install Python dependencies
       shell: bash
       run: |
         pip install requests shodan
-
     - name: Run threat map generator script
       shell: bash
       # Use github.action_path to find the script within the action's repository
-      run: python ${{ github.action_path }}/generate_threat_map.py
       env:
+        ACTION_PATH: ${{ github.action_path }}
         SHODAN_API_KEY: ${{ inputs.SHODAN_API_KEY }}
         OUTPUT_SVG_FILENAME: ${{ inputs.OUTPUT_FILE }}
-
+      run: python "$ACTION_PATH/generate_threat_map.py"
     - name: Commit and push the updated map
       shell: bash
+      env:
+        OUTPUT_FILE: ${{ inputs.OUTPUT_FILE }}
+        MAP_FILE: ${{ inputs.MAP_FILE }}
+        COMMIT_MSG: ${{ inputs.COMMIT_MSG }}
       run: |
         git config --global user.name 'github-actions[bot]'
         git config --global user.email 'github-actions[bot]@users.noreply.github.com'
-        git add ${{ inputs.OUTPUT_FILE }}
-        if [ -f "${{ inputs.MAP_FILE }}" ]; then git add ${{ inputs.MAP_FILE }}; fi
-        
+        git add "$OUTPUT_FILE"
+        if [ -f "$MAP_FILE" ]; then git add "$MAP_FILE"; fi
+
         # Commit only if there are changes
         if ! git diff --staged --quiet; then
-          git commit -m "${{ inputs.COMMIT_MSG }}"
+          git commit -m "$COMMIT_MSG"
           git pull --rebase
           git push
         else
           echo "No changes to the map, skipping commit."
         fi
-

```

Locations:

- `action.yml`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, static-inline-injection, prior-fix

**Notes:**

Pinned actions/setup-python to a26af69be951a213d495a4c3e4e4022e16d87065 (v5). Replaced the inline github.action_path with "$GITHUB_ACTION_PATH" (quoted). Moved inputs.OUTPUT_FILE, inputs.MAP_FILE and inputs.COMMIT_MSG into the step's env: block and expanded them double-quoted in the commit step. This puts back the previous harden's security changes.

