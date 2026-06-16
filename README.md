# CAST Imaging — Kiro Power

A Kiro Power that connects developers to [CAST Imaging](https://www.castsoftware.com/imaging) software intelligence via its MCP server. Enables structural code analysis, impact assessment, quality investigation, and architecture governance directly within Kiro.

## What It Does

This power gives Kiro access to the CAST Imaging graph — a structural model of your scanned applications including code objects, relationships, transactions, data flows, and quality insights. Use it to:

- Understand dependencies before making changes
- Assess impact and target regression tests
- Profile application structure and coupling
- Investigate quality hotspots and security findings
- Plan modernization and refactoring waves

## Installation

Install the power from the Kiro Powers panel. On first connection, you'll be prompted for your CAST Imaging API key.

## Structure

```
powers/cast-imaging-on-prem/
├── mcp.json                  # MCP server configuration (API key auth)
├── POWER.md                  # Power documentation and tool reference
└── steering/
    ├── application-profiling.md   # Application profiling workflows
    ├── architecture-analysis.md   # Architecture exploration guidance
    ├── compatibility.md           # API surface version detection
    ├── impact-analysis.md         # Change impact and dependency analysis
    ├── large-results.md           # Handling paginated/large result sets
    ├── meta-tool-functions.md     # Function catalog for Meta-Tool mode
    ├── object-navigation.md       # Finding and exploring code objects
    ├── quality-remediation.md     # Quality, CVE, ISO-5055 investigation
    ├── tagging-views.md           # Tagging objects, creating views, attaching notes
    └── transaction-workflows.md   # Transaction and endpoint flow analysis
```

## Prerequisites

- A CAST Imaging deployment with scanned applications
- An API key from your account settings

## Quick Start

After installation, ask Kiro:

> "Using CAST Imaging, list my applications"

Then explore with workflows like:

> "Show me the impact of changing the OrderService class in my-app"

> "What are the critical quality issues in my-app?"

> "Profile the transaction structure of my-app"

## Author

CAST Research Labs
