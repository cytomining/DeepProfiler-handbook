

# 6. Train a model and profile cells

After configuring the project, running DeepProfiler is straightforward. There are two main commands that can be run for training and then extracting features with a trained network.


## **6.1 Train a model:**

If using a pretrained model, skip this step and move to 6.2 directly.


The minimal command for training can look like:

```
python3 deepprofiler --root=/home/ubuntu/project/ --config filename.json train
```


## **Optional project-dependent functions:**

````{dropdown} **Prepare a dataset:**

This tool computes illumination statistics, illumination correction functions and compresses images into PNG format. This is useful when the image collection used for training is too large and cannot be kept in a single server (think TBs of imaging data in a data center). Compressing a large collection of images can make training feasible for a diversity of cellular phenotypes. We designed this functionality to compress images as much as possible while losing the minimum amount of information.


```
python3 deepprofiler --root=/home/ubuntu/project/ --config filename.json prepare
python3 deepprofiler --root=/project_folder/example_data --config=config.json prepare
```


````