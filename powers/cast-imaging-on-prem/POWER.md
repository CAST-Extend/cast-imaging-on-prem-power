# CAST Imaging

## Overview

CAST Imaging analyzes application source code and builds a comprehensive structural graph of objects, relationships, transactions, and data flows. This power connects Kiro to a CAST Imaging MCP server, enabling developers to query that structural intelligence directly from conversations.

**Use cases:**
- **Impact analysis** — Understand dependencies before making code changes, target regression tests
- **Application profiling** — See technology mix, object distribution, complexity metrics
- **Quality investigation** — Find CVEs, structural flaws, cloud blockers, ISO-5055 violations
- **Architecture exploration** — Navigate layers, components, and service boundaries
- **Transaction tracing** — Follow execution flows end-to-end through the codebase
- **Modernization assessment** — Identify coupling hotspots and plan refactoring waves

## Onboarding

### First-Time Setup

When this power is activated for the first time, guide the user through configuration:

1. **Ask for FQDN (or IP address) and IP port**: Ask the user to provide their CAST Imaging MCP server FQDN (or IP address), along with the IP port.

2. **Validate the connection information**: Run:
   ```
   curl -s -o NUL -w "%{http_code}" http[s]://<fqdn-or-IP-address-mcp-machine:[mcp-port]>/mcp/healthcheck
   ```
   If the response is not HTTP 2xx, inform the user the FQDN or IP address or IP port is invalid and ask again.

3. **Ask for API Key**: Ask the user to provide their CAST Imaging API key. If they don't have one, recommend generating an API key from their CAST Imaging user profile (accessible after logging in)

4. **Validate the API Key**: Ask the user to provide their CAST Imaging FQDN (or IP address, if different from MCP server hosting machine), along with the IP port, then run:
   ```
   curl -s -o NUL -w "%{http_code}"  -H "x-api-key: <your-imaging-api-key>" http[s]://<fqdn-or-IP-address-imaging-services-machine:[imaging-port]>/rest/ready
   ```
   If the response is not HTTP 2xx, inform the user the key is invalid and ask again.

5. **Configure**: Write `.kiro/settings/mcp.json` with only the `x-api-key` header:
   ```json
   {
     "mcpServers": {
       "CASTImaging": {
         "type": "http",
         "url": "http[s]://<fqdn-or-IP-address-mcp-machine:[mcp-port]>/mcp",
         "headers": {
           "x-api-key": "<validated-api-key>"
         }
       }
     }
   }
   ```

6. **Gitignore**: Remind the user to add `.kiro/settings/mcp.json` to `.gitignore` since it contains their API key.

### Re-Authentication

If the MCP server returns HTTP 401 (Invalid API Key), re-trigger the onboarding flow. If HTTP 403 (Invalid Tenant), re-query tenants and ask user to select.

### Reconnection After Server Restart

If you receive a "Session not found" error, the MCP server was restarted and the previous session is stale. Instruct the user to:

1. Open the **Command Palette** (Ctrl+Shift+P)
2. Search for **"MCP"** and select **"MCP: Reconnect Server"**
3. Choose **CASTImaging**

Alternatively, click the reconnect/refresh icon next to CASTImaging in the **MCP Servers** view in Kiro's feature panel.

After reconnection, verify with a quick `get_mcp_info` call via `run_structural_search_function`.


## Compatibility Check

On activation, verify the MCP server exposes the expected tool surface before proceeding.

**Classic Fingerprint (supported):** The server exposes individual, purpose-built tools directly. Check that these tools are available as direct MCP tools:
- `all_applications`, `objects`, `object_details`, `transactions`, `transaction_details`, `quality_insights`, `stats`

**Meta-Tool Fingerprint (supported):** The server uses a two-tool meta-interface. Check that these MCP tools are available:
- `run_structural_search_function` and `get_structural_search_function_syntax`

**Quick detection approach:** Try calling `run_structural_search_function` with `function_name: "applications"`. If it succeeds, you're in Meta-Tool mode. If the tool doesn't exist but `all_applications` does, you're in Classic mode.

