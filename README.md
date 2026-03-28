# Sample Registry

A sample agent registry for Agent Forge. Contains example agents you can install and try.

The official registry will be a separate project. This repo exists so you can see how the registry works and test it yourself.

## Install an agent

```bash
forge registry pull linkedin-profile-updater
```

This downloads the agent and installs it to `~/.forge/agents/`.

## Browse agents

```bash
forge registry search ""
```

## How it works

The registry is a GitHub repo with two things:

- `index.json` at the root. Lists every agent with its name, version, description, and download URL.
- `.agnt` files attached to GitHub Releases. Each file is a zip with a manifest and agent source files.

When you run `forge registry pull`, the CLI fetches `index.json`, finds the agent, downloads the `.agnt` file, checks its SHA256 hash, and extracts it locally.

## Set up your own registry

You do not need GitHub. Agent Forge supports three backends:

**Local folder.** A directory on disk.

```yaml
# ~/.forge/registry.yaml
registries:
  - name: mine
    type: local
    path: ~/my-agents
```

**HTTP server.** Any server that can serve and accept files.

```bash
forge registry serve --port 9876 --dir ./my-registry --token my-secret
```

**GitHub repo.** Like this one.

```yaml
registries:
  - name: sample
    type: github
    url: https://raw.githubusercontent.com/MONTBRAIN/sample-registry/master
    github_repo: MONTBRAIN/sample-registry
```

You can have multiple registries at once. `forge registry search` checks all of them.

## Security

Every `.agnt` download is verified with SHA256. The hash is stored in `index.json` at publish time and checked at install time. Tampered files are rejected.

Archives are also checked for path traversal, zip bombs, and symlink attacks before extraction.
