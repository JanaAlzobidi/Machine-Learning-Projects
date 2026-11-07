# Customer Segmentation: Comparing Four Clustering Algorithms

We segment the customers of a UK online gift retailer with **K-Means, DBSCAN, Hierarchical clustering and Gaussian Mixture Models**, all run on exactly the same prepared data, and then compare how each algorithm behaves.

| Algorithm | Folder | Owner |
|---|---|---|---|
| K-Means | [`kmeans/`](kmeans/) | LEEN |
| DBSCAN | [`dbscan/`](dbscan/) | AHMAD |
| Hierarchical | [`hierarchical/`](hierarchical/) | Abdulrahman |
| Gaussian Mixture Model | [`gmm/`](gmm/) | JANA |

## Structure
```
customer-segmentation-clustering/
├── data/            raw data, the preparation notebook, the prepared files + README
├── kmeans/          README → notebook + 
├── dbscan/          instructions (README) → notebook + labels_dbscan.npy go here
├── hierarchical/    instructions (README) → notebook + labels_hierarchical.npy go here
├── gmm/             GMM notebook, README and results
├── requirements.txt
└── README.md
```
All data lives in [`data/`](data/). Its README explains what we did, what each file contains, and who needs which file and why. The algorithm folders contain no copies of the data.

## Quick start
```bash
git clone https://github.com/JanaAlzobidi/Machine-Learning-Projects.git
cd Machine-Learning-Projects/Unit-5-Unsupervised-Learning/customer-segmentation-clustering
pip install -r requirements.txt
```
Then open the README in your algorithm's folder. The prepared data is already in `data/processed/`, so you do not need to rerun the preparation.

## Rules for a fair comparison
1. Everyone clusters `data/processed/customer_pca.csv` **without rescaling it**.
2. Everyone describes clusters with `data/processed/customer_features.csv`.
3. Everyone saves `labels_<algorithm>.npy` **in their own folder**, in the row order of `customer_pca.csv` (DBSCAN noise = `-1`).
4. Everyone reports the same things: number of clusters, sizes, silhouette, Davies-Bouldin, a PC1/PC2 plot, sensitivity to noise, strengths and weaknesses.
5. `random_state=42` wherever there is randomness.

## Results so far
**GMM** (4 components, full covariance, silhouette 0.31): one-time buyers (31% of customers, 9% of spend), steady repeat buyers (32%, 29%), big baskets slowing down (9%, 14%), frequent buyers who return items (29%, 48%). Details in [`gmm/`](gmm/).

## Requirements
Python 3.10+. Tested with pandas 2.2 and 3.0, scikit-learn 1.5 and 1.9.

## Data source and acknowledgement
Online Retail dataset ([UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/352/online+retail)), as used in Farzad Nekouee's Kaggle notebook [Customer Segmentation & Recommendation System](https://www.kaggle.com/code/farzadnekouei/customer-segmentation-recommendation-system), from which the preparation pipeline is adapted. Every change is explained in `data/01_data_preparation.ipynb`.
