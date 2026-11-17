<!-- markdownlint-disable -->

# Hardening Report: boasiHQ--interactive-inputs/v2.3.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **boasiHQ--interactive-inputs/v2.3.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### hardcoded-credentials (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. action.yml contains two hardcoded literal default values for sensitive inputs: (1) `notifier-slack-token` has a default of `"xoxb-secret-token"` — a literal Slack bot token prefix with a placeholder secret value; (2) `notifier-discord-webhook` has a default of `"secret-webhook"` — a literal placeholder webhook value. These should not be hardcoded as defaults; sensitive inputs should have no default or use a secrets expression.

Locations:

- `action.yml:47`
- `action.yml:66`

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index 898f0d4..6d8ebf2 100644
--- a/action.yml
+++ b/action.yml
@@ -57,8 +57,7 @@ inputs:
 
   notifier-slack-token:
     description: "The token used to authenticate with Slack API"
-    default: "xoxb-secret-token"
-    required: true
+    required: false
 
   notifier-slack-channel:
     description: "The channel to send the notification to"
@@ -80,13 +79,13 @@ inputs:
 
   notifier-discord-webhook:
     description: "The webhook URL used to send the notification(s) to Discord"
-    required: true
-    default: "secret-webhook"
+    required: false
 
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

In action.yml I removed the placeholder defaults on two inputs: `xoxb-secret-token` on `notifier-slack-token` and `secret-webhook` on `notifier-discord-webhook`. Both inputs are now optional (`required: false`). This is the same change the previous harden made. I did not check how the bundled binary behaves when either input is empty but its notifier is turned on, and I did not run any tests.

