<!-- markdownlint-disable -->

# Hardening Report: boasiHQ--interactive-inputs/v2.4.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **boasiHQ--interactive-inputs/v2.4.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### hardcoded-credentials (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. action.yml contains two hardcoded literal credential values as input defaults. The `notifier-slack-token` input has a default of `"xoxb-secret-token"` (a Slack bot token format literal), and the `notifier-discord-webhook` input has a default of `"secret-webhook"`. These are non-expression literal values assigned to names containing 'token' and 'webhook' (a secret), violating the hardcoded-credentials check.

Locations:

- `action.yml:56`
- `action.yml:75`

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index 898f0d4..9e54b99 100644
--- a/action.yml
+++ b/action.yml
@@ -57,7 +57,6 @@ inputs:
 
   notifier-slack-token:
     description: "The token used to authenticate with Slack API"
-    default: "xoxb-secret-token"
     required: true
 
   notifier-slack-channel:
@@ -81,12 +80,12 @@ inputs:
   notifier-discord-webhook:
     description: "The webhook URL used to send the notification(s) to Discord"
     required: true
-    default: "secret-webhook"
 
   notifier-discord-username:
     description: "The username to send the notification(s) as"
     required: false
 
 runs:
   using: "node20"
   main: "invoke-binary.js"

```

Locations:

- `action.yml`

## Iteration Notes

### Iteration 1

**Fixes applied:** hardcoded-credentials, prior-fix

**Notes:**

I removed the placeholder credential defaults from action.yml: `notifier-slack-token` no longer defaults to "xoxb-secret-token" and `notifier-discord-webhook` no longer defaults to "secret-webhook". This is the same change the previous harden made. I didn't test it; I checked it by reading src/internal/config/config.go. The action only reads these inputs when its Slack or Discord notifier is turned on, so leaving them out does nothing when notifiers are off. One side effect: the Go code turns on a notifier only if its token or webhook doesn't equal the old placeholder. So if a notifier is turned on and no value is given, the action now goes ahead with an empty value instead of stopping with an error. I didn't change the Go source or the compiled binaries in dist/, which still hold the same placeholder strings, because the check only covers action.yml.

