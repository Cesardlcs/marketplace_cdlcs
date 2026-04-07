h1. High Level Solution Design

h2. BO 2.1 Account Management / 2.2 Asset Management

Proposed solution leverages Dynamics 365 Customer Service Workspace, Dataverse, and Power Platform automation to deliver asset lifecycle handling, coverage visibility, and Home Connect transparency for all in-scope SPECIFIED subdemands.

h3. D365 Customer Service Workspace

* Single customer context with appliance list and asset detail navigation
* Agent-friendly read-only coverage visibility and Home Connect status display
* Validation-first appliance create/edit experience to reduce data quality defects

h3. Dataverse Data Model

* Core tables: Account, Contact, Customer Asset, Entitlement, SLA KPI Instance
* Supporting custom tables: Home Connect status mapping, notification policy parameters
* Design principle: avoid deprecated Contract table usage for new implementation; use Entitlement + Asset model for coverage display

h3. Power Platform

* Power Automate for expiry reminders and notification routing
* Business rules and form scripts for mandatory field and conditional visibility enforcement
* Scheduled synchronization checks for Home Connect connectivity state refresh

h3. Integration Layer

* Connectors: Dataverse, Office 365 Outlook, Teams
* External dependency: Home Connect integration feed for Appliance ID and Connection Status
* Governance dependency: Field Service stream for contract data structure reference

In order to fulfill acceptance criteria, the following approaches are proposed:

|| Type || Definition ||
| {status:colour=Green|title=OOB} | Native capabilities that exist immediately after installation. They require no setup other than assigning licenses and security roles. |
| {status:colour=Green|title=COOB} | Standard features configured using official admin center capabilities and model-driven configuration. |
| {status:colour=Yellow|title=LC} | Low-code implementation using Power Platform without pro-code artifacts. |
| {status:colour=Red|title=ALC} | Advanced low-code implementation with cross-system dependency and orchestration complexity. |
| {status:colour=Red|title=CUSTOM} | Developer-written code required because low-code/configuration cannot satisfy requirement. |

h3. Architecture Governance Disclaimer

This design is constrained to linked issues matching status filter SPECIFIED for objective DDP-59197. On-hold linked items are intentionally excluded from this pass and documented in summary.

h2. Detailed BO Design

h3. DDP-59199 - Creation, edition and handle Assets for a Customer

h4. Asset Creation and Validation

|| Acceptance Criterion || FR to Design Mapping || Solution || Complexity || Details ||
| AC-FR-1 - Create appliance successfully when all required fields are entered | FR-1, FR-2 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Implement model-driven Customer Asset create using the approved required field set and guided form sections \ • Save action completes only when mandatory create-time attributes are present and valid |
| AC-FR-2 - Block creation and show validation for missing mandatory fields | FR-2 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Use form-level validation and business rules to hard-stop save when required values are missing \ • Present explicit inline field messages so agents can resolve missing data without ambiguity |
| AC-FR-3 - Enforce field behavior, dropdowns, read-only rules, and validation messages | FR-3 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Configure data type, allowed value, conditional visibility, and read-only behavior directly on the asset form \ • Align dropdown options and validation messages with the appliance definition workbook and country rules |

h4. Asset Visibility and Maintenance

|| Acceptance Criterion || FR to Design Mapping || Solution || Complexity || Details ||
| AC-FR-4 - Show newly created appliance in Customer Appliance / Customer Asset list | FR-4 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Refresh customer asset subgrid after successful create so the new appliance is immediately visible in context \ • Keep list and detail navigation consistent from account/contact to appliance record |
| AC-FR-5 - View, edit, and save existing appliance under configured rules | FR-5, FR-3 | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} | • Reuse the same validation and field-governance model for edit mode to prevent create/edit drift \ • Allow updates only on editable attributes while preserving read-only and conditional enforcement rules |

h3. DDP-59540 - Contract Visibility & Expiry Notification at Appliance Level

h4. Coverage Visibility and Ordering

|| Acceptance Criterion || FR to Design Mapping || Solution || Complexity || Details ||
| AC1.1-FR-1 - Display active contracts at the top of the related list | FR-1 | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} | • Build entitlement-backed related-list logic that prioritizes active coverage records for the selected appliance \ • Apply deterministic ordering so active records are always surfaced first for the agent |
| AC1.2-FR-1 - Display expired and cancelled contracts beneath active contracts | FR-1 | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} | • Segment non-active entries below active coverage to preserve current-state readability \ • Keep expired and cancelled records visible for history without mixing them into active coverage rows |
| AC1.3-FR-1 - Order historical contracts by end date descending | FR-1 | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} | • Sort historical coverage entries by end date descending to surface the most recent history first \ • Use low-code list configuration so ordering remains transparent and maintainable |
| AC2-FR-2 - Show required contract columns in the related list view | FR-2 | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} | • Project contract number, type, status, and service type from the entitlement-backed coverage model into the related list \ • Keep the list layout lightweight so agents can assess coverage without opening the detail form |

