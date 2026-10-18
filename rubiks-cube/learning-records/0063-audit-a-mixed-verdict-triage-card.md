# 0063 — Audit a Mixed-Verdict Triage Card

**Date:** 2026-10-18  
**Lesson:** `0063-audit-a-mixed-verdict-triage-card.html`

## What was taught

- Audit each completed mixed-verdict triage-card line in a fixed order: classification, literal note, then exactly one instruction.
- Identify the earliest failure and make only its minimal repair: condition drift becomes a **FRESH EVIDENCE** replacement-attempt instruction; inaccurate evidence becomes a literal-evidence repair; an unsupported or duplicated action becomes one evidence-matched action.
- Preserve valid rows and valid clauses rather than using one bad card line as a reason to rewrite the set.

## Key non-obvious insights

- A card line marked **ACT** cannot be rescued by a persuasive mismatch story when its four condition fields drifted; drift ends the audit at classification.
- Comparable observations survive a prose error: replace only the inaccurate evidence or action clause, not the recorded Fresh E/F attempts.
- The first failing check sets the repair boundary. Later wording never compensates for an earlier condition or evidence failure.

## Zone of proximal development — next

**F2L: classify an audited mixed triage card.** Given an audited multi-row card and minimal repairs, distinguish action-ready lines from evidence-needed lines and select the single next queue action without transferring evidence between rows.
