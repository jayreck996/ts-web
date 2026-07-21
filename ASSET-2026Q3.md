ASSET LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ASSET ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES.

REQUIRED FORMAT FOR EACH ASSET ENTRY:

## ASSET:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ASSET ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES-->
## ASSET:ts-web 2026-07-22 10:17 → Added could-update.yml as orchestrator (md job → eml job via workflow_call + needs). could-update-md.yml and could-update-eml.yml both gained workflow_call triggers (standalone workflow_dispatch kept for manual runs). could-update-eml.yml's setup job now filters by env (prod/test/all) like md.yml instead of hardcoded prod-only, and a new "Wait for entries to land" step polls (git pull + check for today's dated entry, 20s × 30 attempts ≈10 min) before extraction, closing the async race with the Mac Mini listener.
## ASSET:ts-web 2026-07-06 10:23 → must/should workflows fixed to pure runtime discovery: preset category fallback removed (was TC,PRIVACY,PRICE,USAGE,ROADMAP / ARCH,MIGRATE,RECOVERY); empty output-repo folder now skips create + trigger steps instead of scaffolding junk. Weekly Mon crons no-op cleanly until categories are added.
## ASSET:ts-web 2026-07-05 08:58 → Pipeline confirmed live end-to-end: daily cron 06:00 NZ → workflow → mac-mini listener (ts-web.js :3457, routes /ts-web/{flow}-update-md) → skill → md-redwolf could/ANALYSIS entries. First runs 04–05 Jul all green; secrets set (TSREPO_TOKEN, MACMINI_TRIGGER_TOKEN, TOIGROUP_SECRET).

## ASSET:ts-web 2026-07-05 08:58 → Skill aligned to ts-repo conventions: runtime category discovery from could/ (CATEGORIES_LOCKED), CUSTOM PROMPT + URLS header steering, strict N×2 entries, entry format = one-line summary header + structured **Finding** body (no more single-line walls of text).

## ASSET:md-redwolf 2026-07-05 08:58 → could/ANALYSIS-{ASSET,ISSUE}-2026Q3 headers extended with CUSTOM PROMPT + URLS sections (empty, ready to steer analysis focus).
