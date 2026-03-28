# Agent Forge Sample Registry

This is a sample registry for Agent Forge. It contains example agents you can install and try out.

The official Agent Forge registry will be a separate project. This repo exists so you can see how the registry system works and test it yourself.

## Install an agent

```bash
forge registry pull linkedin-profile-updater
```

This downloads the agent and installs it to `~/.forge/agents/`. You can then run it from the dashboard or CLI.

## Browse available agents

```bash
forge registry search ""
```

## How it works

The registry is a GitHub repo with two things:

- `index.json` at the root. Lists every agent with its name, version, description, and download URL.
- `.agnt` files attached to GitHub Releases. Each file is a zip archive with a manifest and the agent's source files.

When you run `forge registry pull`, the CLI fetches `index.json`, finds the agent, downloads the `.agnt` file, verifies its SHA256 hash, and extracts it locally.

When you run `forge registry push`, the CLI uploads the `.agnt` to a GitHub Release and updates `index.json`.

## Set up your own registry

You do not need GitHub. Agent Forge supports three registry backends:

**Local folder.** Just a directory on disk. Good for personal use or synced folders.

```yaml
# ~/.forge/registry.yaml
registries:
  - name: mine
    type: local
    path: ~/my-agents
```

**HTTP server.** Any server that can serve files and accept uploads.

```bash
forge registry serve --port 9876 --dir ./my-registry --token my-secret
```

Then point your config at it:

```yaml
registries:
  - name: company
    type: http
    url: http://registry.internal:9876
    token: my-secret
```

**GitHub repo.** Like this one. Uses Releases for storage and raw content for the index.

```yaml
registries:
  - name: official
    type: github
    url: https://raw.githubusercontent.com/MONTBRAIN/forge-registry-sample/main
    github_repo: MONTBRAIN/forge-registry-sample
```

You can have multiple registries at once. `forge registry search` checks all of them.

## Security

Every `.agnt` download is verified with SHA256. The hash is stored in `index.json` when the agent is published and checked when you pull it. If someone tampers with the file, the install fails.

Archives are also checked for zip slip attacks, zip bombs, and symlink exploits before extraction.