**Helpful server behaviors in Meta-Tool mode:**
- If you call a wrong function name, the server returns a `did_you_mean` list of similar names
- The error response includes `available_functions_count` telling you how many functions exist
- Some functions may be disabled by the active server profile — these appear in a `not_enabled` field

If neither fingerprint matches, warn about an unknown server version.

See `steering/compatibility.md` for full detection logic and warning templates.

**Important:** Both modes expose the same underlying data. The Classic mode uses individual tool names (e.g., `all_applications`), while the Meta-Tool mode wraps the same functions behind `run_structural_search_function` with slightly different names (e.g., `applications` instead of `all_applications`).


## Classic Mode - Available Tools by Category

SKIP to `Meta-Tool Mode` section if **Meta-Tool Fingerprint** was detected.

### Application Discovery

| Tool | Purpose | Key Parameters |
|------|---------|----------------|
| `all_applications` | List all applications | `page` |
| `my_applications` | List user-owned applications | `page` |
| `stats` | Application statistics (LOC, elements, technologies) | `application` |

### Architecture

| Tool | Purpose | Key Parameters |
|------|---------|----------------|
| `architectural_graph` | Architectural graph at various levels | `application`, `level`, `mode`, `layout` |
| `architectural_graph_focus` | Architectural graph focused on an area | `application`, `area`, `level`, `mode` |

### Transactions

| Tool | Purpose | Key Parameters |
|------|---------|----------------|
| `transactions` | List transactions | `application`, `filters`, `page` |
| `transaction_profiles` | Transaction profiles | `application` |
| `transaction_details` | Transaction drill-down | `application`, `id`, `focus`, `isfullcallgraph` |
| `applications_transactions` | Cross-app transaction listing | `filters`, `page` |

### Data Graphs

| Tool | Purpose | Key Parameters |
|------|---------|----------------|
| `data_graphs` | List data entity networks | `application`, `filters`, `page` |
| `data_graph_profiles` | Data graph profiles | `application` |
| `data_graph_details` | Data graph drill-down | `application`, `id`, `focus` |
| `applications_data_graphs` | Cross-app data graph listing | `filters`, `page` |

### Objects

| Tool | Purpose | Key Parameters |
|------|---------|----------------|
| `objects` | Search objects with filters | `application`, `filters`, `tags`, `page` |
| `object_profiles` | Object type distribution | `application` |
| `object_details` | Object detail views | `application`, `filters`, `focus` |
| `objects_relationships` | Relationships between objects | `application`, `object_ids` |

### Path & Hierarchy

| Tool | Purpose | Key Parameters |
|------|---------|----------------|
| `pathfinder_hierarchy_details` | Execution paths or hierarchy | `application`, `source`, `target`, `focus`, `hops`, `call_type` |

### Quality & Security

| Tool | Purpose | Key Parameters |
|------|---------|----------------|
| `quality_insights` | Quality insights by nature | `application`, `nature` |
| `quality_insight_violations` | Insight violations with locations | `application`, `nature`, `id`, `include_locations` |
| `applications_quality_insights` | Cross-app quality overview | `insights`, `page` |
| `application_iso_5055_explorer` | ISO 5055 characteristics/weaknesses | `application`, `characteristic_id` |

### Source Files & Database

| Tool | Purpose | Key Parameters |
|------|---------|----------------|
| `source_files` | Find source files by path | `application`, `file_path` |
| `source_file_details` | Source file detail views | `application`, `file_path`, `nature` |
| `application_database_explorer` | Explore tables and columns | `application`, `table_name`, `name_filter` |

### Packages

| Tool | Purpose | Key Parameters |
|------|---------|----------------|
| `packages` | List external packages | `application` |
| `package_interactions` | Package interaction details | `application`, `component`, `version` |

### Cross-References

| Tool | Purpose | Key Parameters |
|------|---------|----------------|
| `transactions_using_object` | Transactions through an object | `application`, `filters` |
| `data_graphs_involving_object` | Data graphs involving an object | `application`, `filters` |
| `applications_dependencies` | Inter-app dependencies | `page` |

### Tagging

