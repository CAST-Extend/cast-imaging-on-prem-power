# Quality Remediation

## Purpose

Investigate quality issues, security vulnerabilities, and compliance violations to prioritize remediation work.

## Quality Insight Categories

| Nature | Description |
|--------|-------------|
| `cve` | Common Vulnerabilities and Exposures |
| `cloud-detection-patterns` | Cloud maturity/migration blockers |
| `green-detection-patterns` | Green/sustainability deficiencies |
| `structural-flaws` | Structural flaw detection patterns |
| `iso-5055` | ISO 5055 compliance violations |

## Workflow Steps

### Step 1: Discover Quality Issues

```
quality_insights(application="<app>", nature="cve")
quality_insights(application="<app>", nature="structural-flaws")
quality_insights(application="<app>", nature="iso-5055")
```

Returns a list of insight categories with names, descriptions, and violation counts.

### Step 2: Drill into Violations

Get the impacted objects for a specific insight:

```
quality_insight_violations(application="<app>", nature="iso-5055", id="<insight_id>")
```

### Step 3: Get Detailed Locations

For file-level and line-level detail (with code snippets if available):

```
quality_insight_violations(application="<app>", nature="cve", id="<cve_id>", include_locations=True)
```

This returns:
- File paths
- Line numbers
- Context/code snippets (if the server has code access enabled)

### ISO 5055 Exploration

#### List Characteristics

```
application_iso_5055_explorer(application="<app>")
```

Returns ISO 5055 characteristics (Security, Reliability, Performance Efficiency, Maintainability).

#### Get Weaknesses for a Characteristic

```
application_iso_5055_explorer(application="<app>", characteristic_id="ISO-5055-Security")
```

Returns specific weaknesses and their violation counts.

### Cross-Application Quality Overview

```
applications_quality_insights(insights="cve,structural-flaws")
```

Compares quality across all applications. Use comma-separated insight types to filter.

## Prioritization Guidance

When presenting quality findings to the user:

1. **Severity first**: CVEs and security issues before structural flaws
2. **Count matters**: Higher violation counts indicate systemic issues
3. **Location context**: Show which files/modules are most affected
4. **Transaction impact**: Cross-reference problematic objects with transactions to understand business impact

## Tips

- Start with `quality_insights` to see the big picture before drilling in
- Use `include_locations=True` sparingly — it returns more data and is slower
- The `id` field for violations comes from the initial `quality_insights` response
- For ISO 5055, the characteristic IDs are typically: `ISO-5055-Security`, `ISO-5055-Reliability`, `ISO-5055-Performance-Efficiency`, `ISO-5055-Maintainability`
- Paginate results — quality insights can have many violations
