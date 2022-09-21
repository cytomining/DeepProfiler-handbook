# 9. Processing DeepProfiler features

After aggregating, profiles are typically processed before being used for analysis purposes. Downstream processing can be done with the different operations of [Pycytominer](https://github.com/cytomining/pycytominer). Typically well-level data is spherized and then aggregated to compound level (no normalization and feature selection).

# 9.1 Aggregating and evaluating profiles with Pycytominer

DeepProfiler single-cell profiles can be aggregated via a [Pycytominer](https://github.com/cytomining/pycytominer) function called [DeepProfiler_Processing](https://github.com/cytomining/pycytominer/blob/master/pycytominer/cyto_utils/DeepProfiler_processing.py). This function reads the output features of DeepProfiler, including the metadata, aggregates, and saves the data in a Pycytominer and Cytominer-eval readable data frame format.

This code gives an example on how to run the aggregation function:

Make sure to install this version of pycytominer:


```
pip install git+git://github.com/cytomining/pycytominer@update_agg_TF2

import os
import numpy as np
import pandas as pd

from pycytominer.cyto_utils.DeepProfiler_processing import AggregateDeepProfiler

project_dir = os.path.join(
    os.path.dirname(__file__)
)

experiment = 'folder_name'

profile_dir = os.path.join(project_dir, "outputs", experiment, "features")

index_file = os.path.join(project_dir, "inputs", "metadata", "index.csv")

output_folder = os.path.join(project_dir, 'outputs',experiment,'aggregated')

well_class = AggregateDeepProfiler(
    index_file=index_file,
    profile_dir=profile_dir,
    aggregate_operation="median",
    aggregate_on="well",
    output_file=output_folder,
)
df_well = well_class.aggregate_deep()

df_well.to_csv('aggregated_efficientnet_median.csv')
```

The [Cytominer-eval](https://github.com/cytomining/cytominer-eval) repository contains six functions that calculate different quality metrics for perturbation profiling experiments: Precision@K and Recall@K, Enrichment, Hit@k, Replicate reproducibility, MP-value, and Grit.