| Tool | Purpose | Key Parameters |
|------|---------|----------------|
| `manage_object_tags` | Add/delete tags on known IDs | `application`, `add_tags`, `nodes_for_add` |
| `bulk_manage_object_tags` | Bulk tag by rule (preview first!) | `application`, `add_tags`, `filters`, `execute`, `confirmed` |

### Views

| Tool | Purpose | Key Parameters |
|------|---------|----------------|
| `views` | List saved views | `application` |
| `view_details` | View structure details | `application`, `id` |
| `manage_view` | Create/update/delete views | `application`, `operation`, `name`, `object_ids` |

### Documentation

| Tool | Purpose | Key Parameters |
|------|---------|----------------|
| `add_object_document` | Attach note to an object | `application`, `id`, `title`, `description` |
| `add_view_document` | Attach note to a view | `application`, `id`, `view_type`, `title`, `description` |

### Common Patterns

- **`application`**: Always use the exact `name` field from `all_applications` — never guess
- **`filters`**: Format `"field:operator:value,field:operator:value"` — all filters must use the same operator
- **`page`**: Default 1, paginated responses include `has_next` in metadata
- **`focus`**: Varies by tool — see steering files for guidance on which to use when

## Classic Mode - Quick Start Workflows

### 1. Discover and Profile an Application

```
1. all_applications() → pick application by name
2. stats(application="<app>") → size, technologies, element types
3. object_profiles(application="<app>") → object type distribution
4. architectural_graph(application="<app>", level="component", mode="nodes") → structural overview
```

### 2. Impact Analysis Before a Code Change

```
1. objects(application="<app>", filters="name:contains:<target>") → find object, get ID
2. object_details(application="<app>", filters="id:eq:<id>", focus="inward") → who calls it
3. object_details(application="<app>", filters="id:eq:<id>", focus="outward") → what it calls
4. transactions_using_object(application="<app>", filters="id:eq:<id>") → affected transactions
```

### 3. Investigate Quality Issues

```
1. quality_insights(application="<app>", nature="structural-flaws") → overview of issues
2. quality_insight_violations(application="<app>", nature="structural-flaws", id="<insight_id>") → impacted objects
3. quality_insight_violations(..., include_locations=True) → file/line details
```

### 4. Explore a Transaction

```
1. transactions(application="<app>", filters="name:contains:order") → find transaction
2. transaction_details(application="<app>", id="<txn_id>", focus="type_graph") → structural overview
3. transaction_details(application="<app>", id="<txn_id>", focus="complexity") → complex hotspots
```


## Meta-Tool Mode - Available Tools

ONLY if **Meta-Tool Fingerprint** was detected.

In meta-tool mode the client is provided with two MCP tools plus a set of resources:

- **`get_structural_search_function_syntax`**: Given one or more function names, returns full parameter details, purpose, sample questions, and usage examples for each.
- **`run_structural_search_function`**: Executes a named function with provided arguments. Returns the same results as Classic mode tools.

### Discovery Approach

There is no `list_functions` meta-function. Instead:

1. Read the `structural-search://functions/summary` resource for a categorized list of all function names
2. Call `get_structural_search_function_syntax(["function_name"])` to get full parameter details
3. Call `run_structural_search_function(function_name="...", arguments={...})` to execute

If you call a wrong function name, the server returns helpful error info including `did_you_mean` suggestions and `available_functions_count`.

### Resources

| Resource URI | Purpose |
|--------------|---------|
| `structural-search://functions/summary` | Quick orientation — all function names organized by category |
| `structural-search://functions/catalog` | Full catalog: purpose, parameters, and sample questions for each function |

### Prompts

| Prompt | Purpose |
|--------|---------|
| `code_impact_analysis` | Pre-change impact workflow aligned to the SWE playbook |
| `database_migration_assessment` | Database migration impact assessment |
| `quality_hotspot_investigation` | Quality hotspot investigation aligned to SWE remediation |
| `security_vulnerability_deep_dive` | Security vulnerability deep dive |
| `architecture_compliance_check` | Architecture compliance check |
| `large_scope_analysis` | Guided workflow for analyzing large transactions/data graphs/services |
| `generate_transaction_portfolio_report` | Full transaction portfolio report from download_url datasets |
| `generate_download_url_report` | Generic report generation from download_url datasets |


