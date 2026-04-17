# Data Model: SMS Outbound Notification Logging

**Business Objective**: Enable SMS as an outbound communication channel for repair appointment notifications (BO-1.1 / DDP-59704)

**Date**: 2026-04-17

---

## 0. General Strategy (v3)

The strategy is to use **only** the custom activity table `bhscs_logsms` as the system of record for SMS outbound logging.

### Rationale

`msdyn_ocoutboundmessage` is a native platform table whose records are **not guaranteed to exist** when an SMS is sent. Record creation is gated behind two optional toggles on the outbound configuration (`msdyn_ocoutboundconfiguration`):

| Toggle | Field | Default |
|---|---|---|
| Show in timeline | `msdyn_showinactivities` | **False** |
| Enable Message Logging | `msdyn_enablemessagelogging` | **False** |

Both defaults are `False`. This means that in a standard deployment, no `msdyn_ocoutboundmessage` record is created even when an SMS is successfully sent. Additionally, the table is not customizable and cannot be extended with business-required fields.

Consequently:
1. **If no extension is needed**, `msdyn_ocoutboundmessage` suffices — provided `msdyn_showinactivities` and/or `msdyn_enablemessagelogging` are explicitly enabled in the outbound configuration.
2. **If any extension is needed** (additional fields, custom diagnostics, provider metadata, compliance data), `msdyn_ocoutboundmessage` cannot be used and `bhscs_logsms` is required.
3. For this engagement, `bhscs_logsms` is selected as the sole canonical log because extension is expected and the native record is not guaranteed in a standard deployment.

---

## 1. Microsoft Guidance Validation

This strategy is aligned with Microsoft documentation:

1. **Supported customizations only**: Microsoft states unsupported modifications are not preserved during upgrades and can break with updates.
   - Source: Supported customizations for Dataverse
   - URL: https://learn.microsoft.com/power-apps/developer/data-platform/supported-customizations

2. **Use standard metadata when it fits; create new metadata when it does not**: Microsoft recommends standard tables first, but explicitly says if they do not fit or cannot be edited to match need, create new tables/columns/relationships.
   - Source: Tables and metadata in Dataverse
   - URL: https://learn.microsoft.com/power-apps/maker/data-platform/create-edit-metadata#create-new-metadata-or-use-existing-metadata

3. **msdyn_ocoutboundconfiguration — Show in timeline and Enable Message Logging are opt-in and default to False**: Microsoft documentation confirms that outbound activity record creation in Dataverse depends on toggles that are off by default. For high-volume scenarios, Microsoft explicitly recommends leaving Show in timeline at No to conserve resources and storage.
   - Source: Configure outbound messaging
   - URL: https://learn.microsoft.com/en-us/dynamics365/customer-service/administer/outbound-messaging
   - Source: Outbound Configuration entity reference (`msdyn_showinactivities` DefaultValue: False — *"If this is turned on, outbound activity record will be created in CRM."*; `msdyn_enablemessagelogging` DefaultValue: False)
   - URL: https://learn.microsoft.com/en-us/dynamics365/customer-service/develop/reference/entities/msdyn_ocoutboundconfiguration

---

## 2. Risks and Cons of trying to adjust System/Managed Tables

### Key risks

1. **Extensibility constraints**: In this environment, `msdyn_ocoutboundmessage` cannot be safely extended for future telemetry or business fields.
2. **Configuration dependency**: Native record creation depends on channel configuration, so relying on it as canonical logging can create data gaps.
3. **Upgrade risk**: Any workaround outside supported customization paths is not supportable and may be overwritten or broken in updates.
4. **Dependency coupling**: First-party apps/flows may depend on platform behavior of system tables; over-coupling custom logic to them increases regression risk.
5. **Migration debt later**: If implementation starts native-only and extension needs appear, migration and dual-query complexity become unavoidable.

If the scope expects additional diagnostics, provider metadata, or compliance fields later, native-only logging creates avoidable rework and risk.

---

## 3. Table Comparison

| Logical Name | Type | Record guaranteed on send | Extensible | Verdict |
|---|---|---|---|---|
| `bhscs_logsms` | Custom activity | Yes (flow-controlled) | Yes | **Selected as sole canonical SMS log** |
| `msdyn_ocoutboundmessage` | Standard managed | Yes (optional, opt-in toggles default to False) | No | Valid only if no extension needed |
| `activitypointer` | Standard | N/A | Weak | Rejected |

---

## 4. Architecture Proposal

### Primary strategy

All outbound SMS log records are written exclusively to `bhscs_logsms`. `msdyn_ocoutboundmessage` is not part of the logging architecture.

