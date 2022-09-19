# 6. Training models with DeepProfiler


## **6.1 Export single-cells**

Imagine you already have a dataset of full images and you would like to train a model. Models are trained with single-cell crops, so the single-cells of the dataset should be exported separately.
If the dataset consists of 16-bit TIFF images, it is **strongly** recommended to [pre-process the dataset](https://cytomining.github.io/DeepProfiler-handbook/docs/06-train.html#optional-project-dependent-functions) first. 


The basic command for export is as follows:
```
python deepprofiler --root=/home/ubuntu/project/ --config=export.json --metadata=index.csv --single-cells=single_cells_dataset export-sc
```

The exported images and metadata (`sc-metadata.csv`) will be stored in `/project/outputs/single_cells_dataset/`. If the `single-cells` parameter is not set, the default folder for the exported dataset would be `single-cells`. 
 


```{admonition} About training and validation splits
:class: tip
As mentioned in the [config description](https://cytomining.github.io/DeepProfiler-handbook/docs/05-config.html#train-parameters-for-training-a-deep-learning-model-on-your-data), the metadata field `split_field` and its values from `training` and `validation` configuration config fields define the initial training-validation split, which will be reflected in the single-cell metadata file for each record.
If a different training-validation split is needed, you can modify the `split_field` column (for example with `pandas`) and save the new metadata to a different file and then use it for training, there is no need to export images again. It is also possible to append a new column for a new training-validation split to an existing metadata file, in that case, don't forget to change `split_field` in the training configuration file. 

```


## **6.2 Train a model:**


An example of the training command:

```
python3 deepprofiler --root=/home/ubuntu/project/ --config filename.json --single-cells=single_cells_dataset --gpu 0 train
```


```{admonition} Metadata parameter
:class: tip
The default single-cell metadata file is `sc-metadata.csv`, though it can be different by passing `--metadata` parameter.
`--gpu` parameter is an id of a GPU to be used, available GPUs with their IDs can be listed with `nvidia-smi` command. 

```