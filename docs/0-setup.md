# Activate your environment
```bash
conda activate genomics
```
# Create main project directory
```bash
mkdir ~/chromosome_x
cd ~/chromosome_x
```
# Create subdirectories for each analysis step
```bash
mkdir subsampled_data
mkdir trimmed_data
mkdir references
mkdir -p qc_reports/fastqc_raw qc_reports/fastqc_trimmed qc_reports/fastp
mkdir alignment
mkdir salmon_quant
mkdir counts
mkdir -p results/tables results/figures results/enrichment
mkdir logs
```
# View the structure
```bash
tree
```
