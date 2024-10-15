# Genome assembly using HiFi reads

A short guide to assemble genomes from long reads (PacBio HiFi) using [HiFiasm](https://github.com/chhylp123/hifiasm)


## 1.	Installation

Manually installing `hifiasm` is easy. The instructions as listed in the [GitHub repository](https://github.com/chhylp123/hifiasm) are as follows:

```bash
git clone https://github.com/chhylp123/hifiasm
cd hifiasm && make
```

But in case if you have any problems, you can use the Singularity container. First, download the recipe file from the GitHub repository

```bash
wget blob:https://github.rcac.purdue.edu/fff0ae4d-5967-42e3-95b5-6ff9f1c5beb0 -O Singularity.hifiasm
singularity build hifiasm.sif Singularity.hifiasm
```


## 2.	Download HiFi reads

To run HiFiasm, we need HiFi reads. We will download sample datasets from [PacBio website](https://www.pacb.com/connect/datasets/). For this specific tutorial, we will use the [PacBio HiFi data for maize B73 genome](https://downloads.pacbcloud.com/public/revio/2023Q1/maize-B73-rep1/). 

```bash
wget https://downloads.pacbcloud.com/public/revio/2023Q1/maize-B73-rep1/m84006_221229_002525_s1.hifi_reads.bam
```

First step is to convert the HiFi reads in bam format to fasta (or fastq) format. We can use `samtools` for this. 

```{warning}
Be sure to run the computationally intensive steps on a compute node, not the login node. You can request an interactive session using `salloc` command. 
```


```bash
ml biocontainers
ml samtools
samtools fasta \
    -threads ${SLURM_CPUS_ON_NODE} \
    m84006_221229_002525_s1.hifi_reads.bam > maize-hifi.fasta
```

Before proceeding, lets quickly check our input file (read depth and other stats). 

```bash
ml biocontainers
ml seqkit
seqkit stats  \
    --all \
    --threads ${SLURM_CPUS_ON_NODE} \
    --out-file summary-stats.out \
    maize-hifi.fasta
```

This will generate a file `summary-stats.out` with the statistics of the input file. 


| **Property**  | **Value**         |
|---------------|-------------------|
| file          | maize-hifi.fasta  |
| format        | FASTA             |
| type          | DNA               |
| num_seqs      | 5,142,754         |
| sum_len       | 70,099,174,236    |
| min_len       | 94                |
| avg_len       | 13,630.70         |
| max_len       | 55,776            |
| Q1            | 10,374            |
| Q2            | 13,501            |
| Q3            | 16,921            |
| sum_gap       | 0                 |
| N50           | 15,265            |
| Q20(%)        | 0                 |
| Q30(%)        | 0                 |
| GC(%)         | 45.62             |


So assuming 2,400Mb genome size for maize, our sequencing depth will be 70,099,174,236 bp / 2,400,000,000  = 29.2x.
Ohter stats reported are the number of sequences, minimum, maximum, and average length of the sequences, N50, and GC content. They all reaffirm the quality of the input file.


```{note}
Running `fastqc` on the PacBio HiFi reads does not provide the required information as the reads are too long and the encoded quality scores are not the same as in short reads.  
```

## 3.	Run HiFiasm

Now we can run HiFiasm on the fasta file. Be sure to check the [documentation](https://hifiasm.readthedocs.io/en/latest/parameter-reference.html#parameter-reference) for all the options available.

First, we will run `hifiasm` with default suggested parameters.


```bash
singularity exec hifiasm.sif hifiasm \
    -t ${SLURM_CPUS_ON_NODE} \
    -o maize_B73_hifi.asm \
    maize-hifi.fasta
```

This will generate partially phased contigs with `maize_B73_hifi.asm.bp.hap*.p_ctg.gfa`. This pair of files can be thought to represent the two haplotypes in a diploid genome, though with occasional switch errors. The frequency of switches is determined by the heterozygosity of the input sample. Since B73 is an inbred, we could expect most of the genome represented in the primary contigs to `maize_B73_hifi.asm.bp.p_ctg.gfa`.