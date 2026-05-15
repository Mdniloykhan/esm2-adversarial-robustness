# ESM-2 Adversarial Robustness Experiments

Code and data for the paper:

> **Adversarial Robustness and Biosecurity Risks in Protein Language Models: Evaluating Vulnerabilities and Mitigation Strategies**  
> AIUB Journal of Science and Engineering

---

## Overview

This repository contains the full experimental code, raw results, and figures for our adversarial robustness evaluation of ESM-2, a protein language model developed by Meta AI.

We measure how **substitution-based adversarial attacks** at varying mutation rates (1%, 5%, 10%) affect ESM-2's sequence modeling confidence, quantified via **pseudo-perplexity** using masked marginal scoring.

---

## Repository Structure

```
esm2-adversarial-robustness/
│
├── esm2_adversarial_experiments.ipynb   # Full experiment notebook (run on Google Colab)
├── esm2_adversarial_results.csv         # Per-sequence attack results
├── esm2_baseline_results.csv            # Clean baseline perplexity scores
├── esm2_adversarial_results.png         # Results figure
└── README.md
```

---

## Experimental Setup

| Parameter | Value |
|-----------|-------|
| Model | esm2_t6_8M_UR50D |
| Model parameters | 7,512,474 |
| Device | NVIDIA T4 GPU (Google Colab) |
| Random seed | 42 |
| Test sequences | 8 proteins from UniProt |
| Trials per condition | 5 |
| Attack type | Random non-synonymous substitution |
| Mutation rates | 1%, 5%, 10% |
| Perplexity method | Masked marginal scoring |

---

## Test Sequences

| Protein | UniProt ID | Length |
|---------|-----------|--------|
| Human Ubiquitin | P0CG48 | 76 AA |
| Villin Headpiece HP35 | — | 35 AA |
| Trp-cage miniprotein | — | 20 AA |
| GB1 fragment | — | 56 AA |
| Chignolin | — | 10 AA |
| WW domain | — | 34 AA |
| Human Lysozyme fragment | — | 53 AA |
| Barnase fragment | — | 39 AA |

---

## Key Results

| Condition | Mean Perplexity | Std | Shift | Relative Shift |
|-----------|----------------|-----|-------|----------------|
| Clean (baseline) | 13.16 | ±5.01 | — | — |
| Attack 1% | 14.16 | ±0.80 | +1.00 | +9.9% |
| Attack 5% | 14.36 | ±0.97 | +1.21 | +11.9% |
| Attack 10% | 15.43 | ±1.78 | +2.28 | +23.5% |

Results are mean ± std over 5 independent trials across 8 protein sequences.

### Key Findings

- Perplexity increases consistently with mutation rate, confirming ESM-2's sensitivity to substitution-based attacks.
- Ubiquitin (76 AA) shows the clearest dose-response: +3.8% at 1%, +15.9% at 5%, +29.9% at 10%.
- Lysozyme fragment shows near-zero sensitivity, suggesting structural robustness in longer, evolutionarily conserved sequences.
- Short sequences (≤34 AA) exhibit identical results at 1% and 5% mutation rates because both rates round to 1 substitution — acknowledged as a limitation.

---

## Runtime Measurements

Inference time for defense mechanisms measured on a single 76 AA 
sequence (Ubiquitin) on NVIDIA T4 GPU, averaged over 10 trials.

| Defense | Time (s) | Overhead | Note |
|---------|----------|----------|------|
| Baseline inference | 0.560 ± 0.081 | 1.0× | Single forward pass |
| Bayesian estimation | 5.311 ± 0.486 | 9.5× | 10 dropout passes |
| Ensemble (3 models) | 1.549 ± 0.154 | 2.8× | 3 independent runs |

**Key finding:** Bayesian uncertainty estimation imposes the highest 
overhead (9.5× baseline) due to repeated dropout forward passes. 
Ensemble inference with 3 models is more practically deployable 
at 2.8× overhead.

The runtime experiment code is included in Step 11 of the notebook.
Additional data saved in `esm2_runtime_results.csv`.


## How to Reproduce

1. Open `esm2_adversarial_experiments.ipynb` in [Google Colab](https://colab.research.google.com)
2. Set runtime to **T4 GPU**: Runtime → Change runtime type → T4 GPU
3. Run all cells in order
4. Results are saved automatically as CSV and PNG

No paid compute required. The full experiment runs on a free Colab T4 GPU in approximately 30–60 minutes.

---

## Dependencies

```
fair-esm
torch
transformers
pandas
numpy
matplotlib
seaborn
```

Install via:
```bash
pip install fair-esm torch transformers pandas numpy matplotlib seaborn
```

---

## Citation

If you use this code or data, please cite:

```
@article{niloy2024adversarial,
  title={Adversarial Robustness and Biosecurity Risks in Protein Language Models: 
         Evaluating Vulnerabilities and Mitigation Strategies},
  journal={AIUB Journal of Science and Engineering},
  year={2024}
}
```

---

## License

MIT License. See LICENSE for details.
