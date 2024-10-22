# Gene prediction using Helixer

Helixer is a deep learning-based gene prediction tool that uses a convolutional neural network (CNN) to predict genes in eukaryotic genomes. Helixer is trained on a wide range of eukaryotic genomes and can predict genes in both plant and animal genomes. Helixer can predict genes wihtout any extrinisic information such as RNA-seq data or homology information, purely based on the sequence of the genome.

## 1. Installation

Helixer is available as a Singularity container. You can pull the container using the following command:

```bash
singularity pull docker://gglyptodon/helixer-docker:helixer_v0.3.3_cuda_11.8.0-cudnn8
```

This will create `helixer_v0.3.3_cuda_11.8.0-cudnn8.sif` file in the current directory. We will rename this file to `helixer.sif` for simplicity.


## 2. Downloading trained models


Helixer requires a trained model to predict genes. With the included script `fetch_helixer_models.py` you can download models for specific lineages. Currently, models are available for the following lineages:

- `land_plant`
- `vertebrate`
- `invertibrate`
- `fungi`

There are instructions to [train your own models](https://github.com/weberlab-hhu/Helixer/blob/main/docs/training.md) as well as [fine tune](https://github.com/weberlab-hhu/Helixer/blob/main/docs/fine_tuning.md) the existing models using the RNAseq data for the species of interest. But for this tutorial, we will just use the pre-trained models.



```bash
singularity exec helixer.sif fetch_helixer_models.py --all
```

This will download all lineage models in the `models` directory. You can also download models for specific lineages using the `--lineage` option.

The files downloaded will be in the following structure:

```
└── models
    ├── fungi
    │   └── fungi_v0.3_a_0100.h5
    ├── invertebrate
    │   └── invertebrate_v0.3_m_0100.h5
    ├── land_plant
    │   └── land_plant_v0.3_a_0080.h5
    ├── model_list.csv
    └── vertebrate
        └── vertebrate_v0.3_m_0080.h5
```


## 3. Running Helixer

Helixer requires GPU for prediction. For running Helixer, you need to request a GPU node. You will also need the genome sequence in fasta format. For this tutorial, we will use Maize genome (_Zea mays_ subsp. _mays_), and use the `land_plant` model to predict genes.


**Donwload the genome sequence:**

```bash
wget https://download.maizegdb.org/Zm-B73-REFERENCE-NAM-5.0/Zm-B73-REFERENCE-NAM-5.0.fa.gz
gunzip Zm-B73-REFERENCE-NAM-5.0.fa.gz
```

**Run Helixer:**

```bash
genome=Zm-B73-REFERENCE-NAM-5.0.fa
species="Zea mays subsp. mays"
output=B73-helixer.gff
singularity exec \
    --bind ${RCAC_SCRATCH} \
    --nv helixer.sif Helixer.py \
    --lineage land_plant \
    --fasta-path ${genome} \
    --species ${species} \
    --gff-output-path ${output}
```

```{note}
In `A100` GPU nodes, the run time for predicting genes in Maize genome takes around 1.5 hours (01:36:46), with 6 CPUs `--ntasks-per-node=6`. The memory usage was 5.51 GB. 
```

The GFF format output had 41,923 genes predicted using Helixer. You can view the various features in the `gff` file using the following command:

```bash
grep -v "^#" B73-helixer.gff | cut -f 3 | sort | uniq -c
```

outputs:

```
2,19,488  CDS
2,41,560  exon
  52,607  five_prime_UTR
  41,923  gene
  41,923  mRNA
  52,298  three_prime_UTR
```

```{warning}
As you may have noticed, the number of `mRNA` and `gene` features are the same. This is because isoforms aren't predicted by Helixer and you only have one transcript per gene. 
```



## 4. Comparing and benchmarking

We will download the reference annotations for Maize genome from [MaizeGDB](https://www.maizegdb.org/) and compare the predicted genes with the reference annotations.

```bash
wget https://download.maizegdb.org/Zm-B73-REFERENCE-NAM-5.0/Zm-B73-REFERENCE-NAM-5.0.gff3.gz
gunzip Zm-B73-REFERENCE-NAM-5.0.gff3.gz
```

### A. BUSCO profiling

We will use BUSCO to profile the predicted genes and reference annotations.

```bash
ml biocontainers
ml busco
genome=Zm-B73-REFERENCE-NAM-5.0.fa
