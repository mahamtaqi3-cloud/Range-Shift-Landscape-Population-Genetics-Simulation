# Range shift landscape population genenetics simimulation

A small simulation project exploring whether the geographic origin of a range expansion can be inferred from genetic diversity alone, and whether that inference is robust to heterogeneity in local population size.

## Background

When a species expands its range, each newly colonized population is founded by a small number of individuals. Repeated founder events leave a genetic signature: diversity declines with distance from the expansion's origin. In principle, this gradient can be used to work backwards and locate where the expansion started. Real landscapes, however, are heterogeneous: local population sizes differ, and large populations can look genetically "old" regardless of when they were founded. This project asks how much that heterogeneity affects origin inference.

## Questions

1. Can the origin of a simulated range expansion be recovered from nucleotide diversity (π) across sampled demes?
2. Does heterogeneity in deme size degrade that inference when mean population size is held constant?

## Methods

**Simulation** (`msprime`)
- 5×5 stepping-stone grid of demes (hard edges, 4-neighbor migration, m = 0.01)
- Sequential colonization outward from a chosen origin cell, one grid step every 50 generations
- Each newly founded deme passes through a founder bottleneck (Nb = 5 for 40 generations)
- 5 diploid individuals sampled per deme; 1 Mb sequence; mutation rate 1e-7

**Inference**
- Compute π per deme
- For every grid cell as a candidate origin, correlate π with Manhattan distance from that cell
- The inferred origin is the cell with the strongest negative correlation

**Experiments**
- Baseline: a single replicate, to check the diversity gradient exists
- Replicated experiments with random origins, comparing a uniform landscape against heterogeneous deme sizes
- Dose-response: heterogeneity width varied (Ne drawn from 100 ± 0, 30, 60, 90), mean Ne held constant, 40 paired replicates per condition
- Trend tested with Spearman correlation and a paired Wilcoxon test

## Results

- The simulated founder effect produces a clear decline in diversity away from the origin.
- Origin inference recovered the true origin to within roughly 1.5 grid steps on average, compared with about 3.2 steps expected from random guessing on a 5×5 grid.
- With mean Ne held constant, error increased slightly with heterogeneity (Spearman ρ = 0.12), but the trend was **not statistically significant** (p = 0.14; ±0 vs ±90 Wilcoxon p = 0.17; n = 40 per condition).

| Heterogeneity in Ne | Mean origin error (grid steps) |
|---|---|
| ±0 | 1.45 |
| ±30 | 1.85 |
| ±60 | 1.68 |
| ±90 | 1.95 |

### A note on a corrected confound
An earlier comparison of uniform (Ne = 100) versus heterogeneous (Ne ~ Uniform(30, 300)) landscapes appeared to show *lower* error under heterogeneity (p = 0.037). This was an artifact: the heterogeneous landscape had a higher mean Ne (~165), which strengthens the diversity contrast against the fixed founder size. Holding mean Ne constant removed the effect and reversed its direction. This is documented in the notebook as a reminder to control for confounds in simulation experiments.

## Limitations

- Small grid (5×5), few samples per deme, and a single genomic region
- Heterogeneity is spatially random; real landscapes are spatially autocorrelated, which may affect inference more strongly
- The estimator is a simple diversity-distance correlation, not a dedicated method such as the directionality index (ψ)
- Limited replication: the study is underpowered to detect small effects
- Colonization is modeled with mass migration and a bottleneck, which is a simplification of true range expansion dynamics

## Possible next steps

- Spatially structured Ne landscapes (e.g., a gradient running against the expansion direction)
- Implement and compare the directionality index ψ
- Larger grids and more replicates; vary sample size and number of loci
- Apply to a real elevation or habitat-suitability raster

## Running the project

Open the notebook in Google Colab (badge above), or run locally:

```bash
pip install msprime scikit-allel numpy matplotlib scipy
jupyter notebook range_expansion_origin.ipynb
```

Run cells top to bottom. The replicate experiments take several minutes; reduce `n_reps` for a quicker run.

## Repository structure

```
.
├── range_expansion_origin.ipynb   # Full notebook: simulation, inference, experiments
└── README.md
```

## Requirements

- Python 3.9+
- `msprime`, `scikit-allel`, `numpy`, `matplotlib`, `scipy`

