# Transaction Workflows

## Purpose

Transactions represent end-to-end execution flows (API endpoints, UI pages, batch processes). Use these workflows to discover, analyze, and trace transactions.

## Discovery

### List All Transactions

```
transactions(application="<app>")
```

Returns transactions with: id, name, fullname, type, reducedSize, fullSize.

### Filter Transactions

```
transactions(application="<app>", filters="name:contains:order")
transactions(application="<app>", filters="type:contains:post")
```

### Transaction Profiles

```
transaction_profiles(application="<app>")
```

Shows distribution by size and type.

## Detail Drill-Down

Use `transaction_details` with the transaction ID and a focus type.

### Progressive Drill-Down Strategy

For large transactions, always start with aggregated views:

1. **Start with `type_graph`** — shows object types and their connections (compact)
   ```
   transaction_details(application="<app>", id="<txn_id>", focus="type_graph")
   ```

2. **Then `complexity`** — shows the most complex objects (default: top 10)
   ```
   transaction_details(application="<app>", id="<txn_id>", focus="complexity")
   ```

3. **Then `complexity_graph`** — reduced graph centered on complex objects
   ```
   transaction_details(application="<app>", id="<txn_id>", focus="complexity_graph", focus_size=5)
   ```

4. **Only then `nodes`/`links`/`graph`** if the user needs the full call graph
   ```
   transaction_details(application="<app>", id="<txn_id>", focus="nodes")
   transaction_details(application="<app>", id="<txn_id>", focus="links")
   ```

### Available Focus Types

| Focus | Returns | Use When |
|-------|---------|----------|
| `type_graph` | Object types + connections (aggregated) | First look, understanding structure |
| `complexity` | Most complex objects in the graph | Finding hotspots |
| `complexity_graph` | Reduced graph around complex objects | Visualizing critical paths |
| `insights` | Objects with quality/performance issues | Quality investigation |
| `callers` | Incoming flows to transaction start | Understanding entry points |
| `callees` | Outgoing flows from transaction end | Understanding exit points |
| `nodes` | All objects in the call graph | Full detail (use sparingly) |
| `links` | All connections between objects | Full detail (use sparingly) |
| `graph` | Combined nodes + links | Full detail (use sparingly) |
| `documents` | Attached post-its/notes | Checking documentation |
| `summary` | AI-generated summary | Quick understanding |

### Full Call Graph

When the user explicitly asks for the full call graph:
```
transaction_details(application="<app>", id="<txn_id>", focus="graph", isfullcallgraph=True)
```

The `isfullcallgraph` flag returns the complete graph (vs the reduced default).

## Cross-Referencing

### Find Transactions for an Object

```
transactions_using_object(application="<app>", filters="id:eq:<object_id>")
```

or by name:
```
transactions_using_object(application="<app>", filters="name:contains:<name>")
```

## Tips

- `reducedSize` vs `fullSize`: Use reduced size as a complexity indicator. Large transactions (>100 objects in reduced graph) should use progressive drill-down.
- Always paginate — check `has_next` for large transaction lists
- Use `type_graph` as the default starting point — it gives structure without overwhelming detail
- Transaction names often indicate their HTTP method and path (e.g., "POST /api/orders")
