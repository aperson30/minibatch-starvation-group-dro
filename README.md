# Minibatch Starvation in Group DRO

Experiment notebooks for the paper **"Minibatch Starvation: An Estimator Artifact
Collapses Minority Groups in Group DRO — a Closed-Form Law, the Epoch-Time
Illusion, and a Fix."**

## Summary

Group Distributionally Robust Optimization (Group DRO) is meant to protect
underperforming groups by upweighting them, but it sometimes does the opposite:
it drives a minority group's adaptive weight to zero and collapses that group's
per-class accuracy — often to 0% — while aggregate metrics barely move, hiding
the failure.

This work is an empirical and mechanistic account of *when* and *why* that
collapse happens. The dominant cause is an estimator artifact: the standard
minibatch implementation **zero-fills**, assigning group loss 0 to any group with
no samples in the current batch, and the exponentiated-weight update then drains
that group's share through the normalizer. The paper shows this artifact alone
suffices for collapse, derives a closed-form collapse law

```
T_collapse  ≈  (Δθ / η ℓ̄) · (1 - f) / B ,      f = p / (1 + R)
```

with batch size `B`, minority-class fraction `p`, group size ratio `R`, and
log-odds weight margin `Δθ`, and confirms the mechanism with a kill test:
renormalizing the weight update over only the groups present in each batch
suppresses collapse by more than an order of magnitude. The corrected estimator
is a three-line change to standard implementations.

## Repository contents

This repository contains only the experiment notebooks. They are exploratory
research notebooks with outputs preserved; they are organized here by theme.

### Mechanism and theory

| Notebook | Purpose |
| --- | --- |
| `Mechanism Diagnostic Minibatch Starvation.ipynb` | Core diagnostic isolating the zero-fill estimator artifact |
| `theory-closure.ipynb` | Derivation and closure of the collapse law |
| `theory-closure-minibatch-starvation-mechanism.ipynb` | Theory closure focused on the starvation mechanism |
| `theory-closure-minibatch-starvation-mechanism2.ipynb` | Extended / revised theory-closure run |
| `theory-validation.ipynb` | Empirical validation of the closed-form predictions |
| `notebook-3.ipynb` | Supporting scratch analysis |

### Collapse timescale

| Notebook | Purpose |
| --- | --- |
| `Collapse Timescale Experiment.ipynb` | Measures collapse time vs. controlled factors |
| `collapse-timescale-experiment-v2.ipynb` | Revised collapse-timescale run |

### Sweeps

| Notebook | Purpose |
| --- | --- |
| `eta-sweep.ipynb` | Learning-rate / step-size dependence |
| `geometry-sweep.ipynb` | Embedding-geometry dependence (provably inert under zero-fill) |
| `fixed-step-ratio-sweep-is-r-1-2-physical-or-an.ipynb` | Tests whether the R^(-1/2) exponent is physical or an artifact |
| `R-Exponent Robustness.ipynb` | Robustness of the group-ratio exponent |
| `optimizer-comparison-sgd-vs-adamw-majority-expone.ipynb` | SGD vs. AdamW and the majority exponent |
| `quick-kill-ratio-vs-absolute-count-confound-break.ipynb` | Breaks the ratio vs. absolute-count confound |

### The fix / kill test

| Notebook | Purpose |
| --- | --- |
| `step-3-diagnostic-does-the-fix-change-worst-group.ipynb` | Does the corrected estimator change worst-group accuracy? |
| `mad-corrected-score.ipynb` | Corrected score computation |
| `MAD Risk Score — Waterbirds Full.ipynb` | MAD risk score on Waterbirds |
| `mad-phase-diagram-synthetic-gaussian-experiment.ipynb` | Phase diagram on a synthetic Gaussian setup |

### Real-dataset validation

| Notebook | Purpose |
| --- | --- |
| `real-dataset-validation.ipynb` | Law retrodiction across real datasets |
| `waterbirds-kill-test-starvation-channel-in-real-d.ipynb` | Kill test for the starvation channel on Waterbirds |
| `civilcomments-wilds-v2.ipynb` | CivilComments-WILDS (NLP toxicity) |
| `End-to-End Deep Network Generalization Test.ipynb` | End-to-end deep network generalization check |

## Notes

- Notebooks are provided as-is from the research process; paths, dataset
  locations, and environment setup are specific to the original run environment
  and may need adjustment.
- The paper is under review; please do not redistribute.
