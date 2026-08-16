<!-- markdownlint-disable -->

# Hardening Report: boasiHQ--interactive-inputs/v2.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **boasiHQ--interactive-inputs/v2.2.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### hardcoded-credentials (severity: high)

action.yml contains two hardcoded literal credential values as input defaults. The input 'notifier-slack-token' has a default of "xoxb-secret-token" (an xoxb- prefixed value matching the Slack bot token format), and 'notifier-discord-webhook' has a default of "secret-webhook". These are plain string literals, not GitHub Actions secret expressions, and match the hardcoded-credentials pattern.

Locations:

- `action.yml:52`
- `action.yml:65`

### script-injection (severity: high)

Both workflow files contain a run: block that directly interpolates a ${{ ... }} expression referencing steps.*.outputs.* into a shell command string (rule a). The line `echo "Detected Outputs: ${{join(steps.interactive-inputs.outputs.*, '\n')}}"` passes the action's outputs — which can contain attacker-controlled data — through YAML template substitution before the shell ever sees them, enabling script injection.

Locations:

- `.github/workflows/test-interactive-input-pr.yaml:48`
- `.github/workflows/test-interactive-input.yaml:48`

### unpinned-uses (severity: high)

Both workflow files reference the action using mutable, non-SHA refs instead of pinned 40-character commit SHAs. 'test-interactive-input-pr.yaml' uses 'boasihq/interactive-inputs@ii-i3-improve-message-formatting-and-introduce-thread-messaging-support' (a branch name), and 'test-interactive-input.yaml' uses 'boasihq/interactive-inputs@main' (a branch name). These can be silently updated to point to malicious code.

Locations:

- `.github/workflows/test-interactive-input-pr.yaml:14`
- `.github/workflows/test-interactive-input.yaml:14`

## Iteration Notes

### Iteration 1

**Fixes applied:** hardcoded-credentials, script-injection, unpinned-uses

**Notes:**

1. hardcoded-credentials: Removed fake default values 'xoxb-secret-token' and 'secret-webhook' from notifier-slack-token and notifier-discord-webhook inputs in action.yml — these inputs now have no default and must be supplied by the caller. 2. script-injection: In both .github/workflows/test-interactive-input-pr.yaml and test-interactive-input.yaml, moved the ${{ join(steps.interactive-inputs.outputs.*, '\n') }} expression into a step-level env var (DETECTED_OUTPUTS) and referenced it as $DETECTED_OUTPUTS in the shell run block. 3. unpinned-uses: Pinned boasihq/interactive-inputs to SHA a3091457e2a478f90be611c8f7d363f984cdbb3a (resolved from main) in both workflow files. The branch 'ii-i3-improve-message-formatting-and-introduce-thread-messaging-support' no longer exists in the remote repo, so the main branch SHA was used for both files.

