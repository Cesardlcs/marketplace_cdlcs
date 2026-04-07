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

|| Acceptance Criteria || FR to Design Mapping || Solution || Complexity || Details ||
| AC-FR-1 - Create appliance successfully when all required fields are entered | FR-1, FR-2 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Use standard Customer Asset create form with required columns \\ • Use Business Rules for inline validation before save \\ • Complexity: Medium — multi-rule form behavior and validation paths require coordinated configuration |
| AC-FR-2 - Block creation and show validation for missing mandatory fields | FR-2 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Use Business Rules to block save when mandatory fields are empty \\ • Use form-level field messages for user guidance \\ • Complexity: Medium — multiple mandatory checks and user feedback states must be configured consistently |
| AC-FR-3 - Enforce field behavior, dropdowns, read-only rules, and validation messages | FR-3 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Use Choice columns for controlled dropdown values \\ • Use Business Rules for read-only, visibility, and validation behavior \\ • Complexity: Medium — combined conditional rules across fields/countries increase configuration effort |

h4. Asset Visibility and Maintenance

|| Acceptance Criteria || FR to Design Mapping || Solution || Complexity || Details ||
| AC-FR-4 - Show newly created appliance in Customer Appliance / Customer Asset list | FR-4 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Use Customer Asset subgrid on Account/Contact forms \\ • Use associated view configuration to show new records after save \\ • Complexity: Medium — requires coordinated subgrid, refresh behavior, and navigation consistency |
| AC-FR-5 - View, edit, and save existing appliance under configured rules | FR-5, FR-3 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Use same main form and Business Rules for edit mode \\ • Use column properties for read-only and conditional editability \\ • Complexity: Medium — edit governance adds conditional rule combinations beyond basic form setup |

h3. DDP-59540 - Contract Visibility & Expiry Notification at Appliance Level

h4. Coverage Visibility and Ordering (Model-Agnostic Presentation)

|| Acceptance Criteria || FR to Design Mapping || Solution || Complexity || Details ||
| AC1.1-FR-1 - Display active contracts at the top of the related list | FR-1 | {status:colour=Yellow|title=LC} | {status:colour=Red|title=High} | • Use related-list view with status-first ordering for active contracts \\ • Use model-driven view filters designed to stay model-agnostic \\ • Complexity: High — prioritized multi-state ordering plus model-agnostic design creates significant configuration dependency |
| AC1.2-FR-1 - Display expired and cancelled contracts beneath active contracts | FR-1 | {status:colour=Yellow|title=LC} | {status:colour=Red|title=High} | • Use status-based grouping in the related-list view \\ • Use filtered view or embedded canvas grid for segmented ordering \\ • Complexity: High — segmented ordering needs custom low-code design and strong dependency management |
| AC1.3-FR-1 - Order historical contracts by end date descending | FR-1 | {status:colour=Yellow|title=LC} | {status:colour=Red|title=High} | • Use view-level end-date descending sort for historical records \\ • Use query/view configuration that stays portable across final model options \\ • Complexity: High — combined status grouping and date sorting across uncertain model source increases design complexity |
| AC2-FR-2 - Show required contract columns in the related list view | FR-2 | {status:colour=Yellow|title=LC} | {status:colour=Red|title=High} | • Use related-list view columns: Number, Type, Status, Service Type \\ • Use view projection decoupled from the final persistence model \\ • Complexity: High — field mapping must remain adaptable until model decision is finalized |

h4. Coverage Detail Access (Model-Agnostic Detail Screen)

|| Acceptance Criteria || FR to Design Mapping || Solution || Complexity || Details ||
| AC3-FR-3 - Show required contract fields in the detail screen | FR-3 | {status:colour=Yellow|title=LC} | {status:colour=Red|title=High} | • Use model-driven contract detail form with required field set \\ • Use layout mapped to DAB-approved source (Entitlement/custom/native) \\ • Complexity: High — final field mapping is gated by unresolved canonical model |

h3. DDP-63819 - Home Connect Appliance Handling

h4. Home Connect Display Eligibility and Visibility (Display Scope Only)

|| Acceptance Criteria || FR to Design Mapping || Solution || Complexity || Details ||
| AC1.1 - Show Home Connect fields for eligible appliances | FR-1, FR-5 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Use Business Rule to show HC fields when eligibility = true \\ • Use standard form visibility controls (no PCF/JS) \\ • Complexity: Medium — eligibility-based conditional display adds multi-state UI behavior |
| AC1.2 - Hide Home Connect fields for non-enabled appliances | FR-1, FR-5 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Use inverse Business Rule condition to hide HC fields \\ • Use form behavior to prevent empty placeholders \\ • Complexity: Medium — hidden-state consistency across forms/views requires coordinated rules |

h4. Home Connect Display Rendering and Explanation (Display Scope Only)

|| Acceptance Criteria || FR to Design Mapping || Solution || Complexity || Details ||
| AC1.3 - Reflect integration data with the correct status icon and label | FR-3, FR-4 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Use Choice column to map upstream status to icon/label states \\ • Use form configuration to render value-driven status visuals \\ • Complexity: Medium — status mapping plus visual-state consistency requires coordinated configuration |
| AC3.1 - Display connection status with icon and text | FR-3 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Use read-only Choice field for status icon + text \\ • Use upstream-provided value without CRM sync logic \\ • Complexity: Medium — read-only display with icon/text behavior requires controlled field rendering |
| AC3.2 - Suppress status field and icon for non-enabled appliances | FR-5 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Use existing eligibility Business Rule to hide status field/icon \\ • Reuse same hide condition to avoid duplicate logic \\ • Complexity: Medium — suppression logic must stay consistent with eligibility behavior |
| AC4.1 - Display green/yellow/red/white state mapping correctly | FR-4 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Use Choice option metadata for green/yellow/red/white mappings \\ • Use field description/tooltip for state explanation \\ • Complexity: Medium — multi-state mapping and explanations require governed configuration |

h4. Home Connect Appliance ID Display Governance (Display Scope Only)

|| Acceptance Criteria || FR to Design Mapping || Solution || Complexity || Details ||
| AC2.1 - Retrieve Home Connect Appliance ID automatically | FR-2 | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} | • Use auto-populated HC Appliance ID from upstream value in Dataverse \\ • Use display-only field with no manual input path \\ • Complexity: Medium — upstream dependency and display-governance checks add integration-aware configuration effort |
| AC2.2 - Keep Home Connect Appliance ID read-only | FR-2, FR-5 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Use read-only column setting in model-driven form editor \\ • Use field-level lock to prevent agent override \\ • Complexity: Medium — read-only enforcement must be consistent across all appliance forms |

h4. Home Connect Upstream Dependency Notice

|| Acceptance Criteria || FR to Design Mapping || Solution || Complexity || Details ||
| AC5.1 - Replace status automatically from integration and prevent agent override | FR-3, FR-5 | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} | • Use upstream-owned status value as CRM display source \\ • Use read-only field configuration to block agent override \\ • Complexity: Medium — display behavior depends on external feed reliability and rule consistency |

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
