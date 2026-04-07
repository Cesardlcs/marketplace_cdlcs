# Tool: Atlassian MCP Server

> Purpose: Defines how this repository uses your already configured Atlassian MCP server for Jira and Confluence operations.

---

## Reuse Existing Server Configuration

This project is designed to consume the Atlassian MCP server you already configured in your VS Code user profile.

- Do not create a duplicate server entry unless you need a different Atlassian site.
- Pass your existing server alias into the workflow runtime bindings.
- If no alias is provided, the workflow defaults to `atlassian-mcp`.

Runtime binding example:

```yaml
mcp_server_bindings:
  atlassian: "my-atlassian-server"
```

---

## How It Is Used In This Project

1. Orchestrator fetches Jira requirements during intake.
2. Orchestrator and Document Generator read or publish Confluence pages.
3. Any missing MCP connectivity is surfaced as a warning and the workflow can continue with manually provided inputs.

---

## Minimum Required Capabilities

- Jira: read issues, search issues
- Confluence: read pages
- Optional: create/update Confluence pages for HLD publishing

---

## Security Notes

- Keep credentials in secure environment variables or secrets manager.
- Use least-privilege permissions for the service account.
- Avoid storing tokens in repository files.
