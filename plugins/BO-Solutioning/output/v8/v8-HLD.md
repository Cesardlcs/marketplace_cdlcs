h1. High Level Solution Design

h2. Initial Design

Proposed solution leverages Dynamics 365 Customer Service Workspace combined with the following layers of functionality:

h3. Dynamics 365 Copilot Service Workspace

* Model-driven workspace for appliance creation, edit, and retrieval inside Customer Service
* Agent and supervisor role-specific forms and views for controlled access
* Record-centric customer appliance experience integrated with case handling flow

h3. Dataverse Layer

* Core tables: Account, Contact, Customer Asset, Contract Domain (table-agnostic pending DAB decision)

h3. Power Platform Layer

* Power Automate: Lightweight orchestration only where standard configuration cannot satisfy behavior requirements
* PCF: Not required in current scope
* AI Builder: Not required in current scope
* Power BI: Not required in current scope

h3. Integration Layer

* Connectors: Existing standard Dataverse and platform connectors only
* Scripting/validation: Business Rules and field-level model-driven validation, no custom script required in current scope
* Virtual tables / external data: Not required in current scope

In order to fulfill acceptance criteria, the following approaches are proposed:

|| Type || Definition ||
| {status:colour=Green|title=OOB} | Native capabilities that exist immediately after installation. They require no setup other than assigning licenses and security roles. |
| {status:colour=Green|title=COOB} | Standard features that are designed to be configured using the official Admin Center (UI-based wizards and toggles). No building is required, just selecting options. |
| {status:colour=Yellow|title=LC} | Requirements that go beyond standard settings but use visual drag-and-drop tools (Power Platform) rather than writing code. |
| {status:colour=Red|title=ALC} | Advanced low-code implementations that integrate with external systems, APIs, or data sources. While these use visual tools (for example, Connectors and Power Automate) instead of pro-code, they introduce external dependencies and architectural complexity that exceed simple internal data modeling. |
| {status:colour=Red|title=CUSTOM} | Solutions that require a developer to write programming code (C#, JavaScript, TypeScript, .NET) because the requirement is too complex. |

h3. Architecture Governance Disclaimer

Design follows OOB-first governance and ADR-046 mapping. DDP-59540 remains explicitly table-agnostic and is pending an individual DAB decision for physical model selection (for example custom table, entitlement table, or agreement table). Until DAB confirms the target model, contract requirements are defined as logical capabilities and attribute contracts only. Out-of-scope for this run: DDP-59508 and DDP-59528 (status On Hold), and advanced Home Connect remote-service capabilities.

h2. Solution Design

{info}
For each DDP in scope, one h3 section is provided.
Within each DDP, h4 sections represent key functional clusters.
Each cluster table has one row per acceptance criterion.
{info}

h3. DDP-59199 - PAR_CC: Creation, edition and handle Assets for a Customer

h4. Appliance Creation and Validation

|| Acceptance Criteria || FR to Design Mapping || Solution || Complexity || Details ||
| AC-FR-1 - Appliance creation succeeds when required data is provided | FR-1 | {status:colour=Green|title=COOB} | {status:colour=Green|title=Low} | • Model-driven form on Customer Asset table captures appliance data \\ • Dataverse required-column configuration enforces minimum data set at save time \\ • Complexity: Low because native form + required-column settings satisfy the requirement without custom logic |
| AC-FR-2 - Missing mandatory fields block creation with clear validation | FR-2 | {status:colour=Green|title=COOB} | {status:colour=Green|title=Low} | • Dataverse required fields and model-driven validation prevent record save when mandatory values are empty \\ • Native field-level error prompts identify missing inputs to the agent \\ • Complexity: Low as this is fully achievable via standard admin configuration |
| AC-FR-3 - Field validation, read-only behavior, dropdown limits, and conditional visibility are enforced | FR-3 | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} | • Business Rules configure conditional visibility and read-only behavior per field rules \\ • Choice columns and datatype constraints enforce allowed values and input formats \\ • Complexity: Medium due to the number of rule combinations across field states |

h4. Post-Creation Visibility and Editing

|| Acceptance Criteria || FR to Design Mapping || Solution || Complexity || Details ||
| AC-FR-4 - Newly created appliance appears in customer asset section | FR-4 | {status:colour=Green|title=OOB} | {status:colour=Green|title=Low} | • Standard related list/subgrid on customer context displays linked Customer Asset records \\ • Dataverse relationships provide automatic visibility after successful creation \\ • Complexity: Low because this is native related-record behavior |
| AC-FR-5 - Existing appliance can be viewed and edited according to configured rules | FR-5 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Edit form reuses Business Rules and validation constraints defined for appliance lifecycle fields \\ • Security roles and field properties control which values can be changed by each persona \\ • Complexity: Medium due to multi-rule interaction between create and edit states |

h3. DDP-59540 - PAR_CC: Contract Visibility & Expiry Notification at Appliance Level

