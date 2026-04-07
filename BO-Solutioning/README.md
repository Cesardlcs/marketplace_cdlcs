# BO-Solutioning

> **AI-assisted high-level solution design for Dynamics 365 Copilot Service workspace and Power Platform**

This repository provides a structured, agent-driven framework for producing **High-Level Design (HLD)** documents for Dynamics 365 and Power Platform engagements. It is intended for Solutions Architects who need to analyze requirements, design solutions, and then classify each designed component by implementation approach (OOB/COOB/LC/ALC/CUSTOM).

---

## Purpose

- Capture and analyze business requirements for D365 Copilot Service workspace and Power Platform subdemands
- Design high-level solution architectures following best practices
- Enforce Microsoft-platform architecture principles covering extensibility, upgrade path, release waves, integration patterns, licensing, and platform limits
- Classify designed solution elements by implementation approach: OOB, COOB, LC, ALC, CUSTOM
- Generate structured, customer-ready HLD deliverable documents
- Integrate with **Atlassian** (Jira/Confluence) and **Microsoft Dataverse** via MCP servers

---

## Architecture Principles Enforced By The Workflow

- Decompose business requirements into actionable technical components before design and classification.
- Balance extensibility, performance, supportability, and cost in every architecture decision.
- Always evaluate upgrade path, future extensibility, and release-wave impact before recommending platform features.
- Distinguish clearly between first-party Dynamics 365 capabilities and custom solutions built on Dataverse.
- Choose data models deliberately: standard tables first, custom tables only with clear justification and lifecycle impact.
- Apply only platform-supported performance optimizations for Dataverse and Power Platform; do not propose unsupported database manipulation.
- Select the right integration pattern for the requirement: native connectors, Dataverse Web API, Azure Service Bus, Azure Logic Apps, Power Automate, or middleware.
- Decide synchronous vs. asynchronous integrations explicitly based on latency, resiliency, throughput, and failure isolation needs.
- Never recommend approaches that violate Microsoft platform policies, create unsupported customizations, or weaken data security.
- Call out Preview capabilities explicitly and treat them as non-production-ready unless the user accepts that risk.
- Surface licensing impact proactively whenever feature choice, connector choice, or architecture pattern changes cost or entitlement.

---

## Repository Structure

```
BO-Solutioning/
├── README.md                                      # This file
│
├── design/
│   └── solution-design-specification.md           # Methodology and principles for designing solutions
│
├── classification/
│   └── implementation-classification.md           # Framework for implementation approach classification
│
├── templates/
│   └── high-level-solution-document.md            # HLD deliverable template
│
├── output/                                        # Generated HLD documents land here
│
├── agents/
│   ├── orchestrator.md                            # Orchestrator agent that coordinates the workflow
│   ├── solution-designer-agent.md                 # Produces the solution architecture
│   ├── implementation-classifier-agent.md         # Classifies designed components as OOB/COOB/LC/ALC/CUSTOM
│   ├── document-generator-agent.md                # Assembles the final HLD document
│   └── revision-agent.md                          # Performs blocking AC coverage review (source vs generated HLD)
│
├── subagents/
│   ├── d365-copilot-service-specialist.md         # Expert in D365 Copilot Service workspace
│   ├── ai-specialist.md                           # Expert in D365 Copilot AI features, generative AI, responsible AI
│   ├── power-platform-specialist.md               # Expert in Power Platform components
│   └── integration-specialist.md                  # Expert in integration patterns and connectors
│
├── skills/
│   ├── requirements-analysis.md                   # Skill: analyze and decompose requirements
│   ├── solution-architecture.md                   # Skill: design architectures
│   └── document-authoring.md                      # Skill: author solution documents
│
└── tools/
    ├── atlassian-mcp.md                           # Atlassian MCP server configuration and usage
    └── dataverse-mcp.md                           # Dataverse MCP server configuration and usage
```

---

## Workflow Overview

