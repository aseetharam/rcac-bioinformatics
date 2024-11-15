# Gene prediction using BRAKER3

BRAKER3 is a pipeline that combines GeneMark-ET and AUGUSTUS to predict genes in eukaryotic genomes. This pipeline is particularly useful for annotating newly sequenced genomes. The flexibility of BRAKER3 allows users to provide various input datasets for improving gene prediction accuracy. In this example, we will use various scenarios to predict genes in a Maize genome using BRAKER3. Following are the scenarios we will cover:

| Input Type                | Case 1  | Case 2 | Case 3 | Case 4 | Case 5 | Case 6 | Case 7 | Case 8 |
|---------------------------|---------|--------|--------|--------|--------|--------|--------|--------|
| Genome                    | ✔️      | ✔️     | ✔️     | ✔️     | ✔️     | ✔️     | ✔️     | ✔️   |
| RNA-Seq                   | ❌      | ✔️<sup>*</sup>     | ✔️     | ❌     | ✔️     | ❌     | ❌     | ✔️   |
| Iso-Seq                   | ❌      | ❌     | ❌     | ❌     | ❌     | ❌     | ✔️     | ✔️   |
| Conserved proteins        | ❌      | ❌     | ❌     | ✔️     | ✔️     | ❌     | ✔️     | ❌   |
| Pretrained species model  | ❌      | ❌     | ❌     | ❌     | ❌     | ✔️     | ❌     | ❌   |

<sup>*</sup> minimal RNA-Seq data (one library/one tissue)


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

**With genome only (no external evidence)**

| Input                     | Type                            |
|---------------------------|---------------------------------|
| Genome                    | B73.v5 (softmasked)             |
| RNA-Seq data              | None                            |
| Protein sequences         | None                            |
| Long-read data            | None                            |
| Pretrained species model  | None                            |

```bash
mkdir -p ${workdir}
apptainer exec --bind ${RCAC_SCRATCH} ${BRAKER_SIF} braker.pl \
        --AUGUSTUS_CONFIG_PATH=${AUGUSTUS_CONFIG_PATH} \
        --GENEMARK_PATH=${GENEMARK_PATH} \
        --genome=${genome} \
        --esmode \
        --species=Zm_$(date +"%Y%m%d").c1 \
        --workingdir=${workdir} \
        --gff3 \
        --threads ${SLURM_CPUS_ON_NODE}
```

:::

:::{tab-item} Case 2 

**with minimal RNA-Seq data (one library/one tissue)**

| Input                     | Type                            |
|---------------------------|---------------------------------|
| Genome                    | B73.v5 (softmasked)             |
| RNA-Seq data              | RNAseq (single library)         |
| Protein sequences         | None                            |
| Long-read data            | None                            |
| Pretrained species model  | None                            |


```bash
mkdir -p ${workdir}
apptainer exec --bind ${RCAC_SCRATCH} ${BRAKER_SIF} braker.pl \
        --AUGUSTUS_CONFIG_PATH=${AUGUSTUS_CONFIG_PATH} \
        --GENEMARK_PATH=${GENEMARK_PATH} \
        --genome=${genome} \
        --rnaseq_sets_ids=B73_V11_middle_MN01042 \
        --rnaseq_sets_dirs=${RCAC_SCRATCH}/RNAseq/ \
        --species=Zm_$(date +"%Y%m%d").c2 \
        --workingdir=${workdir} \
        --gff3 \
        --threads ${SLURM_CPUS_ON_NODE}
```


:::

:::{tab-item} Case 3

**with exhaustive RNA-Seq data (11 tissues)**

| Input                     | Type                            |
|---------------------------|---------------------------------|
| Genome                    | B73.v5 (softmasked)             |
| RNA-Seq data              | RNAseq (11 tissues)             |
| Protein sequences         | None                            |
| Long-read data            | None                            |
| Pretrained species model  | None                            |

```bash
rna_seq_sets_ids="B73_8DAS_root_MN01011,B73_8DAS_root_MN01012,B73_8DAS_shoot_MN01021,B73_8DAS_shoot_MN01022,B73_16DAP_embryo_MN01101,B73_16DAP_embryo_MN01102,B73_16DAP_endosperm_MN01091,B73_16DAP_endosperm_MN01092,,B73_R1_anther_MN01081,B73_R1_anther_MN01082,B73_R1_anther_MNA1081,B73_V11_base_MN01031,B73_V11_base_MN01032,B73_V11_middle_MN01041,B73_V11_middle_MN01042,B73_V11_middle_MN01043,B73_V11_tip_MN01051,B73_V11_tip_MN01052,B73_V18_ear_MN01071,B73_V18_ear_MN01072,B73_V18_tassel_MN01061,B73_V18_tassel_MN01062"
mkdir -p ${workdir}
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

:::{tab-item} Case 4 

**with conserved protein sequences**

| Input                     | Type                            |
|---------------------------|---------------------------------|
| Genome                    | B73.v5 (softmasked)             |
| RNA-Seq data              | None                            |
| Protein sequences         | Viridiplantae protein sequences |
| Long-read data            | None                            |
| Pretrained species model  | None                            |

Using the [orthodb-clades](https://github.com/tomasbruna/orthodb-clades) tool, we can download protein sequences for a specific clade. In this scenario, since we are using the Maize genome, we can download the `clade` specific `Viridiplantae.fa`  [OrthoDB v12](https://www.orthodb.org/) protein sets.

```bash
git clone git@github.com:tomasbruna/orthodb-clades.git
ml biocontainers
ml snakemake
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

