# Quality Control (QC) 
The quality control process is divided into three essential steps to ensure data integrity before proceeding with the analysis: 
### First: Reporting (Diagnosis)  
We use FastQC (Fast Quality Control) to perform an initial diagnostic check on the raw reads. This report identifies technical issues such as low-quality base calls and adapter contamination.  

### Second: Trimming (Cleaning)  
We use fastp (fast preprocessor) for a comprehensive cleaning process. This involves trimming low-quality bases (quality trimming), automatically removing adapters (adapter removal), and filtering out short reads to ensure the data is clean and reliable.  

### Third: Comparison (Verification)  
After cleaning, we re-run the quality check and use MultiQC (Multi Quality Control) to aggregate all individual reports into a single file. This allows for a direct comparison between the data states "before and after" trimming to confirm the success of the cleaning process.  

## pre-Trimming FastQC Analysis  
Run FastQC on raw data  
```bash
cd ~/chromosome_x

fastqc raw_data/*.fastq.gz -o qc_reports/fastqc_raw -t 4

# List the generated reports
ls -la qc_reports/fastqc_raw/
```

## Run fastp on all samples

### Navigate to the main directory
```bash
cd ~/chromosome_x
```

### Process all samples with a loop  

```bash
Define sample IDs

SAMPLES="KO_1_SRR10045016 KO_2_SRR10045017 KO_3_SRR10045018 WT_1_SRR10045019 WT_2_SRR10045020 WT_3_SRR10045021"


for SAMPLE in $SAMPLES; do
    fastp \
        --in1 raw_data/${SAMPLE}_1.fastq.gz \
        --in2 raw_data/${SAMPLE}_2.fastq.gz \
        --out1 trimmed_data/${SAMPLE}_1.trimmed.fastq.gz \
        --out2 trimmed_data/${SAMPLE}_2.trimmed.fastq.gz \
        --qualified_quality_phred 20 \
        --length_required 36 \
        --detect_adapter_for_pe \
        --overrepresentation_analysis \
        --thread 4 \
        --json qc_reports/fastp/${SAMPLE}.json \
        --html qc_reports/fastp/${SAMPLE}.html \
        2>> logs/fastp.log
done
```

### Listing Trimmed Data

The command ls -lh trimmed_data/ lists all files inside the trimmed_data folder with their sizes and details. We use this step to verify that the fastp tool successfully created the output files and to ensure they are not empty (size 0) before proceeding.  

```bash
ls -lh trimmed_data/
```

### Get read counts before and after  
```bash
seqkit stats raw_data/*_1.fastq.gz trimmed_data/*_1.trimmed.fastq.gz
```

## Post-Trimming FastQC Analysis 

### Run FastQC on trimmed data  
```bash
cd ~/chromosome_x

# Run FastQC on trimmed files
fastqc trimmed_data/*.fastq.gz -o qc_reports/fastqc_trimmed -t 4

#Compare a sample before and after
#browse the HTML reports

```

## MultiQC Aggregated Report  
MultiQC combines results from multiple samples into a single interactive report.  

### Generate MultiQC reports  

```bash
multiqc qc_reports/ -o qc_reports -n multiqc_all --force
```