```
[Requirements Input]
        │
        ▼
[Source Alignment Step]
   primary source: BO Confluence page -> Business Acceptance Criteria
        Jira scope: linked issues from objective filtered by `jira_status_filter` (default: `SPECIFIED`)
   include: comments from each included linked issue
   gate: user confirms plan + issue list before solutioning
        │
        ▼
[Solution Designer Agent]
   uses: requirements-analysis + solution-architecture skills
   calls: d365-copilot-service-specialist | ai-specialist | power-platform-specialist | integration-specialist
   references: design/solution-design-specification.md
        │
        ▼
[Implementation Classifier Agent]
   classifies designed components by: OOB | COOB | LC | ALC | CUSTOM
   references: classification/implementation-classification.md
        │
        ▼
[Document Generator Agent]
   uses: document-authoring skill
   fills: templates/high-level-solution-document.md
        writes to: output/[engagement_name]/ (overwrite if existing)
        │
        ▼
[Revision Agent - Blocking Gate]
   compares: BO/Jira AC source inventory vs generated HLD rows
   rule: in-scope AC coverage must be 100%
   if fail: orchestrator triggers regeneration with remediation payload
   writes: [engagement_name]-AC-COVERAGE.md and [engagement_name]-AC-COVERAGE.json
        │
        ▼
[Final HLD Document in output/]
```

---

## MCP Server Integrations

| Server | Purpose | Config File |
|---|---|---|
| **Atlassian MCP** | Read Jira issues, Confluence pages; write back results | `tools/atlassian-mcp.md` |
| **Dataverse MCP** | Query/retrieve Dataverse entities, metadata, model-driven app config | `tools/dataverse-mcp.md` |

### Use Existing MCP Servers (Recommended)

This project is designed to use MCP servers that are already configured in your VS Code user profile.

1. Keep your existing server setup in VS Code user settings.
2. Pass server aliases to the workflow as runtime bindings:

```yaml
mcp_server_bindings:
        atlassian: "<your-existing-atlassian-server-alias>"
        dataverse: "<your-existing-dataverse-server-alias>"
```

3. The orchestrator and agents treat these aliases as the active tool endpoints for the current run.

If aliases are not supplied, the workflow falls back to default names (`atlassian-mcp`, `dataverse-mcp`).

---

## Getting Started

1. **Provide BO and Jira objective** -> Share the BO Confluence page and Jira objective page.
2. **Source filtering** -> Use BO Business Acceptance Criteria as primary baseline and keep linked Jira issues matching `jira_status_filter` (default: `SPECIFIED`) including their comments.
3. **Confirm before design** -> Confirm understanding and the exact linked issue list with the user before running solutioning.
4. **Design the solution** -> Run the `solution-designer-agent` using BO acceptance criteria + filtered subdemands context, enforcing release-wave awareness, upgrade-safe customization, supported integration patterns, and platform-limit checks.
5. **Classify implementation approach** -> Run the `implementation-classifier-agent` using the produced solution design and `classification/implementation-classification.md`.
6. **Generate the HLD** -> Run the `document-generator-agent` to populate `templates/high-level-solution-document.md` and save the result to `output/[engagement_name]/`, overwriting if file already exists.
7. **Check architecture disclosures** -> Ensure the generated outputs explicitly mention release-wave impact, upgrade path, preview status, licensing implications, platform limits, and important native-platform limitations where relevant.
8. **Run blocking AC coverage review** -> Run the `revision-agent` to compare BO/Jira source AC inventory against generated HLD rows. If coverage fails, regenerate HLD and re-run review.

Each agent file documents its own invocation contract (inputs, outputs, tools, and skills it uses).

---

## Key Files Reference

| File | Description |
|---|---|
| `design/solution-design-specification.md` | Defines **how** to design: principles, patterns, decision criteria |
| `classification/implementation-classification.md` | Taxonomy and rules for **implementation approach classification** |
| `templates/high-level-solution-document.md` | The **deliverable template** used to produce the HLD |
| `agents/revision-agent.md` | Blocking AC traceability and coverage quality gate |
| `output/` | **Generated documents** folder — includes HLD, summary, delegation, and AC coverage artifacts per engagement |
