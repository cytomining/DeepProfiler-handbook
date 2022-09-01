# 1. Install DeepProfiler

There are two options for installation: installing DeepProfiler from source and using a docker container. We recommend you use docker if your environment needs to be quickly recreated (e.g., virtual machines in the cloud) or if you plan to do large scale processing. Install from source when you need flexibility to adjust code and configurations, for instance when testing new models or for adjusting advanced training settings. If using a docker container, download a [Docker Image](https://hub.docker.com/r/michaelbornholdt/deep_profiler/tags) using the command `docker pull michaelbornholdt/deep_profiler:aws-0` then proceed to step 2. The instructions in this section assist you with installing DeepProfiler from source in a Linux environment. The provided code snippets assume the use of Ubuntu but any Linux distribution can work for installing DeepProfiler; just make sure your environment is configured accordingly.


## **1.1 Clone the repository**

On the command line, enter the directory where you want to install DeepProfiler, then clone the GitHub repo:


```
git clone https://github.com/broadinstitute/DeepProfiler.git
```


Alternatively, you can fork it to keep track of your own changes, and eventually contribute back.


## **1.2 Install dependencies and packages**

First make sure that your python installation is up to date. DeepProfiler runs in Python 3.6 or higher (Python 3.8 recommended).


```
sudo apt update
```


If you don’t have sudo, use `conda update --all`


```
sudo apt install python3
```


We strongly advise using a virtual environment for DeepProfiler. This will help not to interfere with system-wide installed python packages. Virtualenv ([https://pypi.org/project/virtualenv/](https://pypi.org/project/virtualenv/)) can be installed with the following command:


```
sudo install virtualenv
```


If you don’t have sudo privileges in your system, use:


```
pip3 install virtualenv
```


Next, you will be able to create a virtual environment in a current directory:


```
virtualenv -p python3 deepprofenv
```


To activate the created virtualenv use:


```
source ./deepprofenv/bin/activate
```

```{admonition} Note
:class: tip
If you have access to a GPU, make sure that you have the correct drivers and the `tensorflow` package installed and configured correctly. You can follow this link for more details about how to install TensorFlow with GPU support in your machine: [https://www.tensorflow.org/install](https://www.tensorflow.org/install) . The command to check if the correct GPU is being utilized is `nvidia-smi` .
```

Now you can enter the DeepProfiler directory and install it using pip. This will pull the missing dependencies and will also create runnable packages in your clone.


```
cd DeepProfiler/
pip install -e .
python3 deepprofiler
```

```{admonition} Note
:class: tip
The above environment does not include the package of ‘Imagecodecs’, that needs to be installed separately.
```

The following is the expected output of running the DeepProfiler entry point:


```
Using TensorFlow backend.
Usage: deepprofiler [OPTIONS] COMMAND [ARGS]...


Options:
  --root PATH         Root directory for DeepProfiler experiment
  --config TEXT       Path to existing config file (filename in project_root/inputs/config/)
  --cores INTEGER     Number of CPU cores for parallel processing (all=0) for prepare command
  --gpu INTEGER          GPU device id (the id can be checked with nvidia-smi)
  --exp TEXT          Name of experiment, this folder will be created in project_root/outputs/
  --logging TEXT      Path to file with comet.ml API key (filename in project_root/inputs/config/)
  --single-cells TEXT       Name of the folder with single-cell dataset (output for export-sc command, input for training with sampled crop generator or online labels crop generator)
  --metadata TEXT	    Metadata filename, for exporting or profiling it is a filename for project_root/inputs/metadata/, for training with sampled crop generator or online labels crop generator the filename in project_root/outputs/<single-cell-dataset>/
  --help              Show this message and exit.


Commands:
  export-sc export crops of single-cells for training
  prepare   run illumination correction and compression
  profile   run feature extraction
  setup     initialize folder structure of DeepProfiler project
  split     split metadata into multiple parts
  train     train a model
  traintf2  train a model with TensorFlow 2 dataset
```



## **1.4 Troubleshooting**

Some dependencies need to be manually installed depending on your environment. If you pulled the latest version of DeepProfiler in an existing clone, try reinstalling it again as in step 1.2 to make sure the dependencies are still up to date.

