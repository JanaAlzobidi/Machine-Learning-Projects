# Evaluating Clustering Before and After PCA

A mini project that studies how dimensionality reduction (PCA) affects clustering. We apply K-Means to the Wine dataset before and after reducing it to 2 principal components, then compare the results.

## Dataset
**Wine Dataset** from scikit-learn (`sklearn.datasets.load_wine`)
- 178 samples
- 13 numerical features (chemical properties such as alcohol, flavanoids and proline)
- 3 wine classes

## Project Steps
1. **Preprocessing:** checked for missing values (none found), split the data into 80% train and 20% test, and applied standardization with `StandardScaler`.
2. **K-Means before PCA:** applied K-Means (K = 3) on the 13 scaled features.
3. **PCA:** reduced the data to 2 principal components.
4. **K-Means after PCA:** applied K-Means on the 2 PCA components.
5. **Visualization:** plotted both results and the true classes on the PCA components.
6. **Evaluation:** compared the results using the Silhouette Score and the Adjusted Rand Index (ARI) on the train and test sets.
7. **Bonus:** tried different numbers of PCA components, and compared K-Means with DBSCAN.

## Results

| Metric | Before PCA (13 features) | After PCA (2 components) |
|---|---|---|
| Silhouette (train) | 0.2748 | 0.5482 |
| Silhouette (test) | 0.3371 | 0.6083 |
| ARI (train) | 0.8920 | 0.8914 |
| ARI (test) | 0.9246 | 1.0000 |

- The 2 PCA components kept about **55%** of the total variance.
- DBSCAN performed worse than K-Means (ARI **0.35** before PCA and **0.45** after PCA).

## Key Findings
- The Silhouette Score doubled after PCA, but the ARI stayed almost the same, so the clusters themselves did not really change.
- Silhouette Scores calculated on different numbers of features should not be compared directly.
- PCA made the results much easier to visualize.
- K-Means after PCA is a good choice for this dataset: it gives almost the same results with only 2 features instead of 13.


## Files
- `Evaluating_Clustering_Before_and_After_PCA.ipynb`: the full project notebook (code, visualizations, analysis and key insights)
- `README.md`: project description

## Team Members
| Member | Contribution |
|---|---|
| Jana | Data loading, preprocessing, K-Means before PCA |
| Leen | PCA, K-Means after PCA, visualization |
| Abdulrahman | Test set evaluation, ARI comparison, PCA components experiment |
| Ahmed | DBSCAN comparison, write the insights and Key Findings |
