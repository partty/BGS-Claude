# BGS-Claude

Claude Code **plugin marketplace** (`bgs`) for BG Service's internal plugins.
This repository is a thin **catalog** — it holds only the marketplace
definition. Plugin code lives in each plugin's own (private) repository; see the
`source` of each entry in `.claude-plugin/marketplace.json`.

## Plugins

| Plugin | Lives in | Description |
|--------|----------|-------------|
| `nzoom-assistant` | `nzoom-skills` (GitLab), subdir `nzoom-assistant/` | nZoom skills: automations (АД), analyses (справки), documentation navigation, configuration & SQL conventions |

## Prerequisites

Plugins are hosted in **private** repositories. Before installing you need clone
access to them (and to this repo, if it is private). Claude Code shells out to
your local `git`, so it reuses your existing SSH keys / credential helpers.

- **SSH (recommended):** an SSH key registered on `gitlab.bgservice.net` and
  loaded in `ssh-agent`. The catalog references plugins via SSH URLs.
- **HTTPS instead of SSH:** rewrite the SSH URL transparently, then authenticate
  via your git credential helper or a personal access token:
  ```sh
  git config --global url."https://gitlab.bgservice.net/".insteadOf "git@gitlab.bgservice.net:"
  ```
- **Auto-updates:** Claude Code's startup auto-update cannot answer interactive
  prompts. For private plugins to auto-update, either load an ssh-agent key that
  needs no passphrase, or set `GITLAB_TOKEN` / `GL_TOKEN` (and `GITHUB_TOKEN` /
  `GH_TOKEN` if this repo is private).

## Installing

```
/plugin marketplace add partty/BGS-Claude
/plugin install nzoom-assistant@bgs
```

If this repo is private and the `owner/repo` shorthand does not authenticate,
add it by URL instead:

```
/plugin marketplace add git@github.com:partty/BGS-Claude.git
```

To pick up plugins added to the catalog later:

```
/plugin marketplace update bgs
```

## Adding a new plugin

1. Put the plugin in its own (private) repo with a `.claude-plugin/plugin.json`.
2. Add one entry to `.claude-plugin/marketplace.json`:
   - plugin at repo root:
     ```json
     { "name": "<plugin>", "source": { "source": "url", "url": "<git-url>", "ref": "main" }, "description": "..." }
     ```
   - plugin in a subdirectory:
     ```json
     { "name": "<plugin>", "source": { "source": "git-subdir", "url": "<git-url>", "path": "<subdir>", "ref": "main" }, "description": "..." }
     ```
3. Commit & push. Tell users to run `/plugin marketplace update bgs`.

## Updating a plugin's content

Plugin code and versioning live in the plugin's own repo, not here. A plugin
publishes an update by bumping `version` in its `plugin.json` and pushing. This
catalog tracks each source's `main` branch, so no change is needed here.
