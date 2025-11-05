# Research Plan: Incentive-Compatible Societies for Truthful Meta-Knowledge

**Date**: 2025-11-05
**Researcher**: Autonomous Research System
**Estimated Execution Time**: 60 minutes
**Budget**: $100

---

## Research Question

**Primary Question**: Can formal environment engineering of communication protocols, sanction mechanisms, and audit hooks render truthful uncertainty reporting a subgame-perfect equilibrium in multi-agent LLM systems?

**Specific Testable Question**: Do multi-agent systems with long-run reputation penalties and ex-post audits exhibit significantly higher calibration of uncertainty reports compared to systems without such mechanisms?

---

## Background and Motivation

### Why This Matters

1. **Practical Urgency**: Agentic LLMs are being deployed in high-stakes domains (medical diagnosis, financial analysis, logistics) where miscommunicated uncertainty can have catastrophic consequences

2. **Theoretical Gap**: While mechanism design for truthfulness exists in classical economics, adaptation to LLM-based multi-agent systems with metacognitive capabilities remains underexplored

3. **Safety Implications**: Agents that overstate confidence can mislead teammates, causing cascading failures in collaborative systems

4. **Fills Research Gap**: Bridges three literatures:
   - Mechanism design (game theory, incentive compatibility)
   - Multi-agent coordination (formal environment engineering)
   - LLM capabilities (uncertainty quantification, metacognition)

---

## Hypothesis Decomposition

### Main Hypothesis (H1)
Formal environment design with sanction mechanisms renders truthful uncertainty reporting subgame-perfect in multi-agent systems.

### Sub-Hypotheses

**H1a**: Multi-agent systems with long-run penalties exhibit higher calibration (lower ECE) than systems without penalties.
- **Independent Variable**: Presence/absence of reputation system
- **Dependent Variable**: Expected Calibration Error (ECE)
- **Operationalization**: Compare ECE across experimental conditions

**H1b**: Ex-post audits (verification checks) increase truthful reporting even when agents could misreport without immediate detection.
- **Independent Variable**: Audit frequency/mechanism
- **Dependent Variable**: Misreporting rate (confidence >> actual accuracy)
- **Operationalization**: Track agents' reported confidence vs. ground truth accuracy

**H1c**: Agents in incentive-compatible environments develop better meta-knowledge (self-awareness of their own uncertainty) over repeated interactions.
- **Independent Variable**: Number of interactions with feedback
- **Dependent Variable**: Calibration improvement over time
- **Operationalization**: Measure ECE trajectory across episodes

### Alternative Explanations to Test
1. **Learning Effect**: Improved calibration due to experience, not incentives
2. **Selection Effect**: Only certain agent configurations benefit from incentives
3. **Threshold Effect**: Incentives only work above certain penalty/reward magnitudes

---

## Proposed Methodology

### High-Level Approach

**Paradigm**: Computational experiment using simulated multi-agent environment

**Rationale**:
- **Control**: Full control over ground truth for measuring calibration
- **Efficiency**: Can run multiple trials within 1-hour time constraint
- **Realism**: LLM-based agents capture metacognitive reasoning present in real systems
- **Precedent**: Standard approach in mechanism design research (see AAMAS 2024 proceedings)

### Experimental Design: Multi-Agent Collaborative Prediction Task

#### Environment: "Expert Panel Forecasting"

**Scenario**: 3 LLM agents form an "expert panel" making predictions on factual questions

**Agent Roles**:
1. **Agent 1 (Domain Specialist)**: Has access to partial evidence
2. **Agent 2 (Methodologist)**: Evaluates evidence quality
3. **Agent 3 (Synthesizer)**: Combines agent inputs to make final prediction

**Task Flow**:
1. System presents question with partial information
2. Agent 1 makes prediction with confidence level (0-100%)
3. Agent 2 assesses evidence quality and reports confidence
4. Agent 3 synthesizes and makes final team prediction
5. **Audit Phase**: System reveals ground truth, compares reported confidence to accuracy
6. **Sanction Phase**: Update reputation scores based on calibration

**Ground Truth**: Use questions with verifiable answers (trivia, math, logic puzzles)

**Why This Design?**
- **Interdependence**: Agents rely on each other's uncertainty reports
- **Observable**: Can measure both individual and team performance
- **Realistic**: Mirrors real expert panels, medical diagnosis teams, intelligence analysis
- **Controlled**: Full access to ground truth for measuring truthfulness

---

### Experimental Conditions

