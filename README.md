# DevRev SDK MCP Server — Tool Reference & Test Guide

> **Author:** Saurabh Verma
>
> **Server:** `http://198.135.54.2:3000/sse`
>
> **SDK Package:** `@devrev/ts-adaas`

This document covers all 7 tools exposed by the DevRev SDK MCP server.
For each tool you will find: what it does, when to use it, a sample prompt, and a real response.

Demo Video : https://jumpshare.com/s/4jhCegT8ed4vl3MxwhnA
---

## Table of Contents

1. [Setup & Configuration](#setup--configuration)
2. [list_devrev_sdk_versions](#1-list_devrev_sdk_versions)
3. [get_devrev_sdk_context](#2-get_devrev_sdk_context)
4. [search_devrev_sdk](#3-search_devrev_sdk)
5. [get_devrev_examples](#4-get_devrev_examples)
6. [detect_devrev_sdk_version](#5-detect_devrev_sdk_version)
7. [get_devrev_migration_guide](#6-get_devrev_migration_guide)
8. [get_devrev_sdk_version_diff](#7-get_devrev_sdk_version_diff)
9. [Quick Smoke Test](#quick-smoke-test)

---

## Setup & Configuration

### GitHub Token — Why You Need It

The server uses the GitHub API to fetch SDK source code and release data.
Without a token, GitHub allows only **60 requests/hour** (shared across your IP).
With a personal token, the limit increases to **5,000 requests/hour**.

You have two options:
- **No token** — use the server's built-in token. Fine for occasional use.
- **Your own token** — pass it in the URL. Recommended for heavy or team use.

To generate a token: **GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic)** → **do not select any scopes** (no scopes required for public repo access) → generate and copy the token.

---

### Cursor

#### Step 1 — Open MCP settings

Go to **Cursor Settings → MCP** or edit the file directly:
```
~/.cursor/mcp.json
```

#### Step 2 — Add the server

**Without a personal GitHub token** (uses server default):
```json
{
  "mcpServers": {
    "devrev-sdk": {
      "url": "http://198.135.54.2:3000/sse"
    }
  }
}
```

**With your own GitHub token** (recommended — higher rate limits):
```json
{
  "mcpServers": {
    "devrev-sdk": {
      "url": "http://198.135.54.2:3000/sse?github_token=ghp_yourtoken"
    }
  }
}
```

#### Step 3 — Enable Agent Mode

1. Open a chat in Cursor
2. Switch to **Agent** mode (dropdown next to the chat input)
3. The MCP tools will appear automatically — you will see `devrev-sdk` listed under connected tools
4. Ask any question about the DevRev SDK and the agent will call the right tool automatically

---

### VS Code

#### Step 1 — Install the MCP extension

Install **MCP Client** from the VS Code marketplace (search `MCP`).

#### Step 2 — Add to VS Code settings

Open **Settings (JSON)** (`Cmd+Shift+P` → `Open User Settings (JSON)`) and add:

**Without a personal GitHub token:**
```json
{
  "mcp.servers": {
    "devrev-sdk": {
      "url": "http://198.135.54.2:3000/sse"
    }
  }
}
```

**With your own GitHub token:**
```json
{
  "mcp.servers": {
    "devrev-sdk": {
      "url": "http://198.135.54.2:3000/sse?github_token=ghp_yourtoken"
    }
  }
}
```

#### Step 3 — Enable Agent Mode (Copilot Chat)

If you are using **GitHub Copilot Chat** with agent mode:

1. Open Copilot Chat panel
2. Click the **Agent** icon or type `@agent`
3. The MCP tools are available automatically once the server is configured

---

### Health Check

Verify the server is reachable before configuring your client:
```bash
curl http://198.135.54.2:3000/health
# Expected: {"status":"ok","transport":"http"}
```

---

## 1. `list_devrev_sdk_versions`

### What it does
Fetches all published versions of `@devrev/ts-adaas` from the npm registry. Shows version numbers, release dates, and which one is the latest stable release. Useful as a first step before using other tools to know what versions are available.

### Features
- Lists all versions (stable + pre-release)
- Highlights the latest stable version
- Shows release dates
- Use `includePrerelease: true` to include alpha/beta versions

### When to use
- Before upgrading the SDK — check what versions exist
- To find the latest stable version
- To pick a `fromVersion` / `toVersion` for migration or diff tools

### Sample Prompt
```
List all available versions of the DevRev SDK. Show me which one is the latest stable release.
```

### Sample Response
> The latest stable version is **1.15.2** (released February 13, 2026). There are 47 total versions available:
> - **1.15.2** (Latest) — 2026-02-13
> - **1.15.1** — 2026-02-12
> - **1.15.0** — 2026-02-02
> - **1.14.1** — 2026-01-28
> - **1.14.0** — 2026-01-19
> - **1.13.3** — 2026-01-12
> - *(41 more versions...)*

---

## 2. `get_devrev_sdk_context`

### What it does
The primary tool. Returns a complete picture of what the SDK offers for a given version: exported functions with full signatures, interfaces, types, enums, classes, and code examples. Can be filtered by topic to return only relevant parts.

### Features
- Full list of exported methods with parameter types and return types
- All exported interfaces, types, enums, and classes with their properties
- Working code examples
- Filter by `topic` (e.g. `"WorkerAdapter"`, `"webhook"`, `"Mappers"`)
- Auto-detect version from a project path, or specify a version explicitly

### When to use
- Understanding what the SDK provides before writing code
- Looking up method signatures or interface definitions
- Exploring a specific area of the SDK (e.g. state management, attachment handling)

### Sample Prompts
```
Show me all the methods and types available in the latest version of the @devrev/ts-adaas SDK.
```

```
What does the DevRev SDK provide for working with webhooks? Use the latest version.
```

### Sample Response (abbreviated)

**Key Methods:**
- `createWorkerAdapter` — Creates a worker adapter for handling events
- `createAdapterState` — Creates adapter state with initial configuration
- `spawn` — Spawns a new worker thread and manages its lifecycle
- `processTask` — Processes tasks with timeout handling
- `translateIncomingEventType` — Maps old event type strings to new `EventType` enum values
- `getFilesToLoad` — Gets the files to load for the loader
- `compressGzip` / `decompressGzip` / `parseJsonl` — Compression & parsing utilities
- `serializeError` / `formatAxiosError` — Error utilities
- `createEvent`, `createItem`, `createAttachment` — Testing helpers

**Key Classes:** `WorkerAdapter`, `State`, `Mappers`, `Repo`, `Uploader`, `HTTPClient`, `MockServer`

**Key Types:** `AirdropEvent`, `EventType`, `ExtractorEventType`, `LoaderEventType`, `AdapterState<T>`, `SyncMapperRecord`, `NormalizedItem`, `LoaderReport`

---

## 3. `search_devrev_sdk`

### What it does
Full-text search across all SDK methods, types, and examples for a given version. Returns matching items with relevance scores and context snippets. Useful when you know roughly what you're looking for but don't want to browse the entire SDK.

### Features
- Search by method name, type name, or concept
- Filter by version and category (`methods`, `types`, `examples`)
- Returns relevance-ranked results with snippets
- Default limit: 10 results (max: 50)

### When to use
- Quickly find a specific method or type by name
- Explore what the SDK offers around a concept (e.g. `"upload"`, `"retry"`, `"state"`)
- Discover related APIs before writing a feature

### Sample Prompt
```
Search the DevRev SDK for anything related to "WorkerAdapter".
```

### Sample Response (abbreviated)

> **WorkerAdapter** is the main class for interacting with the Airdrop platform in worker threads. Results include:
>
> - `WorkerAdapter` class — emit control events, manage state, handle extraction and loading, upload artifacts, stream attachments
> - `createWorkerAdapter({ event, adapterState, options })` — factory function
> - `WorkerAdapterInterface` — interface for the factory params
> - `WorkerAdapterOptions` — `isLocalDevelopment`, `timeout`, `batchSize`, `workerPathOverrides`
> - `AttachmentsStreamingPoolParams` — takes a `WorkerAdapter` instance
> - `TaskAdapterInterface` — wraps a `WorkerAdapter`

---

## 4. `get_devrev_examples`

### What it does
Returns copy-paste ready code examples organized by category. Each example is a working snippet you can drop into a snap-in project.

### Features
- 8 categories of examples
- Version-specific examples
- Examples include full imports and context

### Available Categories

| Category | Description |
|----------|-------------|
| `webhook` | Event handler setup |
| `automation` | Scheduled task handlers |
| `command` | Slash command handlers |
| `keyring` | Secret/credential access |
| `snapkit` | UI component usage |
| `event-source` | Custom event configuration |
| `api-call` | DevRev API usage patterns |
| `manifest` | Snap-in manifest configuration |

### When to use
- Starting a new snap-in feature from scratch
- Need a working template to build on
- Onboarding new team members to snap-in development

### Sample Prompt
```
Give me a code example for setting up a manifest using the DevRev SDK.
```

### Sample Response (abbreviated)

```yaml
version: "2"

name: "My Snap-in"
description: "A snap-in for syncing external system data with DevRev"

service_account:
  display_name: "My Snap-in Service Account"

keyrings:
  - name: external_system_credentials
    types:
      - snap_in_secret

functions:
  - name: external_sync_units_extractor
  - name: data_extractor
  - name: webhook_handler

automations:
  - name: daily_sync
    schedule:
      - cron: "0 0 * * *"

webhooks:
  - name: external_webhook
    function: webhook_handler
```

---

## 5. `detect_devrev_sdk_version`

### What it does
Reads the `package.json` of a local project, resolves version ranges (`^`, `~`, `>=`), and returns the exact SDK version the project is using. Supports monorepo workspaces (npm, yarn, pnpm, Lerna).

### Features
- Resolves semver ranges to exact versions
- Monorepo-aware (scans workspace packages)
- Returns source file path where version was found

### When to use
- Quickly check what SDK version a project is on before running other tools
- Useful in CI scripts or onboarding flows

### Note
> This feature is currently under development and may be removed in a future version.

### Sample Prompt
```
What version of the DevRev SDK is this project using? Project path: /YOUR_PROJECT_PATH
```

---

## 6. `get_devrev_migration_guide`

### What it does
Compares two SDK versions and generates a human-readable migration guide. Shows breaking changes with before/after code snippets, removed APIs, signature changes, and new features you can adopt.

### Features
- Identifies removed methods and types
- Shows exact signature changes (before/after)
- Lists new helpers you can adopt
- Step-by-step migration instructions
- Before/after TypeScript code snippets for every breaking change

### When to use
- Planning a version upgrade
- Reviewing what breaking changes will affect your codebase
- Sharing upgrade instructions with the team

### Sample Prompt
```
Generate a migration guide for upgrading the DevRev SDK from version 1.0.0 to the latest version.
```

### Sample Response (abbreviated)

**From `1.0.0` → `1.15.2`**

**Removed — must fix:**
- `getErrorExtractorEventType()` — removed, no replacement
- `normalizeIssue()`, `normalizeUser()`, `normalizeAttachment()` — removed

**Signature changes — code will not compile until updated:**

```ts
// spawn — BEFORE (1.0.0)
spawn({ event, initialState, workerPath, options }): Promise<boolean>

// spawn — AFTER (1.15.2)
spawn({ event, initialState, workerPath, initialDomainMapping, options, baseWorkerPath }): Promise<void>
```

```ts
// createWorkerAdapter — BEFORE
createWorkerAdapter({ event, adapterState, parentPort, options })

// createWorkerAdapter — AFTER
createWorkerAdapter({ event, adapterState, options })  // parentPort removed
```

**New helpers you can adopt:**
- `translateIncomingEventType`, `translateExtractorEventType`, `translateLoaderEventType`
- `serializeError`, `serializeAxiosError`, `getSdkLogContextValue`
- `getFilesToLoad`, loader report helpers

---

## 7. `get_devrev_sdk_version_diff`

### What it does
Returns a machine-readable structured diff between two SDK versions. Unlike the migration guide (which is narrative), this gives you exact lists of added, removed, and changed APIs — useful for programmatic processing or a quick technical audit.

### Features
- Exact count of added / removed / changed items
- Full method signatures for every change
- Before/after signatures for changed methods
- Faster than the migration guide (no narrative generation)

### When to use
- Technical audit of what changed between two versions
- Feeding diff data into another process or script
- Quick check of the scope of changes before deciding whether to upgrade

### Sample Prompt
```
What changed in the DevRev SDK API between version 1.12.3-0 and the latest version? Show added, removed, and changed methods.
```

### Sample Response

**Summary (`1.12.3-0` → `1.15.2`):**
- **Added:** 16 methods
- **Removed:** 5 methods
- **Changed:** 2 method signatures

**Added (16):** `translateIncomingEventType`, `translateExtractorEventType`, `translateLoaderEventType`, `translateOutgoingEventType`, `getNoScriptEventType`, `createArtifact`, `createAxiosResponse`, `createDownloadUrlResponse`, `createFileBuffer`, `createFileStream`, `callPrivateMethod`, `spyOnPrivateMethod`, `compressGzip`, `decompressGzip`, `parseJsonl`, `downloadToLocal`

**Removed (5):** `getSyncDirection`, `ensureSdkLogContext`, `normalizeIssue`, `normalizeUser`, `normalizeAttachment`

**Changed (2):**
- `spawn` — added `baseWorkerPath` param
- `createEvent` — all params now optional with defaults; return type unchanged

---

## Quick Smoke Test

Paste this into Cursor to hit 4 tools in one go and verify the server is fully working:

```
Using the DevRev SDK MCP server:
1. List all available SDK versions
2. Search for "AirdropEvent" in the latest version
3. Get a webhook code example
4. Show all methods available in the latest version
```

**Expected behavior:** 4 separate tool calls fire, each returning data.

---

## Connection Details

| Setting | Value |
|---------|-------|
| Server URL | `http://198.135.54.2:3000/sse` |
| Health check | `http://198.135.54.2:3000/health` |
| With personal GitHub token | `http://198.135.54.2:3000/sse?github_token=YOUR_TOKEN` |
| GitHub rate limit (no token) | 60 requests / hour |
| GitHub rate limit (with token) | 5,000 requests / hour |

---

*Maintained by Saurabh Verma*
