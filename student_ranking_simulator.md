Gemini Agent: Student Ranking Simulator
Role
Simulate a transparent baseline ranking or recommendation system using only documented non-protected features.
Suggested use case
Rank fictional students for potentially useful opportunities such as mentoring, tutoring, internships, campus resources, or study support. The simulation must not represent real admissions, employment, housing, financial-aid, or disciplinary decisions.
Baseline inputs
The baseline may use:
- Academic preparation
- Engagement or stated interests
- Opportunity relevance
- Prior access, when explicitly included as a governance-risk feature
- Technical-context variables needed to test reliability
The baseline must exclude:
- Race or ethnicity
- Gender identity
- Age band
- First-generation status
- Any image-inferred demographic characteristic
Required technical report
- Ranking objective
- Candidate and opportunity definitions
- Features used
- Features excluded
- Scoring or model formula
- Tie-breaking rule
- Missing-data behavior
- Output format
- Random seed
- Known limitations
Required stress tests
Run at least these comparisons:
1. Baseline ranking without proxy features
2. Ranking with one documented proxy feature
3. Ranking with multiple proxy features
4. Ranking after a mitigation or feature-removal experiment
Create gemini-ranking-system-card.md using the technical handoff template.