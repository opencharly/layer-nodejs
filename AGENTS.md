# AGENTS.md — layer-nodejs

Standalone candy repo for the `nodejs` layer — the Node.js runtime with the `npm`
and `pnpm` package managers. The candy lives in `charly.yml` at the repo root:
the env vars, the per-distro NodeSource repo arms, the pnpm download step, the
`check:` assertions, and the embedded `skill:` entity projected into the
marketplace corpus as `/charly-coder:nodejs`.

Canonical files:

- `charly.yml` — the `nodejs:` candy entity and the `nodejs-skill:` skill entity.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-coder:nodejs` — the owning skill. The Node.js runtime, the npm global
  prefix, the NodeSource upgrade, and the standalone pnpm binary. Load before
  editing or troubleshooting the layer.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs incl. `command:`/`check:`, per-distro `distro:` arms,
  package/repo sections, and service declarations). Load before editing any
  entity field or plan step.

## Build / validate / test

- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no
  per-repo candy gate** and ships only `.github/workflows/tag-on-merge.yml`.
- The candy's `plan:` `check:` steps are the functional evidence; they must stay
  valid on every distro arm they run on. Scope a distro-specific check in the
  command itself — the check runner does not honour runner-level
  `exclude-distro` fields.
- Two check-step gotchas are load-bearing and documented inline: a `check:`
  command goes through charly variable substitution, so `${...}` (e.g.
  `${HOME:-default}`) is read as an unresolvable variable and the step is
  SKIPPED — use `$( )` and `env HOME=…` instead. And `pnpm` hard-requires a
  homedir, so its version check derives `HOME` from passwd.

## Modify this repo

- Edit the `nodejs:` candy entity AND the `nodejs-skill:` skill entity in
  `charly.yml` together. The skill is the projected usage source, so a version or
  behaviour change not mirrored in the skill leaves the corpus stale.
- `pnpm` is installed as a standalone binary to `/usr/local/bin/pnpm`, NOT as a
  `package.json` dependency — adding a `package.json` would trigger the npm
  multi-stage builder on this candy and break its builder-image consumers.
- The pinned `PNPM_VERSION` and the `check:` assertions must stay in sync.
- New behaviour claims belong in the `plan:` as an observable `check:` step, and
  in the skill body.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge
  on PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the
  PR body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
