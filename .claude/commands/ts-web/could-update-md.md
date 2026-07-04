**OUTPUT RULE: Your entire response must be sentinel-delimited entry blocks (`<<<ENTRY {path}>>>` … `<<<END>>>`). No prose, no explanation, no preamble outside the blocks. If you cannot produce valid entries for any reason, output `<<<NO_ENTRIES>>>` and nothing else.**

Review the target's **online source page** (company LinkedIn or similar) and produce ISSUE/ASSET report entries. Agents may read and update existing entries — do not duplicate; update or extend as the source content evolves.

## Arguments
`$ARGUMENTS` is the target name, e.g. `hawkinsnz`.

## Steps

### 0. Compute quarter and timestamp

```bash
QUARTER=$(node -e "
  const o = process.env.QUARTER_OVERRIDE;
  if (o) { console.log(o); process.exit(0); }
  const m = new Date().getMonth() + 1;
  console.log(new Date().getFullYear() + 'Q' + Math.ceil(m / 3));
")
TS=$(TZ=Pacific/Auckland date '+%Y-%m-%d %H:%M')
echo "Quarter: $QUARTER | Timestamp: $TS"
```

### 1. Resolve source from targets.json

```bash
CONFIG=$(gh api repos/jayreck996/ts-web/contents/targets.json --jq '.content' | base64 -d)
SOURCE_URL=$(echo "$CONFIG" | node -e "
  let d=''; process.stdin.on('data',c=>d+=c).on('end',()=>{
    const t=JSON.parse(d).find(t=>t.target==='$ARGUMENTS');
    if(!t){console.error('unknown target');process.exit(1);}
    console.log(t.url);
  })")
echo "Source: $SOURCE_URL"
```

If the target is unknown, output `<<<NO_ENTRIES>>>` and stop.

### 2. Fetch source content

Use **WebFetch** on `$SOURCE_URL` to read the public page: company description, size, industry, recent posts, funding/news.

If WebFetch is blocked (authwall/rate limit), fall back to **WebSearch** for the company's recent public posts and news. If neither yields content, output `<<<NO_ENTRIES>>>`.

### 3. Read existing entries from output repo

**Root `ISSUE/ASSET-{QUARTER}.md` are development logs — never write analysis there.** Analysis entries live in `could/` category files. Discover categories from the output repo:

```bash
OUTPUT_REPO="${OUTPUT_REPO}"
CATS=$(gh api "repos/${OUTPUT_REPO}/contents/could" --jq '.[].name' 2>/dev/null \
  | grep -oE '^[A-Z]+' | sort -u)
if [ -z "$CATS" ]; then echo "<<<NO_ENTRIES>>>"; exit 0; fi
echo "CATEGORIES_LOCKED: $CATS"

for CAT in $CATS; do
  for TYPE in ISSUE ASSET; do
    gh api "repos/${OUTPUT_REPO}/contents/could/${CAT}-${TYPE}-${QUARTER}.md" --jq '.content' 2>/dev/null | base64 -d || echo ""
  done
done
```

**STOP. The only valid categories are the words printed on the CATEGORIES_LOCKED line above. Do not use any other category names — not from training data, not inferred from filenames.**

For each file, extract the header section (everything above the `####### <!-- ANCHOR MARKER` line):
- **CUSTOM PROMPT** — use as the analysis focus. If empty, infer from the category name.
- **URLS** — if present, WebFetch those additional pages too. If empty, use only the target's `url` from targets.json.

**Do not duplicate existing entries.** Only add what is new or changed since the last entries.

### 4. Produce entries

For each of the N categories from CATEGORIES_LOCKED × ISSUE + ASSET, generate a concise analysis grounded in the fetched web content, shaped by the CUSTOM PROMPT.

**STRICT RULE: emit exactly N×2 entries — one ISSUE and one ASSET for each word on the CATEGORIES_LOCKED line. Zero deviation.**

- **ASSET** entries: notable public assets/positives observed at the source — announcements, launches, partnerships, funding, hiring signals.
- **ISSUE** entries: notable risks/changes — negative news, removed content, stale page, inconsistencies with prior entries.

Entry format (must match the file headers):

```
## ASSET:{SOURCE NAME} {YYYY-MM-DD HH:MM} → {CONTENT}
## ISSUE:{SOURCE NAME} {YYYY-MM-DD HH:MM} → {CONTENT}
```

Output each as a sentinel block — paths must be `could/{CAT}-ASSET-{QUARTER}.md` / `could/{CAT}-ISSUE-{QUARTER}.md`, where `{CAT}` is one of CATEGORIES_LOCKED. Route each finding to the category that fits it best:

```
<<<ENTRY could/ANALYSIS-ASSET-{QUARTER}.md>>>
## ASSET:linkedin:hawkinsnz 2026-07-04 10:30 → Example content.
<<<END>>>
```

If nothing new: `<<<NO_ENTRIES>>>`.