#### Condition 1: **Baseline (No Incentives)**
- Agents report confidence freely
- No penalties or rewards
- No reputation tracking
- **Control Condition**: Isolates effect of incentives

#### Condition 2: **Immediate Penalties**
- Agents lose points for miscalibration
- Penalty applied immediately after each trial
- Short-term memory only
- **Tests**: Whether immediate feedback alone improves calibration

#### Condition 3: **Long-Run Reputation + Audits** (Proposed Mechanism)
- **Reputation System**: Cumulative score tracking calibration history
- **Sanction Mechanism**: Penalty increases with reputation damage
- **Audit Hook**: Random ex-post verification checks
- **Abstention Option**: Agents can decline to answer if very uncertain
- **Tests**: Main hypothesis (H1)

#### Condition 4: **Reputation + Pre-commitment**
- Agents must commit to confidence level before seeing team feedback
- Cannot adjust after learning others' views
- **Tests**: Whether pre-commitment enhances incentive effects

---

### Implementation Steps

#### Step 1: Environment Setup (10 minutes)
1. Install required Python libraries: `anthropic`, `numpy`, `scipy`, `matplotlib`, `pandas`
2. Configure API access (Claude Sonnet 4.5)
3. Set random seed for reproducibility
4. Create results directory structure

#### Step 2: Data Construction (5 minutes)
1. Curate question bank (50 questions across domains):
   - **Science** (10): Verifiable facts
   - **Math** (10): Computational problems
   - **Logic** (10): Syllogisms, puzzles
   - **History** (10): Documented events
   - **General Knowledge** (10): Trivia with clear answers
2. For each question, vary evidence quality (complete, partial, ambiguous)
3. Split: 30 training, 20 testing

**Rationale for Question Selection**:
- **Verifiable**: Clear ground truth for audit
- **Diverse**: Tests robustness across domains
- **Varied Difficulty**: Enables calibration measurement across confidence levels

#### Step 3: Core Implementation (15 minutes)
1. **Agent Class**: LLM-based agent with:
   - `predict(question, evidence)` → (answer, confidence)
   - `update_reputation(outcome)` → reputation score
   - Prompt engineering for uncertainty awareness

2. **Communication Protocol**:
   ```python
   Message = {
       'sender': agent_id,
       'action': 'predict' | 'assess' | 'synthesize',
       'content': prediction,
       'confidence': float(0-1),
       'evidence_quality': 'high' | 'medium' | 'low',
       'timestamp': datetime
   }
   ```

3. **Sanction Mechanism**:
   ```python
   def calculate_penalty(reported_confidence, actual_accuracy, reputation):
       miscalibration = abs(reported_confidence - actual_accuracy)
       penalty = miscalibration * reputation_multiplier(reputation)
       return penalty

   def reputation_multiplier(reputation):
       # Higher reputation = higher penalty for miscalibration
       # Incentivizes maintaining good reputation
       return 1 + (reputation / 100)
   ```

4. **Audit Hook**:
   ```python
   def audit_check(message, ground_truth):
       if random.random() < AUDIT_PROBABILITY:
           accuracy = evaluate(message.content, ground_truth)
           calibration_error = abs(message.confidence - accuracy)
           return calibration_error
       return None
   ```

#### Step 4: Baseline Implementation (5 minutes)
- Implement simplest version (Condition 1): no incentives
- Validate pipeline: agents can communicate, make predictions, system evaluates
- **Checkpoint**: Ensure evaluation metrics work correctly

#### Step 5: Full Experiment Execution (15 minutes)
- Run all 4 conditions (30 trials each = 120 total trials)
- Log all messages, predictions, confidences, outcomes
- Save intermediate checkpoints
- Estimate: ~25-30 API calls per condition (100-120 total) = ~$20-30

#### Step 6: Analysis (10 minutes)
- Compute calibration metrics (ECE, Brier score)
- Statistical tests (t-tests, Mann-Whitney U)
- Visualizations (calibration curves, error distributions)
- Error analysis (qualitative inspection of failures)

---

### Baselines

**Baseline 1: No Incentives**
- **Purpose**: Measures natural calibration of LLM agents
- **Expectation**: Poor calibration (LLMs known to be overconfident)

**Baseline 2: Immediate Penalties**
- **Purpose**: Tests whether short-term feedback suffices
- **Expectation**: Some improvement, but not subgame-perfect

**Baseline 3: Random Confidence**
- **Purpose**: Sanity check (worst-case performance)
- **Expectation**: ECE ≈ 0.33 (completely uncalibrated)

