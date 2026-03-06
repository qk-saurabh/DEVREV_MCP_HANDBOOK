# DevRev SDK MCP Server — Complete Tool Reference

> **Author:** Saurabh Verma
>
> **Server:** `http://198.135.54.2:3000/sse`
>
> **SDK Package:** `@devrev/ts-adaas`
> **Tools:** 41 across 9 categories
> **Transport:** stdio (local) or HTTP/SSE (remote)
>
> **Demo Video:** https://jumpshare.com/s/4jhCegT8ed4vl3MxwhnA

This document covers all 41 tools exposed by the DevRev SDK MCP server. For each tool you will find: what it does, when to use it, and sample prompts.

---

## Table of Contents

1. [Setup & Configuration](#setup--configuration)
2. [SDK Management (11 tools)](#sdk-management-11-tools)
3. [Snap-in Management (3 tools)](#snap-in-management-3-tools)
4. [Information Query (3 tools)](#information-query-3-tools)
5. [Development (8 tools)](#development-8-tools)
6. [Code Review (4 tools)](#code-review-4-tools)
7. [Content Validation (2 tools)](#content-validation-2-tools)
8. [Testing (4 tools)](#testing-4-tools)
9. [Deployment (3 tools)](#deployment-3-tools)
10. [Documentation (3 tools)](#documentation-3-tools)
11. [End-to-End Workflows](#end-to-end-workflows)

---

## Setup & Configuration

### GitHub Token

The server uses the GitHub API to fetch SDK source code and release data. Without a token, GitHub allows only **60 requests/hour**. With a personal token: **5,000 requests/hour**.

To generate a token: **GitHub > Settings > Developer settings > Personal access tokens > Tokens (classic)** > no scopes needed for public repos.

### Cursor (stdio mode)

```json
{
  "mcp.servers": {
    "devrev-sdk": {
      "command": "npx",
      "args": ["devrev-sdk-mcp"],
      "env": {
        "GITHUB_TOKEN": "ghp_yourtoken"
      }
    }
  }
}
```

### Cursor (HTTP/SSE mode)

```json
{
  "mcpServers": {
    "devrev-sdk": {
      "url": "http://198.135.54.2:3000/sse?github_token=ghp_yourtoken"
    }
  }
}
```

### VS Code

Same JSON format as Cursor. Add to VS Code MCP settings or `settings.json`.

### Health Check (HTTP mode)

```bash
curl http://198.135.54.2:3000/health
# Expected: {"status":"ok","transport":"http"}
```

---

## SDK Management (11 tools)

### 1. `list_devrev_sdk_versions`

Lists all published versions of `@devrev/ts-adaas` from npm. Shows version numbers, release dates, and latest stable release.

**When to use:** Before upgrading, to find available versions.

```
List all available versions of the DevRev SDK. Show the 10 most recent stable releases.
```

### 2. `get_devrev_sdk_context`

Returns a complete picture of SDK exports: functions, interfaces, types, enums, classes, and examples for a given version. Can filter by topic.

**When to use:** Understanding what the SDK offers before writing code.

```
Show me all methods and types available in DevRev SDK version 1.14.0
```

```
What does the DevRev SDK provide for working with webhooks?
```

### 3. `search_devrev_sdk`

Full-text search across SDK methods, types, and examples. Returns results with relevance scores.

**When to use:** Finding a specific method or exploring a concept.

```
Search the DevRev SDK for anything related to "WorkerAdapter"
```

### 4. `get_devrev_examples`

Returns copy-paste ready code examples organized by category: webhook, automation, command, keyring, snapkit, event-source, api-call, manifest.

**When to use:** Starting a new feature and need a working template.

```
Show me working code examples for DevRev webhook handlers
```

### 5. `detect_devrev_sdk_version`

Reads package.json, resolves version ranges, and returns the exact SDK version. Supports monorepo workspaces.

**When to use:** Checking what SDK version a project uses.

```
What version of the @devrev/ts-adaas SDK is this project using?
```

### 6. `get_devrev_migration_guide`

Compares two SDK versions and generates a migration guide with breaking changes, removed APIs, and code examples.

**When to use:** Planning a version upgrade.

```
Generate a migration guide from DevRev SDK v1.12.0 to v1.15.2
```

### 7. `get_devrev_sdk_version_diff`

Returns a structured diff between two versions: added, removed, and changed APIs.

**When to use:** Quick technical audit of changes between versions.

```
What changed in the DevRev SDK between version 1.12.0 and 1.15.2?
```

### 8. `check_sdk_compatibility`

Checks if the project's current SDK version is compatible with a target version.

**When to use:** Before upgrading, to assess compatibility.

```
Check if my snap-in is compatible with the latest DevRev SDK version
```

### 9. `get_sdk_methods`

Returns SDK methods with filtering by category and version.

**When to use:** Exploring SDK API surface by category.

```
Show me all SDK methods available for extraction workers
```

### 10. `get_event_types`

Returns ADaaS event types for extraction and loading phases.

**When to use:** Understanding what events your snap-in can handle.

```
What event types are available for extraction and loading phases?
```

### 11. `get_domain_mapping_schema`

Returns domain mapping JSON schema reference and validation rules.

**When to use:** Building or validating domain_mapping.json.

```
Show me the domain mapping JSON schema for ADaaS snap-ins
```

---

## Snap-in Management (3 tools)

### 12. `list_snapins`

Scans a directory for snap-in projects (looks for manifest.yaml).

```
Scan the current directory for snap-in projects
```

### 13. `get_snapin_config`

Reads and parses snap-in configuration: manifest.yaml, domain_mapping.json, package.json.

```
Read the configuration for the snap-in at ./my-snapin
```

### 14. `get_snapin_status`

Gets snap-in project health: config validity, file structure, build status.

```
What's the health status of the snap-in at ./my-snapin?
```

---

## Information Query (3 tools)

### 15. `get_edge_case_patterns`

Returns edge case patterns to handle in ADaaS development. Filterable by category and severity.

```
What edge cases should I handle when building an extraction worker?
```

### 16. `get_lambda_best_practices`

Returns Lambda optimization best practices for snap-ins.

```
What are the Lambda optimization best practices for snap-ins?
```

### 17. `get_common_errors`

Returns common error patterns with causes and fixes.

```
What are the most common errors in ADaaS snap-in development?
```

---

## Development (8 tools)

### 18. `generate_worker_template`

Generates extraction worker template code for a specific entity type.

```
Generate an extraction worker for pulling user data from Jira
```

### 19. `generate_domain_mapping`

Generates domain_mapping.json from entity definitions.

```
Generate a domain mapping for users and issues entities
```

### 20. `generate_loading_worker`

Generates loading worker template for bidirectional sync.

```
Generate a loading worker for pushing issues back to Jira
```

### 21. `generate_test_template`

Generates test file templates (worker, normalization, api-client).

```
Generate a test template for the user extraction worker
```

### 22. `create_devrev_snapin` (scaffold)

Generates a complete, production-ready ADaaS snap-in scaffold from a PRD/TDD document. Creates 20+ files including manifest.yaml v2, all extraction workers with processTask/onTimeout, domain mapping v0.2.0, state management, constants, API client, and test fixtures. Supports extraction_only and bidirectional sync with PAT/OAuth/API key auth. Clones template repo or generates in-memory, rebrands all references, crawls API docs from the PRD.

**When to use:** Starting a new snap-in from scratch or from a PRD. This is the FIRST tool to call. After this, follow the recommended workflow: crawl_api_docs → cleanup_scaffold → validate_snapin_scaffold → analyze_code_quality → run_quality_checks.

**Input:** `snapin_name` (kebab-case), `prd_or_tdd` (full PRD text, min 10 chars), optional `output_directory`, optional `sync_direction`

```
Build a new snap-in for Zoho Desk that extracts users, tickets, and contacts using PAT auth
```

```
Scaffold a bidirectional Gong connector with OAuth auth for users and calls
```

### 23. `validate_snapin_scaffold`

Validates a snap-in project directory against production best practices. Checks manifest v2, workers (processTask + onTimeout), EventType compatibility, domain mapping v0.2.0, fallback types, SDK version, state management, and security. Returns score (0-100) with critical/important/minor issue breakdown.

**When to use:** After generating a scaffold, after `devrev snap_in_version init`, or during code review to ensure snap-in quality.

**Input:** `snapin_path` (directory path), optional `strict_mode` (default: true)

```
Validate the snap-in at ./my-snapin against best practices
```

### 24. `crawl_api_docs`

Crawls API documentation URLs and extracts structured information: endpoints, object schemas, authentication methods, pagination patterns, rate limits, and webhook support. Supports OpenAPI/Swagger specs, HTML docs, and raw JSON. Tracks entity coverage — shows which of your target entities have API endpoint support.

**When to use:** Before or after `create_devrev_snapin` to gather deep API knowledge about the target application. Especially useful when the PRD contains API doc URLs. Returns actionable recommendations for implementation.

**Input:** `urls` (array of API doc URLs), `target_application` (e.g., "Asana"), optional `focus_entities` (e.g., ["tasks", "users"]), optional `include_raw_content`

```
Crawl the Asana API docs and tell me about their endpoints, auth, pagination, and rate limits
```

```
Analyze the Jira API documentation at these URLs and focus on issues, projects, and users
```

### 25. `cleanup_scaffold`

Cleans up a scaffolded snap-in by removing leftover template references (Asana, YourApp, etc.), replacing them with the target application name, removing empty files, updating SDK to latest version, and detecting leftover TODO markers.

**When to use:** After `create_devrev_snapin` and any manual edits to ensure the project is clean. Supports `dry_run` mode to preview changes before applying.

**Input:** `snapin_path`, `target_application` (e.g., "Jira"), optional `dry_run`, optional `fix_sdk_version`

```
Clean up the scaffolded snap-in and replace all template references with Jira
```

```
Dry-run cleanup on my snap-in to see what template leftovers remain
```

---

## Code Review (4 tools)

### 26. `analyze_code_quality`

Comprehensive code review producing a structured report with:
- Overall score (0-100) and rating
- 9 sections: Project Structure, SDK Integration, Code Quality, Event Routing, Error Handling, State Management, Lambda Constraints, Test Coverage, Domain Mapping
- Findings with severity (critical/important/minor) and fix suggestions
- Recommendations

```
Review the code quality of the snap-in at ./my-snapin
```

### 27. `detect_anti_patterns`

Scans code for known anti-patterns (missing timeout handlers, incorrect event types, etc.).

```
Check my snap-in code for anti-patterns
```

### 28. `suggest_optimizations`

Returns performance optimization suggestions.

```
Suggest performance optimizations for my snap-in
```

### 29. `validate_error_handling`

Validates error handling patterns (try/catch coverage, error wrapping, etc.).

```
Validate the error handling in my snap-in code
```

---

## Content Validation (2 tools)

### 30. `validate_markdown`

Validates markdown content for security issues (HTML injection, script tags).

```
Validate this markdown for security issues: "# Title <script>alert('xss')</script>"
```

### 31. `detect_html_injection`

Scans data fields for HTML/script injection risks.

```
Check these data fields for injection risks: {"name": "<img onerror=alert(1)>"}
```

---

## Testing (4 tools)

### 32. `run_tests`

Executes the test suite for a snap-in project.

```
Run the test suite for the snap-in at ./my-snapin
```

### 33. `generate_test_data`

Generates mock test data for entity types (users, issues, calls, custom). Supports edge cases.

```
Generate 10 mock user records for testing, include edge cases
```

### 34. `coverage_report`

Generates test coverage report for a snap-in.

```
Generate a test coverage report for my snap-in
```

### 35. `run_quality_checks`

Runs ALL quality gates in one call: npm install → TypeScript build → unit tests (vitest) → ESLint lint → npm audit. Returns a unified pass/fail report with blocking issues. Steps run sequentially; tests are skipped if build fails. Each step can be individually skipped.

**When to use:** As the final quality gate after `create_devrev_snapin` or after any code changes. Replaces the need to call `build_snapin`, `run_tests`, and lint separately.

**Input:** `snapin_path`, optional `skip_install`, `skip_tests`, `skip_lint`, `skip_audit`, `timeout_ms`

```
Run all quality checks on my snap-in (build, test, lint, audit)
```

```
Run quality checks but skip tests since I haven't written any yet
```

---

## Deployment (3 tools)

### 36. `validate_deployment`

Runs deployment validation checks: config, structure, dependencies, build.

```
Validate the deployment readiness of my snap-in
```

### 37. `build_snapin`

Builds a snap-in project (runs npm build).

```
Build the snap-in at ./my-snapin
```

### 38. `check_lambda_constraints`

Checks snap-in against AWS Lambda constraints: bundle size, dependencies, memory patterns, timeout usage.

```
Check if my snap-in meets AWS Lambda constraints
```

---

## Documentation (3 tools)

### 39. `generate_readme`

Generates README.md for a snap-in project with sections for overview, setup, configuration, domain mapping, project structure, and development commands.

```
Generate a README.md for the snap-in at ./my-snapin
```

### 40. `generate_marketplace_doc`

Generates DevRev marketplace documentation with features, data synced table, setup instructions, and technical details.

```
Generate marketplace documentation for "Jira Connector" snap-in
```

### 41. `generate_architecture_doc`

Generates architecture documentation for a snap-in project, covering system design, data flow, worker responsibilities, and integration patterns.

```
Generate architecture documentation for the snap-in at ./my-snapin
```

---

## End-to-End Workflows

### Workflow 1: SDK Upgrade

```
1. "What version of the DevRev SDK is this project using?"
   → detect_devrev_sdk_version
2. "What is the latest stable version?"
   → list_devrev_sdk_versions
3. "Compare my version with the latest"
   → get_devrev_sdk_version_diff
4. "Generate a migration guide"
   → get_devrev_migration_guide
5. "Show me the methods available in the latest version"
   → get_devrev_sdk_context
6. "Now upgrade my project"
   → AI uses all context to update code
```

### Workflow 2: New Snap-in from PRD (One Command in Cursor)

This is the primary workflow. Give Cursor a single prompt with your PRD and it chains all tools automatically:

```
Build me a Salesforce snap-in from this PRD:

Target: Salesforce
Entities: contacts, accounts, deals
Auth: OAuth 2.0
API docs: https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/
Sync: extraction only
```

**Cursor agent will automatically chain these tools:**

```
Step 1: create_devrev_snapin
   → Parses PRD, generates 20+ production-ready files
   → Clones template, rebrands, crawls API docs from PRD

Step 2: crawl_api_docs  (if API docs URL in PRD)
   → Deep crawl of Salesforce API docs
   → Extracts endpoints, schemas, pagination, rate limits
   → Returns entity coverage + recommendations

Step 3: cleanup_scaffold
   → Removes leftover template references
   → Updates SDK to latest version
   → Fixes naming conventions

Step 4: validate_snapin_scaffold
   → Checks 12+ production rules (score 0-100)
   → Lists critical/important/minor issues

Step 5: Cursor edits files to fix issues from Step 4

Step 6: analyze_code_quality + detect_anti_patterns
   → Deep code review with 10 sections
   → Anti-pattern detection across 22 known patterns

Step 7: run_quality_checks
   → npm install → TypeScript build → vitest → ESLint → npm audit
   → Unified pass/fail report

Step 8: generate_readme + generate_marketplace_doc
   → Documentation ready for deployment
```

### Workflow 2b: Incremental Snap-in Development

```
1. "Generate extraction workers for contacts and deals"
   → generate_worker_template (x2)
2. "Generate domain mapping"
   → generate_domain_mapping
3. "Generate loading workers for bidirectional sync"
   → generate_loading_worker (x2)
4. "Generate tests"
   → generate_test_template
5. "Review the code quality"
   → analyze_code_quality
```

### Workflow 3: Pre-Deployment Check

```
1. "Run all quality checks" → run_quality_checks (build + test + lint + audit in one call)
2. "Validate snap-in scaffold" → validate_snapin_scaffold
3. "Check Lambda constraints" → check_lambda_constraints
4. "Validate deployment" → validate_deployment
5. "Generate README" → generate_readme
6. "Generate marketplace docs" → generate_marketplace_doc
```

### Workflow 4: Code Review

```
1. "Analyze code quality" → analyze_code_quality (score + report)
2. "Check for anti-patterns" → detect_anti_patterns
3. "Suggest optimizations" → suggest_optimizations
4. "Validate error handling" → validate_error_handling
5. "Check common errors" → get_common_errors
```

---

## Quick Smoke Test

Paste this into Cursor to test multiple tools at once:

```
Using the DevRev SDK MCP server:
1. List all available SDK versions
2. Search for "AirdropEvent" in the latest version
3. Get a webhook code example
4. Show all methods available in the latest version
5. What edge cases should I handle for extraction workers?
6. What are common ADaaS development errors?
```

Expected: 6 tool calls fire, each returning structured data.

---

## Response Format

Tool responses use **TOON (Token-Oriented Object Notation)** encoding for array-heavy responses, reducing token consumption by 39-60%. Responses with TOON-encoded fields include a `_format: "toon"` marker. Non-array responses use standard JSON.

## Troubleshooting

| Issue | Solution |
|-------|----------|
| AI says "tool not found" | Restart IDE after adding MCP config |
| Empty methods/types returned | Set `GITHUB_TOKEN` for higher rate limits |
| "Version not found" error | Use `list_devrev_sdk_versions` to check available versions |
| Slow first response | Normal — first call fetches from APIs. Subsequent calls use cache (<100ms) |
| Version detection returns range | Normal when exact version can't be resolved; range is used as fallback |
