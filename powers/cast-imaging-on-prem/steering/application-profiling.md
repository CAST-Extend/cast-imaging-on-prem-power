# Application Profiling

## Purpose

Profile an application to understand its technology mix, size, complexity distribution, and structural organization before diving into specific analysis.

## Workflow Steps

### Step 1: Basic Statistics

```
stats(application="<app>")
```

Returns:
- `nb_LOC`: Lines of code
- `nb_elements`: Total objects in the graph
- `nb_interactions`: Total relationships
- `technologies`: Languages and frameworks detected
- `element_types`: All object types found (C# Method, Java Class, etc.)
- `interaction_types`: All relationship types (CALL, INHERIT, ACCESS, etc.)
- `CRUD_interactions`: Data operations detected (get, post, put, delete)
- `custom_tags`: Tags applied to objects
- `Data_Sentitivity_levels`: Data sensitivity classifications

The response often includes `follow_up_tools` suggesting next steps like `transaction_profiles`, `data_graph_profiles`, `object_profiles`, `packages`.

### Step 2: Object Type Distribution

```
object_profiles(application="<app>")
```

Returns a breakdown by technological context and scope (internal vs external):
- For each technology context: object types with counts
- For internal objects: metrics like avg/min/max code lines, cyclomatic complexity
- Transaction participation counts

Use this to understand the technology mix and identify the dominant object types.

### Step 3: Architectural Overview

```
architectural_graph(application="<app>", level="component", mode="nodes")
```

Returns the architectural components and their roles. Start at `component` level for a high-level view, then drill into `sub-component` or `technology-category` for more detail.

For relationships between components:
```
architectural_graph(application="<app>", level="component", mode="links")
```

### Step 4: Transaction Profile

```
transaction_profiles(application="<app>")
```

Shows how transactions are distributed by size and type — useful for understanding the application's API surface.

### Step 5: Package Dependencies

```
packages(application="<app>")
```

Shows external libraries the application depends on.

## Presenting Results

Structure the profile as:

1. **Size**: LOC, element count, interaction count
2. **Technologies**: Languages, frameworks detected
3. **Composition**: Top object types by count (internal)
4. **Architecture**: Component-level structure
5. **API Surface**: Transaction count and types
6. **Dependencies**: External packages

## Tips

- Start broad (stats) and narrow down based on what's interesting
- Use architectural graph at `layer` level first for the highest abstraction
- If the user asks "what does this app do?", combine stats + transaction_profiles + architectural components for a comprehensive answer