---

### Evaluation Metrics

#### Primary Metric: Expected Calibration Error (ECE)

**Formula**:
```
ECE = Σ (|accuracy_in_bin - avg_confidence_in_bin|) * (count_in_bin / total)
```

**Why ECE?**
- **Standard**: Widely used in ML calibration literature
- **Interpretable**: Measures avg distance between confidence and accuracy
- **Sensitive**: Detects both over- and under-confidence

**Interpretation**:
- ECE = 0: Perfect calibration (confidence matches accuracy)
- ECE = 0.1: Typical for well-calibrated models
- ECE > 0.2: Poorly calibrated

#### Secondary Metrics

1. **Brier Score**: Measures probabilistic prediction accuracy
   - Formula: `1/N Σ (predicted_prob - actual_outcome)²`
   - Lower is better

2. **Overconfidence Rate**: % of cases where confidence > accuracy
   - Measures dangerous overconfidence
   - Critical for safety

3. **Calibration Slope**: Regression of actual accuracy on reported confidence
   - Slope = 1 indicates perfect calibration
   - Slope < 1 indicates overconfidence

4. **Task Performance**: % correct predictions
   - Ensures incentives don't harm accuracy
   - Trade-off analysis: calibration vs. performance

#### Tertiary Metrics

- **Abstention Rate**: How often agents decline to answer
- **Inter-Agent Agreement**: Consensus among agents
- **Reputation Trajectory**: Evolution of reputation scores over time

---

### Statistical Analysis Plan

#### Hypothesis Tests

**H1a Test**: Do long-run penalties improve calibration?
- **Null Hypothesis (H₀)**: ECE_condition3 = ECE_condition1
- **Alternative (H₁)**: ECE_condition3 < ECE_condition1
- **Test**: One-tailed t-test (or Mann-Whitney U if non-normal)
- **Significance Level**: α = 0.05
- **Effect Size**: Cohen's d (small: 0.2, medium: 0.5, large: 0.8)

**H1b Test**: Do audits reduce misreporting?
- **Metric**: Overconfidence rate
- **Test**: Chi-square test for proportions
- **Comparison**: Condition 3 vs. Condition 1

**H1c Test**: Does calibration improve over time?
- **Metric**: ECE trajectory (slope across episodes)
- **Test**: Linear regression (time vs. ECE)
- **Comparison**: Slopes across conditions

#### Multiple Comparison Correction
- **Method**: Bonferroni correction (α / number_of_tests)
- **Justification**: Conservative, maintains family-wise error rate

#### Power Analysis
- **Sample Size**: 30 trials per condition (120 total)
- **Expected Effect Size**: Medium (d = 0.5) based on similar studies
- **Power**: ~0.70 (adequate for exploratory study)
- **Limitation**: Constrained by budget; acknowledge in discussion

---

## Expected Outcomes

### Results Supporting Hypothesis

**Expected Pattern**:
- ECE: Condition 3 < Condition 2 < Condition 1
- Overconfidence Rate: Condition 3 < Condition 1
- Calibration Slope: Condition 3 closest to 1.0

**Interpretation**: Formal environment design with long-run penalties + audits successfully incentivizes truthful reporting

**Theoretical Contribution**: Demonstrates subgame-perfection of truthful reporting in practical LLM-based multi-agent system

### Results Refuting Hypothesis

**Alternative Pattern**:
- No significant difference in ECE across conditions
- Agents ignore incentives or cannot calibrate despite incentives

**Possible Explanations**:
1. **LLM Limitation**: Current LLMs lack sufficient metacognitive capability
2. **Incentive Design**: Penalty structure insufficient to overcome default behavior
3. **Task Complexity**: Multi-agent coordination noise overwhelms incentive signal

**Scientific Value**: Still valuable to document negative result; informs future mechanism design

### Partial Support

**Mixed Pattern**:
- Improvement in some metrics (e.g., ECE) but not others (e.g., overconfidence rate)
- Benefits only in certain task types or agent roles

**Interpretation**: Mechanism works but requires refinement or has boundary conditions

---

## Timeline and Milestones

### Time Allocation (60 minutes total)

| Phase | Activity | Duration | Cumulative |
|-------|----------|----------|------------|
| 0 | ✅ Research & Planning | 30 min | 30 min |
| 1 | ✅ Detailed Plan | 10 min | 40 min |
| 2 | Environment Setup | 5 min | 45 min |
| 3 | Implementation | 20 min | 65 min |
| 4 | Experimentation | 15 min | 80 min |
| 5 | Analysis | 10 min | 90 min |
| 6 | Documentation | 15 min | 105 min |
| **Buffer** | Debugging | 15 min | 120 min |

