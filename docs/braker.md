# Gene prediction using BRAKER3

BRAKER3 is a pipeline that combines GeneMark-ET and AUGUSTUS to predict genes in eukaryotic genomes. This pipeline is particularly useful for annotating newly sequenced genomes. The flexibility of BRAKER3 allows users to provide various input datasets for improving gene prediction accuracy. In this example, we will use various scenarios to predict genes in a Maize genome using BRAKER3. Following are the scenarios we will cover:

1. Scenario 1: Only input genome without any external evidence datasets
2. Scenario 2: Input genome with RNA-Seq data (minimal evidence)
3. Scenario 3: Input genome with RNA-Seq data (full evidence)
4. Scenario 4: Input genome with protein sequences
5. Scenario 5: Input genome with RNA-Seq data and protein sequences
6. Scenario 6: Input genome with pretrained species model
7. Scenario 7: Input genome with Iso-Seq data and protein sequences
8. Scenario 8: Input genome with Iso-Seq data and RNA-Seq data

## Installation

We will use the `apptainer` tool to build a Singularity container for BRAKER3. The Singularity container will contain all the necessary dependencies and tools required to run BRAKER3. To build the Singularity container, run the following command:

```bash
apptainer build --fakeroot braker3.sif docker://teambraker/braker3:latest
```

This will create a Singularity container named `braker3.sif` with BRAKER3 installed.

## Settng up BRAKER3

Before running BRAKER3, we need to set up:

1. `GeneMark-ES/ET/EP/ETP` license key
2. The `AUGUSTUS_CONFIG_PATH` configuration path

The license key for `GeneMark-ES/ET/EP/ETP` can be obtained from the [GeneMark website](http://exon.gatech.edu/GeneMark/license_download.cgi). Once downloaded, you need to place it in your home directory:

```bash
tar xf gm_key_64.gz
cp gm_key_64 ~/.gm_key
```

For the `AUGUSTUS_CONFIG_PATH`, we need to copy the `config` directory from the Singularity container to the scratch directory. This is required because BRAKER3 needs to write to the `config` directory, and the Singularity container is read-only. To copy the `config` directory, run the following command:

```bash
apptainer exec braker3.sif cp -r /opt/Augustus/config ${RCAC_SCRATCH}/braker/augustus_config
```


## Running BRAKER3

The paths to the following variables need to be set:

```bash
BRAKER_SIF="${RCAC_SCRATCH}/braker/braker3.sif"
AUGUSTUS_CONFIG_PATH="${RCAC_SCRATCH}/braker/augustus_config"
GENEMARK_PATH="/opt/ETP/bin/gmes"
genome="${RCAC_SCRATCH}/braker/Zm-B73-REFERENCE-NAM-5.0_softmasked.fa"
workdir=${PWD}/$(basename ${genome%.*})_genomeOnly
species="$(basename ${genome%.*}).$(date +"%Y%m%d")"
```


### Input datasets

::::{tab-set}

:::{tab-item} Case 1 

| Input                     | Type                            |
|---------------------------|---------------------------------|
| Genome                    | B73.v5 (softmasked)             |
| RNA-Seq data              | None                            |
| Protein sequences         | None                            |
| Long-read data            | None                            |
| Pretrained species model  | None                            |


:::

:::{tab-item} Case 2 

| Input                     | Type                            |
|---------------------------|---------------------------------|
| Genome                    | B73.v5 (softmasked)             |
| RNA-Seq data              | RNAseq (single library)         |
| Protein sequences         | None                            |
| Long-read data            | None                            |
| Pretrained species model  | None                            |

:::

:::{tab-item} Case 3

| Input                     | Type                            |
|---------------------------|---------------------------------|
| Genome                    | B73.v5 (softmasked)             |
| RNA-Seq data              | RNAseq (11 tissues)             |
| Protein sequences         | None                            |
| Long-read data            | None                            |
| Pretrained species model  | None                            |

:::

:::{tab-item} Case 4 

| Input                     | Type                            |
|---------------------------|---------------------------------|
| Genome                    | B73.v5 (softmasked)             |
| RNA-Seq data              | None                            |
| Protein sequences         | Viridiplantae protein sequences |
| Long-read data            | None                            |
| Pretrained species model  | None                            |


:::

:::{tab-item} Case 5 

| Input                     | Type                            |
|---------------------------|---------------------------------|
| Genome                    | B73.v5 (softmasked)             |
| RNA-Seq data              | RNAseq (11 tissues)             |
| Protein sequences         | Viridiplantae protein sequences |
| Long-read data            | None                            |
| Pretrained species model  | None                            |


:::

:::{tab-item} Case 6 

| Input                     | Type                            |
|---------------------------|---------------------------------|
| Genome                    | B73.v5 (softmasked)             |
| RNA-Seq data              | None                            |
| Protein sequences         | None                            |
| Long-read data            | None                            |
| Pretrained species model  | "Maize"                         |

:::

:::{tab-item} Case 7 

| Input                     | Type                            |
|---------------------------|---------------------------------|
| Genome                    | B73.v5 (softmasked)             |
| RNA-Seq data              | None                            |
| Protein sequences         | Viridiplantae protein sequences |
| Long-read data            | Iso-Seq data                    |
| Pretrained species model  | None                            |



:::

:::{tab-item} Case 8 

| Input                     | Type                            |
|---------------------------|---------------------------------|
| Genome                    | B73.v5 (softmasked)             |
| RNA-Seq data              | RNAseq (11 tissues)             |
| Protein sequences         | None                            |
| Long-read data            | Iso-Seq data                    |
| Pretrained species model  | None                            |


:::

::::