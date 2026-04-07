h1. High Level Solution Design

h2. BO 2.1 Account Management / 2.2 Asset Management

Proposed solution leverages Dynamics 365 Copilot Service Workspace, Dataverse, and Power Platform configuration to deliver asset lifecycle handling, appliance-level contract information visibility, and Home Connect display transparency for all in-scope SPECIFIED subdemands.

h3. D365 Customer Service Workspace

* Single customer context with appliance list and asset detail navigation
* Validation-first appliance create/edit experience with explicit field governance
* Display-only Home Connect field visibility and status presentation based on upstream data availability

h3. Dataverse Data Model

* Core tables in scope: Account, Contact, Customer Asset
* Contract/coverage canonical model for DDP-59540 remains open pending architecture decision
* Design principle: coverage information must be displayable in CRM regardless of final persisted model choice

h3. Power Platform

* Business rules and form configuration for mandatory fields, read-only behavior, and conditional visibility
* View/list configuration for contract ordering and field projection in appliance context
* Notification and reminder orchestration can be configured after DAB confirms the final DDP-59540 model approach

h3. Integration Layer

* Connectors: Dataverse, Office 365 Outlook, Teams
* Home Connect scope in this pass is limited to displaying Appliance ID and Connection Status from already available upstream values
* No new Home Connect integration implementation is included in this DDP scope

In order to fulfill acceptance criteria, the following approaches are proposed:

|| Type || Definition ||
| {status:colour=Green|title=OOB} | Native capabilities that exist immediately after installation. They require no setup other than assigning licenses and security roles. |
| {status:colour=Green|title=COOB} | Standard features configured using official admin center capabilities and model-driven configuration. |
| {status:colour=Yellow|title=LC} | Low-code implementation using Power Platform without pro-code artifacts. |
| {status:colour=Red|title=ALC} | Advanced low-code implementation with cross-system dependency and orchestration complexity. |
| {status:colour=Red|title=CUSTOM} | Developer-written code required because low-code/configuration cannot satisfy requirement. |

h3. Architecture Governance Disclaimer

This design is constrained to linked issues matching status filter SPECIFIED for objective DDP-59197. On-hold linked items are excluded from this pass and listed in summary. DDP-59540 data-model finalization is intentionally deferred to a dedicated DAB session.

h2. Detailed BO Design

h3. DDP-59199 - Creation, edition and handle Assets for a Customer

h4. Asset Creation and Validation

|| Acceptance Criterion || FR to Design Mapping || Solution || Complexity || Details ||
| AC-FR-1 - Create appliance successfully when all required fields are entered | FR-1, FR-2 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Implement model-driven Customer Asset create using the approved required field set and guided form sections \\ • Save action completes only when mandatory create-time attributes are present and valid \\ • Validate behavior with positive, negative, and role-based scenarios |
| AC-FR-2 - Block creation and show validation for missing mandatory fields | FR-2 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Use form-level validation and business rules to hard-stop save when required values are missing \\ • Present explicit inline field messages so agents can resolve missing data without ambiguity \\ • Validate behavior with positive, negative, and role-based scenarios |
| AC-FR-3 - Enforce field behavior, dropdowns, read-only rules, and validation messages | FR-3 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Configure data type, allowed value, conditional visibility, and read-only behavior directly on the asset form \\ • Align dropdown options and validation messages with the appliance definition workbook and country rules \\ • Validate behavior with positive, negative, and role-based scenarios |

h4. Asset Visibility and Maintenance

|| Acceptance Criterion || FR to Design Mapping || Solution || Complexity || Details ||
| AC-FR-4 - Show newly created appliance in Customer Appliance / Customer Asset list | FR-4 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Refresh customer asset list after successful create so the new appliance is immediately visible in context \\ • Keep list and detail navigation consistent from account/contact to appliance record \\ • Validate behavior with positive, negative, and role-based scenarios |
| AC-FR-5 - View, edit, and save existing appliance under configured rules | FR-5, FR-3 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Reuse the same validation and field-governance model for edit mode to prevent create/edit drift \\ • Allow updates only on editable attributes while preserving read-only and conditional enforcement rules \\ • Validate behavior with positive, negative, and role-based scenarios |

h3. DDP-59540 - Contract Visibility & Expiry Notification at Appliance Level

h4. Coverage Visibility and Ordering (Model-Agnostic Presentation)