**Total**: ~2 hours (including buffer)

**Critical Path**: Implementation → Experimentation (most time-sensitive)

**Contingency Plan**: If behind schedule:
- Reduce trials per condition (30 → 20)
- Simplify to 3 conditions (drop Condition 4)
- Focus on primary metric (ECE) only

---

## Potential Challenges & Mitigation

### Challenge 1: API Rate Limits
**Risk**: Claude API may throttle requests
**Mitigation**:
- Add exponential backoff retry logic
- Batch requests where possible
- Cache responses to avoid redundant calls

### Challenge 2: LLM Variability
**Risk**: High variance in responses (temperature > 0)
**Mitigation**:
- Set temperature = 0.3 (balance diversity and consistency)
- Run multiple seeds, report mean and variance
- Use larger sample size for statistical power

### Challenge 3: Calibration Ceiling
**Risk**: Modern LLMs may already be well-calibrated
**Mitigation**:
- Include questions of varying difficulty
- Partial evidence conditions increase miscalibration
- Worst case: Interesting null result (document baseline calibration)

### Challenge 4: Implementation Bugs
**Risk**: Errors in sanction calculation or audit logic
**Mitigation**:
- Unit tests for each component
- Validation on toy examples
- Assertions to catch logic errors
- Incremental testing (baseline first)

### Challenge 5: Time Overrun
**Risk**: Implementation takes longer than planned
**Mitigation**:
- Simplified fallback design (2 conditions instead of 4)
- Pre-write data loading and evaluation code
- Modular design (can defer non-essential components)

---

## Success Criteria

### Minimum Success (Must Achieve)
- [ ] Implement at least 2 experimental conditions (baseline + proposed)
- [ ] Run ≥20 trials per condition
- [ ] Compute ECE and perform statistical test
- [ ] Document methodology and results in REPORT.md

### Target Success (Aim to Achieve)
- [ ] Implement all 4 experimental conditions
- [ ] Run 30 trials per condition (120 total)
- [ ] Compute all primary and secondary metrics
- [ ] Statistical tests with effect sizes and confidence intervals
- [ ] Calibration curves and error analysis visualizations
- [ ] Complete REPORT.md with all sections

### Stretch Success (If Time Permits)
- [ ] Sensitivity analysis (vary penalty magnitudes)
- [ ] Qualitative analysis of agent reasoning (inspect explanations)
- [ ] Comparison to human calibration baselines
- [ ] Error taxonomy (categorize failure modes)

---

## Deliverables Checklist

### Code
- [ ] Jupyter notebook with all experiments
- [ ] Well-commented, modular code
- [ ] Reproducibility: random seed, versions documented
- [ ] Unit tests for core functions

### Data
- [ ] Question bank (50 questions, JSON format)
- [ ] Raw results (all agent messages, predictions, outcomes)
- [ ] Processed metrics (ECE, Brier, calibration data)
- [ ] Saved in `results/` directory

### Documentation
- [ ] `REPORT.md`: Comprehensive research report with actual results
- [ ] `README.md`: Project overview and reproduction instructions
- [ ] `resources.md`: ✅ Already complete (Phase 0)
- [ ] `planning.md`: ✅ This document (Phase 1)

### Visualizations
- [ ] Calibration curves (confidence vs. accuracy)
- [ ] ECE comparison across conditions (bar plot)
- [ ] Overconfidence rate comparison
- [ ] Reputation trajectory over time

---

## Next Steps

1. **Immediate**: Begin Phase 2 (Environment Setup)
   - Install Python dependencies
   - Test API connectivity
   - Create directory structure

2. **Then**: Phase 3 (Implementation)
   - Start with baseline condition (simplest)
   - Validate end-to-end pipeline
   - Add incremental complexity

3. **Monitor**: Track time against plan
   - Adjust if behind schedule
   - Activate contingency plans if needed

---

**Status**: Planning Complete ✅
**Ready to Proceed**: Yes
**Estimated Probability of Success**: 80% (high confidence given modular design and clear plan)

**Final Pre-Flight Check**:
- ✅ Hypothesis clearly testable
- ✅ Metrics well-defined
- ✅ Baselines identified
- ✅ Timeline realistic with buffer
- ✅ Contingency plans in place
- ✅ Success criteria explicit

**Proceeding to Phase 2: Implementation** 🚀
