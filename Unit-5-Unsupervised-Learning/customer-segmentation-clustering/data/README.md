# Data

Everything the four algorithms need lives in this folder: the raw data, the notebook that prepares it, and the prepared files. The algorithm folders (`kmeans/`, `dbscan/`, `hierarchical/`, `gmm/`) contain **no copies of the data**. They read it from here.

```
data/
├── README.md                     this file
├── 01_data_preparation.ipynb     turns the raw data into the prepared files
├── raw/
│   └── data.csv.gz               raw transactions (gzip, 7.5 MB instead of 45 MB)
└── processed/
    ├── customer_pca.csv                   ← main clustering input
    ├── customer_features.csv              ← readable features, to describe clusters
    ├── customer_pca_all.csv               ← main input + the extreme customers
    └── customer_features_transformed.csv  ← optional experiment
```

**In one line:** 541,909 invoice lines → cleaned → one row per customer with 12 features → 5% extreme customers set aside → Yeo-Johnson transform → 5 PCA components.

---

## 1. The raw data (`raw/data.csv.gz`)

All transactions of a **UK online gift retailer** (most customers are wholesalers) from **01/12/2010 to 09/12/2011**. Each row is **one product on one invoice**, not one customer. An invoice with 10 products takes 10 rows.

**541,909 rows x 8 columns:**

| column | meaning | example |
|---|---|---|
| `InvoiceNo` | invoice number; starting with **C** means a **cancellation** | 536365 |
| `StockCode` | product code | 85123A |
| `Description` | product name | WHITE HANGING HEART T-LIGHT HOLDER |
| `Quantity` | items on this line, **negative** for cancellations | 6 |
| `InvoiceDate` | date and time of the invoice | 12/1/2010 8:26 |
| `UnitPrice` | price per item in pounds (£) | 2.55 |
| `CustomerID` | customer number, **missing in 24.93% of rows** | 17850 |
| `Country` | customer's country | United Kingdom |

