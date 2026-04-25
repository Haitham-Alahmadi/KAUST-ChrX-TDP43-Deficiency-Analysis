# Pseudo-alignment (Salmon)  

## Build Salmon Transcriptome Index  
Salmon requires an index built from transcript sequences (cDNA), not the genome.

### Navigate to the main directory   
```bash
cd ~/chromosome_x
```

### Build Salmon index from chrX transcriptome 
```bash
salmon index \
    -t references/transcriptome_chr11.fa \
    -i references/salmon_index \
    --threads 4
```

### Check the index files  
```bash
ls -lh references/salmon_index/
```

## Quantify Samples with Salmon  

### Quantify all samples  

```bash

cd ~/chromosome_x


# Create logs directory
mkdir -p logs

# Define samples
SAMPLES="KO_1_SRR10045016 KO_2_SRR10045017 KO_3_SRR10045018 WT_1_SRR10045019 WT_2_SRR10045020 WT_3_SRR10045021"

# Process each sample
for SAMPLE in $SAMPLES; do
    # Extract short name (KO_1, KO_2, etc.)
    SHORT_NAME=$(echo $SAMPLE | cut -d'_' -f1,2)
    echo "Quantifying $SHORT_NAME..."

    salmon quant \
        -i references/salmon_index \
        -l A \
        -1 trimmed_data/${SAMPLE}_1.trimmed.fastq.gz \
        -2 trimmed_data/${SAMPLE}_2.trimmed.fastq.gz \
        -o salmon_quant/${SHORT_NAME} \
        --threads 4 \
        --validateMappings \
        --gcBias \
        --seqBias \
        2>> logs/salmon.log
done

echo "Quantification complete!"
ls salmon_quant/
```

### Explore the quantification file  

```bash

## 
# View the quant.sf file
head -10 salmon_quant/KO_1/quant.sf

# Column descriptions:
# Name: Transcript ID (Ensembl format)
# Length: Transcript length in bp
# EffectiveLength: Length adjusted for fragment size and bias
# TPM: Transcripts Per Million (normalized expression)
# NumReads: Estimated number of reads mapping to this transcript
```

### Check mapping rates   

```bash
# View the log file for mapping statistics
cat salmon_quant/KO_1/logs/salmon_quant.log

# Extract mapping rate for all samples
echo "=== Salmon Mapping Rates ==="
for dir in salmon_quant/*/; do
    sample=$(basename $dir)
    rate=$(grep "Mapping rate" $dir/logs/salmon_quant.log | awk '{print $NF}')
    echo "$sample: $rate"
done
```
### Expected Mapping Rates

| Mapping Rate | Quality | Notes |
| :--- | :--- | :--- |
| **>70%** | Good | Expected for most RNA-seq |
| **50-70%** | Acceptable | May indicate some issues |
| **<50%** | Investigate | Check data quality or reference |

> **Note:** Mapping rates for chromosome-specific data may be lower than whole-genome data.

# Aggregate to Gene Level with tximport
Salmon outputs transcript-level counts. For differential expression with DESeq2, we aggregate to gene level using tximport.

## Download the tximport Script
We use a reusable R script that handles both tx2gene generation from GTF and tximport aggregation.
### Navigate to the main directory
```bash
cd ~/chromosome_x
```
### Download the script
```bash
# Create scripts directory
mkdir -p scripts

# Download the tximport script from GitHub
wget -O scripts/run_tximport.R https://raw.githubusercontent.com/bioinfo-kaust/academy-stage3-2026/refs/heads/main/scripts/run_tximport.R

# Make it executable
chmod +x scripts/run_tximport.R

# View script help
Rscript scripts/run_tximport.R --help
```

## Run tximport
### Run the script
```bash
cd ~/chromosome_x

# Run the tximport script with your GTF and Salmon output
Rscript scripts/run_tximport.R \
    --gtf references/Homo_sapiens.GRCh38.110.chrX.gtf \
    --salmon_dir salmon_quant \
    --outdir counts
```
### Check the output files
```bash
ls -lh counts/
```

## Explore the Expression Matrix
### View count matrix and TPM values
```bash
# View the first few genes in the count matrix
head -10 counts/gene_counts.tsv | column -t

# How many genes have counts?
wc -l counts/gene_counts.tsv

# View TPM values (normalized)
head -10 counts/gene_tpm.tsv | column -t

# View sample info
cat counts/sample_info.tsv
```

### Output files description
| File | Description | Use |
| :--- | :--- | :--- |
| **gene_counts.tsv** | Raw counts | Input for DESeq2 |
| **gene_tpm.tsv** | TPM values | Visualization |
| **sample_info.tsv** | Sample metadata | DESeq2 design |
| **tx2gene.tsv** | Transcript-to-gene mapping | Reference |
| **tximport.rds** | R object | Direct DESeq2 input |
