## Security Role Proposal: contactcenter-case-to-booking-validating

**Environment**: bsh-paramount-DEV-CC (5d64560b-62aa-f011-8704-000d3a69df60)
**Purpose**: Enable contact center agents to complete the flow from channel intake to case handling, work-order conversion, and appointment booking.
**Requirement**: End-to-end handling of email or omnichannel conversations, contact and account identification or creation, customer asset handling, functional location handling, case processing, case status mapping reference, diagnose code usage, QT reference usage, billing functional location handling, repair batch handling, queue work, case-to-work-order conversion, appointment booking, and booking cancellation.
**Business Case**: Agents must complete the service intake and dispatch-preparation flow so the field service team receives a ready-to-execute work order and booking.
**Baseline Role**: `Basic User`
**Reference Roles Provided**: BSH Contact Center - Agent (cab1d897-b908-f111-8406-000d3abe0679)

### Existing Role Compliance Check
- Basic User: Partial - conceptual minimum baseline only; in this environment it did not return relevant role privilege rows through the live API comparison and is not sufficient for the service flow.
- BSH Contact Center - Agent: Partial - already covers most case, customer, asset, functional location, queue, omnichannel, and work-order capabilities, but does not fully cover booking creation/update/delete and is missing read access for `bshcs_casestatusmapping`.

**Conclusion**: No compliant role existed yet. A new validating role was created by cloning the reference privileges and adding the exact supported deltas below.

### Proposed Role
- **Role Name**: `contactcenter-case-to-booking-validating`
- **Scope**: Business Unit (`Local`) for transactional service tables, Organization (`Global`) only where already justified by shared lookup/configuration data, and no extra admin scope.
- **Starting Point**: Clone or extend `Basic User`, using `BSH Contact Center - Agent` as the operational comparison baseline in this environment.
- **Temporary Intent**: Validation only until tests and business sign-off are complete.
- **Solution**: `testroles`

### Reference Role Analysis
| Reference Role | Relevant Privileges Reused | Privileges Intentionally Excluded | Notes |
|---|---|---|---|
| BSH Contact Center - Agent | `account`, `contact`, `incident`, `queue`, `msdyn_customerasset`, `msdyn_functionallocation`, `bshcs_billingfunctionallocation`, `bshcs_repairbatch`, `msdyn_workorder`, `msdyn_workorderincident`, omnichannel runtime/config, and supporting read/reference privileges | No extra admin/customization/security privileges; no additional assign/share expansion on booking records; no write access to reference tables like `bshcs_qt_response` or `bshcs_casestatusmapping` | Verified live in Dataverse with role ID `cab1d897-b908-f111-8406-000d3abe0679` |

