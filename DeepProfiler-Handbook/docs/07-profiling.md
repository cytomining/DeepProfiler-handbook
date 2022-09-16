# 7. Profiling

DeepProfiler allows to profile the data using self-trained models or pre-trained models. 

## **7.1 General profiling information:**

General example command to run profiling: 

```
python3 deepprofiler --root=/home/ubuntu/project/ --config filename.json --metadata index.csv --exp experiment_name profile
```


```{admonition} Note
:class: tip
The `--metadata index.csv` parameter points to  a metadata file from /inputs/metadata folder.
The `--config filename.json` parameter points to the name of a file from /inputs/config folder.
The `--exp experiment_name` points to a folder in /outputs/ folder.  
```

Profiling parameters are regulated in _profile_ section of your config, the example for self-trained models is below.
For specific instructions and config examples see further sections.

```
"profile": {
      "feature_layer": "pool5",
      "checkpoint": "checkpoint_0020.hdf5",
      "batch_size": 128
    }
```

```{admonition} Note
:class: tip
The `checkpoint` parameter points to the name of a file from /outputs/experiment_name/checkpoint/ folder.
If your GPU\workstations allows, you can insrease `batch_size` to speed-up the profiling.
```

```{admonition} About feature layers
:class: tip
The `feature_layer` parameter in the configuration file defines the layer of the model to be extracted. `pool5` in this case refers to average pooling penultimate layer. You might want to experiment with other layers. In our analysis, we found that in case of EfficientNet, the best downstream results are obtained with `block6a_activation` intermediate layer.
```

The extracted features are stored in `/outputs/experiment_name/features/`. In case if you want to run another feature extraction for the same experiment, you will need to move (or remove) the contents of this folder. 


## **7.2 Profiling with self-trained model:**

After training your own model, you can profile your data using the checkpoints from the training:

The _profile_ section example:

```
"profile": {
      "feature_layer": "pool5",
      "checkpoint": "checkpoint_0020.hdf5",
      "batch_size": 128
    }
```


## **7.3 Profiling with ImageNet pre-trained model:**

Pre-trained ImageNet model is downloaded automatically for EfficientNet or ResNet. 

The _profile_ section of your config should be adjusted: `checkpoint` field will have `None` and `use_pretrained_input_size` is set according to the chosen model.
  
Example for _EfficientNet-B0_ model:

```
"profile": {
      "use_pretrained_input_size" : 224,
      "feature_layer": "avg_pool",
      "checkpoint": "None",
      "batch_size": 128
    }
```

## **7.4 Profiling with Cell Painting CNN model:**

Cell Painting CNN was pre-trained on diverse dataset (almost 500 treatments and two controls in two different cell lines: U2OS and A549) of Cell Painting images and can be used primarly for processing of new Cell Painting experiments.

To run an experiment, an experiment folder should be created manually in `/outputs/`, for example  `/outputs/experiment/` and then create a checkpoint folder `/outputs/experiment/checkpoint/`. The model should be copied into this folder. After it, the usage of the model does not differ from self-trained models, a configuration example:

```
"profile": {
      "feature_layer": "block6a_activation",
      "checkpoint": "combinedset_cellsout_e30.hdf5",
      "batch_size": 128
    }
```

## **Optional project-dependent functions:**

````{dropdown} **Prepare a dataset:**

This tool computes illumination statistics, illumination correction functions and compresses images into PNG format. This is useful when the image collection used for training is too large and cannot be kept in a single server (think TBs of imaging data in a data center). Compressing a large collection of images can make training feasible for a diversity of cellular phenotypes. We designed this functionality to compress images as much as possible while losing the minimum amount of information.


```
python3 deepprofiler --root=/home/ubuntu/project/ --config filename.json prepare
python3 deepprofiler --root=/project_folder/example_data --config=config.json prepare
```


````

````{dropdown} **Split index file**:

Create multiple files with parts of the index for parallelization purposes. Parallelization is not automatic, it requires manually launching different DeepProfiler instances using different index files. This function just loads the index and creates separate files without repeating information, so a different machine can be assigned to profile different parts of the dataset.

```
python3 deepprofiler --root=/home/ubuntu/project/ --config filename.json split
```


````
