# Implementation Classification Framework

> **Purpose:** This file defines the taxonomy, classification rules, and metadata schema used to classify designed solution components by implementation approach for Dynamics 365 Copilot Service workspace and Power Platform engagements.

Classification in this framework happens **after solution design** so that the design rationale and technical analysis are available.

---

## 1. Classification Dimensions

Each designed component must be classified across two mandatory dimensions:

| Dimension | Description |
|---|---|
| **Implementation Class** | How the designed component is delivered in the platform |
| **Complexity** | Relative implementation effort and risk for the selected approach |

---

## 2. Implementation Class Taxonomy

| Class | Code | Definition | Typical Indicators |
|---|---|---|---|
| Out of the Box | `OOB` | Native capabilities that exist immediately after installation. No setup beyond license and security role assignment. | Existing standard app behavior with no admin configuration needed |
| Configured OOB | `COOB` | Standard features configured through official Admin Center UI wizards and toggles. No build work required. | Enablement through settings, feature flags, routing setup, channel setup |
| Low-Code | `LC` | Requirement implemented using visual tools without pro-code. | Forms, model-driven configuration, tables and fields management, relationships, no custom code files |
| Advanced Low-Code | `ALC` | Advanced low-code implementations that integrate with external systems, APIs, or data sources. While these use visual tools (e.g., Connectors, Power Automate) instead of pro-code, they introduce external dependencies and architectural complexity that exceed simple internal data modeling. | Flos, connectors, external API orchestration, cross-system data contracts, complex retries and monitoring |
| Custom Code | `CUSTOM` | Requirement needs developer-written code (C#, JavaScript, TypeScript, .NET) because low-code is insufficient. | Plugins, PCF controls, custom web resources, Azure Functions with custom code-heavy logic |

---

## 3. When to Classify

Implementation class is assigned only when these inputs exist:

1. A completed solution design (or at least approved design sections for the requirement).
2. Design decisions and constraints are documented.
3. Integration and security implications are understood.

If these are missing, classification status must be `Needs Design Detail`.

---

## 4. Classification Rules

1. Each designed component gets **exactly one** implementation class: `OOB`, `COOB`, `LC`, `ALC`, or `CUSTOM`.
2. Classification is based on the **selected design approach**, not the original business wording.
3. If the chosen pattern includes external integration complexity, classify as `ALC` unless custom code is required.
4. If any required part cannot be delivered through configuration or low-code, classify as `CUSTOM`.
5. `OOB` and `COOB` must not include build artifacts (flows, custom tables, scripts, plugins).
6. Mixed solutions should be decomposed into separate components and classified independently.
7. Ambiguous designs must be marked `Needs Design Detail` and returned for architectural clarification.
8. Every classified component must have one complexity value: `LOW`, `MEDIUM`, or `HIGH`.

---

## 5. Complexity Scale

| Code | Meaning | Effort Anchor | Typical Delivery Time | Typical Indicators |
|---|---|---|---|---|
| `LOW` | Minimal effort and low delivery risk | < 1 day of configuration | Same sprint | Mostly OOB/COOB; straightforward LC with no significant dependencies |
| `MEDIUM` | Moderate effort or moderate risk | 1–5 days of build/config | 1–2 sprints | Multi-step LC/ALC; external dependency coordination; non-trivial data mapping |
| `HIGH` | High effort or high risk | > 5 days or multiple dependencies | Multiple sprints; DAB review recommended | CUSTOM code, complex ALC integrations, strict compliance/performance constraints |

### Complexity Calibration Notes

- **LOW**: Single feature toggle, basic field mapping, simple Power Automate trigger-action flow, straightforward configuration through UI. Expected delivery in the current sprint without blockers.
- **MEDIUM**: Multi-step workflow with decision logic, Dataverse table customization with relationships, connector-based integration with moderate error handling, Power BI dashboard. Expected delivery within 1–2 sprints assuming no external blockers.
- **HIGH**: Custom plugin or PCF control development, complex integration with multiple external systems, strict regulatory compliance logic (encryption, data residency), performance optimization for large datasets, architectural risks. Requires multiple sprints, stakeholder reviews, and may need Design Architecture Board (DAB) review before implementation.

---

## 6. Classification Record Schema

Each classified item must be recorded as:

```yaml
id: IMP-001
requirement_id: REQ-001
title: "Brief one-line title of designed component"
design_reference: "Section 5.3 - Unified Routing and Queues"
implementation_class: COOB
complexity: LOW
rationale: |
  Implemented through standard unified routing configuration in the admin UI.
  No custom development artifacts are required.
delivery_artifacts:
  - "Workstream configuration"
  - "Queue assignment rules"
external_dependencies: []
custom_code_required: false
status: Classified
notes: "Any caveat, assumption, or follow-up"
```

---

## 7. Output Format

The Implementation Classifier Agent produces an **implementation classification register**:

| ID | Requirement ID | Designed Component | Implementation Class | Complexity | Rationale | Status |
|---|---|---|---|---|---|---|
| IMP-001 | REQ-001 | Case summary feature enablement | OOB | LOW | Native feature enabled by licensing and role assignment | Classified |
| IMP-002 | REQ-002 | Unified routing rules | COOB | LOW | Configured via admin center workstream and queue settings | Classified |
| IMP-003 | REQ-003 | SAP customer sync orchestration | ALC | HIGH | Connector-based flow with external API dependency and retry policy | Classified |
| IMP-004 | REQ-004 | PII masking plugin | CUSTOM | HIGH | Requires Dataverse plugin for conditional logic beyond low-code limits | Classified |
| IMP-005 | REQ-005 | SLA automation flow | LC | MEDIUM | Implemented with Power Automate cloud flow, no code | Classified |

**Status values:** `Classified` | `Needs Design Detail` | `Deferred - Phase N` | `Out of Scope`
