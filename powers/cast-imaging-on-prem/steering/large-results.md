# Large Result Handling

## Purpose

The MCP server paginates all list responses. This guide covers strategies for handling large result sets without overwhelming the agent's context or the user.

## Pagination Metadata

Every response includes:

```json
{
  "metadata": {
    "current_page": 1,
    "total_pages": 5,
    "items_per_page": 100,
    "total_items": 437,
    "has_next": true,
    "has_previous": false,
    "full_data_file": "/tmp/imaging_cache/<hash>.json"
  }
}
```

### Key Fields

| Field | Meaning |
|-------|---------|
| `has_next` | More pages available — never ignore this |
| `total_items` | Total result count across all pages |
| `total_pages` | How many pages exist |
| `full_data_file` | Server-side cache with complete dataset |

## Rules

1. **Never present partial results as complete** — always disclose when `has_next` is true
2. **Disclose totals** — tell the user "Showing 100 of 437 results"
3. **Offer to continue** — ask if user wants more pages or a summary

## Strategies

### Strategy 1: Summarize First Page

For exploratory queries (user browsing, not looking for specifics):
- Present first page results as a summary
- State total count: "Found 437 objects. Showing first 100."
- Ask if user wants to filter further or see more pages

### Strategy 2: Paginate for Completeness

When the user needs all results (e.g., impact analysis):
```
objects(application="<app>", filters="...", page=1)
objects(application="<app>", filters="...", page=2)
...
```
Continue until `has_next` is false.

### Strategy 3: Narrow with Filters

If results are too broad, help user add filters:
- Add type filter: `"name:contains:Order,type:contains:Method"`
- Add path filter: `"filepath:contains:controllers"`
- Use more specific operators: `equals` instead of `contains`

### Strategy 4: Use Aggregated Views First

For transactions and data graphs, use aggregated focuses before detailed ones:

| Instead of | Start with |
|------------|------------|
| `focus="nodes"` (all objects) | `focus="type_graph"` (aggregated by type) |
| `focus="links"` (all connections) | `focus="complexity_graph"` (reduced view) |
| `focus="graph"` (everything) | `focus="complexity"` (top N complex objects) |

This dramatically reduces result size while still providing structural insight.

### Strategy 5: Reference full_data_file

For reporting or export scenarios:
- Mention the `full_data_file` path from metadata
- This is a server-side cached JSON file with the complete dataset
- Useful when the user needs to process all data externally

## Large Transaction/Data Graph Handling

Transactions with `fullSize` > 100 objects should always use progressive drill-down:

1. `type_graph` — aggregated view (tens of items)
2. `complexity` — top complex objects (configurable via `focus_size`)
3. `complexity_graph` — reduced graph (manageable size)
4. `nodes`/`links`/`graph` — only if user explicitly requests full detail

## Tips

- Default page size is 100 items
- Check `total_items` to gauge if pagination is needed
- When summarizing, highlight the most relevant items (highest complexity, most connections, etc.)
- For cross-referencing (transactions using an object), results are typically smaller — full pagination is often feasible
- `follow_up_tools` in responses suggest logical next steps
