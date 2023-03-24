# HGUT_arch_viruses

- Manuscript https://www.nature.com/articles/s41467-022-35735-y#data-availability

- Dataset https://figshare.com/articles/dataset/Nucleotide_sequences_and_metadata_file_of_archaeal_viruses/21152404/3


## running CheckV v1.0.1

```
conda activate CheckV
export CHECKVDB=/work_beegfs/sunam162/checkv-db-v1.5
checkv end_to_end vir_1279.fna ../vir_1279_CheckV -t 16
```

## running Virsorter2

```
conda activate vs2
virsorter run -w ../vs2_out -i vir_1279.fna --include-groups "dsDNAphage,ssDNA" -j 4 --min-score 0.8 classify
```
