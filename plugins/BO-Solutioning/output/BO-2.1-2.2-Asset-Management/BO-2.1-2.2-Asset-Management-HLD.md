h1. High Level Solution Design

{info}
*Business Objective:* BO 2.1 Account Management / 2.2 Asset Management
*Jira Objective:* [DDP-59197|https://jira.bsh-group.com/browse/DDP-59197]
*Confluence Page:* [4349401458|https://confluence.bsh-group.com/spaces/PARAMOUNT/pages/4349401458]
*SPECIFIED Subdemands in scope:* DDP-59199, DDP-59540, DDP-63819
*Excluded (On Hold):* DDP-59528 — Auto-Identification for Warranty
*Generated:* 2026-04-06
{info}

----

h2. Initial High Level Design Description

Proposed solution leverages *Copilot Service Workspace (Dynamics 365 Customer Service)* combined with standard Dataverse capabilities, and introduces low-code and advanced low-code integration components only where required by the business acceptance criteria.

h3. Dynamics 365 Customer Service / Copilot Service Workspace

* Model-driven app forms: Customer Asset (appliance) profile, Contract detail screen, related list views
* Session-based workspace navigation for contact centre agents
* Business Rules for conditional field visibility, field-level read-only enforcement, and validation behavior
* Role-based access control and field-level security for integration-owned fields

h3. Dataverse

* Core table: *Customer Asset* ({{msdyn_customerasset}}) — extended with appliance-specific fields (HC Appliance ID, HC Connection Status, warranty dates, country-brand configuration fields)
* Contract backbone: *Agreement* ({{msdyn_agreement}}) with appliance-level relationship extension for contract visibility
* Environment variables and connection references for deployment portability
* Auditing enabled on Customer Asset and contract-related records

h3. Power Platform

* Power Automate for integration orchestration (Home Connect ID retrieval, proactive contract expiry notification), notification triggers, and exception handling
* Environment variables and connection references for deployment portability across Dev / UAT / Production

h3. Integration Layer

* *Home Connect API*: ALC connector integration for automatic retrieval of HC Appliance ID and connection status on asset load or E-Nr. entry
* *Field Service / Contract stream*: Contract data relationship from Field Service-owned domain; ordering and field exposure in CC view context
* Optional server-side plugin only when deterministic transactional control is needed (e.g., warranty calculation — deferred per DDP-59528 On Hold status)

In order to fulfill acceptance criteria, the following approaches are proposed:

|| Type || Definition ||
| {status:colour=Green|title=OOB} | • Native capabilities that exist immediately after installation. They require no setup other than assigning licenses and security roles. |
| {status:colour=Green|title=COOB} | • Standard features configured using the official Admin Center (UI-based wizards and toggles). No building required — just selecting options. |
| {status:colour=Yellow|title=LC} | • Requirements beyond standard settings using visual drag-and-drop tools (Power Platform) rather than writing code. |
| {status:colour=Red|title=ALC} | • Advanced low-code implementations that integrate with external systems, APIs, or data sources. Introduce external dependencies and architectural complexity beyond simple internal data modeling. |
| {status:colour=Red|title=CUSTOM} | • Solutions that require a developer to write programming code (C#, JavaScript, TypeScript, .NET) because the requirement is too complex for low-code. |

h3. Architecture Governance Disclaimer

To ensure focused architectural oversight, only *ALC* and *CUSTOM* items should be escalated to DAB decisioning. OOB / COOB / LC items remain part of implementation governance unless architectural risk changes.

----

h2. Creation, Edition and Handling Assets (DDP-59199)

h3. Appliance Creation + FR-4 Post-Creation Visibility

|| Solution Attributes || Values || Details ||
| Solution type: | {status:colour=Yellow|title=LC} | • Configure Customer Asset ({{msdyn_customerasset}}) create form with all required fields as defined in the Appliance Data Definition Excel document \\ • Extension fields added per field specification (data type, format, conditional visibility) \\ • On successful save, record automatically appears in the Customer Appliance / Customer Asset related subgrid on the customer record |
| Complexity: | {status:colour=Yellow|title=Medium} | • Extension fields and country/brand-specific layout behavior must follow the Appliance Data Definition specification \\ • Requires controlled form and view governance across environments |

h3. Mandatory Field Validation

|| Solution Attributes || Values || Details ||
| Solution type: | {status:colour=Green|title=COOB} | • Dataverse required-column enforcement and native model-driven app validation messages handle mandatory field blocking on form submission \\ • No custom code needed |
| Complexity: | {status:colour=Green|title=Low} | • Standard configuration only — no custom runtime needed \\ • Validation messages are native Dataverse behavior |

h3. Field Behavior and Validation Rules

|| Solution Attributes || Values || Details ||
| Solution type: | {status:colour=Yellow|title=LC} | • Business Rules implement conditional visibility, read-only field behavior, and permitted-value constraints as defined in the Appliance Data Definition document \\ • Controlled choice columns (option sets) enforce dropdown validation \\ • Conditional field visibility handled via Business Rule scoping |
| Complexity: | {status:colour=Yellow|title=Medium} | • Multiple rule combinations by country and brand can increase regression testing effort \\ • Rule governance process recommended to manage combinatorial complexity across market rollouts |

h3. FR-5 — Appliance Editing

|| Solution Attributes || Values || Details ||
| Solution type: | {status:colour=Green|title=COOB} | • Standard model-driven edit behavior on the Customer Asset form \\ • Field-level security profiles restrict editing of integration-owned attributes (e.g., HC fields, exchange-sourced dates) \\ • Role-based access controls which personas can edit versus view |
| Complexity: | {status:colour=Green|title=Low} | • Primarily security role and form configuration \\ • No custom code required |

----

h2. Contract Visibility and Expiry Notification (DDP-59540)

{info}
*Note:* This section has a cross-stream dependency on the Field Service stream for the insurance contract data model and ownership (DDP-59879, DDP-59888). This dependency was flagged in DDP-59540 comments (March 2026) and must be resolved before full implementation can proceed.
{info}

h3. FR-1 — Contract Ordering in Related List (Active First, Historical by End Date Descending)

|| Solution Attributes || Values || Details ||
| Solution type: | {status:colour=Yellow|title=LC} | • Appliance-level related list view configured with ordering logic: Active contracts first, then Expired/Cancelled sorted by Contract End Date descending \\ • View configuration aligned with business acceptance criteria (AC1.1, AC1.2, AC1.3) |
| Complexity: | {status:colour=Yellow|title=Medium} | • Depends on the final appliance-to-contract data relationship model owned by the Field Service stream \\ • View ordering must be verified after cross-stream data model is confirmed |

h3. FR-2 — Contract Fields in List View

|| Solution Attributes || Values || Details ||
| Solution type: | {status:colour=Green|title=COOB} | • Configure related list view columns: Contract Number, Contract Type, Contract Status, Contract Service Type \\ • All columns map to native fields on the Agreement entity or its extension |
| Complexity: | {status:colour=Green|title=Low} | • Native view column configuration in model-driven app designer \\ • No custom code required |

h3. FR-3 — Contract Detail Screen Fields

|| Solution Attributes || Values || Details ||
| Solution type: | {status:colour=Yellow|title=LC} | • Extend or compose the contract main form to surface all required fields per AC3-FR-3: Insurance Company Information, Contract Number, Contracted Service Type, Contract Start/End Dates, Contract Duration, Contract Status, Exchange Information, Renewal Information, Instructions/Notes \\ • Extension fields added to Agreement schema where standard fields are insufficient |
| Complexity: | {status:colour=Yellow|title=Medium} | • Requires harmonization between the standard Agreement entity schema and custom extension fields \\ • Cross-stream dependency with Field Service stream for field ownership and data sourcing |

h3. Proactive Contract Expiry Notification

{info}
*Source:* Stakeholder request raised in DDP-59540 comment (Sebastian Pfahler, March 2026): agents should be notified proactively before a contract expires to support sales outreach.
{info}

|| Solution Attributes || Values || Details ||
| Solution type: | {status:colour=Red|title=ALC} | • Scheduled Power Automate cloud flow to identify contracts approaching expiry within a configurable lead-time window \\ • Creates proactive tasks or notifications assigned to agents to enable pre-expiry outreach \\ • Notification content configurable via environment variables |
| Complexity: | {status:colour=Red|title=High} | • Cross-stream dependency: Field Service owns contract data; trigger ownership must be aligned with CC \\ • Requires agreement on agent assignment model, escalation rules, and notification timing governance \\ • Must be designed jointly with Field Service and CC product ownership before implementation |

----

h2. Home Connect Appliance Handling (DDP-63819)

{info}
*Note:* DDP-63819 was awaiting FCC approval as of March 2026. Confirm go-ahead before implementation sprint begins.
*Scope boundary:* This demand covers only display of HC Appliance ID and connection status. Remote diagnostics, error log visibility, and remote service capabilities are out of scope and deferred to a future demand.
{info}

h3. FR-1 / FR-5 — Conditional Visibility and Read-Only Enforcement (AC1.1, AC1.2, AC2.2, AC3.2, AC5.1)

|| Solution Attributes || Values || Details ||
| Solution type: | {status:colour=Yellow|title=LC} | • Business Rule evaluates Home Connect enablement flag (derived from E-Nr. master data) and conditionally shows or hides the Home Connect section on the Appliance/Asset form \\ • HC Appliance ID and Connection Status fields are always read-only for agents \\ • Business Rule applied across create, edit, and view form contexts |
| Complexity: | {status:colour=Yellow|title=Medium} | • Enablement logic must be robust across all form modes (create/edit/view) \\ • Flag must be reliably populated from integration before the rule is evaluated \\ • Requires integration sequencing alignment |

h3. FR-2 — Home Connect Appliance ID Handling (AC2.1, AC2.2)

|| Solution Attributes || Values || Details ||
| Solution type: | {status:colour=Red|title=ALC} | • Power Automate cloud flow triggered on asset create or E-Nr. field update calls the Home Connect master data API to retrieve the HC Appliance ID \\ • Field is read-only on all forms, enforced by field-level security profile \\ • Retry logic and stale-data protection included in flow design |
| Complexity: | {status:colour=Red|title=High} | • External API dependency requires connector configuration and authentication management (OAuth/API key) \\ • Idempotent behavior for re-triggers and retry policy for transient failures required \\ • Data freshness SLA must be aligned with the Home Connect integration team |

h3. FR-3 / FR-4 — Connection Status Display, Color Mapping and Text Description (AC3.1, AC3.2, AC4.1)

|| Solution Attributes || Values || Details ||
| Solution type: | {status:colour=Yellow|title=LC} | • HC Connection Status stored as a choice column with four values: Green – Connected, Yellow – Unstable/Warning, Red – Disconnected, White – Not Activated \\ • Color-coded rendering via native choice column configuration in model-driven app (or PCF if richer icon rendering is required) \\ • Static descriptive text or mouse-over tooltip provides status explanation; no manual modification allowed |
| Complexity: | {status:colour=Yellow|title=Medium} | • Color mapping logic is straightforward in the UI layer \\ • Data normalization depends on the payload structure delivered by the Home Connect integration \\ • Mapping definition must be confirmed with the integration team before implementation |

h3. AC5.1 — No Manual Override of Integration-Owned Fields

|| Solution Attributes || Values || Details ||
| Solution type: | {status:colour=Green|title=COOB} | • Field-level security profiles restrict write access to HC Appliance ID and HC Connection Status to the integration service account only \\ • Agent-facing security roles have read-only access to these fields \\ • Form-level read-only behavior enforces this additionally at the UI layer |
| Complexity: | {status:colour=Green|title=Low} | • Native Dataverse field-level security and security role model \\ • No custom code required |

----

h2. Solution Summary

|| Acceptance Criteria Group || Acceptance Criterion || Solution || Complexity ||
| Creation & Edition of Assets | AC-FR-1: Appliance created when all required fields completed | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} |
| Creation & Edition of Assets | AC-FR-2: Mandatory field validation blocks submission with message | {status:colour=Green|title=COOB} | {status:colour=Green|title=Low} |
| Creation & Edition of Assets | AC-FR-3: Field validation rules and behaviors enforced | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} |
| Creation & Edition of Assets | AC-FR-4: Newly created appliance visible in Customer Asset section | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} |
| Creation & Edition of Assets | AC-FR-5: Agent can view and edit appliance with configured rules | {status:colour=Green|title=COOB} | {status:colour=Green|title=Low} |
| Insurance Contract Visibility | AC1.1-FR-1: Active contracts displayed at top of list | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} |
| Insurance Contract Visibility | AC1.2-FR-1: Expired/Cancelled contracts below Active | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} |
| Insurance Contract Visibility | AC1.3-FR-1: Historical contracts sorted by Contract End Date desc | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} |
| Insurance Contract Visibility | AC2-FR-2: Contract Number, Status, Service Type visible in list | {status:colour=Green|title=COOB} | {status:colour=Green|title=Low} |
| Insurance Contract Visibility | AC3-FR-3: Full contract detail screen with all required fields | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} |
| Insurance Contract Visibility | Proactive expiry notification (stakeholder request) | {status:colour=Red|title=ALC} | {status:colour=Red|title=High} |
| Home Connect Appliance Handling | AC1.1: HC fields shown for HC-enabled appliances | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} |
| Home Connect Appliance Handling | AC1.2: HC fields hidden for non-HC appliances | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} |
| Home Connect Appliance Handling | AC1.3: Status icon reflects integration data | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} |
| Home Connect Appliance Handling | AC2.1: HC Appliance ID auto-retrieved from master data | {status:colour=Red|title=ALC} | {status:colour=Red|title=High} |
| Home Connect Appliance Handling | AC2.2: HC Appliance ID is read-only | {status:colour=Green|title=COOB} | {status:colour=Green|title=Low} |
| Home Connect Appliance Handling | AC3.1: Connection status shown with color icon and text | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} |
| Home Connect Appliance Handling | AC3.2: No status shown for non-HC appliances | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} |
| Home Connect Appliance Handling | AC4.1: Color status reflects Green/Yellow/Red/White mapping | {status:colour=Yellow|title=LC} | {status:colour=Yellow|title=Medium} |
| Home Connect Appliance Handling | AC5.1: No manual override of integration-owned fields | {status:colour=Green|title=COOB} | {status:colour=Green|title=Low} |

