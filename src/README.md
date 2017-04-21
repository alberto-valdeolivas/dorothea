This folder contains the code needed to estimate sample-level TF activities (or any other gene set enrichment score).

The main function is 


```
SLEA(E, genesets, method, M = NULL, permutations = 1000, filter_E = F)

```


```
  Info:
  - E = Expression matrix (rows=genes; columns=samples) scaled and recentered (use gene_expression_statistic).
  - genesets = list with two elemens: 1) NAME = vector with the names of the genes set; 2) GENES = named list of gene sets
  - method = name of the method used for the SLEA (z-SCORE aka "MEAN"; MLR aka "GSEAlm"; aREA aka "VIPER"; "ssGSEA"; "GSVA")
  - M = Methylation binary matrix (optional). If null will be ignored
  - permutations. Number of permutations needed for the method "MEAN"
  - filter_E. Logical indicating if genes in E not in the gene set should be removed/ Default FALSE.  
```



located in ``lib_enrichment_scores.r``