h4. Coverage Detail Access

|| Acceptance Criterion || FR to Design Mapping || Solution || Complexity || Details ||
| AC3-FR-3 - Show required contract fields in the detail screen | FR-3 | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} | • Present insurance company, contract number, contracted service type, start/end dates, duration, status, exchange or renewal data, and notes in the detail form \ • Anchor the design on Entitlement plus Customer Asset rather than the deprecated Contract table for new implementation decisions |

h3. DDP-63819 - Home Connect Appliance Handling

h4. Home Connect Eligibility Visibility

|| Acceptance Criterion || FR to Design Mapping || Solution || Complexity || Details ||
| AC1.1 - Show Home Connect fields for eligible appliances | FR-1, FR-5 | {status:colour=Red|title=ALC} | {status:colour=Red|title=High} | • Display Home Connect Appliance ID and Connection Status only when the appliance is determined to be Home Connect-enabled by E-Nr logic \ • Keep the visibility rule centralized so all agent entry points behave consistently |
| AC1.2 - Hide Home Connect fields for non-enabled appliances | FR-1, FR-5 | {status:colour=Red|title=ALC} | {status:colour=Red|title=High} | • Suppress Home Connect fields entirely for non-eligible appliances to avoid false expectations for agents \ • Ensure non-HC appliances do not render empty status placeholders or inactive UI elements |

h4. Home Connect Status Mapping

|| Acceptance Criterion || FR to Design Mapping || Solution || Complexity || Details ||
| AC1.3 - Reflect integration data with the correct status icon and label | FR-3, FR-4 | {status:colour=Red|title=ALC} | {status:colour=Red|title=High} | • Render connection status as a synchronized icon and text label sourced from the integration feed \ • Apply governed green, yellow, red, and white mappings so visual state stays aligned with external status semantics |
| AC3.1 - Display connection status with icon and text | FR-3 | {status:colour=Red|title=ALC} | {status:colour=Red|title=High} | • Show both the color icon and readable text description on the asset form for eligible appliances \ • Keep status presentation read-only and integration-driven so agents do not interpret it as editable data |
| AC3.2 - Suppress status field and icon for non-enabled appliances | FR-5 | {status:colour=Red|title=ALC} | {status:colour=Red|title=High} | • Reuse the eligibility rule to suppress status rendering when the appliance is not Home Connect-enabled \ • Prevent stale or irrelevant integration artifacts from appearing in non-HC contexts |
| AC4.1 - Display green/yellow/red/white state mapping correctly | FR-4 | {status:colour=Red|title=ALC} | {status:colour=Red|title=High} | • Maintain a governed status-mapping layer that translates integration states to green, yellow, red, and white UI outcomes \ • Provide tooltip or static explanatory text so the visual state remains interpretable by agents |

h4. Home Connect ID Governance

|| Acceptance Criterion || FR to Design Mapping || Solution || Complexity || Details ||
| AC2.1 - Retrieve Home Connect Appliance ID automatically | FR-2 | {status:colour=Red|title=ALC} | {status:colour=Red|title=High} | • Retrieve the Home Connect Appliance ID automatically from master or integration data when E-Nr is entered or loaded \ • Persist the identifier on the asset record without requiring manual agent input |
| AC2.2 - Keep Home Connect Appliance ID read-only | FR-2, FR-5 | {status:colour=Red|title=ALC} | {status:colour=Red|title=High} | • Keep the Home Connect Appliance ID visible but read-only for agents at all times \ • Prevent manual edits so the stored identifier remains synchronized with the source system |

h4. Home Connect Control and Synchronization

|| Acceptance Criterion || FR to Design Mapping || Solution || Complexity || Details ||
| AC5.1 - Replace status automatically from integration and prevent agent override | FR-3, FR-5 | {status:colour=Red|title=ALC} | {status:colour=Red|title=High} | • Update connection status automatically from the integration feed on each synchronization cycle or triggered refresh \ • Prevent any manual override path so the displayed state remains authoritative and auditable |

h2. Out of Scope for This Pass

h3. DDP-59528 - Auto-Identification for Warranty of the Appliance

