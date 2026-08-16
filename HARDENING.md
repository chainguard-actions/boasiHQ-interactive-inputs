<!-- markdownlint-disable -->

# Hardening Report: boasiHQ--interactive-inputs/v2.3.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **boasiHQ--interactive-inputs/v2.3.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### hardcoded-credentials (severity: high)

action.yml contains two hardcoded literal default values for sensitive inputs: (1) `notifier-slack-token` has a default of `"xoxb-secret-token"` — a literal Slack bot token prefix with a placeholder secret value; (2) `notifier-discord-webhook` has a default of `"secret-webhook"` — a literal placeholder webhook value. These should not be hardcoded as defaults; sensitive inputs should have no default or use a secrets expression.

Locations:

- `action.yml:47`
- `action.yml:66`

### unpinned-uses (severity: high)

Multiple workflow files reference actions and the action itself using mutable tag or branch refs instead of pinned 40-character commit SHAs:
- `actions/checkout@v4` (tag)
- `actions/setup-go@v5` (tag)
- `boasihq/interactive-inputs@main` (branch)
- `boasihq/interactive-inputs@ii-i5-add-file-field-type` (branch)
These are vulnerable to supply-chain attacks if the referenced tag or branch is moved.

Locations:

- `.github/workflows/ci.yaml:16`
- `.github/workflows/ci.yaml:17`
- `.github/workflows/test-interactive-input.yaml:15`
- `.github/workflows/test-interactive-input-pr.yaml:14`

### script-injection (severity: high)

Two `run:` blocks in each of the test workflow files directly interpolate `${{ ... }}` expressions from `steps.*.outputs.*` context into shell commands (rule a — direct expression interpolation). These values flow through YAML template substitution before the shell sees them, allowing an attacker who can influence step outputs to inject arbitrary shell commands:
1. `echo "Detected Outputs: ${{join(steps.interactive-inputs.outputs.*, '\n')}}"` — interpolates all step outputs directly into an echo command.
2. `ls -la ${{ steps.interactive-inputs.outputs.requested-files }}` — interpolates a step output directly as an unquoted argument to `ls`, enabling shell metacharacter injection.

Locations:

- `.github/workflows/test-interactive-input.yaml:52`
- `.github/workflows/test-interactive-input.yaml:58`
- `.github/workflows/test-interactive-input-pr.yaml:52`
- `.github/workflows/test-interactive-input-pr.yaml:58`

## Iteration Notes

### Iteration 1

**Fixes applied:** hardcoded-credentials, unpinned-uses, script-injection

**Notes:**

1. hardcoded-credentials (action.yml lines 47, 66): Removed hardcoded default values 'xoxb-secret-token' from notifier-slack-token and 'secret-webhook' from notifier-discord-webhook. Both inputs now have no default and are marked required: false. 2. unpinned-uses: Pinned actions/checkout@v4 → @34e114876b0b11c390a56381ad16ebd13914f8d5, actions/setup-go@v5 → @40f1582b2485089dde7abd97c1529aa768e1baff in ci.yaml; boasihq/interactive-inputs@main → @a3091457e2a478f90be611c8f7d363f984cdbb3a in test-interactive-input.yaml; boasihq/interactive-inputs@ii-i5-add-file-field-type → @a3091457e2a478f90be611c8f7d363f984cdbb3a (main SHA, as the branch no longer exists) in test-interactive-input-pr.yaml. 3. script-injection: In both test workflow files, moved ${{join(steps.interactive-inputs.outputs.*, '\n')}} into env var DETECTED_OUTPUTS and ${{ steps.interactive-inputs.outputs.requested-files }} into env var REQUESTED_FILES, referencing them as plain shell variables in the run blocks. The ls command now properly quotes "$REQUESTED_FILES".

