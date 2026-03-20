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


