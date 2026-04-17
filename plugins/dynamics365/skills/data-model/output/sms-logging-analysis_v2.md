# Data Model: SMS Outbound Notification Logging

**Business Objective**: Enable SMS as an outbound communication channel for repair appointment notifications (BO-1.1 / DDP-59704)

**Date**: 2026-04-17

---

## 0. General Strategy

Because `msdyn_ocoutboundmessage` is a managed/system table with limited extensibility in this environment, the strategy is to avoid future rework and supportability risk.

1. Use a custom activity table (`bshcs_smslog`) as the primary system of record for SMS logging.
2. Keep all extended/technical logging in `bshcs_smslog` only.

---

## 1. Microsoft Guidance Validation

This strategy is aligned with Microsoft documentation:

1. **Supported customizations only**: Microsoft states unsupported modifications are not preserved during upgrades and can break with updates.
   - Source: Supported customizations for Dataverse
   - URL: https://learn.microsoft.com/power-apps/developer/data-platform/supported-customizations

2. **Use standard metadata when it fits; create new metadata when it does not**: Microsoft recommends standard tables first, but explicitly says if they do not fit or cannot be edited to match need, create new tables/columns/relationships.
   - Source: Tables and metadata in Dataverse
   - URL: https://learn.microsoft.com/power-apps/maker/data-platform/create-edit-metadata#create-new-metadata-or-use-existing-metadata

3. **Supportability and upgradeability**: Customizations made via Power Apps and documented APIs are supported and upgradeable.
   - Source: Supported customizations for Dataverse
   - URL: https://learn.microsoft.com/power-apps/developer/data-platform/supported-customizations

---

## 2. Risks and Cons of trying to adjust System/Managed Tables

### Key risks

1. **Extensibility constraints**: In this environment, `msdyn_ocoutboundmessage` cannot be extended with the future telemetry fields business is likely to require.
2. **Upgrade risk**: Any workaround outside supported customization paths is not supportable and may be overwritten or broken in updates.
3. **Semantic overloading**: Forcing deep technical logging into a channel/system table can make operational reporting and ownership unclear.
4. **Dependency coupling**: First-party apps/flows may depend on platform behavior of system tables; over-coupling custom logic to them increases regression risk.
5. **Migration debt later**: If you start with system-table-only now and business adds fields later, you will need migration and dual-query complexity.


If the scope expects additional diagnostics, provider metadata, or compliance fields later, a system-table-only strategy creates avoidable rework and risk.

---

## 3. Table Comparison

| Logical Name | Type | Future-fit for likely extra info | Verdict |
|---|---|---|---|
| `bshcs_smslog` | Custom (new) | Strong | **Selected as primary table** |
| `msdyn_ocoutboundmessage` | Standard managed | Partial | Reuse as optional channel/timeline projection if needed |
| `activitypointer` | Standard | Weak | Rejected |

---

## 4. Architecture Proposal

### Selected primary table: `bshcs_smslog`

Reason:
1. Future additional fields are likely
 needed.
2. Managed/system table constraints block safe expansion.
3. Custom table keeps design supportable and upgrade-safe using documented customization tools.

### Recommended operating model

1. Trigger flow writes SMS record to `bshcs_smslog`.
2. Router/callback flows update record with delivery and technical details.

---

## 5. Recommended Dataverse Design

### 5.1 Primary table: `bshcs_smslog` (activity - custom)

**Type**: Custom activity table (new)  
**Ownership**: User or Team (default ownership for activity table)

#### Core columns

| Column (Logical Name) | Type | Purpose |
|---|---|---|
| `activityid` | GUID | Primary key |
| `subject` | SingleLine.Text | Human-readable SMS log title/reference |
| `description` | MultiLine.Text | Message body and/or technical notes |
| `regardingobjectid` | Lookup (polymorphic) |
| `bshcs_externalid` | SingleLine.Text |
| `customers` | Customer (`account`/`contact`) | Recipient business party context |
| `to` | Activity party lookup | SMS recipient party |
| `from` | Activity party lookup | SMS sender party |
| `senton` | DateTime | Send timestamp |
| `deliverylastattemptedon` | DateTime | Latest delivery attempt timestamp |
| `statecode` | State | Open, Completed, Canceled, Scheduled |
| `statuscode` | Status | Customizable |
| `ownerid` | Owner | Record owner |
| `createdon` | DateTime | Created timestamp |
| `modifiedon` | DateTime | Last updated timestamp |


### 5.2 Relationship model

| Relationship | Type | Status |
|---|---|---|
| `bshcs_smslog.regardingobjectid` | N:1 |Available |
| `activity_pointer_bshcs_testsms` | N:1 | Available |
| `bshcs_testsms_systemuser_owninguser` | N:1 | Available |


---

## 6. Rejected Options and Reasons

### Rejected table options

| Table Candidate | Why It Looked Viable | Why It Was Rejected |
|---|---|---|
| `msdyn_ocoutboundmessage` as source of truth | Native outbound channel entity with existing relationships and UI integration. | Future field growth is likely and this table is managed/limited here; relying on it as log creates future migration and supportability risk. Not customizable. |
| `activitypointer` as primary | Broad activity timeline support. | Semantic mismatch for high-volume technical SMS telemetry and weaker query/reporting clarity for channel operations. |

### Fit/Gap matrix

| Evaluation criteria | `msdyn_ocoutboundmessage` as primary | `activitypointer` | `bshcs_smslog` primary + optional OC projection |
|---|---|---|---|
| Works today with no extra fields | Strong | Partial | Strong |
| Supports likely future extra fields | Weak | Partial | Strong |
| Upgrade/support safety | Partial | Partial | Strong |
| Operational clarity for SMS diagnostics | Partial | Weak | Strong |
| Long-term change cost | Weak | Weak | Strong |

### Rejection conclusion

For a roadmap where additional SMS info is likely, system-table-only logging is intentionally rejected as the primary strategy.

---

## 7. Explicit Limitation and Decision Gate

### Limitation

If the team still chooses `msdyn_ocoutboundmessage` as sole log now, that choice is valid only while requirements remain limited to existing fields.

### Decision gate

Trigger migration to custom-primary strategy immediately when any of these appear:
1. New required fields not available in `msdyn_ocoutboundmessage`.
2. Need to retain provider payload/body beyond available standard columns.
3. Need deterministic technical audit model not represented in system table.

---

## 8. System Table Field Inventory (Reference)

`msdyn_ocoutboundmessage` currently exposes these key usable columns:
- `subject`
- `description`
- `msdyn_ocmessagetext`
- `regardingobjectid`
- `customers`
- `to`
- `from`
- `msdyn_occhanneltype`
- `msdyn_ocoutboundconfiguration`
- `msdyn_failurereason`
- `msdyn_failurestatuscode`
- `senton`
- `createdon`
- `modifiedon`
- `deliverylastattemptedon`
- `senton`
- `msdyn_ocsenderchannelid`
- `statecode`
- `statuscode` (Open, Completed, Canceled, Scheduled)

---

## 9. Final Recommendation

Given business expectation that additional info is likely, and to avoid system internal field usage, the recommended approach is:

1. **Primary**: `bshcs_smslog` for canonical SMS data and future growth.
2. Keep all custom/technical diagnostics in custom table only.

This approach is the most supportable, upgrade-safe, and lowest-risk path for evolving SMS requirements.
