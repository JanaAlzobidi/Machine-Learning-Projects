# DBSCAN

Create your notebook in this folder as **`dbscan/dbscan.ipynb`**. The data is not copied here: it lives in `../data/processed/` (see [`../data/README.md`](../data/README.md) for what each file contains).

## Load the data
```python
import pandas as pd

BASE = "../data/processed/"   # notebook inside dbscan/ after git clone
# BASE = "https://raw.githubusercontent.com/JanaAlzobidi/Machine-Learning-Projects/main/Unit-5-Unsupervised-Learning/customer-segmentation-clustering/data/processed/"  # Google Colab

Z = pd.read_csv(BASE + "customer_pca.csv", index_col="CustomerID")                       # cluster this: 4,143 x 5
info = pd.read_csv(BASE + "customer_features.csv", index_col="CustomerID").loc[Z.index]   # describe clusters with this

A = pd.read_csv(BASE + "customer_pca_all.csv", index_col="CustomerID")   # noise test: 4,362 customers
P = A.drop(columns="IsOutlier")                                            # the 5 components
```
| file | what it is |
|---|---|
| `customer_pca.csv` | **cluster this**: 5 PCA components for 4,143 customers |
| `customer_pca_all.csv` | the same space **plus the 219 set-aside customers** (column `IsOutlier`); regular customers have exactly the values in `customer_pca.csv` |
| `customer_features.csv` | the 12 readable features (spend, invoices, recency, …) for describing clusters |

## What to do
1. **min_samples**: start with **10**, twice the number of dimensions (5).
2. **eps**: draw a **k-distance plot**: the distance of every customer to its 10th nearest neighbour, sorted. The bend in the curve is a good eps. Reasonable values here are roughly 0.4-1.0.
3. **Show the sensitivity**: a small table or plot of eps against the number of clusters and the share of noise. On this data, noise goes from about half the customers at eps = 0.4 to about 5% at eps = 1.0, so this is a strong point for the comparison.
4. Plot the clusters on **PC1 vs PC2** with the **noise in grey**.
5. Profile the clusters with `info` and give each cluster a name.
6. **Noise test (important):** the main file has the 5% most extreme customers removed, which hides DBSCAN's main strength. Run DBSCAN on `P` as well and check how many of the `IsOutlier` customers it labels as noise by itself.

## Silhouette with noise
Compute the silhouette **without** the noise points (`labels != -1`) and **always report the noise share next to it**. Dropping noise inflates the score.

## Rules
- **Do not rescale** the PCA files. They are already prepared, and rescaling would make PC5 as important as PC1.
- Cluster on the PCA file, but describe clusters with the readable features.

## Save your labels
```python
import numpy as np
np.save("labels_dbscan.npy", db.labels_)   # saved in this folder, same row order as customer_pca.csv, noise stays -1
```
On Colab, save `labels_dbscan.npy` locally and upload it to this folder on GitHub.

## What to report (same for all four algorithms)
| item | how |
|---|---|
| number of clusters | and the eps / min_samples you chose |
| cluster sizes | count per cluster, plus the noise count |
| silhouette score | without noise, with the noise share next to it |
| Davies-Bouldin | `davies_bouldin_score` on the non-noise points (lower is better) |
| cluster shape | what your PC1/PC2 plot shows |
| sensitivity to noise | the `customer_pca_all.csv` test |
| profiles | medians of the readable features per cluster, plus a name for each |
| strengths / weaknesses / when to use | from what you saw on **this** data |
