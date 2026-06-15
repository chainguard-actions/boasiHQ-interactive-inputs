<!-- markdownlint-disable -->

# Hardening Report: boasiHQ--interactive-inputs/v2.3.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **boasiHQ--interactive-inputs/v2.3.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### hardcoded-credentials (severity: high)

Two hardcoded literal credential values are set as input defaults in action.yml:
1. `notifier-slack-token` has `default: "xoxb-secret-token"` — a literal value using the real Slack bot token prefix format (xoxb-). This should be replaced with a secrets expression like `${{ secrets.SLACK_TOKEN }}`.
2. `notifier-discord-webhook` has `default: "secret-webhook"` — a literal webhook value. This should be replaced with a secrets expression like `${{ secrets.DISCORD_WEBHOOK }}`.
Hardcoded credential defaults can be accidentally used by consumers of the action without realising they are using placeholder/example values, and expose the credential format in plaintext.

Locations:

- `action.yml:57`
- `action.yml:79`

## Iteration Notes

### Iteration 1

**Fixes applied:** hardcoded-credentials

**Notes:**

Removed two hardcoded credential defaults from action.yml:
1. `notifier-slack-token`: removed `default: "xoxb-secret-token"` (a real Slack bot token format prefix)
2. `notifier-discord-webhook`: removed `default: "secret-webhook"` (a literal webhook placeholder)

Both inputs retain `required: true`, so consumers must explicitly provide these credential values. Secrets expressions are not valid in action.yml input defaults, so removing the defaults is the correct approach.

