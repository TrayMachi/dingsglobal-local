# DingsGlobal Local Instructions

- This repository wraps the shared JavaMifi local runtime; do not duplicate or
  stop its PostgreSQL container, network, volume, restored snapshot, or
  immutable baseline.
- Keep `.runtime/` and database archives ignored. Never commit environment files,
  credentials, generated runtime state, or the `.gz` snapshot.
- Prefer `dingsglobal-localhost` over manually starting application processes so
  worktree selection, private env overrides, same-origin proxying, and cleanup
  remain consistent.
- The wrapper delegates database operations to the shared JavaMifi `javamifi-db`
  CLI. Run it from a DingsGlobal API checkout so database names are derived from
  that worktree path.
