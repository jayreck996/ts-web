**OUTPUT RULE: Your entire response must be sentinel-delimited entry blocks (`<<<ENTRY {path}>>>` … `<<<END>>>`). No prose, no explanation, no preamble outside the blocks. If you cannot produce valid entries for any reason, output `<<<NO_ENTRIES>>>` and nothing else.**

Review the target's **online source page** (company LinkedIn or similar) and produce ISSUE/ASSET report entries. Agents may read and update existing entries — do not duplicate; update or extend as the source content evolves.

## Arguments
`$ARGUMENTS` is the target name, e.g. `md-redwolf`.

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

```bash
OUTPUT_REPO="${OUTPUT_REPO}"
gh api "repos/${OUTPUT_REPO}/contents/ISSUE-${QUARTER}.md" --jq '.content' | base64 -d
gh api "repos/${OUTPUT_REPO}/contents/ASSET-${QUARTER}.md" --jq '.content' | base64 -d
```

**Do not duplicate existing entries.** Only add what is new or changed since the last entries.

### 4. Produce entries

- **ASSET** entries: notable public assets/positives observed at the source — announcements, launches, partnerships, funding, hiring signals.
- **ISSUE** entries: notable risks/changes — negative news, removed content, stale page, inconsistencies with prior entries.

Entry format (must match the file headers):

```
## ASSET:{SOURCE NAME} {YYYY-MM-DD HH:MM} → {CONTENT}
## ISSUE:{SOURCE NAME} {YYYY-MM-DD HH:MM} → {CONTENT}
```

Output each as a sentinel block:

```
<<<ENTRY ASSET-{QUARTER}.md>>>
## ASSET:linkedin:acme-corp 2026-07-04 10:30 → Example content.
<<<END>>>
```

If nothing new: `<<<NO_ENTRIES>>>`.
