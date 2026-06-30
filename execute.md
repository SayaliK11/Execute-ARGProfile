### ● Input Requirements

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

### ● Setup & Installation

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

ln -s /mnt/myhdd/project/raw_data/MT_2024_0704_R1.fastq.gz local_reads/MT0704_1.fastq.gz
ln -s /mnt/myhdd/project/raw_data/MT_2024_0704_R2.fastq.gz local_reads/MT0704_2.fastq.gz

ln -s /mnt/myhdd/project/raw_data/MT_2024_0705_R1.fastq.gz local_reads/MT0705_1.fastq.gz
ln -s /mnt/myhdd/project/raw_data/MT_2024_0705_R2.fastq.gz local_reads/MT0705_2.fastq.gz

ln -s /mnt/myhdd/project/raw_data/MT_2024_0706_R1.fastq.gz local_reads/MT0706_1.fastq.gz
ln -s /mnt/myhdd/project/raw_data/MT_2024_0706_R2.fastq.gz local_reads/MT0706_2.fastq.gz

ln -s /mnt/myhdd/project/raw_data/MT_2024_0707_R1.fastq.gz local_reads/MT0707_1.fastq.gz
ln -s /mnt/myhdd/project/raw_data/MT_2024_0707_R2.fastq.gz local_reads/MT0707_2.fastq.gz

ln -s /mnt/myhdd/project/raw_data/MT_2024_0708_R1.fastq.gz local_reads/MT0708_1.fastq.gz
ln -s /mnt/myhdd/project/raw_data/MT_2024_0708_R2.fastq.gz local_reads/MT0708_2.fastq.gz
```

### Step 2: Generate `input.json`
```
cat > input.json <<EOF
{
  "MT0704": { "type": "PAIRED" },
  "MT0705": { "type": "PAIRED" },
  "MT0706": { "type": "PAIRED" },
  "MT0707": { "type": "PAIRED" },
  "MT0708": { "type": "PAIRED" }
}
EOF

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

### ● Output Structure

ARGprofiler produces **four main result directories**:

| Folder        | Biological Question |
|---------------|--------------------|
| `kma_panres`  | Which ARGs are present? *(Resistance gene identification)* |
| `ARG_extender`| What are the flanking regions around ARGs? *(Genomic context)* |
| `kma_mOTUs`   | Which taxa carry ARGs? *(Taxonomic assignment)* |
| `mash_sketch` | How similar are samples? *(QC / distance estimation)* |

---

### Notes
- Sample names are derived from FASTQ filenames (e.g., `MT_2024_704` /`CL_2026_00365`)
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

