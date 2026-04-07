# Solutioning Session Summary — BO 2.1 / 2.2 Asset Management

**Generated:** 2026-04-06  
**Jira Objective:** DDP-59197  
**HLD:** `BO-2.1-2.2-Asset-Management-HLD.md`

---

## What Was Solutioned

This run covered **BO 2.1 Account Management / 2.2 Asset Management**, focused on the Contact Centre stream. The solution design is based on the Business Acceptance Criteria from the BO Confluence page, supplemented by three subdemands with status **Specified**.

**Subdemands included:**
- **DDP-59199** — Creation, edition and handling of customer assets (appliances)
- **DDP-59540** — Contract visibility and expiry notification at appliance level
- **DDP-63819** — Home Connect appliance handling (ID display + connection status)

**Subdemands excluded:**
- **DDP-59528** — Auto-identification for warranty *(On Hold — stream ownership between CC, Field Service and Finance not yet resolved)*

---

## Classification Breakdown

**By solution type** (18 items total):
- COOB: 6
- LC: 10
- ALC: 2
- OOB / CUSTOM: 0

**By complexity:**
- LOW: 6
- MEDIUM: 10
- HIGH: 2

The design is predominantly low-code (LC + COOB = 16 of 18 items). The two HIGH complexity items both require ALC patterns with external API dependencies.

---

## DAB Escalation Items

Two items require Architecture Decision Board review before implementation:

1. **Home Connect Appliance ID retrieval** *(DDP-63819 — FR-2)*  
   Power Automate integration with the Home Connect API. External dependency with authentication, idempotent sync, retry, and stale-data handling requirements.

2. **Proactive Contract Expiry Notification** *(DDP-59540 — stakeholder request)*  
   Scheduled flow to notify agents before contract expiry for sales outreach. Cross-stream ownership (CC vs Field Service) must be resolved first.

---

## Risks and Blockers

- **Field Service contract data model not yet defined** — The insurance/contract structure referenced in DDP-59540 is owned by the FS stream and is not yet confirmed. Contract ordering view, list fields, and detail screen (FR-1, FR-2, FR-3) cannot be fully implemented until that model is locked. Related FS tickets: DDP-59879, DDP-59888.

- **DDP-63819 awaiting FCC approval** — Home Connect work should not enter a sprint until FCC approval is received (noted March 2026).

- **Proactive notification ownership unresolved** — Raised by Sebastian Pfahler in DDP-59540 comments. Trigger ownership, agent assignment, and escalation model need joint CC + FS alignment before this can be specced for implementation.

- **Warranty topic (DDP-59528) deferred** — Three streams are competing for ownership. Not included in this HLD. Requires a separate solutioning run once unblocked.
