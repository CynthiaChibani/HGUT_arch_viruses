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

```
## running DeepVirFinder
```

```
## running VIBRANT
```

```
