# Installation

## Cloning the repository

In your local computer's command line, navigate to the directory where you want to install DeepProfiler, then clone it:

```
git clone https://github.com/broadinstitute/DeepProfiler.git
```

Alternatively, you can fork it to keep track of your own changes, and eventually contribute back.

## Installing dependencies and packages

Here, we assume you are using Ubuntu, so first make sure that your python installation is up to date. DeepProfiler runs in Python 3.6 or higher.

```
sudo apt update
sudo apt install python3 python3-pip
```

Example of code `in line` and secondary <code> other style for code </code>

We strongly advise using a virtual environment for DeepProfiler. This will help not to interfere with system-wide installed python packages. [Virtualenv](https://pypi.org/project/virtualenv/) can be installed with the following command:

```
sudo pip3 install virtualenv
```

After that, you will be able to create a virtual environment in a current directory:

```
virtualenv -p python3 deepprofenv
```

To activate the created virtualenv:
```
source ./deepprofenv/bin/activate
```

:::{note}
If you have access to a GPU, make sure that you have the correct drivers and the tensorflow package installed. You can follow [this link](https://www.tensorflow.org/install) for more details about how to install TensorFlow with GPU support in your machine.:::

Now you can enter the DeepProfiler directory and install it using pip. This will pull the missing dependencies and will also create runnable packages in your clone.
```
cd DeepProfiler/
pip install -e .
python3 deepprofiler
```
The following is the expected output of running the DeepProfiler entry point:
```
Using TensorFlow backend.
Usage: deepprofiler [OPTIONS] COMMAND [ARGS]...
 
Options:
  --root PATH         Root directory for DeepProfiler experiment
  --config TEXT       Path to existing config file (filename in project_root/inputs/config/)
  --cores INTEGER     Number of CPU cores for parallel processing (all=0) for prepare command
  --gpu TEXT          GPU device id (the id can be checked with nvidia-smi)
  --exp TEXT          Name of experiment, this folder will be created in project_root/outputs/
  --logging TEXT      Path to file with comet.ml API key (filename in project_root/inputs/config/)
  --sample TEXT       Name of the folder with single-cell sample (output for sample-sc command, input for training with sampled crop generator or online labels crop generator)
  --metadata TEXT	Metadata filename
  --help              Show this message and exit.
 
Commands:
  download-bbbc021
  prepare
  profile
  setup
  split
  train
  traintf2
  export-sc

```

:     A code block

:::{note}
Here is a note!
:::
