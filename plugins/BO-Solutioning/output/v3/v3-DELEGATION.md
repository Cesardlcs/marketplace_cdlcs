# Requirement Delegation and Solution Rationale

## Overview

- BO Name: [BO] 2.1 Account Management / 2.2. Asset management
- Jira Objective: DDP-59197
- Engagement: v3
- Customer: test
- Status Filter Applied: SPECIFIED
- Generation Date: 2026-04-07

This file explains two things in plain language:

1. Which specialist agent was used for each requirement area.
2. Why that agent and solution approach were chosen.

The goal is traceability for the reader, not technical exhaustiveness.
This run uses mixed granularity. Some areas were delegated as full DDPs, while Home Connect was split further so UI/form-governance acceptance criteria stayed with the Customer Service specialist and integration-owned acceptance criteria stayed with the Integration specialist.

## At A Glance

- Asset creation and editing was routed to the Customer Service specialist because the work is primarily model-driven app behavior and Dataverse form governance.
- Coverage visibility was routed to the Power Platform specialist because the solution depends on low-code shaping of appliance-related coverage data.
- Home Connect was not delegated as one block: UI visibility and read-only controls were handled separately from integration synchronization and status mapping.
- The split delegation avoids forcing the integration specialist to own standard form behavior and avoids forcing the Customer Service specialist to own synchronization logic.

## Requirement Areas

### DDP-59199: Customer Asset Creation, Validation, Visibility, and Editing

**Delegation level:** DDP

**Delegated to:** `d365-copilot-service-specialist`  
**Primary domain:** D-CSW, D-DV  
**Selected solution pattern:** COOB model-driven configuration with controlled validation rules

**Why this agent was chosen**

- This requirement is centered on Customer Service Workspace behavior, Dataverse-backed asset forms, and guided agent interaction patterns.
- The specialist is best positioned to choose standard workspace and form patterns without overengineering the solution.

**Why this solution was chosen**

- The requirement can be met with configuration-first design instead of custom code.
- This keeps supportability higher and long-term maintenance lower.
- It aligns well with workbook-driven field rules, mandatory logic, and edit behavior.

**Alternatives considered**

- Pro-code plugins for validation were considered.
- They were not selected because the requirement does not justify custom-code complexity when the same behavior can be handled through platform configuration and low-code controls.

### DDP-59540: Coverage Visibility and Contract Detail Access

**Delegation level:** DDP

**Delegated to:** `power-platform-specialist`  
**Primary domain:** D-PA, D-DV  
**Selected solution pattern:** LC list/detail shaping and low-code orchestration around entitlement-backed data

**Why this agent was chosen**

- The key challenge here is not core case handling, but how related coverage data is surfaced, ordered, and made usable for agents.
- That fits best with a specialist focused on low-code data presentation and orchestration behavior.

**Why this solution was chosen**

- It supports ordered list presentation and detail access without forcing pro-code components.
- It stays compatible with a forward-looking Dataverse pattern anchored on Entitlement plus Customer Asset.
- It gives enough flexibility for visibility and notification behavior while remaining maintainable.

**Alternatives considered**

- A contract-table-centered design was considered.
- It was not selected because that would anchor the solution on a deprecated direction instead of a cleaner and more future-compatible coverage model.

### DDP-63819 / AC1.1: Show Home Connect fields for eligible appliances

**Delegation level:** Acceptance Criterion

**Delegated to:** `d365-copilot-service-specialist`  
**Primary domain:** D-CSW, D-DV  
**Selected solution pattern:** COOB conditional form visibility driven by an integration-managed eligibility indicator

**Why this agent was chosen**

- The acceptance criterion is about what the agent sees in the workspace, not about how the upstream Home Connect data is synchronized.
- The Customer Service specialist is better positioned to keep form visibility aligned with standard model-driven behavior and existing field governance.

**Why this solution was chosen**

- The field-visibility behavior can be delivered with standard configuration once an eligibility indicator is available on the record.
- This keeps the UI rule simple, supportable, and separated from the heavier synchronization logic.
- It also reduces the amount of integration-owned code or orchestration needed for what is fundamentally a workspace presentation rule.

**Alternatives considered**

- Routing the acceptance criterion to the integration specialist was considered.
- It was not selected because that would blur the boundary between data acquisition and standard form behavior without adding design value.

### DDP-63819 / Functional Cluster: Hide non-eligible Home Connect UI elements (AC1.2, AC3.2)

**Delegation level:** Functional Cluster

**Delegated to:** `d365-copilot-service-specialist`  
**Primary domain:** D-CSW, D-DV  
**Selected solution pattern:** COOB conditional suppression of Home Connect sections, icons, and fields

**Why this agent was chosen**

- Both acceptance criteria are controlled by the same form-governance rule: do not render Home Connect UI when the appliance is not eligible.
- Keeping this cluster with the Customer Service specialist reduces fragmentation across the workspace presentation layer.

**Why this solution was chosen**

- The behavior is a straightforward extension of conditional visibility and does not require a custom synchronization pattern of its own.
- It prevents empty or misleading Home Connect UI from appearing to agents.
- It keeps the user experience coherent across detail sections, status icons, and related fields.

**Alternatives considered**

- A custom client-side rendering component was considered.
- It was not selected because native form and section visibility controls are sufficient and easier to maintain.

### DDP-63819 / AC2.2: Keep Home Connect Appliance ID read-only

**Delegation level:** Acceptance Criterion

**Delegated to:** `d365-copilot-service-specialist`  
**Primary domain:** D-CSW, D-DV  
**Selected solution pattern:** COOB field-governance configuration for integration-owned attributes

**Why this agent was chosen**

- The requirement is about preventing agent edits to a field presented on the form.
- That is a standard model-driven governance decision and fits the Customer Service specialist better than the integration specialist.

**Why this solution was chosen**

- Read-only enforcement can be handled directly in form configuration and role-based behavior.
- It preserves the source-of-truth boundary without introducing extra automation.
- It cleanly complements the separate integration pattern that retrieves and refreshes the identifier.

**Alternatives considered**

- Hiding the field entirely was considered.
- It was not selected because agents still need visibility of the identifier for support and traceability, even though they must not edit it.

### DDP-63819 / Functional Cluster: Integration-fed ID retrieval, status mapping, and synchronization (AC1.3, AC2.1, AC3.1, AC4.1, AC5.1)

**Delegation level:** Functional Cluster

**Delegated to:** `integration-specialist` (with Customer Service context)  
**Primary domain:** D-INTG, D-CSW  
**Selected solution pattern:** ALC integration-driven synchronization with governed status mapping and UI consumption

**Why this agent was chosen**

- These acceptance criteria depend on upstream identifier retrieval, connection-state synchronization, mapping logic, and refresh behavior.
- The integration specialist is better positioned to define the interface contract, synchronization timing, and failure-handling approach.

**Why this solution was chosen**

- The appliance ID and status values are system-controlled and should remain integration-owned.
- A governed mapping layer is needed so external states become stable agent-facing labels and colors.
- The pattern supports refresh control, auditability, and separation between source acquisition and workspace presentation.

**Alternatives considered**

- A manually maintained status model inside Customer Service was considered.
- It was not selected because it would duplicate the source of truth, increase operational risk, and weaken auditability.

## Reviewer Notes

- Delegation followed domain ownership boundaries and kept overlap between specialists low.
- Home Connect is the clearest example of why delegation should be allowed below full-DDP level.
- The chosen approaches prioritize maintainability and avoid deprecated implementation anchors.
- Home Connect remains the most architecture-sensitive area and should continue to receive governance attention, especially on the handoff between integration-managed fields and workspace-managed presentation rules.