----

h2. Proposed Technology Platform

*Scope: Paramount – Contact Centre (CC) stream*

The proposed solution is built exclusively on the Microsoft Power Platform and Dynamics 365 ecosystem:

* *Dynamics 365 Customer Service / Copilot Service Workspace* — Primary agent workspace. All appliance management, contract visibility, and Home Connect display surfaces are delivered as model-driven app forms, views, and workspace sessions.
* *Dataverse* — Core data platform. Customer Asset ({{msdyn_customerasset}}) is the central entity. Agreement ({{msdyn_agreement}}) used for contract backbone. Extension columns added to both entities for BO-specific fields.
* *Power Automate* — Integration orchestration for Home Connect ID retrieval (real-time trigger on E-Nr. update) and proactive contract expiry notification (scheduled flow). Connection references and environment variables used for environment-portability.
* *Home Connect Integration API* — External dependency for HC Appliance ID and connection status data. Integrated via ALC connector pattern (Power Automate + custom connector or certified connector if available).
* *Field Service stream (external dependency)* — Contract/Agreement data model ownership. CC team consumes contract data via related views. Cross-stream alignment required before implementation of contract ordering and detail screen.

No Azure-native infrastructure, custom code (C#/.NET), or PCF components are required in the baseline design. PCF is identified as an optional enhancement for richer HC status color rendering if native choice column formatting is insufficient.
