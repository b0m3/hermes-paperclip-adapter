# Hermes Paperclip Adapter (Self-Hosted Edition)

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Node.js >= 20](https://img.shields.io/badge/node-%3E%3D20-339933?logo=node.js&logoColor=white)](https://nodejs.org/)

This repository is a specialized fork of the upstream [Hermes Paperclip Adapter](https://github.com/NousResearch/hermes-paperclip-adapter), optimized for self-hosted environments and local development workflows.

It simplifies integration by enabling plug-and-play behavior with native Hermes configuration defaults.

## Key Enhancements in This Fork

Compared with upstream, this fork prioritizes zero-config operation and clone-first deployment:

- Native default routing:
  - If `model` is omitted in `adapterConfig`, Hermes falls back to the default model in `~/.hermes/config.yaml`.
  - If `provider` is omitted, the adapter does not force `--provider`; Hermes resolves it natively.
- Smart environment fallback:
  - Loads `~/.hermes/.env` automatically when variables are not explicitly provided by Paperclip.
- Credential normalization:
  - Supports mixed naming conventions (for example, maps `ANTHROPIC_AUTH_TOKEN` to `ANTHROPIC_API_KEY` when needed).
- Clone-first workflow:
  - Optimized for `git clone` deployment instead of npm registry installation, suitable for local customization.

## Installation and Setup

### 1) Clone and Build

```bash
git clone https://github.com/b0m3/hermes-paperclip-adapter.git
cd hermes-paperclip-adapter

npm install
npm run build
npm run typecheck
```

### 2) Connect to Paperclip Server

In your Paperclip project, point dependency to the local adapter path:

```json
{
  "dependencies": {
    "hermes-paperclip-adapter": "file:../hermes-paperclip-adapter"
  }
}
```

Adjust `../` to match your directory structure, then run `npm install` in the Paperclip project.

### 3) Register the Adapter

Register in Paperclip server (typically `server/src/adapters/registry.ts`):

```ts
import * as hermesLocal from "hermes-paperclip-adapter";
import {
  execute,
  testEnvironment,
  detectModel,
  listSkills,
  syncSkills,
  sessionCodec,
} from "hermes-paperclip-adapter/server";

registry.set("hermes_local", {
  ...hermesLocal,
  execute,
  testEnvironment,
  detectModel,
  listSkills,
  syncSkills,
  sessionCodec,
});
```

## Configuration

### Minimal Agent Configuration

With native routing enabled, agent configuration can stay minimal while inheriting local Hermes defaults.

```json
{
  "name": "Hermes Engineer",
  "adapterType": "hermes_local",
  "adapterConfig": {
    "maxIterations": 50,
    "timeoutSec": 300,
    "persistSession": true,
    "enabledToolsets": ["terminal", "file", "web"]
  }
}
```

## Security and Best Practices

1. Environment secrets:
   - Never commit `.env` files.
   - Keep local secrets in `~/.hermes/.env`.
2. Process management:
   - Always restart Paperclip/Hermes workers after adapter updates to avoid stale module cache.
3. Least privilege:
   - Use API keys scoped to the minimum required permissions.

## Prerequisites

- Hermes CLI installed and available as `hermes`
- Python 3.10+
- Node.js 20+
- Valid `~/.hermes/config.yaml` for zero-config routing

## License and Attribution

This project is licensed under the MIT License and inherits licensing obligations from the upstream project.

This fork is maintained independently for the community and is not an official upstream distribution.
