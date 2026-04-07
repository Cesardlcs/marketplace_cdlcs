h1. High Level Solution Design

{info}
*Instructions for agents:*
* Replace heading placeholders so section titles are dynamic for the BO.
* Replace all placeholders in brackets.
* Use only these implementation classes: OOB, COOB, LC, ALC, CUSTOM.
* **CRITICAL: Classify each solution and complexity level actively based on requirements analysis. Do NOT default to any classification. Each [SOLUTION_TYPE] and [COMPLEXITY_LEVEL] placeholder must be replaced with an explicit choice justified by the requirements, not accepted as a template default.**
* For solution type and complexity values, use the status macro syntax from the reference below — this produces colored labels when pasted into Confluence via Insert → Confluence Wiki.
* Output the entire document in Confluence Wiki syntax for direct single paste.
* The number of h2, h3, and h4 sections is variable by BO: add, remove, or reorder blocks as needed.
* Keep the section "Proposed Technology Platform" as the final section.
* Details column cells must use • bullets separated by \\ for multi-line content. Use as many bullets as needed (typically 3+ for meaningful coverage).
* **Details content must focus on two things: (1) the specific platform capabilities or features being used to address the requirement (e.g. "Use SLA items and KPIs", "Power Automate cloud flow to trigger on record creation", "Standard field creation on Account entity", "Business Rule to enforce field visibility"); and (2) a brief justification for the chosen complexity level (e.g. "Classified as LC because a canvas app is required beyond standard configuration", "COOB: no custom development needed, fully achievable via Admin Center toggles"). Generic design descriptions are not acceptable — every bullet must name a concrete platform feature or tool. Details should be short and concise**
* Where relevant, details or the disclaimer must explicitly mention: release-wave / Preview status, upgrade-path implications, licensing impact, platform limits, important native-platform limitations, and why unsupported approaches were rejected.
* Example: {{• Standard SLA items and KPI timers on the Case entity \\ • Power Automate cloud flow on status change  \\ • Complexity: fully configurable via Customer Service Hub Admin settings, no custom code required}}
* Structure the detailed design hierarchically as: *DDP -> key functional clusters -> acceptance criteria rows*.
* Each h3 block represents one DDP or subdemand.
* Each h4 block under a DDP represents one key functional cluster.
* Each functional cluster table must contain one row per acceptance criterion.
* Each acceptance-criterion row must include the related FR mapping in the table itself.
* The summary table must follow the same principle: one row per acceptance criterion, with explicit DDP and functional cluster columns.
* The Proposed Technology Platform section should briefly describe the platform in the context of the solutioning.
* Example:  Use Dynamics 365 Customer Service with Copilot Service Workspace as the primary agent experience layer, providing a unified, session-based workspace where agents can efficiently handle customer processes end to end. Store and manage Consumer Profiles and Interaction history in Dataverse as the central record system, ensuring consistent data, auditing, and secure access across the solution. Leverage the Omnichannel framework to enable and orchestrate consumer contact points (digital messaging channels) so all engagements are captured and accessible within the same agent workspace. Use Bing Maps or Google Maps capabilities to support geolocation and map visualization directly from the consumer profile, enabling quick location confirmation.

*Status macro reference:*

|| Implementation Class || Status Macro ||
| OOB | {status:colour=Green|title=OOB} |
| COOB | {status:colour=Green|title=COOB} |
| LC | {status:colour=Yellow|title=LC} |
| ALC | {status:colour=Red|title=ALC} |
| CUSTOM | {status:colour=Red|title=CUSTOM} |

|| Complexity Level || Status Macro ||
| LOW | {status:colour=Green|title=Low} |
| MEDIUM | {status:colour=Yellow|title=Medium} |
| HIGH | {status:colour=Red|title=High} |

h2. [SECTION_INITIAL_DESIGN_TITLE]

Proposed solution leverages [PRIMARY_WORKSPACE_OR_APP] combined with the following layers of functionality:

h3. [SUBSECTION_D365_WORKSPACE_TITLE]

* [D365_CAPABILITY_1]
* [D365_CAPABILITY_2]
* [D365_CAPABILITY_3]

h3. [SUBSECTION_DATAVERSE_TITLE]

* Core tables: [TABLE_LIST]

h3. [SUBSECTION_POWER_PLATFORM_TITLE]

* Power Automate: [PA_FLOW_SCOPE]
* PCF: [PCF_SCOPE]
* AI Builder: [AI_BUILDER_SCOPE]
* Power BI: [POWER_BI_SCOPE]

h3. [SUBSECTION_INTEGRATION_LAYER_TITLE]

* Connectors: [CONNECTOR_SCOPE]
* Scripting/validation: [SCRIPT_SCOPE]
* Virtual tables / external data: [VIRTUAL_TABLE_SCOPE]

In order to fulfill acceptance criteria, the following approaches are proposed:

