# Architecture Analysis

## Purpose

Explore the application's structural organization at different abstraction levels — from high-level layers down to specific element types.

## Abstraction Levels

| Level | Granularity | Example |
|-------|-------------|---------|
| `layer` | Highest — functional layers | User Interaction, Data Access |
| `component` | Major functional areas | Web Interaction, Business Logic |
| `sub-component` | Specific concerns | Web Communication, Data Persistence |
| `technology-category` | By technology | JavaScript Communication, C# Business |
| `element-type` | Most granular | JavaScript Web Service Call, C# Method |

## Basic Usage

### Get Nodes (Components)

```
architectural_graph(application="<app>", level="component", mode="nodes")
```

### Get Links (Relationships Between Components)

```
architectural_graph(application="<app>", level="component", mode="links")
```

### Progressive Level Drill-Down

Start broad and narrow down:
1. `layer` — understand the big picture
2. `component` — see functional areas
3. `sub-component` — identify specific concerns
4. `technology-category` — understand technology boundaries
5. `element-type` — most detailed view

## Focused Views

Zoom into a specific area:

```
architectural_graph_focus(application="<app>", area="communication", level="sub-component", mode="nodes")
```

The `area` parameter filters to show only the specified architectural area and its children.

## Layout Options

When positions are needed (for visualization):

| Layout | Best For |
|--------|----------|
| `none` | Raw data, no positions (default) |
| `spring` | General-purpose force-directed |
| `kamada_kawai` | Emphasizes path distances |
| `spectral` | Good for clustered structures |
| `shell` | Concentric layout |

```
architectural_graph(application="<app>", level="component", mode="nodes", layout="spring")
```

## Workflow Patterns

### Understanding App Structure

1. Start with `layer` level nodes to see functional layers
2. Check links between layers to understand data flow direction
3. Drill into the most interesting layer at `component` level

### Identifying Technology Boundaries

1. Use `technology-category` level to see all technology contexts
2. Check links between technology categories to find integration points
3. Focus on cross-technology links — these are often migration or modernization concerns

### Finding Communication Patterns

1. Use `architectural_graph_focus` with `area="communication"` (or similar)
2. Check which components initiate vs receive communication
3. Cross-reference with transactions to understand API boundaries

## Tips

- `nodes` mode gives you the components themselves; `links` mode gives you the relationships
- You usually want both — call nodes first to understand what exists, then links to see how they connect
- The paginated response applies here too — large applications may have many architectural elements
- Use this as a starting point before drilling into specific objects or transactions
