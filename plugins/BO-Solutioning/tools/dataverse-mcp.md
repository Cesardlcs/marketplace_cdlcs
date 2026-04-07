# Tool: Dataverse MCP Server

> Purpose: Defines how this repository uses your already configured Dataverse MCP server for environment context retrieval.

---

## Reuse Existing Server Configuration

This project is designed to consume the Dataverse MCP server you already configured in your VS Code user profile.

- Do not create a duplicate server entry unless you target a different Dataverse environment.
- Pass your existing server alias into the workflow runtime bindings.
- If no alias is provided, the workflow defaults to `dataverse-mcp-devcc`.

Runtime binding example:

```yaml
mcp_server_bindings:
  dataverse: "my-dataverse-server"
```

---

## How It Is Used In This Project

1. Orchestrator retrieves metadata and existing solution context before design.
2. Solution and subagents consume that context to reduce design assumptions.
3. Workflow continues with a warning if the MCP server is unavailable.

---

## Recommended Access Model

- Prefer read-only access for architecture and discovery operations.
- Grant write access only when a controlled implementation phase requires it.
- Use service principals and least privilege.

---

## Security Notes

- Do not commit secrets in repository files.
- Keep connection details in environment variables or secure vaults.
- Restrict environment access to approved service identities.
