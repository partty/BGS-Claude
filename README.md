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

Plugins are hosted in **private** repositories, so you need read access to them
before installing. The catalog references plugins via **HTTPS** URLs (not SSH) so
the same `marketplace.json` works in Claude Code, the Claude Desktop app, and
Claude.ai / cowork. Authenticate with a token:

- **GitLab token (works everywhere):** create a GitLab personal access token with
  the `read_repository` scope and expose it as `GITLAB_TOKEN` (or `GL_TOKEN`) in
  the environment where Claude runs:
  - **Claude Code (terminal):** export it in your shell, or rely on your existing
    git credential helper for `gitlab.bgservice.net`.
  - **Claude Desktop / Claude.ai / cowork:** set `GITLAB_TOKEN` in that
    environment's settings. These run in a sandbox with **no access to your local
    SSH keys**, so an SSH source URL (`git@…`) cannot authenticate there and the
    marketplace fails to sync — which is why the catalog uses HTTPS + token.
  - If this catalog repo is ever made private, also set `GITHUB_TOKEN` / `GH_TOKEN`.
- **Prefer SSH locally?** SSH still works for Claude Code on your own machine if
  you transparently rewrite the HTTPS URL to SSH (this does **not** affect Desktop
  or Claude.ai, which have no SSH key):
  ```sh
  git config --global url."git@gitlab.bgservice.net:".insteadOf "https://gitlab.bgservice.net/"
  ```
- **Auto-updates:** Claude Code's startup auto-update cannot answer interactive
  prompts, so a `GITLAB_TOKEN` / `GL_TOKEN` (and `GITHUB_TOKEN` / `GH_TOKEN` if
  this repo is private) is the reliable way to keep private plugins updating.

## Installing

```
/plugin marketplace add partty/BGS-Claude
/plugin install nzoom-assistant@bgs
```

This repo is public, so the `owner/repo` shorthand works in Claude Code, Claude
Desktop, and Claude.ai. If it is ever made private, add it by **HTTPS** URL (with
`GITHUB_TOKEN` set) rather than the SSH form, which only authenticates in Claude
Code on your own machine:

```
/plugin marketplace add https://github.com/partty/BGS-Claude.git
```

To pick up plugins added to the catalog later:

```
/plugin marketplace update bgs
```

## Adding a new plugin

1. Put the plugin in its own (private) repo with a `.claude-plugin/plugin.json`.
2. Add one entry to `.claude-plugin/marketplace.json`. Use an **HTTPS** `url`
   (`https://gitlab.bgservice.net/….git`), never an SSH `git@…` URL, so the entry
   works in Claude Desktop and Claude.ai as well as Claude Code:
   - plugin at repo root:
     ```json
     { "name": "<plugin>", "source": { "source": "url", "url": "https://gitlab.bgservice.net/<group>/<repo>.git", "ref": "main" }, "description": "..." }
     ```
   - plugin in a subdirectory:
     ```json
     { "name": "<plugin>", "source": { "source": "git-subdir", "url": "https://gitlab.bgservice.net/<group>/<repo>.git", "path": "<subdir>", "ref": "main" }, "description": "..." }
     ```
3. Commit & push. Tell users to run `/plugin marketplace update bgs`.

## Updating a plugin's content

Plugin code and versioning live in the plugin's own repo, not here. A plugin
publishes an update by bumping `version` in its `plugin.json` and pushing. This
catalog tracks each source's `main` branch, so no change is needed here.
