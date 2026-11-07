# K-Means

Create your notebook in this folder as **`kmeans/kmeans.ipynb`**. The data is not copied here: it lives in `../data/processed/` (see [`../data/README.md`](../data/README.md) for what each file contains).

## Load the data
```python
import pandas as pd

BASE = "../data/processed/"   # notebook inside kmeans/ after git clone
# BASE = "https://raw.githubusercontent.com/JanaAlzobidi/Machine-Learning-Projects/main/Unit-5-Unsupervised-Learning/customer-segmentation-clustering/data/processed/"  # Google Colab

Z = pd.read_csv(BASE + "customer_pca.csv", index_col="CustomerID")                       # cluster this: 4,143 x 5
info = pd.read_csv(BASE + "customer_features.csv", index_col="CustomerID").loc[Z.index]   # describe clusters with this
```
| file | what it is |
|---|---|
| `customer_pca.csv` | **cluster this**: 5 PCA components for 4,143 customers |
| `customer_features.csv` | the 12 readable features (spend, invoices, recency, …) for describing clusters |
| `customer_pca_all.csv` | optional: the same space plus 219 extreme customers (`IsOutlier`) |

## What to do
1. **Choose k**: run k = 2-10 and plot the **elbow** (`inertia_`) and the **silhouette score** side by side.
2. Fit the final model with `KMeans(n_clusters=k, n_init=10, random_state=42)`.
3. Plot the clusters on **PC1 vs PC2** with the centroids marked.
4. Profile the clusters with `info` (medians per cluster) and give each cluster a name.
5. **Noise test (optional):** fit on `customer_pca_all.csv` (drop the `IsOutlier` column first) and see how far the centroids move.

## Hints from a quick test
- The silhouette kept rising up to k = 5 in a quick check, so do not stop at the first k that looks good. Look at the elbow and the silhouette together.
- K-Means assumes round clusters of similar size. Compare your plot with the GMM notebook, where the fitted clusters are stretched ellipses of very different sizes.

## Rules
- **Do not rescale** `customer_pca.csv`. It is already prepared, and rescaling would make PC5 as important as PC1.
- Cluster on the PCA file, but describe clusters with the readable features ("frequent, high-spend buyers", not "high PC1").

## Save your labels
```python
import numpy as np
np.save("labels_kmeans.npy", km.labels_)   # saved in this folder, same row order as customer_pca.csv
```
On Colab, save `labels_kmeans.npy` locally and upload it to this folder on GitHub.

## What to report (same for all four algorithms)
| item | how |
|---|---|
| number of clusters | and how you chose it |
| cluster sizes | count per cluster |
| silhouette score | `silhouette_score(Z, labels)` |
| Davies-Bouldin | `davies_bouldin_score(Z, labels)` (lower is better) |
| cluster shape | what your PC1/PC2 plot shows |
| sensitivity to noise | how the extreme customers affect K-Means |
| profiles | medians of the readable features per cluster, plus a name for each |
| strengths / weaknesses / when to use | from what you saw on **this** data |
