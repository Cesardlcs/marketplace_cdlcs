# Tool: Microsoft Docs MCP Server

> Purpose: Defines how this repository uses your already configured Microsoft Docs MCP server for official Microsoft product research, validation, and design confirmation as one important source alongside BO, Jira, Dataverse, existing solution artifacts, and architect inputs.

---

## Reuse Existing Server Configuration

This project is designed to consume the Microsoft Docs MCP server you already configured in your VS Code user profile.

- Do not create a duplicate server entry unless you need a separate endpoint or custom alias.
- Pass your existing server alias into the workflow runtime bindings.
- If no alias is provided, the workflow defaults to `microsoftdocs-mcp`.

Runtime binding example:

```yaml
mcp_server_bindings:
  msdocs: "my-microsoftdocs-server"
```

---

## How It Is Used In This Project

1. Orchestrator uses it during intake to confirm product terminology, supported capabilities, and obvious constraints before solution design starts.
2. Solution Designer and specialist subagents use it to research official Microsoft guidance, product fit, prerequisites, licensing indicators, and design boundaries.
3. Implementation Classifier can use it to confirm whether a capability is truly OOB/COOB/LC/ALC/CUSTOM before final classification.
4. Microsoft Docs is used as a validation and confirmation source, not as the sole source of design truth.
5. Workflow continues with a warning if the MCP server is unavailable, but any areas not validated against Microsoft documentation should be called out as confidence gaps.

---

## Recommended Usage Pattern

Use Microsoft Docs MCP to validate platform behavior and design assumptions after considering the BO, Jira scope, Dataverse reality, customer constraints, and architect notes.

1. Search official docs first to find relevant product guidance.
2. Fetch full pages when the design depends on prerequisites, limitations, configuration details, or policy language.
3. Use official code-sample search only when implementation examples materially improve the design decision.
4. Record where Microsoft guidance materially changed or confirmed the proposed design.

---

## Minimum Required Capabilities

- Search Microsoft Learn and official Microsoft documentation
- Fetch full Microsoft documentation pages for deeper validation
- Optional: retrieve official code samples for Microsoft technologies when design detail requires it

---

## Security Notes

- Prefer official Microsoft sources over community blogs for design validation.
- Do not treat MCP search output as customer approval; it is validation context only.
- Do not store credentials or tokens in repository files.