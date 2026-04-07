h1. High Level Solution Design

h2. Initial Design

Proposed solution leverages Dynamics 365 Customer Service Workspace combined with the following layers of functionality:

h3. Dynamics 365 Copilot Service Workspace

* Model-driven workspace for appliance creation, edit, and retrieval inside Customer Service
* Agent-facing forms and views designed around first-party Customer Service and Dataverse behavior rather than custom UI redevelopment
* Record-centric customer appliance experience integrated with customer handling flow

h3. Dataverse Layer

* Core tables: Account, Contact, Customer Asset, Contract Domain (physical model intentionally table-agnostic pending DAB decision)
* Standard vs custom table stance: Customer Asset is reused for appliance handling; no custom appliance table is introduced in current scope

h3. Power Platform Layer

* Power Automate: Reserved for lightweight orchestration only where standard configuration cannot satisfy behavior requirements
* Business Rules: Used for form-level validation, conditional visibility, and read-only behavior on supported model-driven forms
* PCF: Not required in current scope
* AI Builder: Not required in current scope
* Power BI: Not required in current scope

h3. Integration Layer

* Connectors: Existing standard Dataverse/platform integration paths only
* Scripting/validation: Business Rules and form-level model-driven validation, no custom script required in current scope
* Virtual tables / external data: Not required in current scope
* Sync vs async stance: Current design assumes existing upstream/master-data provisioning and does not introduce new synchronous custom integrations in this engagement

In order to fulfill acceptance criteria, the following approaches are proposed:

|| Type || Definition ||
| {status:colour=Green|title=OOB} | Native capabilities that exist immediately after installation. They require no setup other than assigning licenses and security roles. |
| {status:colour=Green|title=COOB} | Standard features that are designed to be configured using the official Admin Center (UI-based wizards and toggles). No building is required, just selecting options. |
| {status:colour=Yellow|title=LC} | Requirements that go beyond standard settings but use visual drag-and-drop tools (Power Platform) rather than writing code. |
| {status:colour=Red|title=ALC} | Advanced low-code implementations that integrate with external systems, APIs, or data sources. While these use visual tools (for example, Connectors and Power Automate) instead of pro-code, they introduce external dependencies and architectural complexity that exceed simple internal data modeling. |
| {status:colour=Red|title=CUSTOM} | Solutions that require a developer to write programming code (C#, JavaScript, TypeScript, .NET) because the requirement is too complex. |

h3. Architecture Governance Disclaimer

This design follows an OOB-first and first-party-first approach. Established GA capabilities in Dynamics 365 Customer Service, Dataverse, model-driven apps, and Business Rules are used before any low-code extension is introduced. Review of Microsoft 2026 Wave 1 release plans for Dynamics 365 and Power Platform did not identify any roadmap dependency required for this design, and no selected capability is intentionally based on Preview functionality.

Critical design boundaries are as follows: DDP-59540 remains table-agnostic pending an individual DAB decision on the physical contract model; no unsupported Dataverse SQL, direct database indexing, or filtered-view customization is proposed; and advanced Home Connect remote-service capabilities remain out of scope. Platform behavior is also considered explicitly: Business Rules are relied on for supported model-driven form behavior, not editable grids/subgrids. Licensing assumption for this scope is Dynamics 365 Customer Service plus Dataverse rights already available in the target environment; premium expansion would only become relevant if future contract orchestration or broader Home Connect integrations are added.

h2. Solution Design

{info}
For each DDP in scope, one h3 section is provided.
Within each DDP, h4 sections represent key functional clusters.
Each cluster table has one row per acceptance criterion.
{info}

h3. DDP-59199 - PAR_CC: Creation, edition and handle Assets for a Customer

h4. Appliance Creation and Validation

|| Acceptance Criteria || FR to Design Mapping || Solution || Complexity || Details ||
| AC-FR-1 - Appliance creation succeeds when required data is provided | FR-1 | {status:colour=Green|title=COOB} | {status:colour=Green|title=Low} | • Standard Customer Asset record creation is used instead of introducing a custom appliance entity \\ • Model-driven main form captures required appliance data on supported first-party/Dataverse controls \\ • Complexity: Low because native form and table behavior satisfy the requirement without custom build |
| AC-FR-2 - Missing mandatory fields block creation with clear validation | FR-2 | {status:colour=Green|title=COOB} | {status:colour=Green|title=Low} | • Dataverse required-column configuration prevents save when mandatory values are missing \\ • Native validation messages on the form identify missing fields to the agent \\ • Complexity: Low because requirement enforcement is handled with supported platform configuration |
| AC-FR-3 - Field validation, read-only behavior, dropdown limits, and conditional visibility are enforced | FR-3 | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} | • Business Rules apply conditional visibility, read-only behavior, and dynamic requirement levels on the model-driven form \\ • Choice columns and data types enforce permitted values and input-format constraints \\ • Complexity: Medium because multiple rule combinations must be coordinated on supported form surfaces, not editable grids/subgrids |

