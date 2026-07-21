# ts-web

RSS-source mirror of [ts-repo](https://github.com/jayreck996/ts-repo): reads **RSS feeds** (company news feeds etc.) instead of code repos, and writes ISSUE/ASSET report entries to a single doc repo.

## Folder → Role → Rule

| Path | Role | Rule |
|---|---|---|
| `targets.json` | Source map: RSS feed → output doc repo | Read **from GitHub** by workflows + listener — push to take effect |
| `.github/workflows/*-update-md.yml` | Scaffold quarterly files in output repo, trigger runs | Copied from ts-repo; categories auto-discovered from output repo filenames |
| `ts-web.js` | Mac-mini listener: queues skill runs, writes entries via GitHub API | Same as ts-repo, TARGETS_URL points here |
| `.claude/commands/ts-web/could-update-md.md` | Claude skill: fetch the source RSS feed → produce entry blocks | Output is sentinel-delimited entries only |
| `would/` | Listener + trigger logs, all flows (`{FLOW}-LISTENER-LOG.log`, `{FLOW}-UPDATE-MD-TRIGGER-LOG.log`) | Logs only, no docs |

## Pipeline

```
schedule/trigger → listener → skill (fetch RSS source url) → analyze → append ISSUE/ASSET entries → md-redwolf
```

## Output repo

`jayreck996/md-redwolf` — same structure as the ts document repos: root `ISSUE/ASSET-{QUARTER}.md` + `could/ must/ should/ would/`.
