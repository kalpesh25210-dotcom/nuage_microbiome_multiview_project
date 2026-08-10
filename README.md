# nuage_microbiome_multiview_project

The project integrates three analyses built from the provided NUAGE microbiome and metadata tables:

1. Mediation analysis to test whether change in microbiome score mediates the relationship between diet-score change and health outcomes.
2. Gender effect-size estimation at baseline using Hedges' g across five metrics.
3. K-means clustering of species abundance profiles followed by testing for clinical associations.

## Dataset

- `NUAGE_Metadata.csv`: 1,220 metadata rows across 610 individuals and two time points
- `NUAGE_SpProfile.csv`: species-level abundance table for the same 1,220 microbiome samples
- Sample naming convention: `SubjectID_T0` and `SubjectID_T1`

## Main Questions Answered

- Does gut microbiome change appear to mediate the effect of diet change on inflammation, cognition, or physical strength?
- How large are baseline gender differences in selected clinical and microbiome-related traits?
- Do unsupervised microbiome clusters align with any clinical variable?

## Results Summary

### 1. Mediation analysis

The mediation workflow reproduced the original delta-score design:

- mediator model: `delta_microbiome ~ delta_food + age_t0 + gender_t0 + PolyPharmacy_t0`
- outcome model: `delta_outcome ~ delta_food + delta_microbiome + age_t0 + gender_t0 + PolyPharmacy_t0`

Reproduced indirect effects:

- hsCRP (log delta): ACME -0.000421, 95% CI [-0.001637, 0.000025], bootstrap p=0.086
- cspraxis (delta): ACME -0.000307, 95% CI [-0.002969, 0.002356], bootstrap p=0.849
- hgtdommean (delta): ACME -0.001146, 95% CI [-0.007032, 0.002361], bootstrap p=0.543

Interpretation: none of the reproduced ACMEs showed strong evidence of non-zero mediation. Under the provided modeling design, microbiome-score change did not emerge as a strong mediator between diet-score change and the three selected outcomes.

### 2. Baseline gender effect sizes

- Leptin: Hedges' g=-1.007, 95% CI [-1.177, -0.837], p=0.0000
- hsCRP: Hedges' g=0.053, 95% CI [-0.107, 0.213], p=0.5176
- Grip strength: Hedges' g=2.383, 95% CI [2.176, 2.591], p=0.0000
- Microbiome score: Hedges' g=-0.035, 95% CI [-0.193, 0.124], p=0.6751
- Food score: Hedges' g=-0.057, 95% CI [-0.216, 0.102], p=0.4780

Interpretation:

- Grip strength showed a very large male-higher effect.
- Leptin showed a large female-higher effect.
- hsCRP, microbiome scores, and food scores showed small or negligible baseline gender differences.

### 3. Microbiome clustering

The clustering workflow used log transformation, z-score scaling, PCA retaining 90% of variance, and silhouette-guided K-means selection.

Key findings:

- input species features: `1,896`
- retained PCA components: `194`
- optimal cluster count: `k = 2`
- strongest tested clinical association: `cspraxis (p = 0.005)`
- non-significant associations: `hsCRP (p = 0.332)`, `hgtdommean (p = 0.078)`

Interpretation: the identified microbiome clusters were most meaningfully associated with constructional praxis scores, suggesting a microbiome-cognition relationship in this cohort under the provided preprocessing pipeline.


## Methods and Tools

- R workflow source for Question 1: `mediation`, `dplyr`, `ggplot2`, `patchwork`
- Python workflow source for Questions 2 and 3: `pandas`, `numpy`, `scikit-learn`, `scipy`, `matplotlib`, `seaborn`
- Statistical ideas used: bootstrap mediation, Hedges' g, 95% confidence intervals, silhouette score, Kruskal-Wallis testing
