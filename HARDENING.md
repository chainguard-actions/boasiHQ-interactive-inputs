<!-- markdownlint-disable -->

# Hardening Report: boasiHQ--interactive-inputs/v2.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **boasiHQ--interactive-inputs/v2.2.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### hardcoded-credentials (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. action.yml contains two hardcoded literal credential values as input defaults. The input 'notifier-slack-token' has a default of "xoxb-secret-token" (an xoxb- prefixed value matching the Slack bot token format), and 'notifier-discord-webhook' has a default of "secret-webhook". These are plain string literals, not GitHub Actions secret expressions, and match the hardcoded-credentials pattern.

Locations:

- `action.yml:52`
- `action.yml:65`

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index 4b7401b..7c7bb0f 100644
--- a/action.yml
+++ b/action.yml
@@ -51,7 +51,6 @@ inputs:
 
   notifier-slack-token:
     description: 'The token used to authenticate with Slack API'
-    default: "xoxb-secret-token"
     required: true
 
   notifier-slack-channel:
@@ -75,7 +74,6 @@ inputs:
   notifier-discord-webhook:
     description: 'The webhook URL used to send the notification(s) to Discord'
     required: true
-    default: "secret-webhook"
 
   notifier-discord-username:
     description: 'The username to send the notification(s) as'
@@ -83,5 +81,6 @@ inputs:
 
 
 runs:
   using: 'node20'
   main: 'invoke-binary.js'

```

Locations:

- `action.yml`

## Iteration Notes

### Iteration 1

**Fixes applied:** hardcoded-credentials, prior-fix

**Notes:**

I removed the placeholder defaults "xoxb-secret-token" (notifier-slack-token) and "secret-webhook" (notifier-discord-webhook) from action.yml. This puts back the previous harden's patch. The rest of action.yml is unchanged, and I didn't touch the Go source or the prebuilt binaries. These defaults were placeholders, not real credentials: the Go code only compares input against them to spot a missing token or webhook when a notifier is turned on. Side effect: if someone turns on the Slack or Discord notifier without setting the token or webhook, the input now arrives empty and passes that check. The action won't stop early with its 'not provided' error, so the failure only shows up when the notification is sent. Runs that leave both notifiers off (the default) behave as before.