## Meta-Tool Mode - Function Reference

### Portfolio Discovery

| Function | Purpose |
|----------|---------|
| `applications` | List all applications with status and delivery info |
| `applications_transactions` | Transactions from all applications (cross-app) |
| `applications_data_graphs` | Data graphs from all applications (cross-app) |
| `applications_quality_insights` | Quality insights across all applications |
| `applications_dependencies` | Inter-application dependencies |

### Application Overview

| Function | Purpose |
|----------|---------|
| `stats` | Basic stats: element count, interaction count, technologies |
| `object_profiles` | Object type distribution |
| `transaction_profiles` | Transaction type profiles |
| `data_graph_profiles` | Data graph type profiles |

### Objects & Source Files

| Function | Purpose |
|----------|---------|
| `objects` | Search objects with name/type/path/annotation filters |
| `object_details` | Object drill-down: intra, inward, outward, testing, code, insights, documents, endpoints |
| `source_files` | Find source files by path substring |
| `source_file_details` | Source file detail: inventory, intra, inward, outward, testing |

### Transactions & Data Graphs

| Function | Purpose |
|----------|---------|
| `transactions` | List transactions with filters |
| `transaction_details` | Transaction drill-down: complexity, insights, nodes, links, graph, type_graph, complexity_graph, documents, summary, callers, callees |
| `transactions_using_object` | Find transactions using specific object(s) |
| `data_graphs` | List data graphs with filters |
| `data_graph_details` | Data graph drill-down: same focus types as transaction_details |
| `data_graphs_involving_object` | Find data graphs involving specific object(s) |

### Architecture & Dependencies

| Function | Purpose |
|----------|---------|
| `architectural_graph` | Architectural graph at layer/component/sub-component/technology-category/element-type level |
| `architectural_graph_focus` | Architectural graph focused on a specific area |
| `objects_relationships` | Relationship graph between multiple objects by ID |
| `pathfinder_hierarchy_details` | Execution paths (pathfinder) or hierarchical structure (hierarchy) between objects |

### Quality & Security

| Function | Purpose |
|----------|---------|
| `quality_insights` | Quality insights by nature: cloud, green, cve, structural-flaws, iso-5055 |
| `quality_insight_violations` | Violations with optional file/line locations |
| `application_iso_5055_explorer` | ISO 5055 characteristics and weaknesses explorer |

### Database & Packages

| Function | Purpose |
|----------|---------|
| `packages` | List external packages/libraries |
| `package_interactions` | How an application uses a specific package |
| `application_database_explorer` | Explore database tables and columns |

### Documentation & Tagging

| Function | Purpose |
|----------|---------|
| `manage_object_tags` | Add/delete tags on explicitly identified object IDs |
| `bulk_manage_object_tags` | Bulk tag by rule/filter (preview first, then confirm!) |
| `add_object_document` | Attach a post-it/note to a code object |
| `add_view_document` | Attach a post-it/note to a transaction or data graph |

### Views & Customization

| Function | Purpose |
|----------|---------|
| `views` | List saved views |
| `view_details` | Get view structure and graph details |
| `manage_view` | Create, update, or delete a saved view |

### Guidance & Help

| Function | Purpose |
|----------|---------|
| `get_mcp_info` | MCP server version, Imaging API version, and compatibility check |


## Meta-Tool Mode - Quick Start Workflow

### 1. Discover Applications

```json
{"function_name": "applications", "arguments": {}}
```

Select the target application name exactly as returned in the `name` field.

### 2. Profile the Application

```json
{"function_name": "stats", "arguments": {"application": "<app>"}}
```

Then get object distribution:

```json
{"function_name": "object_profiles", "arguments": {"application": "<app>"}}
```

### 3. Impact Analysis Before a Code Change

