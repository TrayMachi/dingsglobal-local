# DingsGlobal Local Development

This repository is the DingsGlobal local runtime coordinator. It reuses the
JavaMifi local PostgreSQL Compose project and its immutable
`javamifi_worktree_baseline`; it does not copy or own the database volume.

Keep these repositories as siblings under the workspace:

```text
dingsglobal/
|-- dingsglobal-local/
|-- dingsglobal-api/
|-- dingsglobal-web/
`-- dingsglobal-cms/
```

The canonical `dingsglobal-web` and `dingsglobal-api` checkouts are maintained
on the `staging` branch. Use them as application baselines for local worktrees,
create web and API worktrees from `origin/staging`, and target `staging` for
their pull requests unless a task explicitly says otherwise.

The runtime also expects the JavaMifi workspace at the sibling path used by the
wrapper, or at `DINGSGLOBAL_JAVAMIFI_LOCAL` when the workspaces are elsewhere.

## Install

```bash
./install
```

This installs `dingsglobal-db` and `dingsglobal-localhost` in `~/.local/bin`.
The scripts can also be run directly from this repository.

## Baseline

The approved snapshot is intentionally not committed. Restore it once into the
shared JavaMifi PostgreSQL service:

```bash
dingsglobal-db baseline restore \
  ../../javamifi/javamifi-local/new_nocobase_v2-20082026101314.gz \
  javamifi_staging_20260820
```

The command installs `javamifi_worktree_baseline`. Existing application
worktrees never connect to that baseline directly; `dingsglobal-db setup`
clones a disposable database for the current API worktree.

## Start the applications

From this repository:

```bash
dingsglobal-localhost up --all --name main
```

From an application checkout, use its local skill or run the equivalent:

```bash
dingsglobal-localhost up --web ../dingsglobal-web --api ../dingsglobal-api --name main
dingsglobal-localhost up --cms ../dingsglobal-cms --api ../dingsglobal-api --name cms
```

The CLI automatically selects a matching sibling worktree when an explicit
application path is omitted. It starts the shared API, optional web/CMS
containers, same-origin Nginx gateways, and temporary Cloudflare Quick Tunnels.
The printed web/CMS URL is the review URL; `/api/*` is routed to the selected
API. URLs are public, temporary, and development-only.

## Everyday commands

```bash
```

`down` preserves runtime metadata and databases. `cleanup --path` stops and
removes only stacks using the given worktree. It does not stop the shared
PostgreSQL container or remove its volume.

For an API worktree:

```bash
cd ../dingsglobal-api-worktrees/feature-example
dingsglobal-db setup
```

Use `dingsglobal-db drop` only after the PR is merged and the local runtime has
been cleaned. Never run `docker compose down` against the shared JavaMifi local
database project.

## Development flow

- `ship` creates a sibling worktree under `<repo>-worktrees`, validates it,
  pushes the branch, and opens a PR. Multi-repository requests use the
  workspace `dingsglobal-multi-ship` skill and one shared branch name.
- After a single-repository ship, keep its integrated localhost stack running
  for review. Multi-repository ship starts one combined stack after all PRs
  open.
- `LGTM` preflights the PR, merges it, deletes the remote branch, cleans the
  matching runtime and API database, then removes only the clean linked
  worktree. Multi-repository LGTM is all-ready before any merge.
