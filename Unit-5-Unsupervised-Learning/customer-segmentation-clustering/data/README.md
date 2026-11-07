# Data

This folder holds everything the four algorithms need: the raw data, the notebook that prepares it, and the prepared files they cluster.

## Structure
```
data/
├── README.md
├── 01_data_preparation.ipynb
├── raw/
│   └── data.csv.gz
└── processed/
    ├── customer_pca.csv
    ├── customer_features.csv
    └── customer_pca_all.csv
```

## What we did
| step | what | result |
|---|---|---|
| 1. Clean | removed rows with no customer ID, duplicates, postage/fees and zero prices | 399,573 invoice lines, 4,362 customers |
| 2. One row per customer | summarised each customer in 12 features (recency, invoices, spend, products, cancellations, monthly spend, trend…) | 4,362 customers x 12 features |
| 3. Set aside extreme customers | Isolation Forest flagged the 5% most extreme | 219 set aside, 4,143 kept |
| 4. Transform | Yeo-Johnson + standardisation, so huge values don't dominate | all features on the same scale |
| 5. PCA | merged the 12 features into 5 components | 89% of the information kept |

## Files
| file | what it is | who uses it |
|---|---|---|
| `raw/data.csv.gz` | the original 541,909 invoice lines (UK online gift retailer, Dec 2010 - Dec 2011), compressed | the preparation notebook and the GMM notebook |
| `01_data_preparation.ipynb` | the steps above; it reads the raw file and writes the files in `processed/` | already run, rerun only if the preparation changes |
| `processed/customer_pca.csv` | 4,143 customers x 5 PCA components | **all four algorithms**: this is what we cluster |
| `processed/customer_features.csv` | the 12 readable features + `Country` + `IsOutlier` for all 4,362 customers | **all four algorithms**: to describe the clusters |
| `processed/customer_pca_all.csv` | the same 5 components including the 219 extreme customers, + `IsOutlier` | **DBSCAN**: to test whether it finds the extreme customers as noise |

Source: Online Retail dataset ([UCI](https://archive.ics.uci.edu/dataset/352/online+retail)), as used in the Kaggle notebook [Customer Segmentation & Recommendation System](https://www.kaggle.com/code/farzadnekouei/customer-segmentation-recommendation-system).
