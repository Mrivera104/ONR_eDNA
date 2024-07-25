# Mitogenome Preliminary Bait Design and Setup 
07/25/2024: Today Sean and I chopped up the humpback whale and northern elephant seal mitogenomes into 80bp chunks (following Sundararaman et al. 2024) using a 1bp sliding window.

The humpback whale mitogenome: NCBI Reference Sequence: NC_006927.1. Published April 3rd 2023. BioProject: PRJNA927338 

The northern elephant seal mitogenome: GenBank: CM055130.1. Published March 15th 2023. BioProject: PRJNA937321 (CCGP) 

Both downloaded from NCCBI and placed within /scratch1/migriver_ONR/mitogen_files

# Chop up mitogenome into 80bp chunks 
Using seqkit, chop up mitogenome fasta into 80bp length segments in a 1bp sliding window

    seqkit sliding -s 1 -W 80 /scratch1/migriver_ONR/mitogen_files/novaeangliae_NC_006927.fasta | awk '{ if(NR%2==0){print substr($0,1,80)} else {print $0} }' > novaeangliae_mitogenome_chunks.fasta
    
    seqkit sliding -s 1 -W 80 /scratch1/migriver_ONR/mitogen_files/angustirostris_CM055130.fasta | awk '{ if(NR%2==0){print substr($0,1,80)} else {print $0} }' > angustirostris_mitogenome_chunks.fasta
    
