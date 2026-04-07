# 046-ADR Hierarchy of Criticality for Customizations

Source:
- URL: https://confluence.bsh-group.com/spaces/PARAMOUNT/pages/4333860939/046-ADR+Hierarchy+of+Criticality+for+Customizations
- Space: PARAMOUNT
- Page ID: 4333860939
- Retrieved: 2026-04-07

## Context

Primary responsibility is to protect the integrity of the core system. Every deviation from standard Dynamics behavior increases initial cost and long-term maintenance risk, especially during Microsoft release waves.

## Criticality Hierarchy

From highest to lowest risk:

1. P1 - Database schema changes (Avoidable/Critical)
- Modifying standard fields or deleting/repurposing system entities.
- Rule: forbidden. Use standard CDM metadata as-is.

2. P2 - Deep custom code (Plugins and workflow extensions)
- Event-based custom code that moves away from low-code.
- Rule: only if low-code/configuration is demonstrably insufficient.

3. P3 - UI manipulation (DOM/CSS/script hacks)
- Unsupported UI shaping to mimic legacy behavior.
- Rule: avoid unsupported manipulation. Use supported model-driven patterns or embedded Canvas app when required.

4. P4 - Complex integrations (middleware-sensitive)
- Deep custom service orchestration beyond standard connectors.
- Rule: identify complexity early and push deep integration concerns to middleware architecture.

5. Near-standard path - Configuration and new entities
- New tables/fields, model-driven configuration, business rules, supported extension points.
- Rule: preferred path. Configure, do not customize.

## Summary Decision Matrix

| Priority | Type | Risk | Recommendation |
|---|---|---|---|
| P1 | Schema change | Extremely high | Reject and adapt process to standard |
| P2 | Custom code | High | Only if low-code is insufficient |
| P3 | UI customization | Medium | Prefer standard supported UX patterns |
| P4 | Complex integration | Medium/High | Permit with explicit architecture and risk controls |
| Preferred | Configuration/new entities | Low | Standard implementation path |

## Mandatory Mapping Rule For This Workflow

For each DDP and acceptance criterion, record one of the following statuses:
- Aligned
- Exception - Approved
- Exception - Needs Decision
- Not Applicable

If a requirement or AC does not fit the ADR hierarchy directly, it must include explicit rationale:
- why it does not fit,
- what alternative was considered,
- risk introduced,
- mitigation,
- whether DAB decision is required.

## DAB Check Question

If deviation from standard is being considered (especially P1 or P2):

"Is this process step so critical to competitive advantage that we accept reduced updateability and AI compatibility for the next five years?"