```json
// Step 1: Find the object
{"function_name": "objects", "arguments": {"application": "<app>", "filters": "name:contains:<target>"}}

// Step 2: Who calls it?
{"function_name": "object_details", "arguments": {"application": "<app>", "filters": "id:eq:<id>", "focus": "inward"}}

// Step 3: What does it call?
{"function_name": "object_details", "arguments": {"application": "<app>", "filters": "id:eq:<id>", "focus": "outward"}}

// Step 4: Which transactions are affected?
{"function_name": "transactions_using_object", "arguments": {"application": "<app>", "filters": "id:eq:<id>"}}
```

### 4. Investigate Quality Issues

```json
// Step 1: Overview
{"function_name": "quality_insights", "arguments": {"application": "<app>", "nature": "structural-flaws"}}

// Step 2: Violations for a specific insight
{"function_name": "quality_insight_violations", "arguments": {"application": "<app>", "nature": "structural-flaws", "id": "<insight_id>"}}

// Step 3: With file/line details
{"function_name": "quality_insight_violations", "arguments": {"application": "<app>", "nature": "structural-flaws", "id": "<insight_id>", "include_locations": true}}
```

### 5. Explore a Transaction

```json
// Step 1: Find transaction
{"function_name": "transactions", "arguments": {"application": "<app>", "filters": "name:contains:order"}}

// Step 2: Structural overview
{"function_name": "transaction_details", "arguments": {"application": "<app>", "transaction_id": "<txn_id>", "focus": "type_graph"}}

// Step 3: Complexity hotspots
{"function_name": "transaction_details", "arguments": {"application": "<app>", "transaction_id": "<txn_id>", "focus": "complexity"}}
```


## Meta-Tool Mode - Best Practices

- Always use exact application names from `applications` — never guess
- Use `get_structural_search_function_syntax` to check parameter details before calling unfamiliar functions
- Prefer profiling functions (`stats`, `object_profiles`, `transaction_profiles`) to get the big picture before drilling into individual objects
- For large results, paginate with `page` parameter — responses include `has_next` in metadata
- When results include `full_data_file` in metadata, this indicates a cached file with the complete dataset
- For any code change, always produce both: a dependency impact list and a transaction/data-graph test list
- `bulk_manage_object_tags`: Always preview first (default), then ask user approval before setting `execute=True` and `confirmed=True`


## Meta-Tool Mode - Troubleshooting

### No Applications Found

**Problem:** `applications` returns empty results
**Solutions:**
1. Verify that applications have been scanned in your CAST Imaging instance
2. Check that the MCP server is connected to the correct CAST Imaging database
3. Contact your CAST Imaging administrator

### Function Not Found

**Problem:** Server returns `"error": "Function 'X' not found"`
**Solutions:**
1. Check the `did_you_mean` field in the error response for similar function names
2. Some functions may be disabled by the server profile — check `not_enabled` field
3. Use `get_structural_search_function_syntax` with the suggested name to confirm

### Session Not Found

**Problem:** Server returns `"Session not found"` error
**Cause:** The MCP server was restarted, invalidating the previous session
**Solution:** Reconnect the MCP server from Kiro's MCP panel (see Reconnection section above)

### Large Result Handling

**Problem:** Queries return truncated or very large results
**Solutions:**
1. Paginate with `page` parameter
2. For full datasets, check `metadata.full_data_file` for the cached file path
3. Use more specific filters to narrow results


## Steering File Index

| File | Load When |
|------|-----------|
| `steering/compatibility.md` | On activation — to check API surface version |
| `steering/impact-analysis.md` | User asks about change impact, dependencies, or regression testing |
| `steering/application-profiling.md` | User asks to profile, summarize, or understand an application |
| `steering/transaction-workflows.md` | User asks about transactions, API endpoints, or execution flows |
| `steering/quality-remediation.md` | User asks about quality, CVEs, security, ISO compliance |
| `steering/architecture-analysis.md` | User asks about architecture, layers, components, or structure |
| `steering/object-navigation.md` | User asks to find, search, or explore specific code objects |
| `steering/large-results.md` | Results are paginated or very large — need handling strategy |
| `steering/tagging-views.md` | User asks to tag objects, create views, or attach notes |
| `steering/meta-tool-functions.md` | Function Reference for Meta-Tool mode |
