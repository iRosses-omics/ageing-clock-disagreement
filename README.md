# When can we trust an ageing clock?

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/iRosses-omics/ageing-clock-disagreement/blob/main/clock_trust.ipynb)

Different epigenetic clocks often give different ages for the same person.
This project asks: **does that disagreement tell us when an estimate should not be trusted, and can we use it to give every person a reliable uncertainty interval?**

![Main figure](main_figure.png)

## Data
Two independent whole-blood DNA methylation cohorts (Illumina 450k, public on GEO):

| Cohort | Study | People | Ages |
|---|---|---|---|
| GSE40279 | Hannum et al., 2013 | 656 | 19–101 |
| GSE87571 | Johansson et al., 2013 | 729 | 14–94 |

Six published clocks were run with [biolearn](https://github.com/bio-learn/biolearn): Horvath v1, Hannum, PhenoAge, Horvath v2, Lin, Zhang (10 CpG).

## Method
1. Run all six clocks on the same people and convert each to standardised **age acceleration** (residual on chronological age).
2. **Disagreement score** per person = spread of age acceleration across the six clocks.
3. **Split-conformal** 90% prediction intervals for the Horvath v1 clock, repeated over 20 random calibration/test splits:
   - **Standard:** one interval width for everyone.
   - **Disagreement-aware (Mondrian):** people are split into "clocks agree" (80%) and "clocks disagree" (20%) using the calibration half only, and each group gets its own calibrated width.

## Results (replicated in both cohorts)

**Clocks agree only partly.** Correlations of age acceleration between clocks: 0.16–0.63 (GSE40279) and 0.12–0.62 (GSE87571).

**Disagreement rises with age.** Median disagreement under 40 vs over 80: 0.56 → 0.74 (GSE40279) and 0.59 → 0.90 (GSE87571).

**Where clocks disagree, the clock is more wrong.** Median error is about 50–60% larger:

| | GSE40279 | GSE87571 |
|---|---|---|
| Median error, clocks agree | 3.5 years | 3.0 years |
| Median error, clocks disagree | 5.2 years | 4.8 years |

**Standard intervals silently fail these people; disagreement-aware intervals fix it.**

| Coverage of 90% interval | GSE40279 agree | GSE40279 disagree | GSE87571 agree | GSE87571 disagree |
|---|---|---|---|---|
| Standard | 93% | **78%** | 94% | **75%** |
| Disagreement-aware | 89% | **89%** | 90% | **91%** |

Interval width (disagreement-aware): about 15–18 years when clocks agree, 25–29 years when they disagree, so each person gets an interval that matches how much the clocks can actually be trusted for them. No new data or model training is needed — only the existing clocks.

## Limitations
- Two cohorts, one tissue (blood), cross-sectional; no health outcomes yet.
- The Hannum clock was trained on GSE40279.
- The link between disagreement and error is real but modest (Spearman ρ ≈ 0.2); the gain is in fair and honest uncertainty, not in precision.
- Uncertainty for epigenetic clocks has been studied before, e.g. with [conformalized quantile regression (medRxiv, 2024)](https://www.medrxiv.org/content/10.1101/2024.09.06.24313192). The angle here is using **cross-clock disagreement** as the uncertainty signal.

## Next steps
- Longitudinal cohorts: does a high-disagreement flag predict health outcomes or mortality?
- Other tissues, populations and array types (EPIC).
- Add other omics layers (e.g. metabolomics) alongside methylation.
- Package as a small tool that returns, for each person, ages from several clocks, a trust flag and a calibrated interval.

## How to run
Open the notebook in Colab (badge above) and run all cells. Data are downloaded directly from NCBI GEO and read in chunks to fit in Colab memory.

---
*Maryam Hoseini · computational biologist*
