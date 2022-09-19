# 7. Profile cells with DeepProfiler

DeepProfiler allows profiling the data using self-trained models or pre-trained models. 

## **7.1 General profiling information:**

Before profiling the dataset, it is **strongly** recommended to [pre-process the dataset](https://cytomining.github.io/DeepProfiler-handbook/docs/03-images.html#dataset-compression-and-illumination-correction) if you have not done it yet.

General example command to run profiling: 

```
python3 deepprofiler --root=/home/ubuntu/project/ --config filename.json --metadata index.csv --exp experiment_name profile
```


```{admonition} Profiling arguments
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

```{admonition} On configureation parameters
:class: tip
The `checkpoint` parameter points to the name of a file from `/outputs/experiment_name/checkpoint/` folder.
If your GPU\workstation allows, you can increase `batch_size` to speed up the profiling.
```

```{admonition} About feature layers
:class: tip
The `feature_layer` parameter in the configuration file defines the layer of the model to be extracted. `pool5` in this case refers to the average pooling penultimate layer. You might want to experiment with other layers. In our analysis, we found that in the case of EfficientNet, the best downstream results are obtained with `block6a_activation` intermediate layer.
```

The extracted features are stored in `/outputs/experiment_name/features/` as NumPy `npz` files for each site (full image). The array of features can be accessed through `features` field of the object: 
```
with open(filename, "rb") as data:
    info = np.load(data)
    info["features"]
```
The order of vectors matches the order of cell locations for the image. 


In case you want to run another feature extraction for the same experiment, you will need to move (or remove) the contents of this folder. 


## **7.2 Profiling with self-trained model:**

After training your model, you can profile your data using the checkpoints from the training:

The _profile_ section example:

```
"profile": {
      "feature_layer": "pool5",
      "checkpoint": "checkpoint_0020.hdf5",
      "batch_size": 128
    }
```


## **7.3 Profiling with ImageNet pre-trained model:**

Pre-trained ImageNet model is downloaded automatically for EfficientNet or ResNet to the cache folder of Keras.  

The _profile_ section of your config should be adjusted: `checkpoint` field will have `None` and `use_pretrained_input_size` is set according to the chosen model.

The crop_generator parameter should be set to `repeat_channel_crop_generator`, this crop generator feeds each channel to the model seprately, to match the number of channels of ImageNet, each channel is replcated three times. 
  
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

Cell Painting CNN was pre-trained on the diverse dataset (almost 500 treatments and two controls in two different cell lines: U2OS and A549) of Cell Painting images and can be used primarily for the processing of new Cell Painting experiments.

To run an experiment, an experiment folder should be created manually in `/outputs/`, for example  `/outputs/experiment/` and then create a checkpoint folder `/outputs/experiment/checkpoint/`. The model should be copied into this folder. After it, the usage of the model does not differ from self-trained models, a configuration example:

```
"profile": {
      "feature_layer": "block6a_activation",
      "checkpoint": "combinedset_cellsout_e30.hdf5",
      "batch_size": 128
    }
```

## **Optional project-dependent functions:**


````{dropdown} **Split index file**:

Create multiple files with parts of the index for parallelization purposes. Parallelization is not automatic, it requires manually launching different DeepProfiler instances using different index files. This function just loads the index and creates separate files without repeating information, so a different machine can be assigned to profile different parts of the dataset.

```
python3 deepprofiler --root=/home/ubuntu/project/ --config filename.json split
```


````
