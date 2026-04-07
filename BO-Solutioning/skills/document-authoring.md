# Skill: Document Authoring

> **Purpose:** Defines the writing standards, tone guidelines, formatting rules, and quality criteria applied when producing the High-Level Solution Document and any other written deliverables in this workflow.

---

## When This Skill Is Applied

This skill is invoked by:
- **Document Generator Agent** — primary consumer (fills the HLD template)
- Any agent producing written output for external/customer audiences

---

## Writing Principles

### 1. Audience Awareness

The HLD has two audiences — write to satisfy both:

| Audience | What They Need | Tone |
|---|---|---|
| Business stakeholders (sponsor, business owner) | Understand *what* will be built and *why* — without technical jargon | Clear, non-technical, outcomes-focused |
| Technical reviewers (architect, developer, consultant) | Understand *how* it works, what will be configured/built | Precise, structured, technically accurate |

**Rule:** Executive Summary and Scope sections → business language.  
**Rule:** Architecture, Design, and Integration sections → technical language with defined terms.

### 2. Be Decisive

Write what *will* be built, not what *could* be:

> ❌ "It may be possible to configure unified routing to route cases..."  
> ✅ "Unified routing will be configured with two workstreams: Digital Messaging and Voice."

If something is uncertain, state it as an open item or assumption — do not hedge the design prose.

### 3. Be Concise

- One idea per sentence.  
- Maximum 3 sentences per paragraph before using a table or list.  
- Use tables for all comparative or structured information (roles, integrations, components).  
- Use bullet lists for enumerable items without rank.  
- Use numbered lists for sequential steps or ranked items.  

### 4. Be Complete (No Placeholders)

A deliverable with `[PLACEHOLDER]` is not a deliverable. Before writing, ensure all inputs are available. If an input is missing, stop and request it rather than leaving a placeholder.

**Exception:** If a section is explicitly out of scope, replace the placeholder with:  
> *"Not in scope for this engagement. Excluded at customer's request on [date]."*

---

## Formatting Standards

### Headings

- Use the heading hierarchy from the template. Do not skip levels.  
- Section headings: Title Case.  
- Subsection headings: Title Case.  

### Tables

- Every table must have a header row.  
- Column headers: Title Case, concise (max 3 words).  
- Every row must be populated — use "N/A" or "TBD" only when genuinely unknown, and flag TBDs as open items.  
- Avoid tables with more than 7 columns — split into two tables if needed.  

### Code and Technical Strings

Output format is **Confluence Wiki** — use Confluence Wiki syntax throughout, not Markdown.

Use inline code (double curly braces) for:
- Dataverse schema names: {{new_customentity}}
- API endpoints: {{GET /api/data/v9.2/cases}}
- Field names: {{createdon}}, {{statuscode}}
- Environment variable names: {{env_ServiceBusConnectionString}}

Use {code} blocks for:
- YAML schemas
- JSON payloads

### Confluence Wiki Formatting Quick Reference

| Element | Confluence Wiki Syntax |
|---|---|
| Heading 1–3 | `h1. Title` / `h2. Title` / `h3. Title` |
| Bold | `*bold text*` |
| Italic | `_italic text_` |
| Inline code | `{{code}}` |
| Link | `[label\|url]` |
| Bullet list | `* item` |
| Numbered list | `# item` |
| Table header row | `\|\| Col1 \|\| Col2 \|\|` |
| Table data row | `\| value1 \| value2 \|` |
| Info panel | `{info}...{info}` |
| Horizontal rule | `----` |
| Status macro | `{status:colour=Green\|title=OOB}` |
- Query strings
- Multi-line technical content

### Diagrams

- Where a diagram file exists (`.png`, `.svg`, `.drawio`), reference it with a relative path.  
- Where no diagram is available, describe the architecture in structured prose with key component names in **bold**.  
- For simple flows, use an ASCII flow diagram.  

---

## Tone and Voice

| Use | Avoid |
|---|---|
| Active voice: "The system routes the case to..." | Passive voice: "The case will be routed by the system..." |
| Present tense for design statements | Future-uncertain: "should be", "might be" |
| Defined acronyms on first use | Undefined acronyms |
| Microsoft product names in full on first use | Internal or informal nicknames |
| "The solution" / "the system" | "The tool" / "the thing" |

### Acronym First-Use Rule

Define every acronym the first time it appears in a section, then use the acronym thereafter.  
Example: "Unified Routing (UR) will be used for all workstreams. UR supports skills-based assignment..."

---

## Section-Specific Writing Guidance

### Executive Summary
- 2–3 paragraphs.  
- What is the business problem? What platform/product is the solution? What are the key outcomes?  
- No technical details. No table. No bullet lists.  

### Architecture Overview
- Describe the layers: user access → UX → logic → data → integration → identity.  
- Name every major component in **bold**.  
- Follow with the technology stack table.  

### Design Sections (D365, PP, Integration, Security, ALM)
- Open each section with one sentence that states what is being designed.  
- Use tables for all structured data (entities, flows, roles, queues).  
- Add a design decision row for any non-obvious choice.  

### Risks & Assumptions
- Be specific — no generic risks ("project may be delayed").  
- Each risk must have a stated mitigation.  
- Each assumption must have a stated impact if wrong.  

---

## Quality Checklist for Written Deliverables

Apply before finalizing any document:

- [ ] No `[PLACEHOLDER]` tokens remain  
- [ ] All acronyms are defined on first use in each section  
- [ ] Tables are properly formatted (header + rows with no blank cells)  
- [ ] Active voice used throughout  
- [ ] Executive Summary uses no technical jargon  
- [ ] Technical sections are sufficiently detailed for a developer to begin design review  
- [ ] All out-of-scope items are noted explicitly  
- [ ] Document version, date, and author are populated in the control block  
