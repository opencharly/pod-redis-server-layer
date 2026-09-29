# pod-redis-server-layer

The `redis-server-layer` candy of the OpenCharly candy library, as a standalone
repo (the candy de-submodule cutover, kind-prefixed naming). It runs
`redis-server` bound to `0.0.0.0` for cross-pod access.

## What it provides

Runs `/usr/bin/redis-server` (provided by `valkey-compat-redis` on Fedora 43)
bound to `0.0.0.0:6379` with `protected-mode` off and persistence disabled
(`--save ''`), so a sibling `redis-client` pod on the shared `charly` network can
`SET`/`GET` via `redis-cli -h charly-redis`. The service keeps the pod at steady
state and the published port reachable from the host.

| Property | Value |
|---|---|
| Port | `6379` |
| Service | `redis-server` (`--bind 0.0.0.0 --port 6379 --protected-mode no --dir /var/lib/harness-redis --save '' --daemonize no`, `restart: always`, priority 20) |
| Package | `valkey-compat-redis` (fedora; requested as `redis`) |
| Data dir | `/var/lib/harness-redis` |
| Tools | `ss` (iproute), `pgrep` (procps-ng) |

## How to use it

Compose it as the server side of a cross-pod redis test, paired with
`pod-redis-client-layer`:

```yaml
my-redis-server:
  candy:
    base: fedora
    candy:
      - '@github.com/opencharly/pod-redis-server-layer:<tag>'
```

```bash
charly box build my-redis-server
charly start my-redis-server
charly shell my-redis-client -c "redis-cli -h charly-redis GET bench:hello"
```

## Layout

- `charly.yml` — the `redis-server-layer:` candy entity (description, `distro`,
  `port`, `service`, `plan`). No `skill:` entity.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- `/charly-infrastructure:redis` — the family redis candy and its owning skill.
- `/charly-image:layer` — the candy authoring reference.
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
