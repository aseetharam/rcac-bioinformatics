# GeMoMa to merge annotations


## 🛠️ **Prerequisites**

:::{card}
**📁 Input files**
^^^
- Target genome assembly (`fasta` format) - we will use B73.v5 genome
- _ab initio_ predictions (`.gff3` format) - we will use Helixer output
- Homology predictions (`.gff3` format) - Sorghum and B97 annotations 

:::

:::{card}
**💻 Software**
^^^
- GeMoMa (version ≥ 1.9 recommended)
- Dependencies: `java`, `tblastn`, `star` and `samtools`
:::



## 🧬 GeMoMa overview

[Gene Model Mapper (GeMoMa)](https://www.jstacs.de/index.php/GeMoMa) is a homology-based gene prediction tool that transfers protein-coding gene models from one or more reference genomes to a target genome. It can incorporate RNA-seq evidence, filter predictions using customizable criteria, and merge multiple annotations—including ab initio and transcriptome-based predictions—into a unified gene set.


## 🔍 Use case: Merge annotations 

Helixer is a deep learning-based predictor that provides accurate gene predictions from genome sequence alone. However, it does not predict alternative isoforms and tends to **collapse all transcript variants into a single flattened gene model**, especially in regions with overlapping exons or alternative splicing.

Homology predictions can be used to recover these missing isoforms and provide additional context for gene structure. **GeMoMa excels at this task** by leveraging evolutionary conservation across related species, allowing it to transfer annotations from well-annotated genomes to the target genome.

By merging Helixer predictions with GeMoMa:

* 🧠 You retain **Helixer's high confidence gene boundaries** and structure for primary transcripts.
* 🔁 You recover **missing isoforms** and splice variants that Helixer collapses.
* 🔍 You gain **orthology-supported annotation refinement** using multiple maize lines and related species like *Sorghum bicolor*.
* 🧬 You generate a **more complete and biologically realistic gene set**, even without long-read or isoform-resolving RNA-seq data.

This hybrid strategy takes advantage of both **machine learning accuracy** and **evolutionary conservation** to compensate for the limitations of any single method.


## 🗂️ Input preparation

### 📄 Reference genome

### 🧬 Homologous genomes and annotations

### 🧪 Evidence tracks (BAM, Helixer, BRAKER, Mikado)

## 🚀 Running GeMoMa

### 🔧 Environment setup and dependencies

### 🧰 Description of script and its components

### ⚙️ Detailed explanation of key parameters

## 📈 Interpreting results

### 📂 Output directory structure

### 📊 AnnotationFinalizer and GAF filtering

### 🔎 Quality assessment

## 🧵 Post-processing and integration

### 🧬 Merging with existing annotations

### 🧪 Resolving isoforms from collapsed Helixer models

### 📤 Exporting and converting formats

## 💡 Tips and troubleshooting

## 📚 References and resources





## Running GeMoMa

In this example, we will merge _ab initio_ predictions of B73 (Maize reference) from Helixer with the homology predictions from another inbred (Maize B97) and Sorghum.

Download input files:

```bash
wget https://download.maizegdb.org/Zm-B73-REFERENCE-NAM-5.0/Zm-B73-REFERENCE-NAM-5.0.fa.gz
wget 
```

Here’s a refined set of section headings for your tutorial, structured to guide users logically through context, setup, execution, and interpretation—while aligning with your theme of merging Helixer (collapsed isoforms) with homology-based predictions using GeMoMa:

---