|| Acceptance Criterion || FR to Design Mapping || Solution || Complexity || Details ||
| AC1.1-FR-1 - Display active contracts at the top of the related list | FR-1 | {status:colour=Yellow|title=LC} | {status:colour=Red|title=High} | • Implement appliance-level related-list rendering that always prioritizes active coverage entries on top \\ • Keep presentation logic model-agnostic so it can read from the final DAB-approved source without UX redesign \\ • Track this cluster as architecture-sensitive until data model is finalized in DAB |
| AC1.2-FR-1 - Display expired and cancelled contracts beneath active contracts | FR-1 | {status:colour=Yellow|title=LC} | {status:colour=Red|title=High} | • Segment expired and cancelled entries below active items to preserve current-state readability \\ • Keep ordering behavior independent from physical table choice so migration risk stays lower \\ • Validate behavior with representative records once final source model is approved |
| AC1.3-FR-1 - Order historical contracts by end date descending | FR-1 | {status:colour=Yellow|title=LC} | {status:colour=Red|title=High} | • Sort historical contract items by end date descending to surface the most recent history first \\ • Define ordering at the view/query layer so implementation remains adaptable to Entitlement, custom table, or other native model \\ • Validate sorting output during post-DAB model confirmation |
| AC2-FR-2 - Show required contract columns in the related list view | FR-2 | {status:colour=Yellow|title=LC} | {status:colour=Red|title=High} | • Display Contract Number, Contract Type, Contract Status, and Contract Service Type in the related list \\ • Keep field projection abstracted from persistence so display commitments survive model decision changes \\ • Confirm exact field mappings after DAB selects canonical source model |

h4. Coverage Detail Access (Model-Agnostic Detail Screen)

|| Acceptance Criterion || FR to Design Mapping || Solution || Complexity || Details ||
| AC3-FR-3 - Show required contract fields in the detail screen | FR-3 | {status:colour=Yellow|title=LC} | {status:colour=Red|title=High} | • Present required contract detail set (insurance, identifiers, lifecycle, exchange, renewal, notes) from the DAB-approved source \\ • Keep detail UX stable regardless of whether final model is custom table, entitlement, or another native table \\ • Mark the data-model decision as a prerequisite gate for final mapping sign-off |

h3. DDP-63819 - Home Connect Appliance Handling

h4. Home Connect Display Eligibility and Visibility (Display Scope Only)

|| Acceptance Criterion || FR to Design Mapping || Solution || Complexity || Details ||
| AC1.1 - Show Home Connect fields for eligible appliances | FR-1, FR-5 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Display Home Connect Appliance ID and Connection Status only when appliance is identified as Home Connect-enabled \\ • Implement rule via standard model-driven visibility controls \\ • Scope is limited to display behavior and does not introduce new integration implementation |
| AC1.2 - Hide Home Connect fields for non-enabled appliances | FR-1, FR-5 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Suppress Home Connect fields and icons for non-enabled appliances \\ • Ensure no empty placeholders appear in appliance forms \\ • Keep logic in UI visibility layer only |

h4. Home Connect Display Rendering and Explanation (Display Scope Only)

|| Acceptance Criterion || FR to Design Mapping || Solution || Complexity || Details ||
| AC1.3 - Reflect integration data with the correct status icon and label | FR-3, FR-4 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Render status icon and label based on already available upstream status values \\ • Apply governed green/yellow/red/white mappings in the CRM display layer \\ • Do not include new status synchronization build in this scope |
| AC3.1 - Display connection status with icon and text | FR-3 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Show icon plus readable text description on eligible appliance records \\ • Keep status field read-only for agents \\ • Use existing incoming values as-is for display |
| AC3.2 - Suppress status field and icon for non-enabled appliances | FR-5 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Reuse eligibility rules to suppress status rendering when appliance is not Home Connect-enabled \\ • Avoid stale display artifacts in non-eligible contexts \\ • Keep implementation in form/view behavior only |
| AC4.1 - Display green/yellow/red/white state mapping correctly | FR-4 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Display supported color states with matching labels and agent-readable meaning \\ • Provide tooltip or static explanatory text for each color state \\ • Treat mappings as presentation logic, not integration logic |

h4. Home Connect Appliance ID Display Governance (Display Scope Only)

|| Acceptance Criterion || FR to Design Mapping || Solution || Complexity || Details ||
| AC2.1 - Retrieve Home Connect Appliance ID automatically | FR-2 | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} | • Display Home Connect Appliance ID as automatically populated from existing upstream source in CRM \\ • Ensure no manual entry path is exposed to agents \\ • Scope is consumption/display of available value, not implementation of the upstream retrieval integration |
| AC2.2 - Keep Home Connect Appliance ID read-only | FR-2, FR-5 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Keep Home Connect Appliance ID visible and read-only in appliance forms \\ • Enforce read-only governance through standard field configuration \\ • Preserve display transparency while preventing agent override |

h4. Home Connect Upstream Dependency Notice

|| Acceptance Criterion || FR to Design Mapping || Solution || Complexity || Details ||
| AC5.1 - Replace status automatically from integration and prevent agent override | FR-3, FR-5 | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} | • CRM display reflects latest status provided by upstream integration-owned process \\ • Agent override remains blocked in the display layer \\ • This DDP does not implement the upstream synchronization mechanism itself |

