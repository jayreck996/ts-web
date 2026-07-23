ISSUE LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ISSUE ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES.

REQUIRED FORMAT FOR EACH ISSUE ENTRY:

## ISSUE:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ISSUE ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES-->
## ISSUE:ts-web 2026-07-23 07:52 → Chained email send (could-update-eml.yml via workflow_call) reports step success but produces zero log output from dawidd6/action-send-mail@v3 and Brevo's transactional stats show no attempt — action silently no-ops instead of sending. MAIL_USERNAME/MAIL_PASSWORD secrets unverified (values not inspectable via API); ACTIONS_STEP_DEBUG not yet enabled to surface internal action logs. Needs: confirm/re-save mail secrets, add ACTIONS_STEP_DEBUG=true, re-run to capture real diagnostic output.
## ISSUE:ts-web 2026-07-22 10:17 → Client email (could-update-eml.yml) was standalone/manual only, not wired to the content pipeline (could-update-md.yml). Risk: the async gap between the Mac Mini listener's 202-accepted response and its actual commit (observed ~4 min) means a naive chain could check for today's entries before they exist and silently skip.
## ISSUE:ts-web 2026-07-05 08:58 → PENDING: (1) re-copy skill to mac-mini ~/.claude/commands/ts-web/ so format + header-steering fixes take effect on next run; (2) before Oct 1, verify the workflow's make_header template carries CUSTOM PROMPT/URLS into next-quarter files — ts-repo's template doesn't, sections may need re-adding quarterly.