h4. Contract List Visibility and Ordering (Table-Agnostic Pending DAB)

|| Acceptance Criteria || FR to Design Mapping || Solution || Complexity || Details ||
| AC1.1 - Active contracts are shown at top of list | FR-1 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Model-driven view sorting prioritizes active-status records first in related contract list \\ • Logical contract status attribute is used independent of final physical table choice \\ • Complexity: Medium because status-priority ordering requires multi-condition sort design |
| AC1.2 - Expired and cancelled contracts appear below active contracts | FR-1 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • View filter and sort precedence separate active vs non-active contract groups \\ • Status taxonomy is implemented as logical domain behavior pending DAB table binding \\ • Complexity: Medium due to grouped ordering across multiple status categories |
| AC1.3 - Historical contracts sorted by end date descending | FR-1 | {status:colour=Green|title=COOB} | {status:colour=Green|title=Low} | • Descending sort on logical contract end-date field in related list view \\ • Standard model-driven list behavior supports chronological historical access \\ • Complexity: Low with native view configuration |
| AC2 - Related list displays contract number, type, status, and service type | FR-2 | {status:colour=Green|title=COOB} | {status:colour=Green|title=Low} | • View column configuration exposes required contract attributes in list layout \\ • Logical attribute mapping is preserved as table-agnostic contract for later DAB binding \\ • Complexity: Low because column exposure is standard admin configuration |

h4. Contract Detail and Expiry Readiness

|| Acceptance Criteria || FR to Design Mapping || Solution || Complexity || Details ||
| AC3 - Contract detail screen shows defined insurance and lifecycle fields | FR-3 | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} | • Model-driven contract detail form surfaces insurer, lifecycle, renewal, and note attributes \\ • Role-based field visibility controls sensitive contract details for CC personas \\ • Complexity: Medium due to breadth of detail-field configuration and security mapping |
| AC-Comment-01 - Proactive expiry notification capability remains design-ready pending DAB model decision | FR-1, FR-3 + Jira comments | {status:colour=Red|title=ALC} | {status:colour=Red|title=High} | • Notification orchestration pattern is prepared but not physically bound before DAB table decision \\ • Cross-stream dependency with Field Service contract structure is explicitly tracked as escalation \\ • Complexity: High because unresolved upstream model ownership blocks final implementation design |

h3. DDP-63819 - PAR_CC: Home Connect Appliance Handling

h4. Home Connect Field Visibility and Status Display

|| Acceptance Criteria || FR to Design Mapping || Solution || Complexity || Details ||
| AC1.1 - Home Connect ID and status are shown for eligible appliances | FR-1 | {status:colour=Green|title=COOB} | {status:colour=Green|title=Low} | • Conditional section visibility on appliance form is driven by Home Connect eligibility state \\ • Read-only Home Connect ID and status fields are displayed using standard form controls \\ • Complexity: Low using native conditional form configuration |
| AC1.2 - Home Connect fields are hidden for non-enabled appliances | FR-1 | {status:colour=Green|title=COOB} | {status:colour=Green|title=Low} | • Business Rule hides Home Connect controls when eligibility condition is false \\ • Standard model-driven control behavior prevents unnecessary UI elements \\ • Complexity: Low because behavior is achieved with built-in rules |
| AC1.3 - Integration status icon and label reflect upstream Home Connect state | FR-3, FR-4 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Choice column maps upstream Home Connect state to icon/label values \\ • Form rendering applies mapped status display consistently across appliance records \\ • Complexity: Medium because status-to-visual mapping must remain consistent across states |

h4. Home Connect Appliance ID Display Governance

|| Acceptance Criteria || FR to Design Mapping || Solution || Complexity || Details ||
| AC2.1 - Home Connect Appliance ID is retrieved automatically | FR-2 | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} | • Auto-populate Home Connect Appliance ID from upstream/master data source \\ • Dataverse field binding persists value for display-only usage in appliance record \\ • Complexity: Medium because source mapping and update path require low-code configuration |
| AC2.2 - Home Connect Appliance ID is read-only | FR-2, FR-5 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Read-only field property enforced in model-driven forms \\ • Role-based security prevents manual override by contact center agents \\ • Complexity: Medium due to cross-form consistency and permission alignment |

h4. Home Connect Status Rendering and Visibility Control

|| Acceptance Criteria || FR to Design Mapping || Solution || Complexity || Details ||
| AC3.1 - Connection status uses color icon and text explanation | FR-3, FR-4 | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} | • Status choice rendering applies color semantics aligned to connected/warning/disconnected/not-activated states \\ • Text label is displayed alongside status indicator to avoid interpretation ambiguity \\ • Complexity: Medium due to consistent UX mapping across all status states |
| AC3.2 - Status field and icon are hidden for non-enabled appliances | FR-1, FR-5 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Business Rule suppresses status controls when Home Connect eligibility is false \\ • Shared eligibility condition keeps status and ID visibility behavior aligned \\ • Complexity: Medium due to conditional visibility dependency management |
| AC4.1 - Green/yellow/red/white state mapping is displayed correctly | FR-4 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Choice option set defines canonical green/yellow/red/white state mapping \\ • Form configuration displays mapped labels and visual cues consistently \\ • Complexity: Medium because status dictionary governance spans multiple display states |

