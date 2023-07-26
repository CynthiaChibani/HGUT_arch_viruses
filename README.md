# HGUT_arch_viruses

- Manuscript https://www.nature.com/articles/s41467-022-35735-y#data-availability

- Dataset https://figshare.com/articles/dataset/Nucleotide_sequences_and_metadata_file_of_archaeal_viruses/21152404/3

# Viral Peediction
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
## Running minCED
```
cd 1167_arch_MAGs_HGUT
mkdir ../Minced
conda activate minced
# Identify spacers in arch MAGs hosts
for i in *.fa; do minced -minNR 2 $i ../Minced/"$i".crisprs ../Minced/"$i".gff; done
for i in *crisprs; do python minced_parser.py $i spacers > "$i".parsed.fa; done

# make BLAST_DB out of vir_DB
makeblastdb -dbtype nucl -in /work_beegfs/sunam162/arch_MAGs_viruses_Rebuttal/Dataset/vir_1279.fna -out /work_beegfs/sunam162/arch_MAGs_viruses_Rebuttal/BLASTn_vir_1279_Minced_spacers/vir_1279_DB

# BLAST spacers to vir_DB
for i in *.fa; do blastn -ungapped -out ../BLASTn_vir_1279_Minced_spacers/"$i"_blast_out -outfmt 7 -db /work_beegfs/sunam162/arch_MAGs_viruses_Rebuttal/BLASTn_vir_1279_Minced_spacers/vir_1279_DB -query $i; done

# Parse BLASTn output
cd ../BLASTn_vir_1279_Minced_spacers
for f in *_blast_out; do paste $f <(yes $f | head -n $(cat $f | wc -l)) > $f.new; done
cat *new > all_blast.txt
rm *.new *_blast_out
```