### Recommended operating model

1. Trigger flow creates SMS log record in `bhscs_logsms`.
2. Router/callback flows update status and technical details in `bhscs_logsms`.
3. `msdyn_ocoutboundmessage` is not relied upon; its record existence cannot be assumed.

---

## 5. Recommended Dataverse Design

### 5.1 Primary extension table: `bhscs_logsms` (activity - custom)

**Type**: Custom activity table  
**Ownership**: User or Team (default ownership for activity table)

#### Core columns (currently available)

| Column (Logical Name) | Type | Purpose |
|---|---|---|
| `activityid` | GUID | Primary key |
| `subject` | SingleLine.Text | Human-readable SMS log title/reference |
| `description` | MultiLine.Text | Message body and/or technical notes |
| `regardingobjectid` | Lookup (polymorphic) | Case/work-order context |
| `bshcs_externalid` | SingleLine.Text |
| `customers` | Customer (`account`/`contact`) | Recipient business party context |
| `to` | Activity party lookup | SMS recipient party |
| `from` | Activity party lookup | SMS sender party |
| `senton` | DateTime | Send timestamp |
| `deliverylastattemptedon` | DateTime | Latest delivery attempt timestamp |
| `statecode` | State | Open, Completed, Canceled, Scheduled |
| `statuscode` | Status | Open, Completed, Canceled, Scheduled |
| `ownerid` | Owner | Record owner |
| `createdon` | DateTime | Created timestamp |
| `modifiedon` | DateTime | Last updated timestamp |


### 5.2 Relationship model

| Relationship | Type | Status |
|---|---|---|
| `bshcs_smslog.regardingobjectid` | N:1 |Available |
| `activity_pointer_bhscs_logsms` | N:1 | Available |
| `bhscs_logsms_systemuser_owninguser` | N:1 | Available |

---

## 6. Rejected Options and Reasons

### Rejected table options

| Table Candidate | Why It Looked Viable | Why It Was Rejected |
|---|---|---|
| `msdyn_ocoutboundmessage` as sole source of truth | Native outbound channel entity with existing relationships and UI integration. | Record creation is configuration-dependent and table is not customizable for extension needs. |
| `activitypointer` as primary | Broad activity timeline support. | Semantic mismatch for high-volume technical SMS telemetry and weaker operational reporting clarity. |

### Rejection conclusion

When functionality and fields need extension, custom table usage is necessary; native-only logging is intentionally not selected as the primary pattern.

---

## 7. Decision Rationale

`bhscs_logsms` is the only viable canonical log table for this engagement because:

1. `msdyn_ocoutboundmessage` records are **not created by default** when an SMS is sent (`msdyn_showinactivities` defaults to False).
2. Even if the toggle is enabled, the native table **cannot be extended** with additional fields.
3. Microsoft recommends disabling the toggle for high-volume scenarios to conserve platform resources — making the native record unreliable as a system of record.
4. `bhscs_logsms` guarantees a record exists for every SMS sent because its creation is controlled by the triggering flow, not by a platform configuration toggle.

> **Note**: If no extension is ever needed, `msdyn_ocoutboundmessage` is a valid alternative — provided `msdyn_showinactivities` is explicitly set to Yes in the outbound configuration. In that scenario, no custom table is required.

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
- `msdyn_ocsenderchannelid`
- `statecode`
- `statuscode` (Open, Completed, Canceled, Scheduled)

---

## 9. Final Recommendation

Use `bhscs_logsms` as the **sole canonical log table** for all outbound SMS records.

`msdyn_ocoutboundmessage` is excluded from **this engagement's** logging strategy because extension is expected and native records are not guaranteed in a standard deployment.

> **Exception**: If requirements change and no extension is ever needed, `msdyn_ocoutboundmessage` becomes a valid alternative — but only when `msdyn_showinactivities` is explicitly enabled in the outbound configuration. In that scenario, no custom table is required and the native table is sufficient.

This is the most reliable, supportable, and lowest-risk path for SMS logging under the confirmed platform behavior.

---

## 10. Proposal Summary

The proposed design uses `bhscs_logsms` as the main outbound SMS logging table because it guarantees deterministic record creation per sent message and supports future extensions (custom fields, diagnostics, provider metadata, and compliance attributes), while `msdyn_ocoutboundmessage` remains non-extensible; therefore, for this engagement, custom-table-first is the safest and most supportable architecture, with the documented exception that native-only logging is acceptable if and only if no extension is ever required and timeline/message logging toggles are explicitly enabled.
