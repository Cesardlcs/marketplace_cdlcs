# Run Summary - v2 (Second Pass)

## Inputs

- Engagement: v2
- Customer: test
- BO page: [BO] 2.1 Account Management / 2.2. Asset management (Confluence pageId: 4349401458)
- Jira objective: DDP-59197
- Status filter applied: SPECIFIED (default)

## Confirmation and Approval Trail

- Step 5 confirmation received: CONFIRMED
- Step 8b design approval received: APPROVED
- Design correction applied and approved: no deprecated Contract table for new implementation; use Entitlement + Customer Asset model

## Scope Applied

### Included Subdemands (3)

| Jira Key | Summary | Status |
|---|---|---|
| DDP-59199 | PAR_CC: Creation, edition and handle Assets for a Customer | Specified |
| DDP-59540 | PAR_CC: Contract Visibility & Expiry Notification at Appliance Level | Specified |
| DDP-63819 | PAR_CC: Home Connect Appliance Handling | Specified |

### Excluded Subdemands (2)

| Jira Key | Summary | Status | Reason |
|---|---|---|---|
| DDP-59528 | PAR_CC: Auto-Identification for Warranty of the Appliance | On Hold | Not in active_status_filter |
| DDP-59508 | PAR_CC: CEW Sellability Determination & Contract Visibility at Appliance Level | On Hold | Not in active_status_filter |

## Full FR / AC Coverage (In-Scope Items)

### DDP-59199 - Creation, edition and handle Assets for a Customer

**FR worked in this pass**

| FR ID | Requirement |
|---|---|
| FR-1 | Appliance Creation |
| FR-2 | Mandatory Field Validation |
| FR-3 | Field Behavior and Validation Rules |
| FR-4 | Post-Creation Asset Visibility |
| FR-5 | Appliance Editing |

**AC worked in this pass**

| AC ID | Acceptance Criterion |
|---|---|
| AC-FR-1 | Create appliance succeeds when required fields are entered |
| AC-FR-2 | Missing mandatory fields block creation with validation message |
| AC-FR-3 | Field behavior and validation rules enforced on input |
| AC-FR-4 | Newly created appliance visible in Customer Appliance / Asset list |
| AC-FR-5 | Existing appliance can be viewed/edited/saved under configured rules |

### DDP-59540 - Contract Visibility & Expiry Notification at Appliance Level

**FR worked in this pass**

| FR ID | Requirement |
|---|---|
| FR-1 | Related List ordering: active first, expired/cancelled below, historical by end date desc |
| FR-2 | Related List columns: Contract Number, Type, Status, Service Type |
| FR-3 | Contract detail fields for agent visibility (insurance/provider, lifecycle, renewal, notes) |

**AC worked in this pass**

| AC ID | Acceptance Criterion |
|---|---|
| AC1.1-FR-1 | Active contracts always displayed on top |
| AC1.2-FR-1 | Expired and Cancelled appear below Active contracts |
| AC1.3-FR-1 | Historical contracts sorted by end date descending |
| AC2-FR-2 | Related list displays required columns |
| AC3-FR-3 | Contract detail screen displays required field set |

### DDP-63819 - Home Connect Appliance Handling

**FR worked in this pass**

| FR ID | Requirement |
|---|---|
| FR-1 | Display Home Connect Appliance ID and Connection Status only for eligible appliances |
| FR-2 | Home Connect Appliance ID auto-retrieved and read-only |
| FR-3 | Connection status shown as color icon + text from integration data |
| FR-4 | Support green/yellow/red/white status definition and explanation |
| FR-5 | Visibility/read-only rules enforced for HC vs non-HC appliances |

**AC worked in this pass**

| AC ID | Acceptance Criterion |
|---|---|
| AC1.1 | Show HC fields for Home Connect-enabled appliance |
| AC1.2 | Hide HC fields for non-enabled appliance |
| AC1.3 | Status icon/text reflect integration state |
| AC2.1 | Appliance ID retrieved automatically |
| AC2.2 | Appliance ID field is read-only |
| AC3.1 | Status shown with icon and text explanation |
| AC3.2 | No status field/icon for non-enabled appliance |
| AC4.1 | Status color mapping displayed correctly |
| AC5.1 | Status auto-updates from integration; no agent override |

## Classification Snapshot (Second Pass)

| Component | Implementation Class | Complexity | Effort Anchor | Delivery Time |
|---|---|---|---|---|
| Asset create/edit handling (DDP-59199) | COOB | MEDIUM | 1-5 days build/config | 1-2 sprints |
| Coverage list/detail visibility + expiry notification (DDP-59540) | LC | MEDIUM | 1-5 days build/config | 1-2 sprints |
| Home Connect ID/status handling (DDP-63819) | ALC | HIGH | >5 days or multiple dependencies | Multiple sprints, DAB review recommended |

## Open Items and Risks

1. Contract/coverage data ownership remains cross-stream with Field Service for full detail structure.
2. Home Connect integration reliability and refresh strategy may impact agent-facing status accuracy.
3. Notification policy (timing, recipients, localization) still requires market-level decisions.

## Deliverables

- HLD: output/v2/v2-HLD.md
- SUMMARY: output/v2/v2-SUMMARY.md

## Notes

- This is a second pass with same status filter (SPECIFIED) and expanded FR/AC detail.
- The HLD was regenerated against the updated template structure with key functional clusters and embedded FR-to-design mapping rows.
- Excluded On Hold items (DDP-59528, DDP-59508) were not worked and are listed for completeness only.