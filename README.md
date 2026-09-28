# Infinite Leverage (v2)

The Infinite Leverage system in one repo: a small Claude Code plugin, the 4
agent definitions, their workflow skills, and the canonical project scaffold.

**v2 principle: nothing installs globally.** The plugin ships 4 skills and
nothing else — no hooks, no telemetry, no background behavior, no permission
grants. The agents and their workflow skills are installed **into each project**
by `/il-project` or `/il-adopt`. (Edge8-internal telemetry and the v1 cleanup
live in the separate private `edge8-telemetry` plugin.)

## Install

Add this repo as a plugin marketplace in Claude Code and install
`infiniteleverage`:

```bash
claude plugin marketplace add edge8-ai/infinite-leverage
```

```bash
claude plugin install infiniteleverage@infiniteleverage
```

(The GitHub org was renamed from `talentedgeai`; an existing
`talentedgeai/infinite-leverage` marketplace keeps working through GitHub's
redirect.)

Then run `/il-doctor` once. It checks the prerequisites (`git`, an authenticated
`gh`, `perl`, `node`/`npm`/`npx`, `rsync`) and tells you if your installed plugin
is behind the latest release — worth doing before a workshop, since
`/il-project`'s own steps ship inside the plugin.

To update later:

```bash
claude plugin update infiniteleverage@infiniteleverage
```

On an AIO Labs seat the plugin is already installed from the claude.ai
org directory — skip the install and just run `/il-doctor`.

## Using it

| You have | Run | What happens |
|---|---|---|
| Nothing yet | `/il-project` | Scaffolds a new project from `templates/project-scaffold/`: the 4 agents, workflow skills and rules in the project's own `.claude/`, seeded `docs/product/` and `docs/brand/`, a Next.js app under `website/` that builds, and a first commit |
| An existing repo | `/il-adopt` | Installs the same agents, skills and rules into that repo, injects the delegation block into its `CLAUDE.md`, seeds only missing doc anchors. Touches nothing you wrote and commits nothing |
| A project that seems off | `/il-doctor` | Read-only check of prerequisites, repo context, and whether the agents and skills actually landed |

Installing the plugin alone does **not** give you the agents — they arrive when
you run `/il-project` or `/il-adopt` inside the project. Both download the
agents with `gh repo clone`, so run `gh auth login` first if `gh` isn't signed
in. Then **restart Claude Code**: agents added to `.claude/` load only in the
next session. `/il-doctor` confirms all four are there.

After that, work by intent inside the project — "write an epic for onboarding",
"fix this bug", "set up CI" — and the request routes to the right agent
(see `.claude/rules/agent-routing.md`).

## What the plugin contains

| Skill | What it does |
|---|---|
| `/il-project` | New project scaffold (above) |
| `/il-adopt` | The existing-repo counterpart of `/il-project`; also how a project refreshes to the latest agents and skills |
| `/il-doctor` | Setup and version check |
| `/il-memory-cleanup` | Human-in-the-loop cleanup of a multi-account memory mess: reads every memory file, narrates duplicates, conflicts and stale facts, then deletes, merges or re-indexes only what the operator approves — after a backup |

## The 4 agents

**product-manager, developer, qa, devops.** The developer also owns publishing
(the old web-publisher role is now the `web-publisher-publish` skill). The
writer and designer agents were removed in v2.6.0.

Each agent is a thin definition in [`.claude/agents/`](.claude/agents) listing
the workflow skills it uses; the skills live in
[`.claude/skills/`](.claude/skills). Those two directories are the single
source of truth — this README deliberately doesn't enumerate skills, because a
hand-maintained list is how the v1 docs drifted.

## Repo structure

```
.claude-plugin/             ← marketplace manifest (this repo IS the marketplace)
plugin/                     ← the shipped plugin payload
├── .claude-plugin/         ← plugin manifest
└── skills/                 ← il-project, il-adopt, il-doctor, il-memory-cleanup
.claude/
├── agents/                 ← the 4 agent definitions (per-project install source)
├── skills/                 ← agent workflow skills (per-project install source)
└── rules/                  ← engineering guardrails + agent routing
templates/project-scaffold/ ← canonical new-project layout, including the website/ app
docs/                       ← client guides, release checklist, plans, slides
.github/workflows/          ← plugin-ci (every PR) and mirror-release (on tags)
VERSION                     ← kept in lockstep so v1 machines see the update nag
```

## Releasing

The full procedure is in [`CLAUDE.md`](CLAUDE.md#release-flow); in short:

1. Bump the version in `plugin/.claude-plugin/plugin.json`,
   `.claude-plugin/marketplace.json` and `VERSION` together, and update
   `CHANGELOG.md`.
2. Merge to `main`.
3. **Tag the merge commit `vX.Y.Z` and push the tag.** `/il-project` clones the
   tag matching the running plugin's version, and `/il-doctor` compares against
   the newest tag — without it, scaffolds and version checks drift.
4. The tag triggers `mirror-release`, which pushes the plugin to the private
   `edge8-ai/infiniteleverage-8-plugin` mirror that the claude.ai org directory
   syncs from. Check the run is green; if not, mirror by hand as described in
   `CLAUDE.md`.

Before a release a client will run, work through
[`docs/RELEASE-CHECKLIST.md`](docs/RELEASE-CHECKLIST.md).

Existing projects don't update themselves: run `/il-adopt` in the repo to
refresh its agents and skills.

## Migrating from v1

v1 (`/infiniteleverage-init`, `-onboard`, `-patch`, `-validate`, `-project`)
is retired:

| v1 (retired) | v2 |
|---|---|
| `/infiniteleverage-init`, `/infiniteleverage-onboard` | Install the plugin — there is no machine setup anymore |
| `/infiniteleverage-patch` | Marketplace plugin updates; projects refresh via `/il-adopt` |
| `/infiniteleverage-validate` | `/il-doctor` (product checks) + `/edge8-telemetry` (Edge8-internal tracking) |
| `/infiniteleverage-project` | `/il-project` |

Edge8-internal machines are migrated by the private `edge8-telemetry` plugin
(its first run cleans v1's global installs, hash-verified); progress is tracked
in the [fleet migration checklist](https://github.com/edge8-ai/infiniteleverage-plugin/issues/11).
Outside users never had v1 and need nothing.

## CI

`plugin-ci` runs on every PR. Among other things it checks that:

- the plugin manifests are valid and all three versions are in lockstep
- nothing in the plugin payload writes into `~/.claude/`
- the canonical 4-agent team and the AGENT-DELEGATION block are identical
  across `il-project`, `il-adopt` and `il-doctor`
- every bash block in the shipped skills parses, including under macOS bash 3.2
- the scaffold's placeholder substitution, rules, env vars, migrations and
  dependencies stay consistent

The telemetry test suite lives in `edge8-telemetry`.

## More docs

- [`docs/guide/CLIENT-SETUP.md`](docs/guide/CLIENT-SETUP.md) — client setup in five prompts, written for non-coders
- [`docs/guide/troubleshooting.md`](docs/guide/troubleshooting.md) — when something doesn't work
- [`CHANGELOG.md`](CHANGELOG.md) — what changed in each release
