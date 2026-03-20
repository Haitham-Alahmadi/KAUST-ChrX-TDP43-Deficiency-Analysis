# Raw data ( FASTQ )  
We use **seqkit** stats to generate a comprehensive exploratory report for the raw data files, which contain the initial sequencing reads (FASTQ) from the experiment. This step is essential to verify the total number of reads and the distribution of sequence lengths across all samples before starting the quality control process.  

### Navigate to the project main directory
```bash
cd ~/chromosome_x
```
We use the following specific command because the wildcard (*) allows it to process all files in the directory simultaneously, while the -a (advanced) metric provides detailed biological data such as N50, Q20, and Q30 percentages for a deeper quality assessment.
```bash
seqkit stats -a raw_data/*.fastq.gz --threads 8 > seqkit_stats.tsv
```

# Reference data ( FASTA ) 

We use **seqkit** to generate the initial statistics for the Chromosome X genome file, including a detailed report with the -a flag and viewing the sequence header.  

### Navigate to the reference directory
```bash
cd ~/chromosome_x/references
```

### Get genome statistics
We use seqkit stats to obtain a basic summary of the Chromosome X genome file.
```bash
seqkit stats Homo_sapiens.GRCh38.dna_sm.chromosome.X.fa
```
### Get detailed statistics including GC content
We use the -a flag with seqkit stats to generate an advanced report that includes the GC content and other assembly metrics.
```bash
seqkit stats -a Homo_sapiens.GRCh38.dna_sm.chromosome.X.fa
```

### View the sequence header

We use seqkit seq -n to display the name of the sequence header (chromosome ID) to verify it matches our target.

```bash
seqkit seq -n Homo_sapiens.GRCh38.dna_sm.chromosome.X.fa
```

