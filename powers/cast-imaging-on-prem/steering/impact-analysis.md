# Impact Analysis Workflow

## Purpose

Before changing code, assess the blast radius: what depends on the target object, which transactions flow through it, and what needs testing afterward.

## Workflow Steps

### Step 1: Find the Target Object

Search for the object the user wants to change:

```
objects(application="<app>", filters="name:contains:<target_name>")
```

If multiple results come back, **disambiguate**: present id, name, type, fullname, filepath for each and ask the user to select.

### Step 2: Get Callers (Who depends on this?)

Once you have the object ID:

```
object_details(application="<app>", filters="id:eq:<object_id>", focus="inward")
```

This returns all objects that call/reference the target — the "upstream" dependents that will be affected by the change.

### Step 3: Get Callees (What does this depend on?)

```
object_details(application="<app>", filters="id:eq:<object_id>", focus="outward")
```

This shows what the target calls — useful for understanding if the change will affect downstream behavior.

### Step 4: Find Affected Transactions

```
transactions_using_object(application="<app>", filters="id:eq:<object_id>")
```

Transactions represent end-to-end execution flows (API endpoints, UI paths). Any transaction flowing through the changed object is a regression test candidate.

### Step 5: Find Affected Data Graphs

```
data_graphs_involving_object(application="<app>", filters="id:eq:<object_id>")
```

Data graphs show data entity interactions. If the changed object participates in data flows, those flows need validation.

### Step 6: Deeper Path Analysis (Optional)

For understanding the full call chain:

```
pathfinder_hierarchy_details(application="<app>", source="<object_id>", focus="hierarchy", call_type="caller", hops=3)
```

This expands the caller tree up to 3 levels deep.

## Presenting Results

Structure the impact report as:

1. **Target object**: Name, type, file path
2. **Direct callers** (inward): Objects that will be affected
3. **Direct callees** (outward): Dependencies of the target
4. **Affected transactions**: End-to-end flows that need regression testing
5. **Affected data graphs**: Data flows that need validation
6. **Recommended test scope**: List transactions as test targets

## Tips

- Always start with `type_graph` focus for transaction details before drilling to individual `nodes` (avoids overwhelming context for large transactions)
- If the object has many callers, use pagination — check `has_next` in the response
- For broad changes (e.g., a utility class), the impact may be very large — summarize by category rather than listing every single caller
