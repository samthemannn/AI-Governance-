# Gemini Technical Workstream: Campus Mentoring Experiment Plan

**Document:** Campus Mentoring Recommendation Simulation & Stress Testing Plan  
**Workstream:** Gemini Technical Simulation Package  
**Status:** Draft for Review  
**Date:** 2026-10-31  
**Random Seed:** 42  
**Handoff Target:** Governance Agent Package (`agents/`)

---

## 1. Executive Summary & Objective

The objective of this technical workstream is to simulate, evaluate, and stress-test an algorithmic ranking and recommendation system for a fictional higher education platform. The target application is **Campus Mentoring Opportunities** (pairing undergraduate students with alumni, faculty, and industry mentors for career and academic support).

The simulation will produce reproducible datasets, simulate baseline multi-factor scoring, inject controlled socioeconomic and institutional proxy features, and run rigorous fairness audits across synthetic demographic groups.

### Primary Objectives:
1. Generate a statistically realistic, fully synthetic student population ($N = 1,000$) and opportunity portfolio ($M = 50$) under fixed random seeds.
2. Implement a transparent, linear baseline ranking algorithm relying strictly on non-protected, documented features.
3. Conduct 4 distinct experimental stress tests to measure vulnerability to proxy discrimination and structural disparities.
4. Compute statistical fairness and utility metrics using standard responsible-AI frameworks (`Fairlearn`, `scikit-learn`, `pandas`).
5. Deliver structured technical artifacts (`data-card`, `ranking-system-card`, `fairness-audit`) to the Governance and EU AI Act review agents.

---

## 2. Safety Boundaries & Ethical Controls

In alignment with the AI Governance Toolkit specification:
- **Zero Real-World Data:** All student records, identifiers, institutions, and outcomes are entirely synthetic.
- **Strict Attribute Partitioning:** Demographic attributes (`race_ethnicity`, `gender_identity`, `age_band`, `first_generation_status`) are generated solely as downstream audit metadata. They are strictly segregated and inaccessible to the ranking scoring pipeline.
- **No Biometric or Image Inference:** Any secondary verification systems (such as campus badge/photo verification) are structurally detached from student feature extraction. Protected attributes will never be inferred from photos.
- **Non-Adjudicative Context:** Mentoring recommendations are classified as developmental support opportunities and must not model high-stakes punitive decisions (such as academic suspension, financial aid disqualification, or admissions rejection).

---

## 3. Synthetic Population & Opportunity Architecture

### 3.1 Synthetic Student Cohort ($N = 1,000$)
A population of 1,000 fictional undergraduate students will be generated with fixed seed `42`.

| Field Name | Type | Purpose | Inclusion in Ranking |
| :--- | :--- | :--- | :--- |
| `student_id` | String | Unique synthetic identifier (`STU-xxxxx`) | Excluded (ID only) |
| `age_band` | Categorical | Audit attribute: `18-20` (60%), `21-24` (30%), `25+` (10%) | **Excluded (Audit Metadata)** |
| `race_ethnicity` | Categorical | Audit attribute: 5 synthetic demographic categories | **Excluded (Audit Metadata)** |
| `gender_identity` | Categorical | Audit attribute: `Female` (52%), `Male` (44%), `Non-binary/Other` (4%) | **Excluded (Audit Metadata)** |
| `first_generation_status` | Binary | Audit attribute: `first_gen` (32%), `continuing_gen` (68%) | **Excluded (Audit Metadata)** |
| `academic_preparation` | Float (0–100) | Prerequisite readiness, relevant coursework standing | **Included (Baseline)** |
| `interest_match` | Float (0–100) | Cosine similarity / alignment of career/topic interests | **Included (Baseline)** |
| `opportunity_eligibility` | Binary {0, 100} | Prerequisite criteria satisfaction (Boolean gate) | **Included (Baseline)** |
| `engagement_signal` | Float (0–100) | Campus portal interaction, advisor check-in frequency | **Included (Baseline)** |
| `availability_fit` | Float (0–100) | Schedule overlap between mentor and mentee | **Included (Baseline)** |
| `institution_opportunity_access_proxy` | Float (0–100) | Composite index of institutional & advising resource richness | **Excluded in Baseline; Added in Exp 2 & 3** |
| `resume_gap_proxy` | Float (0–100) | Discontinuity or non-traditional work/academic history | **Excluded in Baseline; Added in Exp 3** |
| `extracurricular_access_proxy` | Float (0–100) | Discretionary hours for unpaid leadership/clubs | **Excluded in Baseline; Added in Exp 3** |
| `device_quality_proxy` | Float (0–100) | Hardware & broadband stability index | **Excluded in Baseline; Added in Exp 3** |
| `true_mentoring_utility` | Float (0–100) | Ground-truth synthetic benefit/readiness score | Latent evaluation label |

