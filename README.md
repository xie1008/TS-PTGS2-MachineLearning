# Figure 3 Machine-Learning Reproducibility Package

This package contains the public, Figure 3-specific materials for the Pharmaceuticals manuscript. It is based only on the verified analysis version used for the final Figure 3. It does not include Figure 4, immune-infiltration analyses, GSE237861, single-cell analyses, or docking results.

## Study datasets

The analysis uses GSE10474 (GPL571), GSE32707 (GPL10558), and GSE66890 (GPL6244). The included series-matrix files are the GEO inputs used by the verified analysis. The metadata table documents GSM identifiers, source annotations, labels, patient identifiers, inclusion/exclusion decisions, and platforms. The analysis contains 139 prespecified included samples.

## Candidate hub genes

The 20 candidate genes are the Figure 2D hub-gene universe:

ALOX5, BCL2L1, EP300, ESR1, ESR2, GSK3B, HMGCR, HSP90AA1, IKBKB, IL1B, JUN, MCL1, NFKB1, PKM, PPARA, PPARG, PRKCA, PTGES, PTGS2, STAT3.

They were selected in the upstream Figure 2 biological-screening workflow. Figure 3 is therefore a conditional/exploratory validation within this preselected candidate universe. This package does not claim that the upstream DEG, WGCNA, network-pharmacology, and PPI intersection was independently repeated inside every outer training fold.

## Preprocessing

GSE10474 was treated as log2-scale GCRMA-processed data, GSE32707 underwent the verified log2(x+1) transformation, and GSE66890 was treated as log2-scale RMA data, according to the included preprocessing audit. Probe-to-gene mapping and probe aggregation are recorded in the audit files.

For each outer fold, per-platform location/scale harmonization was fitted using outer-training samples only and applied to held-out samples using those training estimates. This is not a ComBat transform. The saved code records the exact implementation and fixed random seed.

## Nested cross-validation

The verified design uses stratified outer 5-fold cross-validation and inner 3-fold cross-validation. All eight models use the same outer assignments: DT, GBM, GLM, KNN, LASSO, NNET, RF, and SVM. Hyperparameters are selected only in the inner loop. Training-fold differential-expression ranking and the prespecified top-five fallback are recorded in the fold-level gene-selection files. Held-out outer-test predictions are never used to choose a model or feature set.

The ROC positive class is SILI, and AUC is calculated with a fixed direction. fold_auc.csv contains the five independent outer-fold AUCs per model; pooled_auc_ci_metrics.csv contains pooled out-of-fold AUC and confidence intervals. These quantities are not interchangeable.

## Reproduction

From the package root, install the R packages listed in scripts/final_strict_nested_cv_verified.R and use the versions recorded in results/sessionInfo.txt.

To regenerate the verified analysis from the included GEO series matrices:

    Rscript scripts/final_strict_nested_cv_verified.R

This writes regenerated outputs to results/ and is a full analysis rerun.

To regenerate Figure 3 from the preserved verified CSV outputs without retraining:

    Rscript scripts/make_figure3_publication.R

This writes the PDF, PNG, and TIFF to figures/.

To run automated numerical checks:

    Rscript scripts/automated_acceptance_tests.R

The acceptance script verifies sample counts, unique GSM/patient identifiers, platform and hub-gene coverage, fixed fold assignments, eight-model coverage, fold-AUC recalculation from outer-test probabilities, explicit gene-selection reasons, and PTGS2 summary reproducibility.

## Publicity and scope

The package contains no account credentials, access tokens, or personal identifiers beyond public GEO sample identifiers. The model-result CSVs and Figure 3 files are copied from the verified analysis version; no obsolete RF AUC or historical model result is included.

