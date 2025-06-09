# Conda for bioinformatics

Conda simplifies installing and managing bioinformatics software and dependencies, especially on shared systems like HPC clusters. It allows users to create isolated environments without requiring admin rights. This avoids conflicts between packages and ensures reproducibility.

## Initial setup

First, load the anaconda module:

```bash
ml anaconda
```

Since home directories have limited space, store environments in `/depot`:

```bash
mkdir -p /depot/proejct/username/conda_envs
conda config --add envs_dirs /depot/proejct/username/conda_envs
```

(Optional) Speed up downloads by disabling SSL verification (only if needed):

```bash
conda config --set ssl_verify no
```

## Create and activate an environment

Example: create an environment for aligners

```bash
conda create -y -n aligners bwa hisat2 star
conda activate aligners
```

This installs `bwa`, `hisat2`, and `star` in an isolated environment named `aligners`.

## Install packages later

To add more tools later:

```bash
ml anaconda
conda activate aligners
conda install samtools
```

You can also install from specific channels if needed:

```bash
conda install -c bioconda -c conda-forge fastqc
```

## List and manage environments

List all environments:

```bash
conda env list
```

Remove an environment:

```bash
conda remove -n aligners --all
```

Export environment configuration:

```bash
conda env export > aligners.yaml
```

Recreate from config:

```bash
conda env create -f aligners.yaml
```

## Best practices

* Always activate the environment before running tools.
* Avoid installing too many unrelated packages in one environment.
* Use `bioconda` and `conda-forge` channels for most bioinformatics tools.
* Keep environments in `/depot` or other high-capacity paths.

## Example workflow summary

```bash
ml anaconda
mkdir -p /depot/$USER/conda_envs
conda config --add envs_dirs /depot/$USER/conda_envs
conda create -y -n aligners bwa hisat2 star
conda activate aligners
```

