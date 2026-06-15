<!-- markdownlint-disable -->

# Hardening Report: boasiHQ--interactive-inputs/v2.4.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **boasiHQ--interactive-inputs/v2.4.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### hardcoded-credentials (severity: high)

action.yml contains two input defaults with literal hardcoded credential-like values:
1. `notifier-slack-token` (line 57) has `default: "xoxb-secret-token"` — the `xoxb-` prefix is the real Slack bot token format, and this literal value matches the hardcoded-credentials pattern for `token`.
2. `notifier-discord-webhook` (line 74) has `default: "secret-webhook"` — a literal value matching the `secret` keyword pattern.
Neither value is a GitHub Actions expression (they do not start with `${{`). These defaults should be removed or replaced with empty strings, requiring callers to always supply real secrets via `${{ secrets.* }}`.

Locations:

- `action.yml:57`
- `action.yml:74`

## Iteration Notes

### Iteration 1

**Fixes applied:** hardcoded-credentials

**Notes:**

Replaced two hardcoded credential-like default values in action.yml with empty strings:
1. `notifier-slack-token` (line 57): changed `default: "xoxb-secret-token"` to `default: ""`
2. `notifier-discord-webhook` (line 74): changed `default: "secret-webhook"` to `default: ""`
Callers must now supply real secrets via `${{ secrets.* }}` expressions rather than relying on insecure placeholder defaults.