h2. Out of Scope for This Pass

h3. DDP-59528 - Auto-Identification for Warranty of the Appliance

|| Solution Attributes || Values || Details ||
| Solution type: | {status:colour=Green|title=OOB} | • Excluded due to status On Hold and filter mismatch \\ • Can be included in future run when status changes and user confirms scope update |
| Complexity: | {status:colour=Green|title=Low} | • No implementation effort in this pass \\ • Extensive AC set exists but intentionally not executed in current scope |

h3. DDP-59508 - CEW Sellability Determination & Contract Visibility at Appliance Level

|| Solution Attributes || Values || Details ||
| Solution type: | {status:colour=Green|title=OOB} | • Excluded due to status On Hold and filter mismatch \\ • Dependency on Sales-stream CEW logic remains open |
| Complexity: | {status:colour=Green|title=Low} | • No implementation effort in this pass \\ • Re-evaluate once Sales logic is active and status is in filter |

h2. Summary Table

|| DDP || Acceptance Criteria Group || Acceptance Criteria || Solution || Complexity ||
| DDP-59199 | Asset Creation and Validation | AC-FR-1 - Create appliance successfully when all required fields are entered | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} |
| DDP-59199 | Asset Creation and Validation | AC-FR-2 - Block creation and show validation for missing mandatory fields | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} |
| DDP-59199 | Asset Creation and Validation | AC-FR-3 - Enforce field behavior, dropdowns, read-only rules, and validation messages | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} |
| DDP-59199 | Asset Visibility and Maintenance | AC-FR-4 - Show newly created appliance in Customer Appliance / Customer Asset list | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} |
| DDP-59199 | Asset Visibility and Maintenance | AC-FR-5 - View, edit, and save existing appliance under configured rules | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} |
| DDP-59540 | Coverage Visibility and Ordering (Model-Agnostic Presentation) | AC1.1-FR-1 - Display active contracts at the top of the related list | {status:colour=Yellow|title=LC} | {status:colour=Red|title=High} |
| DDP-59540 | Coverage Visibility and Ordering (Model-Agnostic Presentation) | AC1.2-FR-1 - Display expired and cancelled contracts beneath active contracts | {status:colour=Yellow|title=LC} | {status:colour=Red|title=High} |
| DDP-59540 | Coverage Visibility and Ordering (Model-Agnostic Presentation) | AC1.3-FR-1 - Order historical contracts by end date descending | {status:colour=Yellow|title=LC} | {status:colour=Red|title=High} |
| DDP-59540 | Coverage Visibility and Ordering (Model-Agnostic Presentation) | AC2-FR-2 - Show required contract columns in the related list view | {status:colour=Yellow|title=LC} | {status:colour=Red|title=High} |
| DDP-59540 | Coverage Detail Access (Model-Agnostic Detail Screen) | AC3-FR-3 - Show required contract fields in the detail screen | {status:colour=Yellow|title=LC} | {status:colour=Red|title=High} |
| DDP-63819 | Home Connect Display Eligibility and Visibility (Display Scope Only) | AC1.1 - Show Home Connect fields for eligible appliances | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} |
| DDP-63819 | Home Connect Display Eligibility and Visibility (Display Scope Only) | AC1.2 - Hide Home Connect fields for non-enabled appliances | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} |
| DDP-63819 | Home Connect Display Rendering and Explanation (Display Scope Only) | AC1.3 - Reflect integration data with the correct status icon and label | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} |
| DDP-63819 | Home Connect Appliance ID Display Governance (Display Scope Only) | AC2.1 - Retrieve Home Connect Appliance ID automatically | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} |
| DDP-63819 | Home Connect Appliance ID Display Governance (Display Scope Only) | AC2.2 - Keep Home Connect Appliance ID read-only | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} |
| DDP-63819 | Home Connect Display Rendering and Explanation (Display Scope Only) | AC3.1 - Display connection status with icon and text | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} |
| DDP-63819 | Home Connect Display Rendering and Explanation (Display Scope Only) | AC3.2 - Suppress status field and icon for non-enabled appliances | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} |
| DDP-63819 | Home Connect Display Rendering and Explanation (Display Scope Only) | AC4.1 - Display green/yellow/red/white state mapping correctly | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} |
| DDP-63819 | Home Connect Upstream Dependency Notice | AC5.1 - Replace status automatically from integration and prevent agent override | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} |

h2. Proposed Technology Platform

*Scope: BO 2.1 Account Management / 2.2 Asset Management (status filter: SPECIFIED)*

The implementation is centered on Copilot Service Workspace and Dataverse with Customer Asset as the appliance anchor. DDP-59540 commits to model-agnostic contract information display while final data model selection is deferred to a dedicated DAB decision. DDP-63819 is intentionally limited to display behavior of Home Connect fields and status values already provided by upstream systems; no new Home Connect integration build is in scope for this pass.
