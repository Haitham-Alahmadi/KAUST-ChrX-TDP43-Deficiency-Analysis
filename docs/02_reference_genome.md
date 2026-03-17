# Reference Genome Acquisition  
The reference genome files are sourced from the following primary Ensembl directory:  
https://ftp.ensembl.org/pub/release-110/fasta/homo_sapiens/   
The following code utilizes direct links to the specific files required for this analysis.  

## Navigate to the reference directory
```bash
cd ~/chromosome_x/references
```
## 1- Genomic DNA (FASTA)
```bash
wget https://ftp.ensembl.org/pub/release-110/fasta/homo_sapiens/dna/Homo_sapiens.GRCh38.dna_sm.chromosome.X.fa.gz
```
### unzip
We use gunzip (GNU unzip) to restore the compressed file to its original readable state, ensuring indexing tools can directly access the raw nucleotide sequence to build the genomic map.
```bash
gunzip Homo_sapiens.GRCh38.dna_sm.chromosome.X.fa.gz
```

## 2- Gene Annotation (GTF)

https://ftp.ensembl.org/pub/release-110/gtf/homo_sapiens/  
Download the human annotation and subset it for Chromosome X to optimize memory (RAM) usage and processing speed.  

### Download full GTF
```bash
wget https://ftp.ensembl.org/pub/release-110/gtf/homo_sapiens/Homo_sapiens.GRCh38.110.gtf.gz
```
### unzip
```bash
gunzip Homo_sapiens.GRCh38.110.gtf.gz
```
### grep (Global Regular Expression Print):

Filters for lines starting with "X" (-E "^#|^X	") to create a lightweight, chromosome-specific annotation file.  
```bash
grep -E "^#|^X	" Homo_sapiens.GRCh38.110.gtf > Homo_sapiens.GRCh38.110.chrX.gtf
```

## 3- Transcriptome (cDNA)

https://ftp.ensembl.org/pub/release-110/fasta/homo_sapiens/cdna/  
Download and decompress the known transcript sequences required for the final quantification of gene expression.  

### Download
```bash
wget https://ftp.ensembl.org/pub/release-110/fasta/homo_sapiens/cdna/Homo_sapiens.GRCh38.cdna.all.fa.gz
```

### unzip 
Unlike GTF files where each line is independent, cDNA (FASTA) records are multi-line. We use awk instead of grep to ensure that when a header matches Chromosome X, the entire associated nucleotide sequence is preserved, not just the header line.  

```bash
gunzip -c Homo_sapiens.GRCh38.cdna.all.fa.gz | awk '/^>/ {keep = /chromosome:GRCh38:X:/} keep' > Homo_sapiens.GRCh38.cdna.chrX.fa
```