|| Type || Definition ||
| {status:colour=Green|title=OOB} | Native capabilities that exist immediately after installation. They require no setup other than assigning licenses and security roles. |
| {status:colour=Green|title=COOB} | Standard features that are designed to be configured using the official Admin Center (UI-based wizards and toggles). No building is required, just selecting options. |
| {status:colour=Yellow|title=LC} | Requirements that go beyond standard settings but use visual drag-and-drop tools (Power Platform) rather than writing code. |
| {status:colour=Red|title=ALC} | Advanced low-code implementations that integrate with external systems, APIs, or data sources. While these use visual tools (for example, Connectors and Power Automate) instead of pro-code, they introduce external dependencies and architectural complexity that exceed simple internal data modeling. |
| {status:colour=Red|title=CUSTOM} | Solutions that require a developer to write programming code (C#, JavaScript, TypeScript, .NET) because the requirement is too complex. |

h3. [SUBSECTION_DISCLAIMER_TITLE]

[ARCHITECTURE_GOVERNANCE_DISCLAIMER]

h2. Solution Design

h3. [DDP_1_KEY] - [DDP_1_TITLE]

h4. [DDP_1_FUNCTIONAL_CLUSTER_1]

|| Acceptance Criteria || FR to Design Mapping || Solution || Complexity || Details ||
| [AC_ID_1] - [AC_SHORT_DESCRIPTION_1] | [FR_ID_1], [FR_ID_2] | [SOLUTION_TYPE] | [COMPLEXITY_LEVEL] | • [PLATFORM_FEATURE_OR_CAPABILITY_USED_1] \\ • [PLATFORM_FEATURE_OR_CAPABILITY_USED_2] \\ • Complexity:[ONE_SENTENCE_JUSTIFICATION] |
| [AC_ID_2] - [AC_SHORT_DESCRIPTION_2] | [FR_ID_1], [FR_ID_2] | [SOLUTION_TYPE] | [COMPLEXITY_LEVEL] | • [PLATFORM_FEATURE_OR_CAPABILITY_USED_1] \\ • [PLATFORM_FEATURE_OR_CAPABILITY_USED_2] \\ • Complexity: [ONE_SENTENCE_JUSTIFICATION] |

h4. [DDP_1_FUNCTIONAL_CLUSTER_2]

|| Acceptance Criteria || FR to Design Mapping || Solution || Complexity || Details ||
| [AC_ID_3] - [AC_SHORT_DESCRIPTION_3] | [FR_ID_3] | [SOLUTION_TYPE] | [COMPLEXITY_LEVEL] | • [PLATFORM_FEATURE_OR_CAPABILITY_USED_1] \\ • [PLATFORM_FEATURE_OR_CAPABILITY_USED_2] \\ • Complexity:[ONE_SENTENCE_JUSTIFICATION] |
| [AC_ID_4] - [AC_SHORT_DESCRIPTION_4] | [FR_ID_4] | [SOLUTION_TYPE] | [COMPLEXITY_LEVEL] | • [PLATFORM_FEATURE_OR_CAPABILITY_USED_1] \\ • [PLATFORM_FEATURE_OR_CAPABILITY_USED_2] \\ • Complexity:[ONE_SENTENCE_JUSTIFICATION] |

h3. [DDP_N_KEY] - [DDP_N_TITLE]

h4. [DDP_N_FUNCTIONAL_CLUSTER_1]

|| Acceptance Criteria || FR to Design Mapping || Solution || Complexity || Details ||
| [AC_ID_N1] - [AC_SHORT_DESCRIPTION_N1] | [FR_ID_N1] | [SOLUTION_TYPE] | [COMPLEXITY_LEVEL] | • [PLATFORM_FEATURE_OR_CAPABILITY_USED_1] \\ • [PLATFORM_FEATURE_OR_CAPABILITY_USED_2] \\ • Complexity:[ONE_SENTENCE_JUSTIFICATION] |

h2. [SECTION_SUMMARY_TITLE]

|| DDP || Acceptance Criteria Group || Acceptance Criteria || Solution || Complexity ||
| [DDP_1_KEY] | [FUNCTIONAL_CLUSTER_1] | [AC_ID_1] - [AC_SHORT_DESCRIPTION_1] | [SOLUTION_TYPE] | [COMPLEXITY_LEVEL] |
| [DDP_1_KEY] | [FUNCTIONAL_CLUSTER_1] | [AC_ID_2] - [AC_SHORT_DESCRIPTION_2] | [SOLUTION_TYPE] | [COMPLEXITY_LEVEL] |
| [DDP_1_KEY] | [FUNCTIONAL_CLUSTER_2] | [AC_ID_3] - [AC_SHORT_DESCRIPTION_3] | [SOLUTION_TYPE] | [COMPLEXITY_LEVEL] |
| [DDP_N_KEY] | [FUNCTIONAL_CLUSTER_N] | [AC_ID_N1] - [AC_SHORT_DESCRIPTION_N1] | [SOLUTION_TYPE] | [COMPLEXITY_LEVEL] |

h2. [SECTION_TECH_PLATFORM_TITLE]

[PROPOSED_TECH_PLATFORM_NARRATIVE]
