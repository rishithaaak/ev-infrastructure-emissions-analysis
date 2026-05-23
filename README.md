# Electric Vehicle Infrastructure and Regional Emissions Analysis

This repository contains a data analytics framework designed to evaluate the relationship between public electric vehicle (EV) charging infrastructure deployment, regional consumer EV adoption, and total transportation sector carbon dioxide emissions across the United States. The project consolidates a heavy tabular location dataset, web-scraped market distribution registries, and structured REST API environmental indexes to determine regional asset coverage and evaluate net emission correlations.

---

## Technical Stack

* **Programming Language:** Python
* **Data Management:** Pandas, NumPy
* **Data Ingestion Subsystem:** BeautifulSoup (HTML Document Parsing), Requests (REST API Integration)
* **Statistical Modeling & Graphics:** Scikit-Learn (Linear and Non-Linear Regression), Matplotlib, Seaborn

---

## Data Engineering and Core Operations

The pipeline standardizes, processes, and matches records from three distinct data environments using standard U.S. State structural identifiers.

### Data Environment Inventories
* **Alternative Fuel Stations (Tabular):** An industry registry containing 96,448 infrastructure records documenting charging node classifications, port count attributes, hardware connector profiles, and administrative operator networks.
* **State EV Registrations (Web-Scraped):** A state-by-state market summary extracted directly from the HTML structures of the Alternative Fuel Data Center table matrix.
* **Transportation Emissions (API JSON Payload):** Long-term carbon tracking vectors queried programmatically from the Energy Information Administration (EIA) State Energy Data System.

### Cleaning Routines and Inconsistency Reductions
To guarantee structural alignment across all inputs, the data processing workflow corrects several systemic formatting variances using Pandas:
* **Asset Allocation Defaults:** For active, existing refueling nodes with missing data points or character strings in their terminal hardware columns, a default base unit value of 1 is applied to preserve the facility's location record.
* **Operational Status Constraints:** Future planned locations are removed from the tabular data matrices to prevent artificial capacity skews, leaving only verified operational infrastructure.
* **Text Array Sanitization:** Footnotes, punctuation flags, and comma thousands separators are removed from the web-scraped table string elements via regular expressions before casting the fields to integers.

---

## Analytical Modeling and Empirical Results

Using the completed dataset, two distinct validation pipelines were executed to test the underlying system behavior.

### Linear Regression Framework: Infrastructure Density vs. Adoption Volume
An Ordinary Least Squares model evaluated whether public charging availability acts as a reliable predictor for state-level vehicle procurement.

* **Coefficient of Determination (R²):** 0.988
* **Analysis:** The model exhibits an exceptionally strong linear relationship. The calculated slope establishes that the deployment of a single public charging port statistically corresponds with an approximate increase of 32 localized vehicle registrations.

### Instance-Space Modeling: Fleet Electrification vs. Macro Sector Emissions
A K-Nearest Neighbors Regressor (K=3) mapped total charging nodes and fleet volumes against total annual transportation emissions to evaluate direct carbon containment.

* **Coefficient of Determination (R²):** 0.332
* **Analysis:** The low fit metric confirms that state-level transportation carbon output cannot be modeled through a function of electric vehicle deployment parameters alone. Total net emission metrics remain structurally tied to broader macro variables, including annual vehicle miles traveled, commercial freight network volume, and the generation fuel mix of regional utility grids.

---

## Strategic System Metrics

### Hardware Node Distribution by Power Class
Pandas descriptive aggregation establishes that the domestic public infrastructure network is predominantly composed of destination hardware rather than high-speed transit equipment:
* **Level 1 Charging Ports:** 38,938 (28.92%)
* **Level 2 Charging Ports:** 79,306 (58.90%)
* **DC Fast Charging Ports:** 16,390 (12.17%)

### Commercial Point-of-Interest Capture Rates
Filtering data specifically for high-speed DC Fast equipment highlights that fast-charging resources are heavily concentrated within retail destination hubs:
* **Shopping Malls:** 80.19% of stations contain fast-charging infrastructure
* **Shopping Centers:** 43.30% of stations contain fast-charging infrastructure
* **Automotive Dealership Networks:** 31.67% of stations contain fast-charging infrastructure

### Geographic Concentration Metrics
The aggregated data registers a severe geographic imbalance in overall infrastructure volume across the country, where the top ten states maintain a disproportionate share of total active charging assets, led heavily by the state of California.

---

## File System Structure

* **data/**: Directory containing raw data downloads and processed pipeline dataframes.
* **src/**: Directory containing Python ingestion engines, scraping algorithms, and modeling routines.
* **requirements.txt**: Plain text tracking manifest documenting Python library dependencies.
* **README.md**: Standard documentation rendering page.
