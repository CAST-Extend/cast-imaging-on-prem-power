# Object Navigation

## Purpose

Search for code objects, filter results, explore relationships, and drill into details using the object-related tools.

## Searching Objects

### Basic Search

```
objects(application="<app>", filters="name:contains:OrderService")
```

### Filter Syntax

Format: `"field:operator:value,field:operator:value"`

**Important**: All filters in one query must use the **same operator**.

### Available Fields

| Field | Description |
|-------|-------------|
| `name` | Object short name |
| `fullname` | Fully qualified name |
| `type` | Object type (e.g., "C# Method", "Java Class") |
| `filepath` | Source file path |
| `mangling` | Compiler-mangled name |
| `annotation` | Object annotations |

### Operators

| Operator | Behavior |
|----------|----------|
| `contains` | Substring match (default) |
| `equals` | Exact match |
| `startswith` | Prefix match |
| `endswith` | Suffix match |

### Filter Examples

```
# Find methods containing "get"
filters="name:contains:get,type:contains:method"

# Find exact class name
filters="name:equals:OrderService,type:equals:C# Class"

# Find by file path
filters="filepath:contains:controllers"

# Find by prefix
filters="name:startswith:Get,type:startswith:C#"
```

### Searching by Tag

```
objects(application="<app>", tags=["review-needed"])
```

### Searching by ID

```
objects(application="<app>", filters="id:eq:12345")
```

Note: `id` uses `eq` operator (not `equals`).

## Disambiguation

When multiple objects match a search, present to the user:

| Field | Purpose |
|-------|---------|
| `id` | Unique identifier for tool calls |
| `name` | Short name |
| `type` | Object type |
| `fullname` | Fully qualified path |
| `filepath` | Source file location |

Ask the user to select before proceeding with detail queries.

## Object Details

### Focus Types

```
object_details(application="<app>", filters="id:eq:<id>", focus="<focus>")
```

| Focus | Returns | Use When |
|-------|---------|----------|
| `intra` | Properties + children | Understanding internal structure |
| `inward` | Callers (who calls this) | Impact analysis upstream |
| `outward` | Callees (what this calls) | Understanding dependencies |
| `testing` | Transaction/data graph participation | Finding test coverage |
| `code` | Source code snippet | Reading the implementation |
| `insights` | Quality issues on this object | Quality investigation |
| `documents` | Attached post-its | Checking notes/documentation |
| `endpoints` | Start/end points connected | Understanding entry/exit |

## Object Relationships

Find how multiple objects relate to each other:

```
objects_relationships(application="<app>", object_ids=["1803", "1571", "2"])
```

Returns nodes and edges showing connections between the specified objects.

## Path and Hierarchy

### Pathfinder (Two Objects)

Find execution path from source to target:

```
pathfinder_hierarchy_details(application="<app>", source="<source_id>", target="<target_id>", focus="pathfinder")
```

### Hierarchy (Single Object)

Expand structure around an object:

```
pathfinder_hierarchy_details(application="<app>", source="<object_id>", focus="hierarchy", call_type="callee", hops=2)
```

**Critical**: These tools require numeric **IDs**, not names. Always resolve names to IDs first using `objects()`.

### Parameters

| Parameter | Values | Default |
|-----------|--------|---------|
| `call_type` | `callee`, `caller`, `all` | `callee` |
| `hops` | 1-10 | 1 |
| `external` | true/false | true |
| `main_objects_only` | true/false | false |

## Object Profiling

Get technology mix and complexity distribution:

```
object_profiles(application="<app>")
```

Returns breakdown by technological context with counts, avg/min/max code lines, and cyclomatic complexity.

## Tips

- Combine name + type filters for precision: `"name:contains:Order,type:contains:Class"`
- Objects are sorted by cyclomatic complexity (highest first) — page 1 shows the most complex
- Use `testing` focus to understand which transactions/data graphs an object participates in
- For large result sets, check `has_next` and paginate or summarize
- `id:eq` field has precedence over other filters when used
