# Requirement Delegation and Solution Rationale

## Overview

- BO Name: [BO] 2.1 Account management / 2.2 Asset Management
- Jira Objective: DDP-59197
- Engagement: v9
- Status Filter Applied: SPECIFIED
- Generation Date: 2026-04-07
- Step 8b Approval: approved (recorded 2026-04-08)

This document records how requirement areas were delegated for solutioning and why the selected approaches were chosen.

## At A Glance

- DDP-59199 was delegated to the Customer Service and Dataverse design domain because the requirement is primarily about first-party asset handling, validation, and form behavior.
- DDP-59540 remained architecture-led and governance-constrained because the physical contract model is still pending DAB approval.
- DDP-63819 was delegated to workspace UX and display governance because the confirmed scope is display-only and read-only, not broad Home Connect service enablement.
- The overall solution stayed OOB/COOB-first, with LC used only where supported form logic required more than standard field settings.

## Requirement Areas

### DDP-59199 - Appliance Creation, Validation, and Editing

Delegation level: DDP

Delegated to: solution-designer-agent (Customer Service + Dataverse design domain)  
Primary domain: model-driven asset lifecycle experience using first-party and standard Dataverse behavior  
Selected solution pattern: OOB/COOB-first with targeted LC for conditional field behavior

Why this agent/domain was chosen:
- The requirement set is centered on appliance creation, validation, related-record visibility, and controlled editing.
- These concerns map directly to Dataverse table behavior, model-driven forms, Business Rules, and security controls.

Why this solution was chosen:
- Standard Customer Asset behavior avoids unnecessary custom data-model sprawl and supports the upgrade path better than a custom appliance table.
- Mandatory fields, related-record visibility, and most edit governance are already supported natively or through supported configuration.
- LC is used only where conditional field behavior and read-only logic exceed simple required-field settings.

Alternatives considered:
- Introduce a custom appliance table with custom UI and custom validation logic.
- Why not selected: It weakens OOB-first posture, increases support burden, and is not justified by the current requirements.

### DDP-59540 - Contract Visibility and Expiry Behavior (Table-Agnostic)

Delegation level: DDP

Delegated to: solution-designer-agent with architecture governance constraints  
Primary domain: contract-domain UX and governance-safe logical design independent of the physical model  
Selected solution pattern: table-agnostic logical contract capability model, pending DAB physical binding

Why this agent/domain was chosen:
- The requirement is not just a screen-design problem; it has unresolved data ownership and physical model implications across streams.
- It requires a design that preserves user-facing behavior while deliberately avoiding premature model commitment.

Why this solution was chosen:
- It preserves progress on list ordering, field visibility, and detail-screen behavior without hard-binding the design to a contract model that may later be rejected.
- It keeps the design extensible and upgrade-safe while respecting governance and dependency boundaries.
- It also avoids recommending unsupported or speculative physical patterns before DAB approval.

Alternatives considered:
- Immediate hard-binding to a custom contract table.
- Immediate hard-binding to a first-party entitlement/agreement structure.
- Why not selected: Either option would front-run DAB and increase rework risk.

### DDP-63819 - Home Connect Display-Only Handling

Delegation level: DDP

Delegated to: solution-designer-agent (workspace UX + conditional visibility domain)  
Primary domain: status display, read-only enforcement, and conditional presentation in the existing asset experience  
Selected solution pattern: COOB-first display behavior with limited LC for rule-based UI consistency

Why this agent/domain was chosen:
- The confirmed scope is intentionally narrow: display Home Connect identifiers and connection state without expanding into remote-service capabilities.
- The design is therefore mostly about form behavior, visibility logic, and read-only governance.

Why this solution was chosen:
- It fulfills the confirmed business need using supported model-driven display patterns instead of introducing broader integration or service logic.
- It preserves the separation between current display needs and future Home Connect service capabilities.
- It keeps the solution aligned to OOB/COOB-first principles while still allowing upstream-controlled values to be presented clearly.

Alternatives considered:
- Broader Home Connect service integration in the same engagement.
- Custom UI behavior beyond supported model-driven display patterns.
- Why not selected: Both options would expand beyond the confirmed scope and increase dependency and supportability risk.

## Reviewer Notes

- DDP-59540 must remain table-agnostic until DAB issues a physical model decision.
- On-Hold subdemands remain excluded by the confirmed status filter and must not be pulled into v9 scope.
- No selected design element in v9 depends on Preview functionality.
- The strongest OOB-first signal in this run is the explicit reuse of standard asset handling behavior instead of introducing a custom appliance domain.