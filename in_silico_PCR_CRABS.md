# Creating a  curated reference database for metagenomic analysis and in silico PCR using CRABS

We need to check that our chosen primer (MarVer3 primer) is going to work well for our purposes. The MarVer3 primer (Valsecchi et al. 2020) was made using 71 complete mitochondrial genome sequences retrieved from GenBank. These sequences represent 30 marine vertebrate families, including most marine mammal families, with focus on species in the Mediterranean Sea. Four human mitochondrial genomes were used to design primers with reduced amplification efficiency for human DNA. 

MarVer3: 
- MarVer3F: AGACGAGAAGACCCTRTG 18bp 16S region
- MarVer3R: GGATTGCGCTGTTATCCC 18bp

Average amplicon size: ~245bp

CRABS (Creating Reference databases for Amplicon-Based Sequencing) is a versatile software program that generates curated reference databases for metagenomic analysis. Here is the link to that github repository:
https://github.com/gjeunen/reference_database_creator#52-module-2-import-downloaded-data-into-crabs-format

Installing CRABS is as easy as cloning the github repo. After that, we can get started by compiling our reference database.

# CRABS workflow

The CRABS workflow consists of seven total modules with unique functionalities. We probably won't need to use every single module for what we need to use CRABS for, but we'll see as we go along. 

Module 1: download data from online repositories\
Module 2: import downloaded data into CRABS format\
Module 3: extract amplicon regions through in silico PCR\
Module 4: retrieve amplicons without primer-binding regions\
Module 5: curate and subset the local database via multiple filtering parameters\
Module 6: export  the local database\
Module 7: post-processing functions to explore and provide a summary overview of the local reference database\

# STEP 1: Download data from online repositories 
Initial sequencing data can be downloaded by CRABS from multiple different online repositories. I will be downloading sequencing data from NCBI for this project, as it's the one I'm most comfortable with and probably the one that has the most data? I could be wrong ... 

I compiled my reference database using the following code: 

```
    crabs --download-ncbi \
  --query '(
    "Artiodactyla"[Organism] OR
    "Odontoceti"[Organism] OR
    "Mysticeti"[Organism] OR
    "Phocidae"[Organism] OR
    "Otariidae"[Organism] OR
    "Carnivora"[Organism] OR
    "Aves"[Organism] OR
    "Chondrichthyes"[Organism] OR
    "Dipnomorpha"[Organism] OR
    "Coelacanthiformes"[Organism] OR
    "Actinopterygii"[Organism] OR
    "Homo"[Organism] OR
    "Homininae"[Organism])
  AND mitochondrion[filter]' \
  --output ncbi_onr_16S_mito.fasta \
  --email migriver@ucsc.edu \
  --database nucleotide
```
I pretty much made sure to include any close relative, or any other vertebrate we may or may not have in the environment we're collecting these water samples from. I also included humans (and other primates) because of, well, all the human contamination that goes into these samples. 


# STEP 2: Import downloaded data into CRABS format

First, we need to download the appropriate taxonomy database files to map accession numbers to taxonomic ranks. These are separate from the fasta file we compiled from NCBI with all the sequence data. 

We need: 
1.) NBCI names.dmp file (taxon names)
2.) NCBI nodes.dmp file (taxonomix hierarchy)
3.) NCBI accession -> taxid mapping file 

```
wget https://ftp.ncbi.nlm.nih.gov/pub/taxonomy/taxdump.tar.gz
tar -xvf taxdump.tar.gz  # extracts names.dmp and nodes.dmp

wget https://ftp.ncbi.nlm.nih.gov/pub/taxonomy/accession2taxid/nucl_gb.accession2taxid.gz
gunzip nucl_gb.accession2taxid.gz
```

Next, we will import these files into CRABS and create a CRABS format file for in silico PCR. 

```
crabs --import --import-format ncbi --input ncbi_onr_16S_mito.fasta --output ncbi_onr_marver3_16S_mito_refdatabase.txt --ranks 'superkingdom;phylum;class;order;family;genus;species' --names names.dmp --nodes nodes.dmp --acc2tax nucl_gb.accession2taxid
```
# STEP 3: Extract Amplicon Regions through in silico PCR Analysis

```
crabs --in-silico-pcr --relaxed --input ncbi_onr_marver3_16S_mito_refdatabase.txt --output ncbi_onr_marver3_16S_mito_refdatabase.txt --forward AGACGAGAAGACCCTRTG --reverse GGATTGCGCTGTTATCCC
```
