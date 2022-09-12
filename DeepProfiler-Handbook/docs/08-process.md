# 8. Processing DeepProfiler features

After aggregating, profiles are typically processed before being used for analysis purposes. Downstream processing can be done with the different operations of [Pycytominer](https://github.com/cytomining/pycytominer). Typically well-level data is spherized and then aggregated to compound level (no normalization and feature selection).

The [Cytominer-eval](https://github.com/cytomining/cytominer-eval) repository contains six functions that calculate different quality metrics for perturbation profiling experiments: Precision@K and Recall@K, Enrichment, Hit@k, Replicate reproducibility, MP-value, and Grit.