h4. Post-Creation Visibility and Editing

|| Acceptance Criteria || FR to Design Mapping || Solution || Complexity || Details ||
| AC-FR-4 - Newly created appliance appears in customer asset section | FR-4 | {status:colour=Green|title=OOB} | {status:colour=Green|title=Low} | • Standard Dataverse relationship behavior surfaces linked Customer Asset records in related lists/subgrids \\ • No additional orchestration is required once the record is saved successfully \\ • Complexity: Low because this is native related-record visibility |
| AC-FR-5 - Existing appliance can be viewed and edited according to configured rules | FR-5 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Existing appliance records are opened in the standard model-driven edit experience \\ • Security roles, form properties, and Business Rules govern which fields remain editable vs read-only \\ • Complexity: Medium because edit-state governance must stay aligned with creation rules and role boundaries |

h3. DDP-59540 - PAR_CC: Contract Visibility & Expiry Notification at Appliance Level

h4. Contract List Visibility and Ordering (Table-Agnostic Pending DAB)

|| Acceptance Criteria || FR to Design Mapping || Solution || Complexity || Details ||
| AC1.1 - Active contracts are shown at top of list | FR-1 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Related-list sorting prioritizes active-status contract records first in the view configuration \\ • Contract status is treated as a logical attribute contract independent of final physical table selection \\ • Complexity: Medium because grouped ordering logic must remain stable despite pending DAB model choice |
| AC1.2 - Expired and cancelled contracts appear below active contracts | FR-1 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • View sort/filter rules separate active contracts from expired and cancelled records \\ • Standard list configuration is used rather than custom rendering or unsupported query tuning \\ • Complexity: Medium because multiple status buckets must be preserved in a deterministic order |
| AC1.3 - Historical contracts sorted by end date descending | FR-1 | {status:colour=Green|title=COOB} | {status:colour=Green|title=Low} | • Descending sort on contract end date is configured in the related view \\ • Historical contract access remains available without custom archive logic \\ • Complexity: Low because standard view sorting satisfies the requirement |
| AC2 - Related list displays contract number, type, status, and service type | FR-2 | {status:colour=Green|title=COOB} | {status:colour=Green|title=Low} | • Related-list columns expose contract number, type, status, and service type directly in the standard view \\ • Table-agnostic logical attribute naming avoids premature binding to an unsupported or unapproved model \\ • Complexity: Low because this is standard view-column configuration |

h4. Contract Detail and Expiry Readiness

|| Acceptance Criteria || FR to Design Mapping || Solution || Complexity || Details ||
| AC3 - Contract detail screen shows defined insurance and lifecycle fields | FR-3 | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} | • Model-driven detail form exposes insurer, contract lifecycle, renewal, exchange, and instruction fields \\ • Form-level security and display logic are used instead of custom UI redevelopment \\ • Complexity: Medium because the detail experience spans multiple attribute groups and governance dependencies |
| AC-Comment-01 - Proactive expiry notification capability remains design-ready pending DAB model decision | FR-1, FR-3 + Jira comments | {status:colour=Red|title=ALC} | {status:colour=Red|title=High} | • Future notification orchestration would require an approved contract source model plus cross-stream event ownership \\ • Integration/resiliency pattern should remain asynchronous once defined to avoid tight coupling with external contract lifecycle events \\ • Complexity: High because unresolved ownership and data-model decisions block final orchestration design in this engagement |

h3. DDP-63819 - PAR_CC: Home Connect Appliance Handling

h4. Home Connect Field Visibility and Status Display

