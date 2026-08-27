# DingsGlobal Local Instructions

- This repository owns the DingsGlobal local runtime and its separate PostgreSQL
  container, network, volume, restored snapshot, and immutable baseline.
- Keep `.runtime/` and database archives ignored. Never commit environment files,
  credentials, generated runtime state, or the `.gz` snapshot.
- Prefer `dingsglobal-localhost` over manually starting application processes so
  worktree selection, private env overrides, same-origin proxying, and cleanup
  remain consistent.
- Review web and CMS stacks through the printed `http://localhost:<gateway-port>`
  URL. Do not start or expect Cloudflare Quick Tunnels.
- `dingsglobal-db` manages the DingsGlobal PostgreSQL service. Baseline restore
  may run from any checkout; run worktree database commands from a DingsGlobal
  API checkout so database names are derived from that worktree path.
