# Hierarchical (Agglomerative) Clustering

Create your notebook in this folder as **`hierarchical/hierarchical.ipynb`**. The data is not copied here: it lives in `../data/processed/` (see [`../data/README.md`](../data/README.md) for what each file contains).

## Load the data
```python
import pandas as pd

BASE = "../data/processed/"   # notebook inside hierarchical/ after git clone
# BASE = "https://raw.githubusercontent.com/JanaAlzobidi/Machine-Learning-Projects/main/Unit-5-Unsupervised-Learning/customer-segmentation-clustering/data/processed/"  # Google Colab

Z = pd.read_csv(BASE + "customer_pca.csv", index_col="CustomerID")                       # cluster this: 4,143 x 5
info = pd.read_csv(BASE + "customer_features.csv", index_col="CustomerID").loc[Z.index]   # describe clusters with this
```
| file | what it is |
|---|---|
| `customer_pca.csv` | **cluster this**: 5 PCA components for 4,143 customers |
| `customer_features.csv` | the 12 readable features (spend, invoices, recency, …) for describing clusters |
| `customer_pca_all.csv` | optional: the same space plus 219 extreme customers (`IsOutlier`) |

All 4,143 customers fit comfortably. `scipy.cluster.hierarchy.linkage(Z, "ward")` takes under a second, so no sampling is needed.

## What to do
1. **Compare linkage methods**: `ward`, `complete`, `average`, `single`. For each, report **cluster sizes as well as the silhouette**.
2. Draw the **dendrogram** (`truncate_mode="lastp", p=30` keeps it readable) and choose where to cut. Big vertical jumps mean well-separated groups.
3. Fit the final model with `AgglomerativeClustering(n_clusters=k, linkage=...)`.
4. Plot the clusters on **PC1 vs PC2** and profile them with `info`, giving each cluster a name.
5. **Noise test (optional):** rerun on `customer_pca_all.csv` (drop the `IsOutlier` column first) and see whether the extreme customers become tiny clusters of their own.

## Warning: do not pick the linkage by silhouette alone
In a quick test with 4 clusters, **single linkage had the highest silhouette (0.70) but put 4,138 of the 4,143 customers into one cluster**, and average linkage did almost the same. Ward gave four groups of reasonable size. The high score comes from separating a few odd customers, not from finding segments, and it is a good point for the critical-thinking section.

## Rules
- **Do not rescale** `customer_pca.csv`. It is already prepared, and rescaling would make PC5 as important as PC1.
- Cluster on the PCA file, but describe clusters with the readable features.

## Save your labels
```python
import numpy as np
np.save("labels_hierarchical.npy", hc.labels_)   # saved in this folder, same row order as customer_pca.csv
```
On Colab, save `labels_hierarchical.npy` locally and upload it to this folder on GitHub.

## What to report (same for all four algorithms)
| item | how |
|---|---|
| number of clusters | and where you cut the dendrogram |
| cluster sizes | count per cluster, for every linkage you tried |
| silhouette score | `silhouette_score(Z, labels)` |
| Davies-Bouldin | `davies_bouldin_score(Z, labels)` (lower is better) |
| cluster shape | what your PC1/PC2 plot shows |
| sensitivity to noise | how the extreme customers affect each linkage |
| profiles | medians of the readable features per cluster, plus a name for each |
| strengths / weaknesses / when to use | from what you saw on **this** data |