h4. Home Connect Upstream Value Control

|| Acceptance Criteria || FR to Design Mapping || Solution || Complexity || Details ||
| AC5.1 - Agents cannot modify Home Connect values manually | FR-5 | {status:colour=Green|title=COOB} | {status:colour=Green|title=Low} | • Field read-only properties and role permissions block manual edits for HC attributes \\ • Values are controlled by integration-sourced updates, not manual form input \\ • Complexity: Low with standard field-security and form behavior controls |

h2. Solution Summary

|| DDP || Acceptance Criteria Group || Acceptance Criteria || Solution || Complexity ||
| DDP-59199 | Appliance Creation and Validation | AC-FR-1 - Appliance creation succeeds when required data is provided | {status:colour=Green|title=COOB} | {status:colour=Green|title=Low} |
| DDP-59199 | Appliance Creation and Validation | AC-FR-2 - Missing mandatory fields block creation with clear validation | {status:colour=Green|title=COOB} | {status:colour=Green|title=Low} |
| DDP-59199 | Appliance Creation and Validation | AC-FR-3 - Field validation, read-only behavior, dropdown limits, and conditional visibility are enforced | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} |
| DDP-59199 | Post-Creation Visibility and Editing | AC-FR-4 - Newly created appliance appears in customer asset section | {status:colour=Green|title=OOB} | {status:colour=Green|title=Low} |
| DDP-59199 | Post-Creation Visibility and Editing | AC-FR-5 - Existing appliance can be viewed and edited according to configured rules | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} |
| DDP-59540 | Contract List Visibility and Ordering | AC1.1 - Active contracts are shown at top of list | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} |
| DDP-59540 | Contract List Visibility and Ordering | AC1.2 - Expired and cancelled contracts appear below active contracts | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} |
| DDP-59540 | Contract List Visibility and Ordering | AC1.3 - Historical contracts sorted by end date descending | {status:colour=Green|title=COOB} | {status:colour=Green|title=Low} |
| DDP-59540 | Contract List Visibility and Ordering | AC2 - Related list displays contract number, type, status, and service type | {status:colour=Green|title=COOB} | {status:colour=Green|title=Low} |
| DDP-59540 | Contract Detail and Expiry Readiness | AC3 - Contract detail screen shows defined insurance and lifecycle fields | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} |
| DDP-59540 | Contract Detail and Expiry Readiness | AC-Comment-01 - Proactive expiry notification capability remains design-ready pending DAB model decision | {status:colour=Red|title=ALC} | {status:colour=Red|title=High} |
| DDP-63819 | Home Connect Field Visibility and Status Display | AC1.1 - Home Connect ID and status are shown for eligible appliances | {status:colour=Green|title=COOB} | {status:colour=Green|title=Low} |
| DDP-63819 | Home Connect Field Visibility and Status Display | AC1.2 - Home Connect fields are hidden for non-enabled appliances | {status:colour=Green|title=COOB} | {status:colour=Green|title=Low} |
| DDP-63819 | Home Connect Field Visibility and Status Display | AC1.3 - Integration status icon and label reflect upstream Home Connect state | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} |
| DDP-63819 | Home Connect Appliance ID Display Governance | AC2.1 - Home Connect Appliance ID is retrieved automatically | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} |
| DDP-63819 | Home Connect Appliance ID Display Governance | AC2.2 - Home Connect Appliance ID is read-only | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} |
| DDP-63819 | Home Connect Field Visibility and Status Display | AC3.1 - Connection status uses color icon and text explanation | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} |
| DDP-63819 | Home Connect Status Rendering and Visibility Control | AC3.2 - Status field and icon are hidden for non-enabled appliances | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} |
| DDP-63819 | Home Connect Status Rendering and Visibility Control | AC4.1 - Green/yellow/red/white state mapping is displayed correctly | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} |
| DDP-63819 | Home Connect Field Visibility and Status Display | AC5.1 - Agents cannot modify Home Connect values manually | {status:colour=Green|title=COOB} | {status:colour=Green|title=Low} |

h2. Proposed Technology Platform

Use Dynamics 365 Customer Service Workspace as the primary agent experience layer and Dataverse as the central record system for customer and appliance data. Implement appliance creation, validation, edit governance, and Home Connect display behavior with model-driven forms, Business Rules, view/subgrid configuration, and role-based security to maximize OOB/COOB coverage and minimize customization risk. For DDP-59540, keep the contract domain explicitly table-agnostic until DAB confirms the physical model (for example custom table, entitlement table, or agreement table), while maintaining logical attribute/relationship contracts so post-DAB binding can be completed without redesigning user-facing behavior.