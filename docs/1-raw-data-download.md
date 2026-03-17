# Raw Data Download

## Context & Provenance
The full experimental dataset is archived on NCBI GEO (GSE136366).  
* **Metadata:**  Available on the GEO portal (includes sample types WT vs KO, platform details, and experimental conditions).
* **Raw Data:** Consists of 6 FASTQ files totaling approximately 25-27GB

## Logic for Subsetting

To optimize storage and processing time, we utilized a pre-subsetted version of the dataset focused on Chromosome X and hosted on Zenodo. This allows for a focused analysis on the assigned genomic region without the computational overhead of the full 27GB dataset

## Download  
We use the wget (Web Get) utility to download the data container directly to the server
```bash
cd ~/chromosome_x/raw_data
```
```bash
wget https://zenodo.org/records/18481303/files/chrX_data.zip
```
## Extract and cleanup  
Extract the data files from the container, then delete the original compressed file
```bash
unzip chrX_data.zip
rm chrX_data.zip
```
## Verification  
Confirm the presence of the 12 files and review their sizes
```bash
ls -lh *.fastq.gz
```

### Expected Outcome (Paired-End Files)  

Upon successful extraction, you should find 12 files in your directory. This is because the experiment uses Paired-End sequencing, where each biological sample is sequenced from both ends, resulting in a Forward read (_1) and a Reverse read (_2).
| Expected Filename | Sample Identity | Read Direction |
| :--- | :--- | :--- |
| **KO_1_SRR10045016_1.fastq.gz** | TDP43_KO (Rep 1) | Forward (R1) |
| **KO_1_SRR10045016_2.fastq.gz** | TDP43_KO (Rep 1) | Reverse (R2) |
| **KO_2_SRR10045017_1.fastq.gz** | TDP43_KO (Rep 2) | Forward (R1) |
| **KO_2_SRR10045017_2.fastq.gz** | TDP43_KO (Rep 2) | Reverse (R2) |
| **KO_3_SRR10045018_1.fastq.gz** | TDP43_KO (Rep 3) | Forward (R1) |
| **KO_3_SRR10045018_2.fastq.gz** | TDP43_KO (Rep 3) | Reverse (R2) |
| **WT_1_SRR10045019_1.fastq.gz** | TDP43_WT (Rep 1) | Forward (R1) |
| **WT_1_SRR10045019_2.fastq.gz** | TDP43_WT (Rep 1) | Reverse (R2) |
| **WT_2_SRR10045020_1.fastq.gz** | TDP43_WT (Rep 2) | Forward (R1) |
| **WT_2_SRR10045020_2.fastq.gz** | TDP43_WT (Rep 2) | Reverse (R2) |
| **WT_3_SRR10045021_1.fastq.gz** | TDP43_WT (Rep 3) | Forward (R1) |
| **WT_3_SRR10045021_2.fastq.gz** | TDP43_WT (Rep 3) | Reverse (R2) |
