# notebook-ops
*This Documentation is translated by ChatGPT based on [Japanese Documentation](https://github.com/k0tk267/notebook-ops/blob/main/docs/ja/README.md).*

A template that is useful when conducting machine learning experiments at the individual level.
More explanations can be found in my blog post [solo MLOps for small-scale machine learning experiments](https://k0tk267.github.io/posts/minimal-mlops).

### Setup
The following command must be executed first to set up the name in the pyproject.toml file and environment variables.
```
make setup \
    project_name=<replace current project name> \
    your_name=<replace your name> \
    your_email=<replace your email> \
    package_name=<replace your package name>
```

### Directory Structure
```
.
├── data
│   ├── processed          <- Folder to put the data after it has been processed
│   ├── raw                <- Folder to put the raw data
│   └── results
│         ├── experiments  <- Folder to put the logs and models of each experiment
│         └── notebooks    <- Folder where Notebooks generated by papermill will automatically be placed
├── docs
│   └── ja                 <- Folder to put the Japanese documents
├── scripts
│   ├── run.py             <- File used when executing Notebooks with papermill
│   ├── run.sh             <- File used when executing Notebooks with papermill
│   └── setup.sh           <- File that performs initial setup of this template when used
├── src
│   ├── lib                <- Folder to store classes and functions extracted
│   └── notebooks          <- Folder to store Notebooks
├── .env.example           <- File to set environment variables
├── .gitignore             <- File to specify files to exclude from Git management
├── .rsyncignore           <- File to specify files to exclude from synchronization when using rsync
├── docker-compose.yml     <- File to manage containers
├── Dockerfile             <- File to build Docker Image
├── poetry.lock            <- File to manage libraries
├── pyproject.toml         <- File to manage libraries
└── README.md              <- File to write explanations for the repository, etc.
```

##### About the data directory
This is basically the place to put data, but for Notebooks generated by papermill, logs are kept and placed in this directory. With regard to the data placed in the raw directory, it must not be processed, except in the case of an extremely rare scenario, in which case the data must not be processed. All preprocessed data must be stored in processed.

##### About the scripts directory:
This directory stores files that contain the logic of commands that will be executed. The commands themselves are defined in the Makefile.

##### About the src directory:
The lib directory stores script files (.py), and the notebooks directory stores Notebooks (.ipynb). It is assumed that the results will be viewed by executing Notebooks, so the files under lib are intended to be used by importing them from Notebooks.

##### About Dockerfile and docker-compose.yml:
Dockerfile and docker-compose.yml are created and placed here in order to eliminate completely environment-dependent issues, but the originally created execution scripts are not designed to work on Docker. Docker has a limitation on assigned memory, so this is the reason why it is done this way in case the scripts require a large amount of memory and cannot use the full specifications of the machine.

##### About pyproject.toml and poetry.lock:
These are used to manage libraries using Poetry. By default, papermill, python-dotenv, black, isort, jupyterlab are installed. The Python version is set to 3.8, so if a different version is used, there is likely to be an error or warning when installing the libraries.

### Usage
It is basically assumed that the analysis will be done using Notebooks. Personally, I think Notebooks tend to be a problem of reproducibility and a breeding ground for bugs, so I don't want to use them as much as possible for experiments, but I have created this with the purpose of making them usable as much as possible for reporting purposes, as they are convenient for reporting purposes.

##### How to run Notebook:
When running Notebooks, always use papermill. Conversely, a Notebook that is executed without using papermill should be considered an untrusted Notebook.
Use the following command to run a Notebook.
By specifying the name of the Notebook located under src/notebooks in the input_file, and by putting the name of the newly generated Notebook in the output_file, a Notebook with a timestamp added to the name specified in output_file will be generated under data/results/notebooks. This generated Notebook should be kept for storage and should never be altered (it is set so that it cannot be overwritten unless the permissions are changed).
```
make run \
    input_file=train.ipynb \
    output_file=result.ipynb \
    batch_size=1 \
    epoch=100
```

##### Pre-created Commands
- Command for executing a notebook
```
make run \
    input_file=train.ipynb \
    output_file=result.ipynb \
    batch_size=8 \
    epoch=1000
```
- Command for initial setup
```
make setup \
    project_name="Treadstone" \
    your_name="Jason Bourne"
    your_email="Jason@Bourne.com"
    package_name="treadstone"
```
- Command for formatting
  - Executes formatter for Python files under src/lib and scripts/
  - Does not include linter as fixing it manually is tedious
```
make format
```
- Command for synchronizing with computation server and files
  - Use make sync-push when you want to send files (when you want to synchronize local file changes)
  - Use make sync-pull when you want to receive files (when you want to bring experiment results to local)
```
make sync-push TARGET_DIR="Jason@Bourne.com:/treadstone"
or
make sync-pull TARGET_DIR="Jason@Bourne.com:/treadstone"
```
