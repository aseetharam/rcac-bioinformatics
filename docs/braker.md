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

::::{tab-set}

:::{tab-item} ### Scenario 1

**Only input genome without any external evidence datasets**

In this scenario, we will predict genes in a Maize genome using only the input genome without any external evidence datasets. The following command will run BRAKER3 with the input genome:

```bash
mkdir -p ${workdir}
apptainer exec --bind ${RCAC_SCRATCH} ${BRAKER_SIF} braker.pl \
        --AUGUSTUS_CONFIG_PATH=${AUGUSTUS_CONFIG_PATH} \
        --GENEMARK_PATH=${GENEMARK_PATH} \
        --genome=${genome} \
        --esmode \
        --species=Zm_$(date +"%Y%m%d").3b \
        --workingdir=${workdir} \
        --gff3 \
        --threads ${SLURM_CPUS_ON_NODE}
```

:::

:::{tab-item} Scenario 2

**Scenario 2: Input genome with RNA-Seq data (minimal evidence)**

In this scenario, we will predict genes in a Maize genome using the input genome and RNA-Seq data as minimal evidence (just one RNAseq library - `B73_V11_middle_MN01042`). The following command will run BRAKER3 with the input genome and RNA-Seq data:

```bash
apptainer exec --bind ${RCAC_SCRATCH} ${BRAKER_SIF} braker.pl \
        --AUGUSTUS_CONFIG_PATH=${AUGUSTUS_CONFIG_PATH} \
        --GENEMARK_PATH=${GENEMARK_PATH} \
        --genome=${genome} \
        --rnaseq_sets_ids=B73_V11_middle_MN01042 \
        --rnaseq_sets_dirs=${RCAC_SCRATCH}/RNAseq/ \
        --workingdir=${workdir} \
        --gff3 \
        --threads ${SLURM_CPUS_ON_NODE}
```

:::

:::{tab-item} Scenario 3

**Input genome with RNA-Seq data (full evidence)**

In this scenario, we will predict genes in a Maize genome using the input genome and RNA-Seq data as full evidence (multiple RNAseq libraries; 10 tissues with replicates). The following command will run BRAKER3 with the input genome and RNA-Seq data:

```bash
rna_seq_sets_ids="B73_8DAS_root_MN01011,B73_8DAS_root_MN01012,B73_8DAS_shoot_MN01021,B73_8DAS_shoot_MN01022,B73_16DAP_embryo_MN01101,B73_16DAP_embryo_MN01102,B73_16DAP_endosperm_MN01091,B73_16DAP_endosperm_MN01092,,B73_R1_anther_MN01081,B73_R1_anther_MN01082,B73_R1_anther_MNA1081,B73_V11_base_MN01031,B73_V11_base_MN01032,B73_V11_middle_MN01041,B73_V11_middle_MN01042,B73_V11_middle_MN01043,B73_V11_tip_MN01051,B73_V11_tip_MN01052,B73_V18_ear_MN01071,B73_V18_ear_MN01072,B73_V18_tassel_MN01061,B73_V18_tassel_MN01062"
apptainer exec --bind ${RCAC_SCRATCH} ${BRAKER_SIF} braker.pl \
        --AUGUSTUS_CONFIG_PATH=${AUGUSTUS_CONFIG_PATH} \
        --GENEMARK_PATH=${GENEMARK_PATH} \
        --genome=${genome} \
        --rnaseq_sets_ids=${rnaseq_sets_ids} \
        --rnaseq_sets_dirs=${RCAC_SCRATCH}/rnaseq/ \
        --workingdir=${workdir} \
        --gff3 \
        --threads ${SLURM_CPUS_ON_NODE}
```

:::

:::{tab-item} ### Scenario 4

**Input genome with protein sequences**

Using the [orthodb-clades](https://github.com/tomasbruna/orthodb-clades) tool, we can download protein sequences for a specific clade. In this scenario, since we are using the Maize genome, we can download the `clade` specific `Viridiplantae.fa`  [OrthoDB v12](https://www.orthodb.org/) protein sets.

```bash
git clone git@github.com:tomasbruna/orthodb-clades.git
snakemake --cores ${SLURM_CPUS_ON_NODE} 
```

When this is done, you should see a folder named `clade` with `Viridiplantae.fa` in the `orthodb-clades` directory. We will use this as one of the input datasets for BRAKER3. The following command will run BRAKER3 with the input genome and protein sequences:

```bash
protein_sequences="${RCAC_SCRATCH}/braker/orthodb-clades/clade/Viridiplantae.fa"
apptainer exec --bind ${RCAC_SCRATCH} ${BRAKER_SIF} braker.pl \
        --AUGUSTUS_CONFIG_PATH=${AUGUSTUS_CONFIG_PATH} \
        --GENEMARK_PATH=${GENEMARK_PATH} \
        --species=Zm_$(date +"%Y%m%d").1a \
        --prot_seq=${proteins} \
        --genome=${genome} \
        --workingdir=${workdir} \
        --gff3 \
        --threads ${SLURM_CPUS_ON_NODE}
```

:::

:::{tab-item} Scenario 5

**Input genome with RNA-Seq data and protein sequences**

In this scenario, we will predict genes in a Maize genome using the input genome, RNA-Seq data (all libraries), and protein sequences. The following command will run BRAKER3 with the input genome, RNA-Seq data, and protein sequences:

```bash
apptainer exec --bind ${RCAC_SCRATCH} ${BRAKER_SIF} braker.pl \
        --AUGUSTUS_CONFIG_PATH=${AUGUSTUS_CONFIG_PATH} \
        --GENEMARK_PATH=${GENEMARK_PATH} \
        --species=Zm_$(date +"%Y%m%d").2b \
        --rnaseq_sets_ids=${rnaseq_sets_ids} \
        --rnaseq_sets_dirs=${RCAC_SCRATCH}/helixer/rnaseq/ \
        --prot_seq=${proteins} \
        --genome=${genome} \
        --workingdir=${workdir} \
        --gff3 \
        --threads ${SLURM_CPUS_ON_NODE}
```

:::

:::{tab-item} Scenario 6

**Input genome with pretrained species model**

This is similar to Scenario 1, but we will use a pretrained species model for Maize that comes with BRAKER3. It is recommended to use either pretrained model of your species or closely related species when available:

```bash
apptainer exec --bind /scratch/negishi/aseethar ${BRAKER_SIF} braker.pl \
        --AUGUSTUS_CONFIG_PATH=${AUGUSTUS_CONFIG_PATH} \
        --GENEMARK_PATH=${GENEMARK_PATH} \
        --genome=${genome} \
        --species=maize5 \
        --skipAllTraining \
        --workingdir=${workdir} \
        --gff3 \
        --threads ${SLURM_CPUS_ON_NODE}
```


:::

:::{tab-item} Scenario 7

**Input genome with Iso-Seq data and protein sequences**


In this final scenario, we will predict genes in a Maize genome using the input genome, Iso-Seq data, and protein sequences. The following command will run BRAKER3 with the input genome, Iso-Seq data, and protein sequences:

```bash
```

:::

:::{tab-item Scenario 8

xyz

:::

::::




::::{tab-set}

:::{tab-item} counts 


**Figure 6: Genes in each starta for the predictions (count)**

:::

:::{tab-item} percent  



**Figure 7: Genes in each starta for the predictions (percent)**

:::

::::