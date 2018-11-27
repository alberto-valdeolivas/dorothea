---
layout: page
title: Usage
---

## DoRothEA (v1)
### Example
Below is an example how to run the first version of DoRothEA. The corresponding GitHub repository can be found [here](https://github.com/saezlab/DoRothEA/releases/tag/version1).
```
# Load functions
source('src/lib_enrichment_scores.r')

# Load TF regulon genesets
load('data/CTFRs_v122016.rdata')
# Load expression matrix
load('data/example_expression_mat.rdata')

# Estimate TF activities
TF_activities = SLEA(E = E, genesets = CTFRs_genesets, method = 'GSVA')$NES
```

## DoRothEA (v2)
### Example
Below you can find two examples of how to calculate TF activities using the [second version of DoRothEA](https://github.com/saezlab/DoRothEA/).

```
# Luz Garcia-Alonso
# Saez-Rodriguez Lab
# European Bioinformatics Institute (EMBL-EBI)
# 2018.03.26



# Load requeired packages
require(viper) 



# Load TF regulon genesets in VIPER format
load('data/TFregulons/consensus/Robjects_VIPERformat/normal/TOP10score_viperRegulon.rdata')
# Clean TF names & explore object
names(viper_regulon) = sapply(strsplit(names(viper_regulon), split = ' - '), head, 1)
# Explore the regulons object
names(viper_regulon)[1:10]
viper_regulon[[1]]





##########################################################################################
## Example 1: Computing single-sample TF activities from a normalized gene expression matrix 
##########################################################################################
# Load expression matrix: NOTE that genes at comparable scales (e.g. zscores)
load('data/expression/example_expressionMatrix_zscores.rdata')
# Explore the matrix
E[1:5, 1:5]
# Estimate TF activities
TF_activities = viper(eset = E, regulon = viper_regulon, nes = T, method = 'none', minsize = 4, eset.filter = F)
# Save results
write.csv(TF_activities, file = 'TFactivities_example1.csv')




##########################################################################################
## Example 2: Computing TF activity changes from a differential gene expression signature
##########################################################################################
# Load differential expression signature
load('data/expression/example_differentialExpression_results.rdata')
# Explore the signature
DEsignature[1:5, ]
# Exclude probes with unknown or duplicated gene symbol
DEsignature = subset(DEsignature, Symbol != "" )
DEsignature = subset(DEsignature, ! duplicated(Symbol))
# Estimatez-score values for the GES. Cheeck VIPER manual for details
myStatistics = matrix(DEsignature$logFC, dimnames = list(DEsignature$Symbol, 'logFC') )
myPvalue = matrix(DEsignature$P.Value, dimnames = list(DEsignature$Symbol, 'P.Value') )
mySignature = (qnorm(myPvalue/2, lower.tail = FALSE) * sign(myStatistics))[, 1]
mySignature = mySignature[order(mySignature, decreasing = T)]
# Estimate TF activities
mrs = msviper(ges = mySignature, regulon = viper_regulon, minsize = 4, ges.filter = F)
TF_activities = data.frame(Regulon = names(mrs$es$nes),
                           Size = mrs$es$size[ names(mrs$es$nes) ], 
                           NES = mrs$es$nes, 
                           p.value = mrs$es$p.value, 
                           FDR = p.adjust(mrs$es$p.value, method = 'fdr'))
TF_activities = TF_activities[ order(TF_activities$p.value), ]
# Save results
write.csv(TF_activities, file = 'TFactivities_example2.csv')
```
### Regulons access
Next to the default [DoRothEA regulons](https://github.com/saezlab/DoRothEA/tree/master/data/TFregulons/consensus/Robjects_VIPERformat/normal) we also provide an [alternative version of DoRothEA](https://github.com/saezlab/DoRothEA/tree/master/data/TFregulons/consensus/Robjects_VIPERformat/pancancer) where GTEx data were replaced in the contruction process with expression data from TCGA. This might be useful esspecially for people interested in cancer research. Furthermore, we provide tissue specific regulons from [GTEx (corrected for batch effects using COMBAT)](https://github.com/saezlab/DoRothEA/tree/master/data/TFregulons/advanced_single_evidences/Robjects_VIPERformat/inferred_ARACNe/normal_GTEx/tissue_specific) and [GTEx (non-batch-corrected)](https://github.com/saezlab/DoRothEA/tree/master/data/TFregulons/advanced_single_evidences/Robjects_VIPERformat/inferred_ARACNe/normal_GTEx_non_batch_corrected/tissue_specific).