Source: the Online Retail dataset, as used in the Kaggle notebook [Customer Segmentation & Recommendation System](https://www.kaggle.com/code/farzadnekouei/customer-segmentation-recommendation-system), originally from the [UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/352/online+retail) (Chen, Sain & Guo, 2012).

---

## 2. What we did (`01_data_preparation.ipynb`)

### Step 1: Cleaning
| removed | rows removed | why |
|---|---|---|
| no `CustomerID` or `Description` | 135,080 | we cluster customers, so anonymous rows cannot be used |
| exact duplicate rows | 5,225 | recording errors, not real repeat purchases |
| postage, fees, manual adjustments (`POST`, `M`, `D`, `BANK CHARGES`, …) | 1,998 | not products |
| zero unit price | 33 | data-entry errors |

**Left: 399,573 lines from 4,362 customers.** Cancellations (2.1% of lines) are kept, because how often a customer cancels is useful information.

### Step 2: One row per customer
Each customer's year is summarised in **12 features**:

| feature | meaning | typical customer (median) |
|---|---|---|
| `Recency_Days` | days since the last purchase | 50 |
| `N_Invoices` | number of invoices | 3 |
| `Total_Quantity` | items bought, net of returns | 365 |
| `Total_Spend` | money spent, net of returns (£) | 642 |
| `Unique_Products` | different products bought | 35 |
| `Avg_Invoice_Value` | average invoice value (£) | 237 |
| `Avg_Days_Between_Purchases` | average gap between purchase days (0 = bought on one day only) | 31 |
| `N_Cancellations` | cancelled invoices | 0 |
| `Cancellation_Rate` | share of invoices cancelled | 0 |
| `Monthly_Spend_Mean` | average spend per active month (£) | 304 |
| `Monthly_Spend_Std` | how much monthly spend varies (£) | 63 |
| `Spend_Trend` | change in monthly spend over time (£ per month) | 0 |

The typical customer is small, but the largest are hundreds of times bigger: up to 242 invoices and £278,778 spent. The next two steps deal with that.

Compared with the original Kaggle pipeline we dropped "favourite weekday" and "favourite hour", which are categories stored as numbers. We also fixed how purchase gaps and spending trends are computed. The notebook explains each change.

### Step 3: Setting aside extreme customers
An **Isolation Forest** flags the most isolated **5% (219 customers)**: heavy accounts with a median of 16 invoices and £8,568 spent, against 2 invoices and £605 for a typical customer. They are left out of the main file but **not deleted**. `customer_pca_all.csv` keeps them for noise tests.

### Step 4: Yeo-Johnson transform
This works like a log but also accepts zeros and negative values. It shrinks the huge values and puts every feature on the same scale (mean 0, standard deviation 1), so money does not outweigh days or counts. It is fitted on the regular customers and then applied to the extreme ones, so everyone lives in the same space.

### Step 5: PCA
The 12 features are merged into **5 uncorrelated components that keep 89% of the information**. This removes duplicated information (for example, number of cancellations and cancellation rate correlate at 0.98).

| component | variance | roughly means |
|---|---|---|
| PC1 | 44% | overall activity: invoices, items, spend, product range |
| PC2 | 20% | basket size vs. cancellations |
| PC3 | 11% | cancellations vs. long gaps between purchases |
| PC4 | 8% | spending trend |
| PC5 | 6% | recency (how long since the last purchase) |

---

## 3. The prepared files (`processed/`)

| file | rows x cols | contents |
|---|---|---|
| `customer_pca.csv` | 4,143 x 5 | PC1-PC5 for the regular customers |
| `customer_features.csv` | 4,362 x 14 | the 12 readable features + `Country` + `IsOutlier`, for every customer |
| `customer_pca_all.csv` | 4,362 x 6 | PC1-PC5 for **all** customers + `IsOutlier`; regular customers have exactly the values in `customer_pca.csv` |
| `customer_features_transformed.csv` | 4,143 x 12 | the 12 features after Yeo-Johnson, before PCA |

All files are indexed by `CustomerID`, with the regular customers in the same order everywhere.

---

## 4. Who needs what, and why

| who | files | why |
|---|---|---|
| **K-Means** | `customer_pca.csv`, `customer_features.csv` (optional: `customer_pca_all.csv`) | cluster on the PCA file and describe clusters with the readable features; the optional file shows how far the extreme customers pull the centroids |
| **DBSCAN** | `customer_pca.csv`, `customer_pca_all.csv`, `customer_features.csv` | `customer_pca_all.csv` is **essential**: the main file has the extreme customers removed, which hides DBSCAN's strength. This file shows whether DBSCAN labels them as noise by itself |
| **Hierarchical** | `customer_pca.csv`, `customer_features.csv` (optional: `customer_pca_all.csv`) | the optional file shows whether extreme customers become tiny clusters of their own |
| **GMM** | `raw/data.csv.gz` + `customer_pca.csv`, `customer_features.csv` | the GMM notebook rebuilds the data from the raw file (its product suggestions need the invoice lines) and checks that it gets exactly `customer_pca.csv` |
| **Final comparison** | `customer_pca.csv`, `customer_features.csv` + each folder's `labels_<algorithm>.npy` | same rows for every algorithm, so the labels can be compared side by side |
| anyone, optional | `customer_features_transformed.csv` | test whether skipping PCA changes your result |

**Why everyone clusters `customer_pca.csv`:** if every algorithm gets the same input, any difference in the results comes from the algorithms and not from the data. PCA also removes duplicated features, makes distances clearer in fewer dimensions, and gives everyone the same PC1/PC2 view for plots.

**Why describe clusters with `customer_features.csv`:** "frequent buyers who spend a lot" means something to a reader, while "high PC1" does not.

---

## 5. How to load the data

From a notebook inside an algorithm folder (after `git clone`):
```python
import pandas as pd

BASE = "../data/processed/"
# Google Colab (repository must be public):
# BASE = "https://raw.githubusercontent.com/JanaAlzobidi/Machine-Learning-Projects/main/Unit-5-Unsupervised-Learning/customer-segmentation-clustering/data/processed/"

Z = pd.read_csv(BASE + "customer_pca.csv", index_col="CustomerID")                       # cluster this
info = pd.read_csv(BASE + "customer_features.csv", index_col="CustomerID").loc[Z.index]   # describe with this
```

## 6. Rules for a fair comparison
1. **Do not rescale** the PCA files. They are already prepared, and rescaling would make PC5 (6% of the information) as important as PC1 (44%).
2. Keep the row order: save your labels as `labels_<algorithm>.npy` **in your own folder**, in the row order of `customer_pca.csv`. DBSCAN noise stays `-1`.
3. Use `random_state=42` wherever there is randomness.

## 7. Rerunning the preparation
You only need this if the preparation itself changes. Open `01_data_preparation.ipynb` **from this folder** and run all cells. It reads `raw/data.csv.gz` and overwrites the files in `processed/`, and everyone must then use the new files.
