# DevRev SDK MCP Server — Complete Tool Reference

> **Author:** Saurabh Verma
>
> **Server:** `http://198.135.54.2:3000/sse`
>
> **SDK Package:** `@devrev/ts-adaas`
> **Tools:** 38 across 9 categories
> **Transport:** stdio (local) or HTTP/SSE (remote)
>
> **Demo Video:** https://jumpshare.com/s/4jhCegT8ed4vl3MxwhnA

This document covers all 38 tools exposed by the DevRev SDK MCP server. For each tool you will find: what it does, when to use it, and sample prompts.

---

## Table of Contents

1. [Setup & Configuration](#setup--configuration)
2. [SDK Management (11 tools)](#sdk-management-11-tools)
3. [Snap-in Management (4 tools)](#snap-in-management-4-tools)
4. [Information Query (3 tools)](#information-query-3-tools)
5. [Development (5 tools)](#development-5-tools)
6. [Code Review (4 tools)](#code-review-4-tools)
7. [Data Extraction (3 tools)](#data-extraction-3-tools)
8. [Testing (3 tools)](#testing-3-tools)
9. [Deployment (3 tools)](#deployment-3-tools)
10. [Documentation (2 tools)](#documentation-2-tools)
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

## Snap-in Management (4 tools)

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

### 14. `validate_snapin_config`

Validates snap-in configuration against schema rules.

```
Validate the snap-in configuration at ./my-snapin
```

### 15. `get_snapin_status`

Gets snap-in project health: config validity, file structure, build status.

```
What's the health status of the snap-in at ./my-snapin?
```

---

## Information Query (3 tools)

### 16. `get_edge_case_patterns`

Returns edge case patterns to handle in ADaaS development. Filterable by category and severity.

```
What edge cases should I handle when building an extraction worker?
```

### 17. `get_lambda_best_practices`

Returns Lambda optimization best practices for snap-ins.

```
What are the Lambda optimization best practices for snap-ins?
```

### 18. `get_common_errors`

Returns common error patterns with causes and fixes.

```
What are the most common errors in ADaaS snap-in development?
```

---

## Development (5 tools)

### 19. `generate_project_structure`

Generates a complete snap-in project scaffold with manifest, package.json, workers, and config files.

```
Generate a new ADaaS snap-in project for a Jira connector
```

### 20. `generate_worker_template`

Generates extraction worker template code for a specific entity type.

```
Generate an extraction worker for pulling user data from Jira
```

### 21. `generate_domain_mapping`

Generates domain_mapping.json from entity definitions.

```
Generate a domain mapping for users and issues entities
```

### 22. `generate_loading_worker`

Generates loading worker template for bidirectional sync.

```
Generate a loading worker for pushing issues back to Jira
```

### 23. `generate_test_template`

Generates test file templates (worker, normalization, api-client).

```
Generate a test template for the user extraction worker
```

---

## Code Review (4 tools)

### 24. `analyze_code_quality`

Comprehensive code review producing a structured report with:
- Overall score (0-100) and rating
- 9 sections: Project Structure, SDK Integration, Code Quality, Event Routing, Error Handling, State Management, Lambda Constraints, Test Coverage, Domain Mapping
- Findings with severity (critical/important/minor) and fix suggestions
- Recommendations

```
Review the code quality of the snap-in at ./my-snapin
```

### 25. `detect_anti_patterns`

Scans code for known anti-patterns (missing timeout handlers, incorrect event types, etc.).

```
Check my snap-in code for anti-patterns
```

### 26. `suggest_optimizations`

Returns performance optimization suggestions.

```
Suggest performance optimizations for my snap-in
```

### 27. `validate_error_handling`

Validates error handling patterns (try/catch coverage, error wrapping, etc.).

```
Validate the error handling in my snap-in code
```

---

## Data Extraction (3 tools)

### 28. `extract_metadata`

Extracts metadata from snap-in configuration files.

```
Extract metadata from the snap-in at ./my-snapin
```

### 29. `validate_markdown`

Validates markdown content for security issues (HTML injection, script tags).

```
Validate this markdown for security issues: "# Title <script>alert('xss')</script>"
```

### 30. `detect_html_injection`

Scans data fields for HTML/script injection risks.

```
Check these data fields for injection risks: {"name": "<img onerror=alert(1)>"}
```

---

## Testing (3 tools)

### 31. `run_tests`

Executes the test suite for a snap-in project.

```
Run the test suite for the snap-in at ./my-snapin
```

### 32. `generate_test_data`

Generates mock test data for entity types (users, issues, calls, custom). Supports edge cases.

```
Generate 10 mock user records for testing, include edge cases
```

### 33. `coverage_report`

Generates test coverage report for a snap-in.

```
Generate a test coverage report for my snap-in
```

---

## Deployment (3 tools)

### 34. `validate_deployment`

Runs deployment validation checks: config, structure, dependencies, build.

```
Validate the deployment readiness of my snap-in
```

### 35. `build_snapin`

Builds a snap-in project (runs npm build).

```
Build the snap-in at ./my-snapin
```

### 36. `check_lambda_constraints`

Checks snap-in against AWS Lambda constraints: bundle size, dependencies, memory patterns, timeout usage.

```
Check if my snap-in meets AWS Lambda constraints
```

---

## Documentation (2 tools)

### 37. `generate_readme`

Generates README.md for a snap-in project with sections for overview, setup, configuration, domain mapping, project structure, and development commands.

```
Generate a README.md for the snap-in at ./my-snapin
```

### 38. `generate_marketplace_doc`

Generates DevRev marketplace documentation with features, data synced table, setup instructions, and technical details.

```
Generate marketplace documentation for "Jira Connector" snap-in
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

### Workflow 2: New Snap-in Development

```
1. "Generate a new snap-in for a Salesforce connector"
   → generate_project_structure
2. "Generate extraction workers for contacts and deals"
   → generate_worker_template (x2)
3. "Generate domain mapping"
   → generate_domain_mapping
4. "Generate loading workers for bidirectional sync"
   → generate_loading_worker (x2)
5. "Generate tests"
   → generate_test_template
6. "Review the code quality"
   → analyze_code_quality
```

### Workflow 3: Pre-Deployment Check

```
1. "Validate snap-in config" → validate_snapin_config
2. "Check Lambda constraints" → check_lambda_constraints
3. "Run tests" → run_tests
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
