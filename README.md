# ARGprofiler Execution 

## This repository documents how to run ARGprofiler locally using paired-end metagenomic reads.

This is execution workflow for ARGprofiler


For detailed methodology, output interpretation, and theoretical background, refer to the official repository: 
https://github.com/genomicepidemiology/ARGprofiler

---

This workflow:
- Organizes raw FASTQ files
- Generates required input.json
- Runs ARGprofiler via Snakemake
- Produces resistance profiling, taxonomic, and QC outputs
