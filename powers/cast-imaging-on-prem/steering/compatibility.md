# API Surface Compatibility

## Purpose

The CAST Imaging Express MCP server may evolve its API surface over time. This power targets the **v1 direct-tool surface**. On activation, verify compatibility before proceeding.

## v1 Surface Fingerprint (Current — Supported)

The server exposes direct, purpose-built tools. Check for these:

**Required tools (must all be present):**
- `all_applications`
- `objects`
- `object_details`
- `transactions`
- `transaction_details`
- `quality_insights`
- `stats`

If all are available → proceed normally with this power's guidance.

## v2 Surface Fingerprint (Future — Incompatible)

The server uses a meta-tool pattern with a function catalog. Indicators:

**Presence of either:**
- `run_text2graph_function`
- `get_text2graph_function_syntax`

If detected → this power's steering files are **not compatible** with the v2 surface.

## Detection Procedure

1. After MCP connection is established, check which tools are available
2. Look for v1 fingerprint tools first
3. If v1 tools are missing, check for v2 indicators
4. Issue appropriate message based on findings

## Warning Templates

### v2 Surface Detected

```
⚠️ Server API Incompatibility Detected

The CAST Imaging Express MCP server is exposing a meta-tool API surface 
(run_text2graph_function, get_text2graph_function_syntax) that this power 
does not support.

This power targets the direct-tool surface (all_applications, objects, 
transactions, etc.). Steering file guidance may produce incorrect results.

Action: Please update this power to the latest version, or contact support.
```

### Unknown Surface

```
⚠️ Unknown Server API Surface

The CAST Imaging Express MCP server is not exposing the expected tools.

Expected (v1): all_applications, objects, object_details, transactions, 
               transaction_details, quality_insights, stats
Found: [list actual tools found]

This power may not be compatible with this server version.

Action: Please check the server configuration or update this power.
```

## Graceful Degradation

If incompatibility is detected:
- Issue the warning to the user
- The agent may still attempt to use whatever tools are available
- Caveat all responses: "Note: This guidance is based on the v1 tool surface which may not match your server."
- Recommend the user update the power or verify their server version