### 3.2 Mentoring Opportunity Portfolio ($M = 50$)
50 distinct mentoring opportunities across four domains:
- STEM & Technical Mentoring (15 slots)
- Humanities & Pre-Law Mentoring (10 slots)
- Business & Entrepreneurship Mentoring (15 slots)
- General Professional Development & First-Year Transition (10 slots)

Capacity per opportunity will be simulated (e.g., top-$k$ selection threshold where $k = 5$ to $k = 20$ students per track).

---

## 4. Ranking Model Specification

### 4.1 Baseline Scoring Formula
The baseline ranking formula utilizes exclusively the five documented non-protected features, normalized onto a $0 - 100$ continuous scale:

$$\text{Score}_{\text{baseline}} = 0.30 \times \text{academic\_preparation} + 0.25 \times \text{interest\_match} + 0.20 \times \text{opportunity\_eligibility} + 0.15 \times \text{engagement\_signal} + 0.10 \times \text{availability\_fit}$$

### 4.2 Technical Invariants & Execution Rules
1. **Normalization:** All continuous inputs must reside within $[0, 100]$. `opportunity_eligibility` is strictly encoded as $\{0, 100\}$.
2. **Tie-Breaking Rule:** Deterministic lexicographical tie-breaking:
   - Primary: `baseline_score` (rounded to 4 decimal places)
   - Secondary: `interest_match`
   - Tertiary: `engagement_signal`
   - Final: Pseudorandom hash of `student_id` with seed `42`
3. **Missing Data Handling:** If a profile field is missing:
   - Numerical indicators (`engagement_signal`, `availability_fit`) default to cohort median.
   - Profile flag `is_imputed = 1` is recorded for audit transparency.

---

## 5. Controlled Proxy Features & Correlation Design

To evaluate vulnerability to indirect discrimination, four synthetic proxy features will be systematically generated with documented correlations:

1. **`institution_opportunity_access_proxy`:**
   Synthesized from 5 structural indicators:
   $$\text{Proxy}_{\text{inst}} = 0.30 \times \text{instructional\_res} + 0.25 \times \text{advising\_access} + 0.20 \times \text{employer\_network} + 0.15 \times \text{alumni\_access} + 0.10 \times \text{program\_vis}$$
   *Injected bias:* Modestly positively correlated with continuing-generation status ($r \approx 0.38$) and non-traditional age ($r \approx -0.25$).
2. **`resume_gap_proxy`:**
   Measures career/academic interruptions. Modestly correlated with non-traditional age ($25+$) and caregiving responsibilities ($r \approx 0.45$).
3. **`extracurricular_access_proxy`:**
   Reflects hours logged in unpaid extracurricular activities. Strongly penalizes students working $>20$ hours/week off-campus (correlating with low SES / first-generation status, $r \approx 0.40$).
4. **`device_quality_proxy`:**
   Simulates network latency and hardware age, testing algorithmic sensitivity to technical friction.

---

## 6. Experimental Design: 4 Required Stress Tests

