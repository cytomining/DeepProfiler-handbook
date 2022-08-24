

# 4. Train a model and profile cells

After configuring the project, running DeepProfiler is straightforward. There are two main commands that can be run for training and then extracting features with a trained network.


## **4.1 Train a model:**

If using a pretrained model, skip this step and move to 4.2 directly.


The minimal command for training can look like:

```
python3 deepprofiler --root=/home/ubuntu/project/ --config filename.json train
```


## **4.2 Infer profiles from a model:**

```
python3 deepprofiler --root=/home/ubuntu/project/ --config filename.json --exp experiment_name profile
```

```{admonition} Note
:class: tip
The `--config filename.json` parameter points to the name of a file from /inputs/config folder.
```


### Profiling with self-trained model:

After training your own model, you can profile your data using the checkpoints from the training:

The _profile_ section of your config should look something like this:


```
"profile": {
      "feature_layer": "pool5",
      "checkpoint": "checkpoint_0020.hdf5",
      "batch_size": 128
    }
```


### Profiling with pre-trained model: 

When profiling from a model pre-trained on ImageNet images, the ‘checkpoint’ simply is replaced with “None” and ‘use_pretrained_input_size’ is set according to the chosen model.

The _profile_ section of your config should look something like this (example for _EfficientNet-B0_ model):

```
"profile": {
      "use_pretrained_input_size" : 224,
      "feature_layer": "avg_pool",
      "checkpoint": "None",
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
