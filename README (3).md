# Gemini Technical Simulation Package

This folder is the Gemini-specific technical workstream for the AI Governance Toolkit.

## Gemini owns

1. Synthetic student and opportunity data generation
2. Student-ranking or recommendation simulation
3. Statistical fairness and performance calculations
4. Reproducible technical reports and limitations

## Gemini does not own

- EU AI Act legal interpretation
- Final governance decisions
- Privacy or accessibility approval
- Human-oversight policy
- Final executive recommendation

Those responsibilities remain with the governance agent files in `agents/`.

## Execution order

`synthetic-data-generator.md` → `student-ranking-simulator.md` → `fairness-audit.md` → `technical-handoff-template.md`

## Safety boundary

All demographic attributes are synthetic audit metadata. They must not be inferred from student photographs, used as baseline ranking inputs, or treated as evidence about real students.
