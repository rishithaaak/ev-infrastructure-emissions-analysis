# EV Infrastructure, Adoption, and Carbon Emissions Analytics

This project analyzes how electric vehicle (EV) charging infrastructure relates to state-level EV adoption and transportation $CO_2$ emissions across the United States. It combines a large tabular dataset, web-scraped table data, and live REST API environmental metrics to evaluate where infrastructure gaps exist and whether increased charging capacity correlates with lower emissions.

---

## Technical Stack
* **Language:** Python
* **Data Processing:** Pandas, NumPy
* **Data Ingestion:** BeautifulSoup (HTML Scraping), Requests (REST API JSON parsing)
* **Modeling & Visualization:** Scikit-Learn (Linear Regression, KNN Regressor), Matplotlib, Seaborn

---

## Data Pipeline & Architecture

The pipeline ingests data from three distinct sources, normalizes their formats, and joins them on standard U.S. State abbreviations.

### 1. Source Datasets
* **Alternative Fuel Stations (AFDC Tabular Data):** 96,448 rows, 75 columns (.csv). Contains tracking data for alternative refueling stations, charging port levels (Level 1, Level 2, DC Fast), connector types, and network operators.
* **State EV Registrations (AFDC Web Data):** Extracted via `BeautifulSoup` from the AFDC state registration table to gather raw consumer EV ownership counts by state.
* **Transportation Emissions (EIA REST API):** Gathered via the Energy Information Administration (EIA) SEDS API. Returns annual state-level transportation energy statistics and carbon outputs in JSON format.

### 2. Data Cleaning & Data Inconsistency Resolution
To join the datasets reliably, the pipeline handles several specific data quality issues:
* **Missing Port Counts:** Stations with blank or "N/A" entries in their charging port counts are converted to numeric values. If the station status is "E" (Existing/Operational), the missing count defaults to `1` to prevent data loss.
* **Status Filtering:** The raw AFDC data includes both operational and planned locations. The pipeline filters out planned sites (Status Code "P") and retains only existing locations (Status Code "E") to eliminate future-planning noise.
* **String Sanitization:** Commas, footnotes, and non-numeric symbols are stripped from the scraped HTML registration tables using regular expressions before casting the data to integers.
* **Unit Standardization:** The EIA API returns emission values in mixed units (e.g., "Billion Btu" vs. "Million Metric Tons of $CO_2$"). The pipeline applies a constant conversion factor ($x_{\text{tons}} = x_{\text{btu}} \times 0.00005307$) to standardize all environmental metrics.

---

## Modeling and Statistical Results

Once merged into a unified state-by-state dataframe, the data was used to train two separate predictive models to test our core hypotheses.

### Linear Model: Deployed Infrastructure vs. Consumer Adoption
We applied an Ordinary Least Squares (OLS) Linear Regression model to test if public charging port availability reliably predicts EV adoption.
* **Formula:** $$\hat{y}_{\text{registrations}} = \beta_0 + \beta_1 \cdot x_{\text{total\_chargers}}$$

* **Intercept ($\beta_0$):** $-12,715.59$
* **Slope ($\beta_1$):** $31.78$
* **$R^2$ Score:** **0.988**

**Takeaway:** The model demonstrates an incredibly tight linear fit ($R^2 = 0.988$). The slope indicates that, on average, every single public charging port deployed in a state corresponds to approximately 32 registered electric vehicles.

### Non-Linear Model: Electrification Metrics vs. $CO_2$ Emissions
We trained a $K$-Nearest Neighbors (KNN) Regressor ($K=3$) using total ports and EV registrations as input features to predict a state's total transportation $CO_2$ emissions.
* **$R^2$ Score:** **0.332**
* **Root Mean Squared Error (RMSE):** $34.74$ Million Metric Tons

**Takeaway:** The low $R^2$ score highlights that state-level transportation emissions cannot be predicted solely by looking at EV adoption and charging ports. Other heavy variables—such as total vehicle miles traveled (VMT), commercial freight volume, and the carbon intensity of the local power grid—play massive roles in a state's net carbon output.

---

## Core Infrastructure Insights

### 1. Port Distribution by Charger Type
The vast majority of the public infrastructure is dominated by Level 2 chargers, which are suited for overnight or workplace charging rather than rapid highway travel.

| Charger Level | Total Ports | Percentage Share |
| :--- | :--- | :--- |
| **Level 1** | 38,938 | 28.92% |
| **Level 2** | 79,306 | 58.90% |
| **DC Fast** | 16,390 | 12.17% |

### 2. Commercial DC Fast Charger Placement
When filtering specifically for rapid DC Fast Charging, infrastructure is heavily concentrated around commercial shopping centers and vehicle transit points:
* **Shopping Malls:** 80.19% of stations include DC Fast ports
* **Shopping Centers:** 43.30% of stations include DC Fast ports
* **Car Dealerships:** 31.67% of stations include DC Fast ports

### 3. Geographic Infrastructure Concentration
The dataset shows a stark geographic imbalance in infrastructure deployment. The top 10 states control a disproportionate volume of the nation's total charging capacity, led heavily by California:

```
 Rank   State   Total Stations   Total Public Ports
---------------------------------------------------
  1      CA          9,868             33,979
  2      NY          2,118              7,246
  3      FL          1,611              5,504
  4      TX          1,823              5,272
  5      MA          1,567              4,737
  6      WA          1,169              3,808
  7      CO          1,156              3,746
  8      GA          1,004              3,323
  9      MD            876              2,979
 10      PA            973              2,788
```

---

## File Structure
```
├── data/                    # Local CSVs and processed API outputs
├── src/                     # Source code scripts
│   ├── ingest_engine.py     # Scraping, cleaning, and API requests
│   └── analysis.py          # OLS and KNN modeling scripts
├── requirements.txt         # Project dependencies
└── README.md                # Project documentation
```