| Run ID | Experiment Title | Model Formulation | Focus & Hypothesis |
| :--- | :--- | :--- | :--- |
| **EXP-01** | **Baseline Clean Ranking** | Baseline 5-feature formula (Weights: 0.30, 0.25, 0.20, 0.15, 0.10) | Establishes reference parity and match quality without proxy contamination. |
| **EXP-02** | **Single Proxy (Institutional Access)** | Add Institutional Access at 15%; reweight baseline: Prep (0.25), Match (0.20), Elig (0.15), Eng (0.15), Avail (0.10), Proxy (0.15) | Measures how quickly socioeconomic/resource advantages skew top-$k$ allocations. |
| **EXP-03** | **Multi-Proxy Stress Test** | Incorporate Institutional Access (10%), Resume Gap (-10%), Extracurricular (10%), Device Quality (5%); scale others to 65% | Simulates real-world legacy screening bias. Expected substantial disparate impact on first-gen and older students. |
| **EXP-04** | **Mitigation & Parity Adjustment** | Feature suppression + bounded score calibration (e.g., demographic parity constraint or access-neutralized baseline) | Assesses recovery of group fairness while measuring any trade-offs in candidate-opportunity utility. |

---

## 7. Audit & Evaluation Framework

### 7.1 Group Fairness Metrics
Using Fairlearn and scikit-learn, outcomes across synthetic demographic categories (`race_ethnicity`, `gender_identity`, `first_generation_status`, `age_band`) will be audited for:
- **Selection Rate ($SR$):** Proportion of group members selected in top-$k$ recommendations ($k \in \{10, 25, 50\}$).
- **Selection Rate Ratio (Disparate Impact Ratio / 80% Rule):**
  $$\text{DIR} = \frac{\min_{g} SR_g}{\max_{g} SR_g}$$
- **Selection Rate Difference:** Absolute gap between highest and lowest group selection rates.
- **Average Ranking Position (Rank Exposure):** Mean rank assigned to candidates in each group.
- **Top-$k$ Exposure Share:** Percentage of top-$k$ positions held by each group relative to their population share.

### 7.2 Performance & Utility Metrics
- **Recommendation Match Quality:** Mean alignment score between selected students and mentor profiles.
- **Abstention / Low-Confidence Rate:** Percentage of students where recommendation confidence falls below review threshold ($\text{score} < 50.0$).
- **Disparity vs. Quality Frontier:** Quantitative comparison of match quality vs. fairness ratio across EXP-01 through EXP-04.

---

## 8. Technical Handoff Deliverables

Upon completion of the simulation runs, Gemini will produce the following handoff files:
1. `gemini/gemini-data-card.md`: Documentation of synthetic data distributions, correlations, and generation scripts.
2. `gemini/gemini-ranking-system-card.md`: Full architectural system card detailing weights, decision logic, and stress tests.
3. `gemini/gemini-fairness-audit.md`: Tabulated fairness metrics, disparate impact ratios, and sensitivity analysis.
4. `pipelines/student_ranking_audit_pipeline.py`: Fully reproducible Python execution script with CLI execution flags.

---

## 9. Governance Questions for Agent Review

To facilitate the handoff to the Governance Agents (`agents/`), the following governance decisions are requested:
1. **EU AI Act Scope:** Should campus mentoring matching under these conditions be categorized as High-Risk Annex III (Educational access/vocational training) or as a Limited/Transparency Risk system?
2. **Disparate Impact Threshold:** Should the toolkit enforce a hard $80\%$ four-fifths rule threshold, or a custom statistical parity band for flagging human oversight?
3. **Mitigation Trade-Off Policy:** When mitigation slightly reduces nominal `interest_match` to eliminate first-generation exposure deficit, what oversight committee approves the pareto tradeoff?
4. **Human Review Trigger:** What threshold of score proximity (e.g., ranks near the top-$k$ cutoff $\pm 3$ positions) mandates mandatory human advisor review?

---

*Authored by: Gemini Technical Workstream*  
*Ready for handoff and governance review.*
