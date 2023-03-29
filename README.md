# HGUT_arch_viruses

- Manuscript https://www.nature.com/articles/s41467-022-35735-y#data-availability

- Dataset https://figshare.com/articles/dataset/Nucleotide_sequences_and_metadata_file_of_archaeal_viruses/21152404/3

## running CheckV v1.0.1
```
conda activate CheckV
export CHECKVDB=/work_beegfs/sunam162/checkv-db-v1.5
checkv end_to_end vir_1279.fna ../vir_1279_CheckV -t 16
```

## running VirSorter 2.2.4
```
conda activate vs2
virsorter run -w ../vir_1279_vs2_out -i vir_1279.fna --include-groups "dsDNAphage,ssDNA" -j 4 --min-score 0.8 classify
```
## running VirSorter v1.0.6
```
source activate virsorter
wrapper_phage_contigs_sorter_iPlant.pl -f vir_1279.fna --db 1 --wdir vs_out --ncpu 12 --data-dir /path/to/virsorter-data
```
## running VirFinder
```
conda activate virfinder
## (1) set the input fasta file name. 
library(VirFinder)

## (2) prediction
predResult <- VF.pred("vir_1279.fna")
predResult

#### (2.1) sort sequences by p-value in ascending order
predResult[order(predResult$pvalue),]

#### (2.2) estimate q-values (false discovery rates) based on p-values
predResult$qvalue <- VF.qvalue(predResult$pvalue)

Error in pi0est(p, ...) : 
  ERROR: The estimated pi0 <= 0. Check that you have valid p-values or use a different range of lambda.

```
## running VIBRANT v1.2.1 and DeepVirFinder
- on sunam088 ViWrap env
- incompatible with module load gcc12-env/12.1.0 on caucluster
```

conda activate /zfshome/sunam088/.conda/envs/ViWrap/
cd /work_beegfs/sunam088/ViWrap

./ViWrap run_wo_reads --input_metagenome /work_beegfs/sunam088/BGR_Viwrap/vir_1279.fna --out_dir /work_beegfs/sunam088/BGR_Viwrap/vir_1279_Out --db_dir ../ViWrap_DB/ --identify_method vb-vs-dvf --conda_env_dir /zfshome/sunam088/.conda/envs/ViWrap/ --threads 12
```

## running geNomad v1.5.0
```
conda activate genomad
genomad end-to-end --cleanup --splits 8 vir_1279.fna ../vir_1279_genomad ../../genomad_db/
```
## running minCED
```
cd 1167_arch_MAGs_HGUT
mkdir ../Minced
conda activate minced
for i in *.fa; do echo  minced -minNR 2 $i ../Minced/"$i".crisprs ../Minced/"$i".gff; done
for i in *crisprs; do python minced_parser.py $i spacers > "$i".parsed.fa; done
```
