# AbATCC19606_AMR_DefenseSystems

This repository contains a reproducible workflow to download *Acinetobacter baumannii* ATCC 19606 genomes from NCBI, annotate antimicrobial resistance genes using ABRicate (with the CARD database), and predict anti-phage defense systems with PADLOC and DefenseFinder.

## Download Genomes of *Acinetobacter baumannii* ATCC 19606

This section describes how to download genomic sequences of *Acinetobacter baumannii* ATCC 19606 from NCBI GenBank. The workflow uses Linux command-line tools and is reproducible.

```bash
# 1. Create a directory for the genome sequences
mkdir AbATCC19606_Sequences
cd AbATCC19606_Sequences

# 2. Download the GenBank assembly summary file
wget https://ftp.ncbi.nlm.nih.gov/genomes/genbank/assembly_summary_genbank.txt

# 3. Filter the table for Acinetobacter baumannii ATCC 19606 assemblies
cat assembly_summary_genbank.txt | grep "Acinetobacter" | grep "baumannii" | grep "ATCC" | grep "19606" > AbATCC19606_assembly_summary_genbank.tsv

# 4. (Optional) Clean and transform the file manually if needed

# 5. Generate the URLs of genomic FASTA files
awk -F'\t' 'NR>1 && $9!="" {split($9,a,"/"); base=a[length(a)]; print $9 "/" base "_genomic.fna.gz"}' AbATCC19606_assembly_summary_genbank.tsv > AbATCC19606_genomes_urls.txt

# 6. Download the genome FASTA files
wget -i AbATCC19606_genomes_urls.txt

# 7. Decompress the downloaded FASTA files
gzip -d *.fna.gz
```

## Detect antimicrobial resistance genes

This step identifies antimicrobial resistance (AMR) genes in the genomes using **ABRicate** with the **CARD** database.

```bash
# Create directory for results
mkdir -p Antimicrobial_resistance_genes

# Run ABRicate on all genome sequences
for f in ./AbATCC19606_Sequences/*.fna; do
    base=$(basename "$f" .fna)
    for db in card; do
        abricate --db $db "$f" > "Antimicrobial_resistance_genes/${base}_${db}.tab"
    done
done
```
