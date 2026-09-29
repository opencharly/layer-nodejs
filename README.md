# layer-nodejs

A Node.js runtime with the `npm` and `pnpm` package managers on the default
`PATH`, as a standalone OpenCharly layer repo.

The candy installs the distro `nodejs` package (NodeSource node 22.x on Ubuntu
and Debian so the in-box CLIs that require node >=22 work — the deepseek-harness
`dsh` floor), the matching `npm` CLI, and a self-contained `pnpm` standalone
binary at `/usr/local/bin/pnpm`. Every artifact is a real file at a known path
with a working `--version`, so the runtime is directly verifiable.

## What it provides

| Property | Value |
|---|---|
| Layer / candy | `nodejs` |
| Binaries | `/usr/bin/node`, `/usr/bin/npm`, `/usr/local/bin/pnpm` |
| Pinned pnpm | `PNPM_VERSION` `10.33.4` |
| Env | `NPM_CONFIG_PREFIX=~/.npm-global`; `~/.npm-global/bin` on `PATH` |
| Service / port | none |

On Ubuntu/Debian the `nodejs` package comes from NodeSource (node 22.x) and
bundles `npm`; the separate apt `npm` is deliberately dropped because installing
it alongside pulls conflicting `node-*` deps. Fedora/Arch keep their
`dnf`/`pacman` `npm` (their base node is already >=22).

## How to use it

Compose the layer as a nested `candy:` list inside a named box body:

```yaml
my-node-box:
  candy:
    base: fedora
    candy:
      - '@github.com/opencharly/layer-nodejs:v2026.239.1624'
```

`pnpm` is installed as a standalone binary — deliberately **not** a
`package.json` dependency — because a `package.json` would trigger the npm
multi-stage builder on this candy, and the builder images that compose nodejs
cannot self-provide the npm builder.

## Layout

- `charly.yml` — the `nodejs:` candy entity (the env vars, the NodeSource repo
  arms, the pnpm download step, the `check:` assertions, and the embedded
  `nodejs-skill:` skill entity).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-coder:nodejs` — the Node.js runtime, the npm global
  prefix, and the standalone pnpm binary.
- `/charly-coder:pre-commit` — depends on nodejs.
- `/charly-image:layer` — candy authoring reference.
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
