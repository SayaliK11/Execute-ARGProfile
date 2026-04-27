# ARGprofiler Execution 

## This repository documents how to run ARGprofiler locally using paired-end metagenomic reads.

This is execution workflow for ARGprofiler


For detailed methodology, output interpretation, and theoretical background, refer to the official repository: 
https://github.com/genomicepidemiology/ARGprofiler

---

### Overview

This workflow:
- Organizes raw FASTQ files
- Generates required input.json
- Runs ARGprofiler via Snakemake
- Produces resistance profiling, taxonomic, and QC outputs

---

### Input Requirements

Place your raw Illumina paired-end reads in a directory:
```
/path/to/raw_data/
├── SAMPLE_1_R1.fastq.gz
├── SAMPLE_1_R2.fastq.gz
├── SAMPLE_2_R1.fastq.gz
├── SAMPLE_2_R2.fastq.gz
```
Expected naming patters:
```
*_R1_001.fastq.gz
*_R2_001.fastq.gz
```

---

### Setup & Installation

Go to project directory
```
cd /mnt/myssd/PROJECT
```

Install snakemake
```
conda create -n snakemake -c conda-forge -c bioconda snakemake -y
conda activate snakemake
snakemake --version
```

Activate conda environment
```
conda activate snakemake 
```

Remove previous run (if any)
```
rm -rf ARGprofiler
```

Clone ARGprofiler
```
git clone https://github.com/genomicepidemiology/ARGprofiler.git
cd ARGprofiler
```

---

### Step 1: Prepare Input Reads

Create a local directory and link all FASTQ files:
```
mkdir local_reads

RAW_DIR="/media/fmr/Backup Plus/WWS-AMR-NBG(2026)/raw_data"

for f in "$RAW_DIR"/*.fastq.gz; do
    ln -s "$f" "local_reads/$(basename "$f")"
done
```

### Step 2: Generate `input.json`
Automatically create input file for all samples:
```
RAW_DIR="/mnt/myssd/PROJECT/raw_data"

echo "{" > input.json
first=1

for r1 in "$RAW_DIR"/*_R1_001.fastq.gz; do
    base=$(basename "$r1")
    sample=$(echo "$base" | cut -d'_' -f1-3)

    if [ $first -eq 0 ]; then
        echo "," >> input.json
    fi

    echo "  \"$sample\": { \"type\": \"PAIRED\" }" >> input.json
    first=0
done

echo "}" >> input.json
```

### Step 3: Dry Run
```
snakemake \
  --profile profile_argprofiler \
  --use-conda \
  --cores 8 \
  -n
```

### Step 4: Run Pipeline
```
snakemake \
  --profile profile_argprofiler \
  --use-conda \
  --cores 8 \
  --rerun-incomplete
```

---

### Output Structure

ARGprofiler produces **four main result directories**:

| Folder        | Biological Question |
|---------------|--------------------|
| `kma_panres`  | Which ARGs are present? *(Resistance gene identification)* |
| `ARG_extender`| What are the flanking regions around ARGs? *(Genomic context)* |
| `kma_mOTUs`   | Which taxa carry ARGs? *(Taxonomic assignment)* |
| `mash_sketch` | How similar are samples? *(QC / distance estimation)* |

---

### Notes
- Sample names are automatically derived from FASTQ filenames (e.g., `CL_2026_00365`)
- Ensure consistent pairing (`R1` and `R2`)
- Uses conda environments internally via `--use-conda`


---

### Reference

Please refer to the official documentation for:
- Detailed methodology
- Parameter explanations
- Output interpretation

---

This repository provides a practical execution workflow for running ARGprofiler locally ,not a replacement for the official documentation.

