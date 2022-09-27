# 8. Downstream analysis

After aggregating, profiles are typically processed before being used for analysis purposes. 
Data-processing approaches for profiles extracted with CellProfiler were described in the paper 
"[Data analysis strategies for image-based profiling](https://doi.org/10.1038/nmeth.4397)". 
Some of those approaches were used for features extracted with DeepProfiler.

# 8.1 Aggregating and evaluating profiles with profiling notebooks

To analyse benchmark datasets [analysis paper](https://www.biorxiv.org/content/10.1101/2022.08.12.503783v1.full) we created 
Jupyter notebooks, which are available in [DeepProfilerExperiments Github repository](https://github.com/broadinstitute/DeepProfilerExperiments). 
Folders `ta-orf` (BBBC037 dataset), `bbbc022` and `cdrp` (BBBC036 dataset) correspond to benchmark datasets and contain 
Jupyter notebooks, `profiling` folder contains utility functions. Next, we are going to go through the most important 
analysis notebooks. You can reuse this code to build a custom analysis pipeline.


## 8.1.1 Aggregation of profiles and batch-correction

Aggregation of features can be done with `01-create-profiles.ipynb` Jupyter notebook. The inputs are: [project metadata 
file](https://cytomining.github.io/DeepProfiler-handbook/docs/04-metadata.html#the-index-csv-file), ground truth metadata 
with annotations of treatments by the mechanism of action and the `.npz` files with features  organized in 
`{Plate}/{Well}/{Site}.npz` folder structure. 

1. Define the parameters: project path and experiment folder name as in DeepProfiler, sphering regularization parameter and 
   output file names: `OUTPUT_FILE` - output file that will contain well-level profiles after sphering and `MATRIX_FILE` that will contain similarity matrix of treatments.
2. Load single-cell data by passing through metadata and reading files with features. It might require a significant amount of RAM (depending on the size of the dataset).
3. Aggregate features of single-cells by site with median aggregation. `sites` Pandas dataframe will contain those aggregated profiles.
4. Aggregate site-level profiles to well-level using mean aggregation and store them in `wells` dataframe. 
5. Sphering: calculate the sphering transformation **using control wells only** and then apply this transformation to all well-level profiles in `wells`.
6. Aggregate well-level profiles to treatment-level profiles. Only treatments with ground truth annotations are aggregated and stored in `profiles` dataframe.
7. Create a similarity matrix from `profiles`. We use cosine similarity as a similarity metric. Save the matrix to `OUTPUT_FILE`.


## 8.1.2 Evaluation of profiles

Evaluation of profiles can be done with `04-downstream-analysis.ipynb`. This notebook uses the cosine similarity matrix 
obtained in `01-create-profiles.ipynb` as an input. 
1. First parts (*Load similarity matrix* and *MOA matching*) of the notebook perform input pre-processing. `moa_matches` 
   is a binary matrix of size `n x n`, where `n` is the number of treatments in the similarity matrix. This matrix is symmetrical 
   and each row (column) relates is a treatment, and values are `True` in each row (column) for the current treatment (diagonal) 
   and treatments that belong to the same mechanism of action as the current treatment.
2. Calculate the precision metrics: `Average precision` and `Precision @ top 1%`. 
3. Calculate the recall metrics: `Recall` and `Average Recall at top 10%` (a different cut-off was used on purpose). 
4. Calculate mean Average Precision, and area under interpolated Precision-Recall curve and plot it. 
5. Save results to the `.csv` table and `pickle` (contains Python dictionary and can be loaded with Python). 

# 8.2 Aggregating and evaluating profiles with Pycytominer

DeepProfiler single-cell profiles can be aggregated via a [Pycytominer](https://github.com/cytomining/pycytominer) 
function called [DeepProfiler_Processing](https://github.com/cytomining/pycytominer/blob/master/pycytominer/cyto_utils/DeepProfiler_processing.py). 
This function reads the output features of DeepProfiler, including the metadata, aggregates, and saves the data in a Pycytominer and Cytominer-eval readable data frame format.

This code gives an example of how to run the aggregation function:

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