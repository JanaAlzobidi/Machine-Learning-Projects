# GMM Customer Segmentation

Notebook: `gmm_customer_segmentation.ipynb`

This notebook groups customers into segments of similar buyers using a **Gaussian Mixture Model (GMM)**. It is one of four clustering methods in the project (K-Means, Hierarchical, DBSCAN, GMM).

## What is GMM?

- GMM assumes the customers come from **k hidden groups**. Each group is a bell-shaped "cloud" of points with its own center, shape and size.
- It learns the groups with the **EM algorithm**, which repeats two steps:
  - **E-step:** compute the probability that each customer belongs to each group.
  - **M-step:** update each group to fit its customers.
- Unlike K-Means, GMM gives a **probability for each cluster** (soft assignment), not only one label.

## Input files

The notebook reads from `../data/processed/`:

| File | Rows | Used for |
|---|---|---|
| `customer_pca.csv` | 4,143 customers × PC1–PC5 | Training the model |
| `customer_features.csv` | 4,362 customers × 12 features + Country + IsOutlier | Describing the clusters (filtered to the 4,143 customers) |

The 219 outlier customers were removed during preprocessing and are **not** used for training.

## How to run

1. Put the two CSV files in `../data/processed/` (relative to the notebook). If your files are somewhere else, change only the `BASE` line in step 1.
2. Install the libraries: `numpy`, `pandas`, `matplotlib`, `scikit-learn`.
3. Run all cells from top to bottom. It takes about one minute, mostly for the BIC loop.

## Steps in the notebook

1. **Load the data:** PCA components for training, and readable features for describing the clusters.
2. **Choose k with BIC:** try k = 1 to 10 and look for the elbow.
3. **Train the final model:** k = 4, `covariance_type="full"`, `n_init=10`, `random_state=42`. Clusters are renumbered from lowest (0) to highest (3) median spend.
4. **Scores:** silhouette and Davies-Bouldin.
5. **Plot the clusters:** PC1 vs PC2, plus one picture for each cluster.
6. **How sure is the model:** certainty per customer, and a plot of the customers the model is not sure about.
7. **Describe each cluster:** median features, share of customers and share of spend.
8. **Save the results.**

## Results

**Choosing k:** BIC drops by 17,461 from 3 → 4 clusters, but only by 3,019 from 4 → 5. So **k = 4**.

**Scores:**

| Metric | Value | Meaning |
|---|---|---|
| Silhouette | 0.313 | Higher is better (−1 to 1) |
| Davies-Bouldin | 2.45 | Lower is better |

The clusters exist but overlap, which is normal for customer data.

**Segments:**

| Cluster | Segment | Customers | % of customers | % of spend |
|---|---|---|---|---|
| 0 | One-time buyers | 1,288 | 31% | 9% |
| 1 | Steady repeat buyers | 1,311 | 32% | 29% |
| 2 | Big baskets, slowing down | 360 | 9% | 14% |
| 3 | Frequent buyers who return items | 1,184 | 29% | 48% |

Cluster 3 is under a third of the customers but brings almost half of all spending.

**Certainty:**
- 84.2% of customers are assigned with 99% certainty or more.
- Only 64 customers (1.5%) are below 70% certainty.
- All 64 sit between cluster 2 and a neighbour: 36 with cluster 3, 28 with cluster 1. The model is never unsure about cluster 0.

## Output files

Saved in the same folder as the notebook, in the same row order as `customer_pca.csv`:

| File | Shape | Content |
|---|---|---|
| `labels_gmm.npy` | (4143,) | Cluster number (0–3) for each customer. Used in the team comparison. |
| `proba_gmm.npy` | (4143, 4) | Probability of each cluster for each customer |

## Strengths and weaknesses

**Strengths**
- Gives a probability for each cluster, so borderline customers can be found.
- Clusters can have different shapes and sizes.
- BIC gives a clear way to choose k.

**Weaknesses**
- k must still be chosen.
- Assumes each cluster is bell-shaped, which is not always true.
- Every customer is forced into a cluster. There is no "noise" label like in DBSCAN.
- Results depend on the random start, so `n_init=10` and `random_state=42` are used.
