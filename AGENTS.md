# AGENTS.md — pod-redis-server-layer

Standalone candy repo for the `redis-server-layer` candy — a `redis-server` bound
to `0.0.0.0:6379` with `protected-mode` off, for cross-pod access. The entire
candy lives in `charly.yml` at the repo root. There is no source tree.

Canonical files:

- `charly.yml` — the `redis-server-layer:` candy entity (description, `distro`,
  `port`, `service`, `plan`).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-infrastructure:redis` — the family redis candy: the distro package
  divergence (`valkey-compat-redis` on Fedora) and the server binary. Load before
  editing, building, or troubleshooting this candy.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs incl. `check:`, service declarations).
- `/charly-check:check` — the check/R10 framework (`charly check box`,
  `charly check run <bed>`).
- `/charly-internals:git-workflow` — before any git/PR action.

The candy carries no `skill:` entity of its own; the family skill
`/charly-infrastructure:redis` covers the surface. The gap is routed to the named
skill-authoring batch
[opencharly/opencharly#291](https://github.com/opencharly/opencharly/issues/291);
when that lands, add the owning skill here.

## Build / validate / test

- `charly box validate` at the repo root — the structural check: the manifest
  must parse and validate at the installed charly.
- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no**
  per-repo candy gate. Its only workflow file is
  `.github/workflows/tag-on-merge.yml`.
- The candy's own `check:` steps assert the `redis-server` / `redis-cli`
  binaries, the `valkey-compat-redis` package, a live `PONG`, the reachable port,
  the running `redis-server` service, and the listening `6379` port.

## Modify this repo

- Edit the `redis-server-layer:` candy entity in `charly.yml`. The `0.0.0.0`
  bind, `protected-mode no`, and `--save ''` are the product.
- A `package:` check must query the real installed name on Fedora 43
  (`valkey-compat-redis`), not the `redis` request name.
- Keep the `--dir /var/lib/harness-redis` path in step with the `mkdir:` step.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge on
  PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the PR
  body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
