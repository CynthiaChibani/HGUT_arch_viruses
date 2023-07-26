# HGUT_arch_viruses

- Manuscript https://www.nature.com/articles/s41467-022-35735-y#data-availability

- Dataset https://figshare.com/articles/dataset/Nucleotide_sequences_and_metadata_file_of_archaeal_viruses/21152404/3

# Viral Prediction
- Command lines used to predict viruses from the HGAVD
- Same command lines were used for archaea Viruses downloaded from RefSeq database
## Running CheckV v1.0.1
- https://bitbucket.org/berkeleylab/checkv/src/master/
- Using a single command to run the full pipeline as recommended:
- `checkv end_to_end input_file.fna output_directory -t 16`
```
conda activate CheckV
checkv end_to_end vir_1279.fna ../vir_1279_CheckV_Out -t 16
```

## Running VirSorter 2.2.4
- https://github.com/jiarong/VirSorter2
- Quick Run : To run viral sequence identification on a test dataset
- `virsorter run -w test.out -i test.fa --min-length 1500 -j 4 all`
```
conda activate vs2
virsorter run -w ../vir_1279_vs2_Out -i vir_1279.fna -j 4 all
```

 ## Running VirSorter v1.0.6
- https://github.com/simroux/VirSorter
- To run VirSorter, type the following:
- `wrapper_phage_contigs_sorter_iPlant.pl -f assembly.fasta --db 1 --wdir output_directory --ncpu 4 --data-dir /path/to/virsorter-data`
```
conda activate virsorter
wrapper_phage_contigs_sorter_iPlant.pl -f vir_1279.fna --db 1 --wdir ../vir_1279_vs_Out --ncpu 12 --data-dir ../virsorter-data
```

## Running VIBRANT v1.2.1
- https://github.com/AnantharamanLab/VIBRANT
- Test out a small dataset of mixed viral and non-viral scaffolds in nucleotide format
- `python3 VIBRANT_run.py -i example_data/mixed_example.fasta`
```
conda activate VIBRANT
VIBRANT_run.py -i vir_1279.fna -folder ../vir_1279_VIBRANT_Out -t 16 -virome -d ../VIBRANT_db
```

## Running geNomad v1.5.0
- https://github.com/apcamargo/genomad
- The command to execute geNomad is structured like this:
- `genomad end-to-end [OPTIONS] INPUT OUTPUT DATABASE`
```
conda activate genomad
genomad end-to-end --cleanup --splits 8 vir_1279.fna ../vir_1279_genomad_Out ../genomad_db/
```

## Running ViralVerify
- https://github.com/ablab/viralVerify
```
conda activate ViralVerify
viralverify -f vir_1279.fna -o vir_1279_ViralVerify --hmm ../Pfam/nbc_hmms.hmm -p -t 10
```

# Hits to CRISPR spacers

## Identification of CRISPR spacers in archaea MAGs hosts
### Running minCED on the 1167 archaea MAGs
- archaea MAGs from the human gut detailed in:
- https://www.nature.com/articles/s41564-021-01020-9
- https://www.nature.com/articles/s41587-020-0603-3
- https://www.ebi.ac.uk/metagenomics/genome-catalogues/human-gut-v2-0-1

- https://github.com/ctSkennerton/minced
- To find repeats in short sequences, we need to decrease the minimum number of repeats to find. For example, in 100 bp reads, we could not possibly find more than 2 repeats:
- `minced -minNR 2 metagenome.fna`
- the minced_parser script was used from https://github.com/Russel88/EsoPy  
```
conda activate minced
for i in *.fa; do minced -minNR 2 $i ../Minced/"$i".crisprs ../Minced/"$i".gff; done
for i in *crisprs; do python minced_parser.py $i spacers > "$i".parsed.fa; done
```
### BLAST of spacers against the HGAVD
```
conda activate BLAST
# make BLAST_DB out of vir_DB
makeblastdb -dbtype nucl -in vir_1279.fna -out vir_1279_DB

# BLAST spacers to vir_DB
for i in *.fa; do blastn -ungapped -out "$i"_blast_out -outfmt 7 -db vir_1279_DB -query $i; done
```

# Verification of Viral Hallmark Genes
## Running eggNOG-mapper
- https://github.com/eggnogdb/eggnog-mapper
- Run search and annotation for assembled contigs, using MMseqs2 "blastx" hits for gene prediction
- `emapper.py -m mmseqs --itype metagenome -i FASTA_FILE_NTS -o test`
```
conda activate eggNOGmapper
emapper.py -i Hallmark_Genes_for_Archaeal_Virus.faa --itype proteins -o Hallmark_Genes_for_Archaeal_Virus_eggNOG.out -m mmseqs
```

## Running DIAMOND 
- https://github.com/bbuchfink/diamond
- creating a diamond database our of the Viral Hallmark genes published by Li et al and using the database to scan the HGAVD.
```
conda activate eggNOGmapper
diamond makedb --in Hallmark_Genes_for_Archaeal_Virus.faa -d Hallmark_Genes_for_Archaeal_Virus_db
diamond blastx -d Hallmark_Genes_for_Archaeal_Virus_db.dmnd --fast -q vir_1279.fna -o DIAMOND_vir_1279_against_Hallmark_genes_matches_10e5_FINAL.csv -e 0.00001
```

## Running HMMsearch
- https://github.com/hyattpd/Prodigal
- Fast, reliable protein-coding gene prediction for prokaryotic genomes (-predict CDS of HGAVD viruses)
- `prodigal -i my.genome.fna -o my.genes -a my.proteins.faa`
```
conda activate prokka
prodigal -i vir_1279.fna -o vir_1279.genes -a vir_1279.faa
```
- against VOG HMMs
- downloaded from https://vogdb.org/
```
conda activate hmmer
for i in *.hmm; do hmmsearch --tblout "$i".hmm.out -E 1e-5 $i vir_1279.faa; done
```
- against VPF HMMs
- downloaded from [https://vogdb.org/](https://img.jgi.doe.gov//docs/final_list.hmms.gz)https://img.jgi.doe.gov//docs/final_list.hmms.gz
```
conda activate hmmer
hmmsearch --tblout vir_1279_against_VPF.hmm.out -E 1e-5 VPF_final_list.hmms vir_1279.faa; done
```