### Privilege Matrix
| Table/Process | Privilege | Access Level | Include? | Source | Dependency Type | Rationale |
|---|---|---|---|---|---|---|
| account | Create | Local | Yes | Reference role | Primary requirement | Already present and needed to create customer accounts during intake. |
| account | Read | Local | Yes | Reference role | Primary requirement | Already present and needed to find customer accounts within BU operations. |
| account | Write | Local | Yes | Reference role | Primary requirement | Already present and needed to maintain customer context. |
| account | Append | Local | Yes | Reference role | Underlying dependency | Already present and needed to relate cases, assets, and service records. |
| account | AppendTo | Local | Yes | Reference role | Underlying dependency | Already present and needed for relationship binding. |
| contact | Create | Local | Yes | Reference role | Primary requirement | Already present and needed to create customer contacts. |
| contact | Read | Local | Yes | Reference role | Primary requirement | Already present and needed to identify contacts. |
| contact | Write | Local | Yes | Reference role | Primary requirement | Already present and needed to maintain contact context. |
| contact | Append | Local | Yes | Reference role | Underlying dependency | Already present and needed for service relationships. |
| contact | AppendTo | Local | Yes | Reference role | Underlying dependency | Already present and needed for service relationships. |
| incident | Create | Local | Yes | Reference role | Primary requirement | Already present and needed to create cases. |
| incident | Read | Local | Yes | Reference role | Primary requirement | Already present and needed to work team/BU cases. |
| incident | Write | Local | Yes | Reference role | Primary requirement | Already present and needed to progress cases. |
| incident | Append | Local | Yes | Reference role | Underlying dependency | Already present and needed to bind related service records. |
| incident | AppendTo | Local | Yes | Reference role | Underlying dependency | Already present and needed to bind related service records. |
| queue | Read | Global | Yes | Reference role | Primary requirement | Already present and needed for queue visibility. |
| queue | AppendTo | Global | Yes | Reference role | Underlying dependency | Already present and needed for queue item operations. |
| msdyn_customerasset | Create | Local | Yes | Reference role | Primary requirement | Already present and needed to create customer assets. |
| msdyn_customerasset | Read | Local | Yes | Reference role | Primary requirement | Already present and needed to identify customer assets. |
| msdyn_customerasset | Write | Local | Yes | Reference role | Primary requirement | Already present and needed to update asset data. |
| msdyn_customerasset | Append | Local | Yes | Reference role | Underlying dependency | Already present and needed for service relationships. |
| msdyn_customerasset | AppendTo | Local | Yes | Reference role | Underlying dependency | Already present and needed for service relationships. |
| msdyn_functionallocation | Create | Local | Yes | Reference role | Primary requirement | Already present and needed to create functional locations. |
| msdyn_functionallocation | Read | Local | Yes | Reference role | Primary requirement | Already present and needed to identify functional locations. |
| msdyn_functionallocation | Write | Local | Yes | Reference role | Primary requirement | Already present and needed to update functional locations. |
| msdyn_functionallocation | Append | Local | Yes | Reference role | Underlying dependency | Already present and needed for service relationships. |
| msdyn_functionallocation | AppendTo | Local | Yes | Reference role | Underlying dependency | Already present and needed for service relationships. |
| bshcs_billingfunctionallocation | Create | Local | Yes | Reference role | Primary requirement | Already present and needed for billing functional location handling. |
| bshcs_billingfunctionallocation | Read | Local | Yes | Reference role | Primary requirement | Already present and needed for billing functional location handling. |
| bshcs_billingfunctionallocation | Write | Local | Yes | Reference role | Primary requirement | Already present and needed for billing functional location handling. |
| bshcs_billingfunctionallocation | Append | Local | Yes | Reference role | Underlying dependency | Already present and needed for service relationships. |
| bshcs_billingfunctionallocation | AppendTo | Local | Yes | Reference role | Underlying dependency | Already present and needed for service relationships. |
| bshcs_repairbatch | Create | Local | Yes | Reference role | Primary requirement | Already present and needed for repair batch handling. |
| bshcs_repairbatch | Read | Local | Yes | Reference role | Primary requirement | Already present and needed for repair batch handling. |
| bshcs_repairbatch | Write | Local | Yes | Reference role | Primary requirement | Already present and needed for repair batch handling. |
| bshcs_repairbatch | Append | Local | Yes | Reference role | Underlying dependency | Already present and needed for service relationships. |
| bshcs_repairbatch | AppendTo | Local | Yes | Reference role | Underlying dependency | Already present and needed for service relationships. |
| bshcs_diagnosecode | Read | Global | Yes | Reference role | Underlying dependency | Already present and treated as shared reference data. |
| bshcs_qt_cause | Read | Global | Yes | Reference role | Underlying dependency | Already present and treated as shared reference data. |
| bshcs_qt_fault | Read | Global | Yes | Reference role | Underlying dependency | Already present and treated as shared reference data. |
| bshcs_qt_faulttype | Read | Global | Yes | Reference role | Underlying dependency | Already present and treated as shared reference data. |
| bshcs_qt_project | Read | Global | Yes | Reference role | Underlying dependency | Already present and treated as shared reference data. |
| bshcs_qt_question | Read | Global | Yes | Reference role | Underlying dependency | Already present and treated as shared reference data. |
| bshcs_qt_response | Read | Global | Yes | Reference role | Underlying dependency | Already present and confirmed to remain reference only. |
| bshcs_qt_task | Read | Global | Yes | Reference role | Underlying dependency | Already present and treated as shared reference data. |
| bshcs_qt_translation | Read | Global | Yes | Reference role | Underlying dependency | Already present and treated as shared reference data. |
| bshcs_qt_troubleshoot | Read | Global | Yes | Reference role | Underlying dependency | Already present and treated as shared reference data. |
| bshcs_casestatusmapping | Read | Local | Yes | Newly required | Underlying dependency | Missing from the reference role and needed as read-only support for case progression logic. |
| msdyn_workorder | Create | Local | Yes | Reference role | Primary requirement | Already present and needed for case-to-work-order conversion. |
| msdyn_workorder | Read | Local | Yes | Reference role | Primary requirement | Already present and needed for work-order review. |
| msdyn_workorder | Write | Local | Yes | Reference role | Primary requirement | Already present and needed for work-order preparation. |
| msdyn_workorder | Append | Local | Yes | Reference role | Underlying dependency | Already present and needed for service relationships. |
| msdyn_workorder | AppendTo | Local | Yes | Reference role | Underlying dependency | Already present and needed for service relationships. |
| msdyn_workorderincident | Create | Local | Yes | Reference role | Underlying dependency | Already present and needed for work-order detail generation. |
| msdyn_workorderincident | Read | Local | Yes | Reference role | Underlying dependency | Already present and needed for work-order detail review. |
| msdyn_workorderincident | Write | Local | Yes | Reference role | Underlying dependency | Already present and needed for work-order detail maintenance. |
| msdyn_workorderincident | Append | Local | Yes | Reference role | Underlying dependency | Already present and needed for service relationships. |
| msdyn_workorderincident | AppendTo | Local | Yes | Reference role | Underlying dependency | Already present and needed for service relationships. |
| msdyn_workorderservice | Read | Local | Yes | Reference role | Underlying dependency | Already present and sufficient as reference in current design. |
| msdyn_workorderservicetask | Read | Local | Yes | Reference role | Underlying dependency | Already present and sufficient as reference in current design. |
| msdyn_resourcerequirement | Create | Local | Yes | Reference role | Underlying dependency | Already present and needed for scheduling setup. |
| msdyn_resourcerequirement | Read | Local | Yes | Reference role | Underlying dependency | Already present and needed for scheduling setup. |
| msdyn_resourcerequirement | Append | Local | Yes | Reference role | Underlying dependency | Already present and needed for scheduling setup. |
| msdyn_resourcerequirement | AppendTo | Local | Yes | Reference role | Underlying dependency | Already present and needed for scheduling setup. |
| msdyn_resourcerequirement | Write | Local | Yes | Newly required | Underlying dependency | Missing from the reference role and needed to fully manage scheduling requirements. |
| bookableresource | Read | Global | Yes | Reference role | Underlying dependency | Already present and needed to select/understand resources. |
| bookingstatus | Read | Local | Yes | Reference role | Underlying dependency | Already present and needed for booking state handling. |
| calendar | Create | Global | Yes | Reference role | Underlying dependency | Already present in the reference role. |
| calendar | Read | Global | Yes | Reference role | Underlying dependency | Already present in the reference role. |
| calendar | Write | Global | Yes | Reference role | Underlying dependency | Already present in the reference role. |
| calendar | Append | Global | Yes | Reference role | Underlying dependency | Already present in the reference role. |
| calendar | AppendTo | Global | Yes | Reference role | Underlying dependency | Already present in the reference role. |
| bookableresourcebooking | Read | Local | Yes | Reference role | Primary requirement | Already present and needed for booking review. |
| bookableresourcebooking | Create | Local | Yes | Newly required | Primary requirement | Missing from the reference role and needed to create bookings. |
| bookableresourcebooking | Write | Local | Yes | Newly required | Primary requirement | Missing from the reference role and needed to modify bookings. |
| bookableresourcebooking | Delete | Local | Yes | Newly required | Primary requirement | Missing from the reference role and explicitly required for booking cancellation. |
| bookableresourcebooking | AppendTo | Local | Yes | Newly required | Underlying dependency | Missing from the reference role and needed to associate related service records to bookings. |

