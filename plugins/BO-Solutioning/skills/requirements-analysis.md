# Skill: Requirements Analysis

> **Purpose:** Defines the cognitive process, heuristics, and techniques used to analyze, decompose, and classify business requirements for Dynamics 365 and Power Platform engagements.

---

## When This Skill Is Applied

This skill is invoked by:
- **Implementation Classifier Agent** — primary consumer (post-design classification)
- **Solution Designer Agent** — when mapping requirements to design components

---

## Core Techniques

### 1. Decomposition

Break compound requirements into atomic, testable statements:

> ❌ "The system should handle customer service efficiently."  
> ✅ "Cases must be automatically routed to the appropriate queue based on case type within 30 seconds of creation."

**Rule:** One requirement = one verifiable behavior.

After decomposition, map each behavior to actionable technical concerns:

- Data model impact
- User experience / app surface
- Automation or business logic
- Integration need
- Security / compliance implication
- Licensing or capacity implication

### 2. Disambiguation

For vague terms, apply the **SMART** test:
- **Specific** — Is the requirement specific enough to design against?
- **Measurable** — Can it be objectively verified?
- **Achievable** — Is it technically feasible on the platform?
- **Relevant** — Does it support a stated business goal?
- **Testable** — Can a test case be written for it?

Flag requirements that fail any SMART criterion with a clarification question.

### 3. Gap Detection

Identify what is *not* stated but *required* for a complete solution:

- If routing is required → queue structure is also required (even if not mentioned)
- If bot is required → escalation path is also required
- If integration is required → authentication method is also required
- If case management is required → security roles are also required

Add these implied requirements as explicit assumptions in analysis notes or raise them as clarification questions.

Also detect platform-governance implications early:

- If a requirement seems to recreate first-party D365 behavior, flag native-fit review.
- If a requirement suggests custom storage, flag standard-table vs custom-table review.
- If a requirement depends on external data or workflows, flag sync vs async and integration-pattern review.
- If a requirement may rely on roadmap or early features, flag release-wave / Preview review.

### 4. Conflict Detection

Identify requirements that contradict each other:

- Requirement A: "All agents see all cases" vs. Requirement B: "Cases are restricted by region"  
- Requirement A: "No custom code" vs. Requirement B: "Complex real-time API integration"  

Flag conflicts and present both requirements with a note: "These requirements conflict — stakeholder decision required."

Also flag when a requirement conflicts with platform supportability or Microsoft policy, for example unsupported database access, insecure credential handling, or excessive customization of first-party behavior.

### 5. Prioritization Assistance

When priority is not provided by the business, apply this heuristic to propose priority:

| Indicator | Suggested Priority |
|---|---|
| Required for regulatory compliance | P1-MUST |
| Blocking core business process | P1-MUST |
| Significantly improves agent efficiency | P2-SHOULD |
| Replaces manual workaround | P2-SHOULD |
| Requested by a single stakeholder group | P3-COULD |
| Nice to have / low adoption expected | P3-COULD |
| Future phase / unclear ROI | P4-WONT |

**Always flag**: Priority was proposed by the analyst — stakeholder must confirm.

### 6. Feasibility and Limitation Detection

When a requirement is analyzed, state explicitly if it is:

- Natively supported
- Supported only through configuration or low code
- Supported only through advanced integration or custom code
- Dependent on Preview capability
- Not realistically achievable within Microsoft platform constraints

If material limitations exist, document them directly rather than softening them.

### 7. Implementation Class Assignment (Post-Design)

Apply these heuristics after solution design is completed:

| Signal | Implementation Class |
|---|---|
| Native capability with license and role assignment only | OOB |
| Admin center setup (wizards, toggles, routing UI) | COOB |
| Visual build in Power Platform with no custom code | LC |
| Visual build with external systems/APIs/connectors and dependency complexity | ALC |
| Developer-written code in C#, JavaScript, TypeScript, or .NET | CUSTOM |

---

## Output Standards

The output of this skill is always a structured item conforming to the schema in `classification/implementation-classification.md`:

```yaml
id: IMP-NNN
requirement_id: REQ-NNN
title: "[concise one-line title]"
design_reference: "[section in solution design]"
implementation_class: [OOB | COOB | LC | ALC | CUSTOM]
rationale: |
  [Why this class is selected based on the design]
delivery_artifacts:
  - "[artifact 1]"
external_dependencies: []
custom_code_required: false
status: [Classified | Needs Design Detail | Deferred - Phase N | Out of Scope]
notes: "[Flags, questions, or clarifications]"
```

---

## Quality Heuristics

Before finalizing any classified component:

1. Can a developer read this requirement and know exactly what to build? If not → flag for clarification.  
2. Can a tester read this requirement and write a test case? If not → acceptance criteria are missing.  
3. Does this requirement introduce a new Dataverse entity, field, or security role? → cross-reference with domain design sections.  
4. Is this requirement already handled by an OOB platform feature? → note it to avoid over-engineering.  
5. Does this requirement imply licensing, Preview usage, API limits, storage growth, or throttling concerns? → carry that forward into design notes.  
