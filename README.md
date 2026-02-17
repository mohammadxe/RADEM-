# RADEM
## Reliability-Aware Decision Evaluation for LLM Agents

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
![Focus](https://img.shields.io/badge/focus-decision%20reliability-critical)
![Stage](https://img.shields.io/badge/status-active%20research-blue)

> RADEM is a model-agnostic, cost-aware reliability evaluation layer for discrete decision agents under uncertainty and distribution shift.

---

## Motivation

LLM-based systems are increasingly deployed as **decision agents** in high-stakes domains such as:

- Risk assessment  
- Fraud detection  
- Content moderation  
- Financial triage  
- Operational automation  

Standard evaluation focuses primarily on **accuracy**.

However, real-world deployment requires answering deeper questions:

- Is the agent well-calibrated?
- Does overconfidence increase under distribution shift?
- What is the expected economic loss?
- How does reliability degrade out-of-domain?
- What is the optimal abstention threshold?

RADEM provides a unified reliability scorecard to answer these questions.

---

## What RADEM Is — and Is Not

### RADEM IS:
- A metric-centric evaluation framework  
- Designed for discrete decision agents  
- Cost-aware and deployment-oriented  
- Compatible with CI pipelines  
- Model-agnostic  

### RADEM IS NOT:
- An LLM-as-judge evaluation system  
- A free-form text generation benchmark  
- A multi-agent debate framework  
- A safety red-teaming benchmark  

RADEM evaluates agents that output:
- A discrete decision  
- Confidence or probabilities  
- Optional abstention  
- Scenario metadata  

---

## Core Dimensions Evaluated

### 1. Accuracy
- Accuracy / F1-score  
- Confusion matrix  
- Class imbalance analysis  

---

### 2. Calibration
- Brier Score  
- Expected Calibration Error (ECE)  
- Reliability curves  
- Overconfidence rate  

Calibration metrics are computed globally and per scenario (e.g., ID vs OOD).

---

### 3. Selective Prediction & Abstention

RADEM supports threshold-based selective prediction using model confidence.

Abstention is treated as a special decision with its own cost in the cost matrix.

Metrics include:

- Risk–Coverage curves  
- Area Under Risk–Coverage Curve (AURC)  
- Cost-weighted risk–coverage  
- Abstention-aware expected loss  

If no explicit abstention action is available, RADEM reduces to standard selective prediction via confidence thresholds without an explicit abstain decision.

---

### 4. Economic Risk Modeling

Users provide a cost matrix:

```
C[true_label][predicted_label]
C[true_label][abstain]
```

RADEM computes:

- Expected Monetary Loss  
- Cost-weighted F1  
- Expected Cost at Operating Threshold  

This enables optimization for deployment scenarios with asymmetric error costs.

---

### 5. Distribution Shift & Robustness

Datasets may include scenario tags:

```
scenario: "ID" | "OOD" | "adversarial"
```

RADEM reports per-scenario metrics and degradation:

- Accuracy_ID vs Accuracy_OOD  
- Cost_ID vs Cost_OOD  
- Calibration drift  

#### Cost-Based Reliability Degradation Index (RDI_cost)

```
RDI_cost = (Cost_OOD − Cost_ID) / Cost_ID
```

This measures robustness in terms of deployment-relevant economic impact rather than pure accuracy.

(When `Cost_ID = 0`, RADEM also reports an absolute degradation metric `RDI_cost_abs = Cost_OOD − Cost_ID`.)

---

### 6. Stability Under Perturbation

RADEM supports repeated evaluation under controlled perturbations to compute:

- Decision consistency score  
- Confidence variance  
- Reliability stability curves  

Reliability stability curves show decision consistency as a function of perturbation strength.

---

## Agent Interface Contract

An agent evaluated by RADEM must return:

```json
{
  "decision": "label",
  "confidence": 0.83,
  "abstain": false,
  "metadata": {
      "scenario": "ID"
  }
}
```

RADEM is agnostic to how confidence is obtained:

- Native LLM probabilities  
- External calibration layer  
- Conformal scores  
- Heuristic scores  

---

## Contribution & Research Direction

RADEM introduces a unified reliability scorecard that integrates:

- Calibration  
- Selective prediction  
- Economic cost modeling  
- Distribution shift robustness  

A planned extension is the **Cost-Aware Reliability Curve (CARC)**, which generalizes risk–coverage and AURC by integrating economic cost rather than pure error rate. This provides a deployment-oriented reliability profile across operating thresholds.

Key research questions include:

- How does overconfidence amplify economic loss under distribution shift?
- Can abstention reduce deployment risk without sacrificing coverage?
- What reliability profile best predicts real-world failure?

---

## Example Workflow

```
Agent → Predictions → RADEM → Reliability Metrics → Structured Report
```

Run evaluation:

```bash
python run_eval.py --dataset datasets/risk_benchmark_v1.jsonl --agent agents.example_agent --cost_matrix cost_matrix.json
```

---

## Roadmap

- [x] Core accuracy + calibration engine  
- [x] Risk–coverage and AURC (cost-based)  
- [x] Cost-aware evaluation + threshold optimization  
- [x] Distribution shift protocol (ID / OOD / adversarial)  
- [x] Cost-based degradation metrics (`RDI_cost_abs`)  
- [ ] CARC prototype  
- [ ] Public benchmark suite + leaderboard  

---

## Vision

RADEM aims to become a standardized evaluation layer for:

- Reliability-aware AI deployment  
- Cost-sensitive decision systems  
- Shift-robust agent evaluation  
- Production CI for LLM decision agents  

---

## License

MIT License
