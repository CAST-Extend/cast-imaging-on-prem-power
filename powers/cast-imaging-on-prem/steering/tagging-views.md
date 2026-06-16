# Tagging and Views

## Purpose

Tag objects to organize findings, create saved views to preserve useful object groupings, and attach documentation for team context sharing.

## Object Tagging

### Individual Tagging (Known IDs)

When you have specific object IDs:

```
manage_object_tags(application="<app>", add_tags=["review-needed", "critical"], nodes_for_add=["8216", "12688"])
```

To remove tags:
```
manage_object_tags(application="<app>", delete_tags=["fixed"], nodes_for_delete=["8216"])
```

### Bulk Tagging (By Rule)

When the target set is defined by a rule (filter, transaction, data graph):

**Step 1: Always preview first**
```
bulk_manage_object_tags(application="<app>", add_tags=["api-hotspot"], filters="type:contains:Controller Action")
```

This returns a preview of affected objects — **never execute without showing preview to user first**.

**Step 2: Ask for confirmation**

Show the preview result and ask: "This will tag X objects. Proceed?"

**Step 3: Execute after approval**
```
bulk_manage_object_tags(application="<app>", add_tags=["api-hotspot"], filters="type:contains:Controller Action", execute=True, confirmed=True)
```

### Bulk Tagging Selectors

| Selector | Example |
|----------|---------|
| `filters` | `"name:contains:abc,type:contains:Java Method"` |
| `filter_tags` | Existing tags objects must have |
| `transaction_ids` | Objects in these transactions |
| `transaction_names` | Objects in transactions matching names |
| `data_graph_ids` | Objects in these data graphs |
| `data_graph_names` | Objects in data graphs matching names |

**Critical Rules:**
- Always preview first (default `execute=False`)
- Never auto-execute after preview — ask user for approval
- Approval for one operation does not carry to another
- Each bulk add/delete is a separate confirmation

## Saved Views

### List Views

```
views(application="<app>")
views(application="<app>", sort_key="createdAt", sort_order="DESC")
```

### Get View Details

```
view_details(application="<app>", id="<view_id>")
```

### Create a View

```
manage_view(application="<app>", operation="create", name="My Analysis View", object_ids=["1752", "1751", "1753"])
```

**Important**: If the user provides object names instead of IDs:
1. Search for each name using `objects()`
2. If ambiguous (multiple matches), disambiguate with user
3. Only create the view after collecting confirmed IDs

### Update a View

```
manage_view(application="<app>", operation="update", id="<view_id>", name="Updated View", object_ids=["1744", "1743", "1742"])
```

### Delete a View

```
manage_view(application="<app>", operation="delete", id="<view_id>")
```

**Always inform the user** before deletion — this is irreversible. Only custom views can be deleted.

## Document Attachment

### Attach to an Object

```
add_object_document(application="<app>", id="<object_id>", title="Code Review Note", description="This method needs refactoring due to high cyclomatic complexity.")
```

### Attach to a View (Transaction or Data Graph)

```
add_view_document(application="<app>", id="<view_id>", view_type="transaction", title="Analysis Summary", description="This transaction has 3 critical security flaws.")
```

`view_type` can be `"transaction"` or `"datagraph"`.

## Safety Rules

1. **Bulk tag operations**: Always preview → show user → get explicit confirmation → execute
2. **View deletion**: Always inform user before proceeding
3. **View creation**: Resolve all object names to IDs before creating
4. **Never assume**: If a previous approval was for a different operation, ask again