|| Solution Attributes || Values || Details ||
| Solution type: | {status:colour=Green|title=OOB} | • Excluded due to status On Hold and filter mismatch \ • Can be included in future run when status changes and user confirms scope update |
| Complexity: | {status:colour=Green|title=Low} | • No implementation effort in this pass \ • Extensive AC set exists but intentionally not executed in current scope |

h3. DDP-59508 - CEW Sellability Determination & Contract Visibility at Appliance Level

|| Solution Attributes || Values || Details ||
| Solution type: | {status:colour=Green|title=OOB} | • Excluded due to status On Hold and filter mismatch \ • Dependency on Sales-stream CEW logic remains open |
| Complexity: | {status:colour=Green|title=Low} | • No implementation effort in this pass \ • Re-evaluate once Sales logic is active and status is in filter |

h2. Summary Table

|| DDP || Acceptance Criteria Group || Acceptance Criteria || Solution || Complexity ||
| DDP-59199 | Asset Creation and Validation | AC-FR-1 - Create appliance successfully when all required fields are entered | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} |
| DDP-59199 | Asset Creation and Validation | AC-FR-2 - Block creation and show validation for missing mandatory fields | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} |
| DDP-59199 | Asset Creation and Validation | AC-FR-3 - Enforce field behavior, dropdowns, read-only rules, and validation messages | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} |
| DDP-59199 | Asset Visibility and Maintenance | AC-FR-4 - Show newly created appliance in Customer Appliance / Customer Asset list | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} |
| DDP-59199 | Asset Visibility and Maintenance | AC-FR-5 - View, edit, and save existing appliance under configured rules | {status:colour=Green|title=COOB} | {status:colour=Yellow|title=Medium} |
| DDP-59540 | Coverage Visibility and Ordering | AC1.1-FR-1 - Display active contracts at the top of the related list | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} |
| DDP-59540 | Coverage Visibility and Ordering | AC1.2-FR-1 - Display expired and cancelled contracts beneath active contracts | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} |
| DDP-59540 | Coverage Visibility and Ordering | AC1.3-FR-1 - Order historical contracts by end date descending | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} |
| DDP-59540 | Coverage Visibility and Ordering | AC2-FR-2 - Show required contract columns in the related list view | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} |
| DDP-59540 | Coverage Detail Access | AC3-FR-3 - Show required contract fields in the detail screen | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} |
| DDP-63819 | Home Connect Eligibility Visibility | AC1.1 - Show Home Connect fields for eligible appliances | {status:colour=Red|title=ALC} | {status:colour=Red|title=High} |
| DDP-63819 | Home Connect Eligibility Visibility | AC1.2 - Hide Home Connect fields for non-enabled appliances | {status:colour=Red|title=ALC} | {status:colour=Red|title=High} |
| DDP-63819 | Home Connect Status Mapping | AC1.3 - Reflect integration data with the correct status icon and label | {status:colour=Red|title=ALC} | {status:colour=Red|title=High} |
| DDP-63819 | Home Connect ID Governance | AC2.1 - Retrieve Home Connect Appliance ID automatically | {status:colour=Red|title=ALC} | {status:colour=Red|title=High} |
| DDP-63819 | Home Connect ID Governance | AC2.2 - Keep Home Connect Appliance ID read-only | {status:colour=Red|title=ALC} | {status:colour=Red|title=High} |
| DDP-63819 | Home Connect Status Mapping | AC3.1 - Display connection status with icon and text | {status:colour=Red|title=ALC} | {status:colour=Red|title=High} |
| DDP-63819 | Home Connect Status Mapping | AC3.2 - Suppress status field and icon for non-enabled appliances | {status:colour=Red|title=ALC} | {status:colour=Red|title=High} |
| DDP-63819 | Home Connect Status Mapping | AC4.1 - Display green/yellow/red/white state mapping correctly | {status:colour=Red|title=ALC} | {status:colour=Red|title=High} |
| DDP-63819 | Home Connect Control and Synchronization | AC5.1 - Replace status automatically from integration and prevent agent override | {status:colour=Red|title=ALC} | {status:colour=Red|title=High} |

h2. Proposed Technology Platform

*Scope: BO 2.1 Account Management / 2.2 Asset Management (status filter: SPECIFIED)*

The implementation is centered on Dynamics 365 Customer Service and Dataverse with Customer Asset as the appliance anchor and Entitlement as coverage source. Low-code automation handles notification orchestration and synchronization controls, while integration patterns support Home Connect status visibility. Deprecated Contract table usage is not used for new design decisions in this pass.