|| Acceptance Criteria || FR to Design Mapping || Solution || Complexity || Details ||
| AC1.1 - Home Connect ID and status are shown for eligible appliances | FR-1 | {status:colour=Green|title=COOB} | {status:colour=Green|title=Low} | • Standard model-driven form sections display Home Connect ID and status only when eligibility conditions are met \\ • No custom appliance UX is introduced; display is layered on the existing asset experience \\ • Complexity: Low because supported conditional form configuration satisfies the requirement |
| AC1.2 - Home Connect fields are hidden for non-enabled appliances | FR-1 | {status:colour=Green|title=COOB} | {status:colour=Green|title=Low} | • Business Rule hides Home Connect controls when the appliance is not eligible \\ • This keeps non-Home-Connect assets on the standard first-party asset experience without extra UI clutter \\ • Complexity: Low because built-in rule-based visibility is sufficient |
| AC1.3 - Integration status icon and label reflect upstream Home Connect state | FR-3, FR-4 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Choice/status mapping presents the upstream Home Connect state using supported labels and visual semantics \\ • Display logic assumes status data is already provisioned through an approved upstream path and does not introduce new remote-service scope \\ • Complexity: Medium because multiple status states must be mapped consistently and read-only |

h4. Home Connect Appliance ID Display Governance

|| Acceptance Criteria || FR to Design Mapping || Solution || Complexity || Details ||
| AC2.1 - Home Connect Appliance ID is retrieved automatically | FR-2 | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} | • Home Connect Appliance ID is populated from approved upstream/master data rather than manual entry \\ • Dataverse field binding stores the retrieved value for display-only usage on the asset record \\ • Complexity: Medium because data retrieval depends on an upstream source contract, even though no custom code is proposed in this scope |
| AC2.2 - Home Connect Appliance ID is read-only | FR-2, FR-5 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Form field properties enforce read-only behavior for Home Connect Appliance ID \\ • Security-role alignment prevents agents from manually overriding upstream-controlled values \\ • Complexity: Medium because read-only enforcement must be consistent across all supported asset entry points |

h4. Home Connect Status Rendering and Visibility Control

|| Acceptance Criteria || FR to Design Mapping || Solution || Complexity || Details ||
| AC3.1 - Connection status uses color icon and text explanation | FR-3, FR-4 | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} | • Status choice rendering provides a clear color and text explanation for each supported state \\ • Tooltip or static explanatory text is handled within supported model-driven presentation patterns \\ • Complexity: Medium because status communication must remain clear without introducing unsupported UI customization |
| AC3.2 - Status field and icon are hidden for non-enabled appliances | FR-1, FR-5 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Shared eligibility logic suppresses status controls for non-enabled appliances \\ • The design avoids unnecessary custom conditions by reusing the same supported visibility rule pattern across fields \\ • Complexity: Medium because multiple UI elements must remain synchronized to one eligibility decision |
| AC4.1 - Green/yellow/red/white state mapping is displayed correctly | FR-4 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Standard option/status mapping defines connected, warning, disconnected, and not-activated semantics \\ • Display remains informational only; no Preview-only or unsupported remote-service feature is required \\ • Complexity: Medium because the status dictionary must remain consistent and agent-readable across states |

h4. Home Connect Upstream Value Control

|| Acceptance Criteria || FR to Design Mapping || Solution || Complexity || Details ||
| AC5.1 - Agents cannot modify Home Connect values manually | FR-5 | {status:colour=Green|title=COOB} | {status:colour=Green|title=Low} | • Read-only form properties and role permissions prevent manual updates of Home Connect values \\ • Upstream-owned values remain system-controlled and are not exposed for manual override \\ • Complexity: Low because supported field-security and form configuration satisfy the requirement |

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
| DDP-63819 | Home Connect Status Rendering and Visibility Control | AC3.1 - Connection status uses color icon and text explanation | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} |
| DDP-63819 | Home Connect Status Rendering and Visibility Control | AC3.2 - Status field and icon are hidden for non-enabled appliances | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} |
| DDP-63819 | Home Connect Status Rendering and Visibility Control | AC4.1 - Green/yellow/red/white state mapping is displayed correctly | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} |
| DDP-63819 | Home Connect Upstream Value Control | AC5.1 - Agents cannot modify Home Connect values manually | {status:colour=Green|title=COOB} | {status:colour=Green|title=Low} |

h2. Proposed Technology Platform

Use Dynamics 365 Customer Service Workspace as the primary agent experience layer and Dataverse as the central record system for customer and appliance data. Reuse the standard Customer Asset pattern for appliance handling to preserve first-party semantics, upgradeability, and supportability, and implement validation/visibility behavior with supported model-driven configuration and Business Rules rather than custom code. Keep the contract domain explicitly table-agnostic until DAB confirms the physical model, so user-facing behavior can be designed now without locking the solution into an unsupported or premature customization path. Based on current Microsoft 2026 Wave 1 guidance, the selected approach relies on established GA platform capabilities and does not require Preview-dependent functionality for production delivery.