### Underlying Dependencies Considered
- Queue handling was retained because `ReadQueue` and `AppendToQueue` are already present and required for queue item operations.
- Booking dependencies were validated live: the reference role already has `ReadBookableResource`, `ReadBookableResourceBooking`, `ReadBookingStatus`, calendar access, and resource requirement setup, but not booking create/write/delete.
- `bookableresourcebooking` does not expose a separate `Append` privilege in this environment, so only `AppendTo` was added as a supported delta.
- `bshcs_qt_response` was kept read-only because the user confirmed it is reference-only.
- `bshcs_casestatusmapping` was added as read-only because it is absent from the reference role but required by the process.
- No extra assign/share privileges were added for booking because they are not required to achieve the stated objective.

### Risk Controls
- Keep booking privileges at `Local` to avoid unnecessary org-wide booking manipulation.
- Keep `bshcs_casestatusmapping` read-only.
- Keep `bshcs_qt_response` read-only.
- Do not add booking `Assign` or `Share` privileges.
- Do not add admin, customization, or security-management privileges.

### Microsoft Guidance Referenced
- Security roles and privileges for Dataverse: https://learn.microsoft.com/power-platform/admin/security-roles-privileges
- Security concepts in Microsoft Dataverse: https://learn.microsoft.com/power-platform/admin/wp-security-cds
- Security concepts for developers: https://learn.microsoft.com/power-apps/developer/data-platform/security-concepts
- Queue tables: https://learn.microsoft.com/power-apps/developer/data-platform/queue-entities#inherit-privileges-and-provide-limited-access-to-a-queue
- RetrieveRolePrivilegesRole Function: https://learn.microsoft.com/en-us/power-apps/developer/data-platform/webapi/reference/retrieveroleprivilegesrole

