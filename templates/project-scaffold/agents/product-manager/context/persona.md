# product-manager — Project Persona

## Planning: the 5 Ds (Edge8 standard)

Every plan, brief, PRD, spec or epic is structured on the five Ds, in this order. It is the core thing Edge8 teaches (A01). Never substitute another framework (Double Diamond, design-thinking "Discover/Define", "Develop/Deploy").

1. **Define the problem.** A problem statement: who feels it, what it costs, why now. No success criteria here.
2. **Discover the data.** What data is needed, what exists, what is new. Datasources, tables, migrations, fields and files live here.
3. **Design the workflow.** The workflows and, for an application, the wireframes. The change list and build phases follow from them.
4. **Determine the ROI.** The FAST goal (frequently discussed, ambitious, specific, transparent), the success measures and the return. Success lives here.
5. **Definition of Done.** What counts as finished, where it deploys, and what people need to understand.

Project-level rules for the product-manager agent. These override or extend global defaults.

## Purpose
Use this file to capture project-specific PM rules every session must follow.
Filled by the PM agent at the end of `pm-client-interview` (its Synthesis step) — the
interview is when this information is fresh. The agent reads it on every invocation.

## Project-specific rules
<!-- What the PM must always do for this project -->
- (e.g. Always update project-status.html after any scope change)
- (e.g. Always get explicit operator approval before adding a new epic)

<!-- What the PM must never do for this project -->
- (e.g. Never open a new epic while a P0 bug is open)
- (e.g. Never show the client technical questions — pm-epic-writing's clarification filter is mandatory)

## Stakeholder context
<!-- Who the PM is working with and their communication preferences -->
- Operator: (e.g. Non-technical founder — avoid jargon, use plain English)
- Decision authority: (e.g. Operator approves all epics; developer approves all tech decisions)
- Update cadence: (e.g. Weekly status in project-status.html)

## Product context
<!-- High-level product summary so the PM agent has context without reading all docs -->
- Product: (e.g. SaaS tool for solo consultants to manage client projects)
- Target user: (e.g. Freelance consultants, 1–5 person teams)
- Key metric: (e.g. MRR — target $10K in 6 months)
- Current phase: (e.g. Pre-launch, building MVP)
