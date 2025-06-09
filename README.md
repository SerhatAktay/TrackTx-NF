# TrackTx-NF

**TrackTx-NF** is a modular, reproducible Nextflow pipeline for functional genomic mapping from nascent RNA sequencing data (e.g. PRO-seq, GRO-seq), supporting multi-species experiments and complex designs.

It provides fully strand-specific normalized signal tracks, implements a custom divergent transcription detection algorithm, and outputs an interactive HTML report summarizing the results.

A built-in **HTML config interface** lets users easily generate a Nextflow config and launch the pipeline via a simple script.

---

## Table of Contents

1. [Installation](#installation)
2. [Dependencies](#dependencies)
3. [Usage](#usage)
4. [Pipeline Overview](#pipeline-overview)
5. [Output](#output)
6. [Example Data](#example-data)
7. [Citation](#citation)
8. [License](#license)

---

## Installation

### 1️⃣ Install Nextflow

```bash
curl -s https://get.nextflow.io | bash
mv nextflow ~/bin/ # or other directory in $PATH
nextflow -version
```

Docs: https://www.nextflow.io/docs/latest/getstarted.html

---

### 2️⃣ Install Mamba

```bash
conda install mamba -n base -c conda-forge
```

---

### 3️⃣ Set up pipeline environment

```bash
mamba env create -f environment.yml
conda activate tracktx
```

---

## Dependencies

### External dependencies (user must install manually):

| Dependency | Notes |
|------------|-------|
| Nextflow   | Workflow engine |
| Mamba / Conda | To create pipeline env |
| Bash >= 4  | For launcher script |
| Web browser | To use HTML config tool |

---

### Installed via pipeline environment (environment.yml):

```yaml
name: tracktx
channels:
  - conda-forge
  - bioconda
  - defaults
dependencies:
  - nextflow
  - fastqc
  - multiqc
  - seqkit
  - bowtie2
  - samtools
  - bedtools
  - biopython
  - pandas
  - numpy
  - pybedtools
  - pysam
  - pybigwig
  - wget
  - bc
  - coreutils
  - umi_tools
  - cutadapt
```

---

## Usage

### Launch the HTML config tool + pipeline

```bash
bash launch_TrackTx.sh
```

- This opens the **HTML interface** to generate your Nextflow config.
- The script then **automatically launches the pipeline** with your selected parameters.

---

### Manual run (advanced users)

```bash
nextflow run main.nf -c my_config.config --input /path/to/fastqs --genome hg38 --outdir results
```

---

## Pipeline Overview

### Input

- FASTQ files (SE or PE), or SRR accessions (via `nf-core/fetchngs` / SRA download module)
- Optional: spike-in genome
- Optional: UMIs/barcodes

---

### Major steps

✅ **QC**  
- FastQC  
- MultiQC  

✅ **UMI / adapter trimming**  
- `umi_tools`  
- `cutadapt`  

✅ **Alignment**  
- Bowtie2 (end-to-end)  
- Optional spike-in genome  

✅ **Track generation**  
- BedGraph  
- BigWig (strand-specific, 3' end counting)  

✅ **Normalization**  
- CPM  
- RPKM  
- Optional: spike-in-based normalization  

✅ **Divergent transcription detection**  
- Custom algorithm (replaces dREG/groHMM)  

✅ **Reporting**  
- Final HTML report (interactive)  

---

## Output

```
results/
├── fastqc/
├── trimmed_fastq/
├── alignments/
│   ├── bam/
│   ├── bam_sorted/
├── tracks/
│   ├── bedgraph/
│   ├── bigwig/
├── divergent_transcription/
├── normalization/
├── reports/
│   ├── multiqc.html
│   ├── TrackTx_final_report.html
└── logs/
```

---

## Example Data

Test with public data, e.g.:

- [GEO: GSE171042](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE171042) (example PRO-seq dataset)

---

## Citation

If you use **TrackTx**, please cite:

> *Your forthcoming publication / GitHub / Zenodo DOI*

---

## License

MIT License

---

## Notes

- Species supported: human (hg38), mouse (mm10), drosophila, dog, arabidopsis  
- Interactive **HTML config + launcher script** simplifies use  
- Pure **Nextflow + Conda** (no Docker/Singularity required)  
- Multi-core local execution supported  

---
