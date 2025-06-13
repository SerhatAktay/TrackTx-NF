# TrackTx :musical_note:
*High‑resolution Pol II transcription tracking pipeline*

---

## 1  Overview
**TrackTx** is an open‑source, container‑ready **Nextflow DSL2** pipeline for high‑resolution mapping of **RNA polymerase II** transcription dynamics from nascent‑RNA sequencing assays such as **PRO‑seq, TT‑seq, GRO‑seq** and **NET‑seq**. Starting from raw FASTQ files, TrackTx performs a complete, reproducible analysis and returns publication‑ready signal tracks, peak calls, and quality metrics with a single command.

> **Why TrackTx?**  
> • **High resolution** – single‑nucleotide bigWigs allow precise pause‑site and transcription‑unit mapping.  
> • **Assay‑agnostic** – autodetects protocol strandness, spike‑in multiplexing, and supports 3′‑barcoded libraries.  
> • **Rigorous QC** – MultiQC dashboard flags read‑through, insert‑size bias, rRNA contamination, and more.  
> • **Reproducible** – locked tool versions via Docker or Micromamba; provenance captured in Tower or trace files.  
> • **Scale‑adaptive** – runs in minutes on a laptop test dataset yet scales to 1000‑sample cohorts on HPC or cloud without config changes.

### What the pipeline does
1. **Quality control** with FastQC to assess raw libraries.  
2. **Adaptor & quality trimming** using fastp.  
3. **Strand‑aware alignment** (STAR by default, BWA‑MEM optional) to a user‑supplied genome plus optional spike‑in.  
4. **Deduplication & UMI filtering** with Je‑Suite for unique signal.  
5. **Signal track generation** with deepTools `bamCoverage` producing strand‑specific and merged bigWigs.  
6. **Peak/feature calling** via groHMM or dREG to identify transcribed units, promoters, and enhancers.  
7. **Comprehensive reporting** – MultiQC HTML plus JSON summary for downstream dashboards.

### Key usability features
- **HTML Config Generator** – ships as `TrackTx_config_generator.html`; completes the entire input schema via a friendly form and exports a ready‑to‑run `samplesheet.csv` & `params.yaml`.  
- **Zero‑touch resources** – TrackTx introspects host CPU/RAM and auto‑assigns sensible defaults, yet parameters remain fully overridable.  
- **Live TUI monitor** – `nf-monitor.sh` shows real‑time progress, resource usage, and I/O without installing Python or Node.  
- **Tower integration** – add `-with-tower` and watch runs live, retry failed tasks, or share dashboards with collaborators.

---

## 2  Requirements & installation
| Component | Tested version | Install hint |
|-----------|---------------|--------------|
| **Java** | ≥ 11 | Ubuntu: `sudo apt-get install openjdk-11-jdk` • **macOS (Intel & Apple Silicon)**: `brew install openjdk@11` • Windows: see cheat‑sheet below |
| **Nextflow** | 24.04 (DSL2) | `curl -s https://get.nextflow.io | bash` (make it executable & put in $PATH) |
| **Docker Desktop / Engine** | ≥ 24 | https://docs.docker.com/engine/install/ |
| **Micromamba / Conda** (optional) |  | `curl micro.mamba.pm/install.sh | bash` then: `micromamba create -n tracktx -f envs/environment.yml` |

> **Tip — choose one runtime:** Docker *or* Conda. Docker offers maximal reproducibility; Conda keeps everything inside $HOME for clusters where Docker is unavailable.

### 2.1  OS‑specific step‑by‑step
*(see README source for detailed copy‑paste blocks for Linux, macOS Intel, macOS ARM, Windows WSL2 & native)*

---

## 3  Quick‑start
1. **Clone** the repo:
   ```bash
   git clone https://github.com/your‑org/TrackTx.git && cd TrackTx
   ```  
2. **Generate inputs** with the **HTML Config Generator**:
   ```bash
   open TrackTx_config_generator.html   # double‑click on Windows/macOS or xdg‑open on Linux
   ```  
   Fill in the form ▶ **Download** ▶ copy the resulting `samplesheet.csv` *and* `params.yaml` into the pipeline root.
3. **Run** the pipeline (Docker profile shown):
   ```bash
   nextflow run main.nf      -entry TrackTx      -params-file params.yaml      -profile docker      -with-tower      -with-trace
   ```  
   Replace `docker` with `conda` if you created the micromamba environment.
4. **(Optional) Live monitor** in a second terminal:
   ```bash
   ./nf-monitor.sh    # opens a live TUI; Ctrl‑C to exit
   ```

---

## 4  Inputs in detail
| File | Produced by generator | Purpose |
|------|-----------------------|---------|
| `samplesheet.csv` | ✔ | Lists samples, conditions, replicates, paths/URLs to FASTQ files. |
| `params.yaml` | ✔ | Reference genome, aligner, peak caller, spike‑in options, etc. |

---

## 5  Running with Nextflow Tower
1. Sign up at https://tower.nf → **Generate access token**.  
2. Export token:
   ```bash
   export NXF_TOWER_ACCESS_TOKEN="<token>"
   ```  
3. Add `-with-tower` to run command – CLI prints dashboard URL.

---

## 6  Live monitoring (`nf-monitor.sh`)
```bash
./nf-monitor.sh --refresh 2   # update every 2 s
```
Shows per‑process CPU, RAM, elapsed time, and I/O; exits automatically when the pipeline finishes.

---

## 7  Workflow outline
1. QC – FastQC, MultiQC summary  
2. Trim – fastp adaptor/quality trimming  
3. Align – STAR or BWA  
4. Deduplicate – UMI‑aware (optional)  
5. BigWig generation – deepTools bamCoverage  
6. Peak calling – groHMM or dREG  
7. Reporting – MultiQC + JSON summary  

---

## 8  Output structure
```
results/
 ├─ fastqc/
 ├─ bam/
 ├─ bw/
 ├─ peaks/
 ├─ multiqc_report.html
 └─ tower/          # JSON export from Tower API (if enabled)
```

---

## 9  Updating the pipeline
| Purpose | Command |
|---------|---------|
| Pull latest code | `git pull` |
| Switch to a tagged release | `git checkout tags/v0.2.0` |
| Nextflow pull (immutable)  | `nextflow pull your‑org/TrackTx` |

---

## 10  License & citation
TrackTx is released under a **custom permissive license inspired by Parallels CLI**.

- Free for academic & non‑commercial use.
- Cite the forthcoming TrackTx paper (pre‑print DOI will be added) in any publication using results from this pipeline.
- Contact authors for commercial licensing.

Full text in `LICENSE`.

---

## 11  Contributing & support
Please open GitHub issues or pull requests; follow **conventional commits** (`feat: …`, `fix: …`).  