:::{tab-item} Case 5 

**with RNA-Seq and conserved protein sequences**

| Input                     | Type                            |
|---------------------------|---------------------------------|
| Genome                    | B73.v5 (softmasked)             |
| RNA-Seq data              | RNAseq (11 tissues)             |
| Protein sequences         | Viridiplantae protein sequences |
| Long-read data            | None                            |
| Pretrained species model  | None                            |

```bash
mkdir -p ${workdir}
apptainer exec --bind ${RCAC_SCRATCH} ${BRAKER_SIF} braker.pl \
        --AUGUSTUS_CONFIG_PATH=${AUGUSTUS_CONFIG_PATH} \
        --GENEMARK_PATH=${GENEMARK_PATH} \
        --species=Zm_$(date +"%Y%m%d").c5 \
        --rnaseq_sets_ids=${rnaseq_sets_ids} \
        --rnaseq_sets_dirs=${RCAC_SCRATCH}/rnaseq/ \
        --prot_seq=${proteins} \
        --genome=${genome} \
        --workingdir=${workdir} \
        --gff3 \
        --threads ${SLURM_CPUS_ON_NODE}
```

:::

:::{tab-item} Case 6 

**with pretrained species model ("Maize")**

| Input                     | Type                            |
|---------------------------|---------------------------------|
| Genome                    | B73.v5 (softmasked)             |
| RNA-Seq data              | None                            |
| Protein sequences         | None                            |
| Long-read data            | None                            |
| Pretrained species model  | "Maize"                         |

```bash
mkdir -p ${workdir}
apptainer exec --bind ${RCAC_SCRATCH} ${BRAKER_SIF} braker.pl \
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

:::{tab-item} Case 7 

**with Iso-Seq and conserved protein sequences**

| Input                     | Type                            |
|---------------------------|---------------------------------|
| Genome                    | B73.v5 (softmasked)             |
| RNA-Seq data              | None                            |
| Protein sequences         | Viridiplantae protein sequences |
| Long-read data            | Iso-Seq data                    |
| Pretrained species model  | None                            |

For IsoSeq data, we need to provide the sorted BAM file as input. The data was downloaded from the [ENA database](https://www.pacb.com)


```bash
wget ftp://ftp.sra.ebi.ac.uk/vol1/fastq/ERR326/004/ERR3261694/ERR3261692_subreads.fastq.gz
wget ftp://ftp.sra.ebi.ac.uk/vol1/fastq/ERR326/004/ERR3261694/ERR3261693_subreads.fastq.gz
wget ftp://ftp.sra.ebi.ac.uk/vol1/fastq/ERR326/004/ERR3261694/ERR3261694_subreads.fastq.gz
cat ERR3261692_subreads.fastq.gz \
        ERR3261693_subreads.fastq.gz \
        ERR3261694_subreads.fastq.gz > maize_isoseq.fastq.gz
genome="${RCAC_SCRATCH/Zm-B73-REFERENCE-NAM-5.0_softmasked.fa}"
ml biocontainers
ml minimap2
ml samtools
threads=${SLURM_CPUS_ON_NODE}
minimap2 \
        -t${threads} \
        -ax splice:hq \
        -uf ${genome} \
        maize_isoseq.fastq.gz > isoseq.sam
samtools view \
        -bS \
        --threads ${threads} \
        isoseq.sam -o isoseq.bam
samtools sort \
        --threads ${threads} \
        -o isoseq_sorted.bam isoseq.bam
```


```bash
isoseq="/scratch/negishi/aseethar/isoseq/isoseq_sorted.bam"
apptainer exec --bind /scratch/negishi/aseethar ${BRAKER_SIF} braker.pl \
        --AUGUSTUS_CONFIG_PATH=${AUGUSTUS_CONFIG_PATH} \
        --GENEMARK_PATH=${GENEMARK_PATH} \
        --species=Zm_$(date +"%Y%m%d").3 \
        --prot_seq=${proteins} \
        –-bam=${isoseq} \
        --genome=${genome} \
        --workingdir=${workdir} \
        --gff3 \
        --threads ${SLURM_CPUS_ON_NODE}
```

:::

:::{tab-item} Case 8

**with Iso-Seq, RNA-Seq and conserved protein sequences**

| Input                     | Type                            |
|---------------------------|---------------------------------|
| Genome                    | B73.v5 (softmasked)             |
| RNA-Seq data              | RNAseq (11 tissues)             |
| Protein sequences         | Viridiplantae protein sequences |
| Long-read data            | Iso-Seq data                    |
| Pretrained species model  | None                            |

```bash
tbd
```
:::

::::