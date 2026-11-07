# Gaussian Mixture Model (GMM)

**Notebook:** [`gmm_customer_segmentation.ipynb`](gmm_customer_segmentation.ipynb), already run with all outputs.

## How it runs
- Run it from this folder. It reads the raw data from `../data/raw/data.csv.gz`.
- Part 1 repeats the shared preparation so the notebook also works on its own, then **checks that it reproduced `data/processed/customer_pca.csv` exactly**. The GMM labels therefore line up row by row with the other three algorithms.
- Results are saved in this folder.

## What is inside
1. Shared preparation (the same steps as `../data/01_data_preparation.ipynb`, plus the check)
2. How GMM and the EM algorithm work, and how GMM differs from K-Means
3. Choosing k and the covariance type with BIC and AIC (1-10 components x 4 covariance types)
4. Stability across 10 random starts
5. Final model, metrics, and a K-Means reference on the same data
6. Clusters and their fitted ellipses on PC1/PC2
7. Soft assignments: how sure the model is about each customer
8. Sensitivity to noise: refitting with the extreme customers, and density as a noise score
9. Cluster profiles and segment names
10. Simple product suggestions per segment
11. Strengths, weaknesses and key insights

## Result in short
| | |
|---|---|
| model | 4 components, full covariance (BIC improvement collapses after 4) |
| stability | identical clusters from 10 random starts (ARI = 1.00) |
| silhouette / Davies-Bouldin | 0.31 / 2.45 |
| uncertain customers | 1.5% below 70% certainty |
| noise | outliers are absorbed into clusters (9.1% of regular customers move), but 83% of them fall in the lowest 5% of density |

| segment | customers | share of spend |
|---|---|---|
| One-time buyers | 31% | 9% |
| Steady repeat buyers | 32% | 29% |
| Big baskets, slowing down | 9% | 14% |
| Frequent buyers who return items | 29% | 48% |

## Saved in this folder
`labels_gmm.npy`, `proba_gmm.npy`, `gmm_segments.csv`, `gmm_recommendations.csv`
