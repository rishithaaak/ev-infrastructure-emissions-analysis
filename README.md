# EV Infrastructure, Adoption, and Carbon Emissions Analysis

This project looks at how electric vehicle (EV) charging infrastructure relates to state-level EV adoption and transportation CO2 emissions across the US. I combined a large tabular dataset, web-scraped table data, and live REST API environmental metrics to see where infrastructure gaps exist and whether adding more chargers actually correlates with lower emissions.

---

## Technical Stack
* **Language:** Python
* **Data Processing:** Pandas, NumPy
* **Data Ingestion:** BeautifulSoup (HTML Scraping), Requests (REST API JSON parsing)
* **Modeling & Visualization:** Scikit-Learn (Linear Regression, KNN Regressor), Matplotlib, Seaborn

---

## Data Pipeline & Cleaning

The pipeline pulls data from three different places, normalizes the formats, and joins them using standard U.S. State abbreviations.

### 1. The Datasets
* **Alternative Fuel Stations:** A large CSV (96,448 rows) containing tracking data for alternative refueling stations, charging port levels (Level 1, Level 2, DC Fast), connector types, and network operators.
* **State EV Registrations:** Extracted via BeautifulSoup from the AFDC state registration table to get raw consumer EV ownership counts by state.
* **Transportation Emissions:** Gathered via the Energy Information Administration (EIA) SEDS API, which returns annual state-level transportation energy stats and carbon outputs in JSON format.

### 2. Cleaning and Fixing Inconsistencies
To join the datasets reliably, I handled a few data quality issues:
* **Missing Port Counts:** For operational stations with blank or "N/A" entries in their port counts, I defaulted the missing count to 1 to preserve the location without losing data.
* **Status Filtering:** The raw data included future planned locations. I filtered these out and kept only existing, operational locations to keep the analysis accurate.
* **String Sanitization:** Cleaned up commas and footnotes from the scraped HTML registration tables using regular expressions so they could be converted to integers.
* **Unit Standardization:** The EIA API returned emission values in mixed units (like Billion Btu vs. Million Metric Tons of CO2). I applied a conversion factor to standardize everything into carbon emissions mass.

---

## Modeling and Results

Once everything was merged into a single state-by-state dataframe, I ran two separate predictive models to test our core hypotheses.

### Linear Model: Public Chargers vs. EV Adoption
I used a Linear Regression model to see if public charging port availability reliably predicts EV adoption.

* **R² Score:** 0.988
* **Takeaway:** The model shows an incredibly tight linear fit. The slope indicates that, on average, every single public charging port deployed in a state corresponds to roughly 32 registered electric vehicles.

### Non-Linear Model: Electrification Metrics vs. CO2 Emissions
I trained a K-Nearest Neighbors (KNN) Regressor using total ports and EV registrations as input features to try and predict a state's total transportation CO2 emissions.

* **R² Score:** 0.332
* **Takeaway:** The low R² score highlights that state-level transportation emissions cannot be predicted just by looking at EV adoption and charging ports. Other massive variables—like total vehicle miles traveled, commercial freight volume, and the carbon intensity of the local power grid—play a much bigger role in a state's net carbon footprint.

---

## Key Infrastructure Insights

### 1. Port Distribution by Charger Type
The vast majority of public infrastructure is dominated by Level 2 chargers, which are best for overnight or workplace charging rather than rapid highway travel.
* **Level 1 Ports:** 38,938 (28.92%)
* **Level 2 Ports:** 79,306 (58.90%)
* **DC Fast Nodes:** 16,390 (12.17%)

### 2. Where are DC Fast Chargers Placed?
When filtering specifically for rapid DC Fast Charging, the infrastructure is heavily concentrated around commercial shopping centers and vehicle transit points:
* **Shopping Malls:** 80.19% of stations include DC Fast ports
* **Shopping Centers:** 43.30% of stations include DC Fast ports
* **Car Dealerships:** 31.67% of stations include DC Fast ports

### 3. Geographic Concentration
The data shows a massive geographic imbalance in infrastructure deployment. The top 10 states control a huge chunk of the nation's total charging capacity, heavily led by California.

---

## File Structure
* **data/**: Local CSVs and processed API outputs
* **src/**: Ingestion, scraping, and modeling scripts
* **requirements.txt**: Project dependencies
* **README.md**: Project documentation
