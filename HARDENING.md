<!-- markdownlint-disable -->

# Hardening Report: boasiHQ--interactive-inputs/v2.4.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **boasiHQ--interactive-inputs/v2.4.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### hardcoded-credentials (severity: high)

action.yml contains two hardcoded literal credential values as input defaults. The `notifier-slack-token` input has a default of `"xoxb-secret-token"` (a Slack bot token format literal), and the `notifier-discord-webhook` input has a default of `"secret-webhook"`. These are non-expression literal values assigned to names containing 'token' and 'webhook' (a secret), violating the hardcoded-credentials check.

Locations:

- `action.yml:56`
- `action.yml:75`

### script-injection (severity: high)

Both test workflow files interpolate ${{ }} expressions directly inside run: shell command strings (sub-rule a). In `test-interactive-input.yaml` and `test-interactive-input-pr.yaml`, the 'Display Outputs' step uses `${{join(steps.interactive-inputs.outputs.*, '\n')}}` directly in an echo command, and the 'List the uploaded files' step uses `${{ steps.interactive-inputs.outputs.requested-files }}` directly in an `ls -la` command. These step output values are substituted by the YAML template engine before the shell sees them, allowing shell metacharacter injection.

Locations:

- `.github/workflows/test-interactive-input.yaml:52`
- `.github/workflows/test-interactive-input.yaml:59`
- `.github/workflows/test-interactive-input-pr.yaml:52`
- `.github/workflows/test-interactive-input-pr.yaml:59`

### unpinned-uses (severity: high)

Multiple workflow files reference actions by mutable tag or branch refs instead of full 40-character commit SHA pins:
- `.github/workflows/ci.yaml`: `actions/checkout@v4` (tag) and `actions/setup-go@v5` (tag)
- `.github/workflows/test-interactive-input.yaml`: `boasihq/interactive-inputs@main` (branch)
- `.github/workflows/test-interactive-input-pr.yaml`: `boasihq/interactive-inputs@v2` (tag)
These can be silently updated by the upstream repository, enabling supply-chain attacks.

Locations:

- `.github/workflows/ci.yaml:16`
- `.github/workflows/ci.yaml:17`
- `.github/workflows/test-interactive-input.yaml:13`
- `.github/workflows/test-interactive-input-pr.yaml:13`

## Iteration Notes

### Iteration 1

**Fixes applied:** hardcoded-credentials, script-injection, unpinned-uses

**Notes:**

Fixed all three findings: (1) Removed hardcoded credential defaults 'xoxb-secret-token' and 'secret-webhook' from action.yml inputs notifier-slack-token and notifier-discord-webhook. (2) Fixed script injection in both test workflow files by moving ${{ }} expressions into env: blocks (DETECTED_OUTPUTS and REQUESTED_FILES) and referencing them as plain shell variables. (3) Pinned all unpinned action references to full commit SHAs: actions/checkout@v4→34e114876b0b11c390a56381ad16ebd13914f8d5, actions/setup-go@v5→40f1582b2485089dde7abd97c1529aa768e1baff, boasihq/interactive-inputs@main and @v2→a3091457e2a478f90be611c8f7d363f984cdbb3a.

