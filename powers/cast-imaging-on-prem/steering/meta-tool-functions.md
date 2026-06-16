---
inclusion: manual
---

# CAST Imaging — Meta-Tool Mode - Functions Reference

Complete catalog of all structural-search functions organized by category. Use this to discover what's available before calling functions.

> **MCP Resource equivalents:** `structural-search://functions/summary`, `structural-search://functions/catalog`

---

## How to Use Functions

1. Find the function you need in the catalog below
2. Call `get_structural_search_function_syntax` with one or more function names to get full parameter details
3. Call `run_structural_search_function` with `function_name` and `arguments` to execute

**Error handling:** If you call a wrong function name, the server returns:
- `did_you_mean`: list of similar function names
- `available_functions_count`: total number of available functions
- `not_enabled`: functions disabled by the active server profile

---

## Function Catalog by Category

### Portfolio Discovery

| Function | Purpose |
|----------|---------|
| `applications` | List all applications with status, delivery info. Start here. |
| `applications_transactions` | Transactions from all applications (cross-app listing) |
| `applications_data_graphs` | Data graphs from all applications (cross-app listing) |
| `applications_quality_insights` | Quality insights across all applications |
| `applications_dependencies` | Inter-application dependencies |

---

### Application Overview

| Function | Purpose |
|----------|---------|
| `stats` | Basic stats: element count, interaction count, technologies |
| `object_profiles` | Object type distribution for an application |
| `transaction_profiles` | Transaction type profiles for an application |
| `data_graph_profiles` | Data graph type profiles for an application |

---

### Objects & Source Files

| Function | Purpose |
|----------|---------|
| `objects` | Search objects with name/type/path/annotation filters. Supports `tags` filtering. Results sorted by cyclomatic complexity (highest first). |
| `object_details` | Object drill-down with focus: `intra`, `inward`, `outward`, `testing`, `code`, `insights`, `documents`, `endpoints` |
| `source_files` | Find source files by path substring match |
| `source_file_details` | Source file details by nature: `inventory`, `intra`, `inward`, `outward`, `testing` |

---

### Transactions & Data Graphs

| Function | Purpose |
|----------|---------|
| `transactions` | List transactions with name/type filters |
| `transaction_details` | Transaction drill-down with focus: `complexity`, `insights`, `nodes`, `links`, `graph`, `type_graph`, `complexity_graph`, `documents`, `summary`, `callers`, `callees`. Supports `full_call_graph` flag. |
| `transactions_using_object` | Find transactions that use specific object(s) |
| `data_graphs` | List data graphs with name/type filters |
| `data_graph_details` | Data graph drill-down with focus: `complexity`, `insights`, `nodes`, `links`, `graph`, `type_graph`, `complexity_graph`, `documents`, `summary` |
| `data_graphs_involving_object` | Find data graphs involving specific object(s) |

---

### Architecture & Dependencies

| Function | Purpose |
|----------|---------|
| `architectural_graph` | Architectural graph at level: `layer`, `component`, `sub-component`, `technology-category`, `element-type`. Mode: `nodes` or `links`. Optional layout: `spring`, `kamada_kawai`, `spectral`, `shell`, `none`. |
| `architectural_graph_focus` | Architectural graph focused on a specific area |
| `objects_relationships` | Relationship graph between multiple objects by their IDs. Supports `link_mode`: `default`, `escalated`, `all`. |
| `pathfinder_hierarchy_details` | Find execution paths between objects (`focus: "pathfinder"`) or expand hierarchical structure (`focus: "hierarchy"`). Supports `call_type`, `hops`, `external`, `main_objects_only`. |

---

### Quality & Security

| Function | Purpose |
|----------|---------|
| `quality_insights` | Quality insights by nature: `cloud-detection-patterns`, `green-detection-patterns`, `cve`, `structural-flaws`, `iso-5055` |
| `quality_insight_violations` | Violations for a specific insight with optional `include_locations=true` for file/line details |
| `application_iso_5055_explorer` | ISO 5055 characteristics and weaknesses explorer. Supports `characteristic_id` for drill-down. |

---

### Database & Packages

| Function | Purpose |
|----------|---------|
| `packages` | List external packages/libraries used by an application |
| `package_interactions` | How an application uses a specific package (requires `component` and `version`) |
| `application_database_explorer` | Explore database tables and columns. Supports `table_name` for column details, `name_filter` for table filtering. |

---

### Documentation & Tagging

| Function | Purpose |
|----------|---------|
| `manage_object_tags` | Add/delete tags on explicitly identified object IDs. Use when you already have the IDs. |
| `bulk_manage_object_tags` | Bulk tag by rule/filter. **Always preview first** (default), then ask user approval before `execute=True, confirmed=True`. Supports `filters`, `filter_tags`, `transaction_ids`, `transaction_names`, `data_graph_ids`, `data_graph_names`. |
| `add_object_document` | Attach a post-it/note to a code object by ID |
| `add_view_document` | Attach a post-it/note to a transaction or data graph by ID. Requires `view_type`: `transaction` or `datagraph`. |