## Final Output

- **Environment confirmation**: user `dee7mu@bosch.com`, organization `5d64560b-62aa-f011-8704-000d3a69df60`, friendly name `bsh-paramount-DEV-CC`
- **Purpose and requirement interpreted**: contact center agents must complete intake, service-case handling, work-order conversion, booking creation, booking modification, and booking cancellation while keeping reference/configuration data read-only where appropriate
- **Baseline role confirmation**: `Basic User` remains the conceptual baseline rule; `BSH Contact Center - Agent` is the verified operational comparison baseline in this environment
- **Reference roles analyzed**: `BSH Contact Center - Agent` (`cab1d897-b908-f111-8406-000d3abe0679`)
- **Existing-role compliance outcome**: the reference role was partially compliant; the created validating role closes the verified gaps by adding `bookableresourcebooking` create/write/delete/appendto, `msdyn_resourcerequirement` write, and `bshcs_casestatusmapping` read
- **Role review outcome**: approved and created in the environment
- **Created validating role name**: `contactcenter-case-to-booking-validating` (`3cad4e10-4f38-f111-88b5-6045bd96431c`)
- **Solution where role was added**: `testroles`
- **Privilege/depth matrix actually implemented**: cloned 481 privileges from `BSH Contact Center - Agent`, added 6 supported deltas, and verified a final total of 487 privileges on the created role
- **Underlying dependencies considered**: queue handling, scheduling requirements, booking dependencies, QT reference tables, diagnose codes, case status mapping, customer/service transactional records
- **References to Microsoft documentation consulted**: see links above
