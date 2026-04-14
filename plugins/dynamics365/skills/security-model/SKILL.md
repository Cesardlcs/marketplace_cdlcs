---
name: security-model
description: Design and validate Dataverse security roles from business requirements. Gather requirement and business case, propose role privileges and access levels using Microsoft best practices, check for existing compliant roles, and after approval create a temporary role with "validating" in its name.
license: MIT
compatibility: Designed for GitHub Copilot CLI or Claude Code in Power Platform / Dataverse development projects. Requires PAC CLI >= 2.3.1.
metadata:
  author: Cdlcs
  version: "1.1.0"
  argument-hint: "[security requirement or business case]"
---

# Dataverse Security Model Designer

**Triggers**: dataverse-security, security model, security roles, privileges, access levels, role hardening, least privilege
**Aliases**: /security-model, /dataverse-security, /security-roles

---

## Instructions

Follow these steps in order for every `/security-model` invocation.

### Governing Security Principles (Mandatory)

- **Mínimo privilegio**: Cada rol solo tiene los permisos necesarios para su función.
- **Zero Trust**: No se asume confianza implícita; todo acceso es verificado.

### Step 1: Verify Environment

```powershell
pac auth list
pac auth who
pac env who
```

If there is no authentication, tell the user to run:

```powershell
pac auth create --environment https://your-env.crm.dynamics.com
```

Capture and report:
- Current user
- Environment ID
- Organization ID
- Organization Friendly Name

### Step 2: Collect Requirement And Business Case (Mandatory)

Ask these questions. The solution question is mandatory on every invocation:

1. **Solution target (always ask)**: "Inside which solution should this security role be created?"
2. **Requirement**: "What actions must this role be able to perform, and on which tables/processes?"
3. **Business case**: "What business outcome depends on this access, and what operational risk or service failure occurs if the role does not have it?"
4. **Scope constraints**: "Is this for end users, agents, integrations, or admins? Any data boundaries (BU/team/org)?"
5. **Lifecycle**: "Is this temporary validation access only, or expected to become permanent later?"

Do not proceed to design until solution target, requirement, and business case are explicit.

### Step 3: Research Best Practices And Discover Current Security State

#### 3.1 Microsoft guidance (required)
- Use `mcp_microsoftdocs_microsoft_docs_search` to find current official guidance for:
  - Dataverse security model and privilege depth (User/Business Unit/Parent:Child BU/Organization)
  - Security role design and least privilege
  - Team vs user-owned access strategy where relevant
- Use `mcp_microsoftdocs_microsoft_docs_fetch` on high-value result pages before finalizing the proposal.

#### 3.2 Environment role inventory (required)
- Inspect existing roles in the connected environment and identify candidate matches.
- Use Dataverse MCP and/or PAC CLI as needed to gather:
  - Existing role names and intended purpose
  - Relevant privileges and depth
  - Business unit scope where applicable

Prefer Dataverse MCP for metadata/data retrieval and PAC CLI for environment/session validation and supplemental checks.

If direct role query automation is not available in the current toolset, inspect existing roles through the Power Platform admin center:
1. Go to **Manage** > **Environments** > current environment.
2. Open **Settings**.
3. Open **Users + Permissions** > **Security roles**.
4. Review candidate roles before deciding whether a new validating role is needed.

### Step 4: Propose The Role Plan (Before Any Creation)

Present a structured proposal in plan mode including:

1. **Role name**:
   - Must include the word `validating`.
   - Recommended format: `[domain-or-scope]-[purpose]-validating`.
2. **Role objective** and explicit non-goals.
3. **Privileges matrix** by table/process with:
   - Privilege type (Create, Read, Write, Delete, Append, AppendTo, Assign, Share, plus any special privileges as needed)
   - Access level/depth (User, BU, Parent:Child BU, Organization)
   - Rationale tied to requirement and business case
4. **Segregation and risk controls**:
   - Least privilege justification (Mínimo privilegio)
   - Zero Trust verification points (what is explicitly validated before access is granted)
   - Sensitive actions explicitly denied or out of scope
5. **Validation notes**:
   - Why this role is temporary
   - What must be tested before promotion to a permanent role

### Step 5: Determine If An Existing Role Already Complies (Mandatory)

Before creating anything, provide a compliance check:

- List candidate existing roles that partially or fully match.
- State clearly one of:
  - **Compliant role exists** (no new role required), or
  - **No compliant role exists** (new validating role required).
- Include gap analysis when there is only partial alignment (missing/excess privileges, wrong depth, wrong scope).

### Step 6: Explicit Approval Gate (Do Not Skip)

Ask for user approval after presenting:
- Requirement understanding
- Proposed privilege matrix
- Existing-role compliance result
- Planned role name including `validating`

Do not create or modify roles before explicit approval.

### Step 7: Create The Role After Approval

Create the role in the connected environment using available MCP/PAC capabilities:

1. Create a new role with `validating` in the name.
2. Assign privileges exactly as approved (no implicit privilege inflation).
3. Set privilege depth exactly as approved.
4. Add/create the role inside the user-selected solution.
5. Confirm the created role and summarize applied privileges/depth.

If technical limitations block direct creation, provide the exact command/script payload needed and explain the blocker clearly.

### Step 8: Final Output

Return a concise summary including:
- Environment confirmation (user, org, friendly name)
- Solution where the role was created
- Requirement and business case interpreted
- Existing-role compliance outcome
- Created validating role name
- Privilege/depth matrix actually implemented
- References to Microsoft documentation consulted

### Step 9: Persist Final Output File (Mandatory)

Always write the **Step 8 final output** to a file in an `output` folder.

Rules:
1. Create `security-model/output/` if it doesn't exist.
2. Write one markdown file per execution.
3. Recommended filename format: `security-model/output/[role-name].md`.
4. The file content must be the same final output delivered to the user in Step 8.
5. If role creation is blocked, still write the final output file and include blockers and exact next actions.

## Output Template (Use In Plan Mode Before Approval)

```markdown
## Security Role Proposal: [RolePurpose]-validating

**Environment**: [Friendly Name] ([Organization ID])
**Solution**: [solution unique name]
**Requirement**: [user requirement]
**Business Case**: [user business case]

### Existing Role Compliance Check
- [Role A]: [Compliant / Partial / Not compliant] - [why]
- [Role B]: [Compliant / Partial / Not compliant] - [why]

**Conclusion**: [Compliant role exists / No compliant role exists]

### Proposed Role
- **Role Name**: `[name-with-validating]`
- **Scope**: [User / BU / Parent:Child BU / Organization]
- **Temporary Intent**: Validation only until tests and business sign-off are complete.

| Table/Process | Privilege | Access Level | Include? | Rationale |
|---|---|---|---|---|
| account | Read | BU | Yes | [reason] |
| incident | Write | User | Yes | [reason] |
| incident | Delete | User | No | [risk control reason] |

### Risk Controls
- [control 1]
- [control 2]

### Microsoft Guidance Referenced
- [doc title](url)
- [doc title](url)
```