---

### Views & Customization

| Function | Purpose |
|----------|---------|
| `views` | List saved views for an application. Supports `sort_key` and `sort_order`. |
| `view_details` | Get view structure: metadata, canvas, graph nodes/edges |
| `manage_view` | Create/update/delete a saved view. Operations: `create` (requires `name`, `object_ids`), `update` (requires `view_id`, `name`, `object_ids`), `delete` (requires `view_id`). |

---

### Guidance & Help

| Function | Purpose |
|----------|---------|
| `get_mcp_info` | MCP server version, Imaging API version, compatibility status |

---

## Common Parameters

| Parameter | Description | Notes |
|-----------|-------------|-------|
| `application` | Application name (exact match from `applications`) | Required for most functions — never guess |
| `filters` | Query string: `"field:operator:value,field:operator:value"` | All filters in one query must use the same operator |
| `page` | Page number for pagination (default: 1) | Responses include `has_next` in metadata |
| `focus` | View type for drill-down functions | Varies by function — see individual descriptions |

### Filter Operators

| Operator | Meaning | Example |
|----------|---------|---------|
| `contains` | Substring match (default) | `type:contains:method` |
| `equals` | Exact match | `type:equals:Cobol Transactional Program` |
| `startswith` | Prefix match | `name:startswith:get` |
| `endswith` | Suffix match | `name:endswith:Controller` |
| `eq` | Exact match (for `id`, `searchtype`, `select_calls`) | `id:eq:12345` |

### Special Filter Fields

| Field | Values | Usage |
|-------|--------|-------|
| `searchtype` | `external`, `internal` | Filter by object search scope |
| `select_calls` | `bookmarks` | Use with `inward`/`outward` focus in `object_details` |
| `insight_type` | `iso-5055`, `cloud`, `green`, `structural-flaw` | Use with `object_details` focus=`insights`. Multiple values with `\|` separator. |

---

## Quick Discovery Workflow

```
1. Call get_structural_search_function_syntax(["applications"])
   → See full parameter details and sample questions

2. Call run_structural_search_function("applications", {})
   → List available applications

3. Call run_structural_search_function("stats", {"application": "MyApp"})
   → Get application overview
```

---

## Sample Questions → Function Mapping

| Question | Function |
|----------|----------|
| "What applications are available?" | `applications` |
| "Give me an overview of this app" | `stats` |
| "What object types exist?" | `object_profiles` |
| "Find objects named OrderController" | `objects` |
| "Who calls this method?" | `object_details` (focus: `inward`) |
| "What does this class call?" | `object_details` (focus: `outward`) |
| "What transactions exist?" | `transactions` |
| "Show me this transaction's structure" | `transaction_details` (focus: `type_graph`) |
| "What quality issues exist?" | `quality_insights` |
| "Show me the architecture" | `architectural_graph` |
| "What data flows exist?" | `data_graphs` |
| "Which transactions use this object?" | `transactions_using_object` |
| "How are these objects connected?" | `objects_relationships` |
| "Find the path between A and B" | `pathfinder_hierarchy_details` (focus: `pathfinder`) |
| "What packages does this app use?" | `packages` |
| "Show database tables" | `application_database_explorer` |
| "What's the MCP server version?" | `get_mcp_info` |

---

## Function Aliases

Some functions have aliases that the server also recognizes:

| Function | Aliases |
|----------|---------|
| `quality_insight_violations` | `quality_insight_occurrences` |
| `application_database_explorer` | `database_explorer` |
| `views` | `static_views`, `saved_views`, `list_views`, `list_saved_views`, `show_saved_views` |
| `view_details` | `static_view_details`, `saved_view_details`, `get_saved_view_details` |
| `manage_view` | `manage_static_view`, `create_view`, `delete_view`, `update_view`, `create_saved_view`, `delete_saved_view` |
| `manage_object_tags` | `tag_objects`, `tag_specific_objects`, `apply_tags_to_objects_based_on_ids` |
| `bulk_manage_object_tags` | `bulk_tag_objects`, `bulk_tagging`, `bulk_apply_tags`, `tag_objects_by_filter`, `tag_objects_by_rule` |
| `add_view_document` | `add_transaction_post_it`, `add_data_graph_post_it`, `add_transaction_document`, `add_data_graph_document` |
| `application_iso_5055_explorer` | `iso_5055_explorer` |

---

## Notes on Server Profile

- Some functions may be disabled by the active server profile configuration
- `my_applications` exists but may be disabled — use `applications` as the universal fallback
- If a function returns a `not_enabled` error, it means the function exists but is turned off in the current deployment
