# EV Infrastructure, Adoption, and Carbon Emissions Analysis

This project looks at how electric vehicle (EV) charging infrastructure relates to state-level EV adoption and transportation CO2 emissions across the US. I combined a large tabular dataset, web-scraped table data, and live REST API environmental metrics to see where infrastructure gaps exist and whether adding more chargers actually correlates with lower emissions.

---

## Technical Stack
* **Language:** Python
* **Data Core (Pandas & NumPy):** Used to load raw files, handle heavy data-cleaning matrices, filter features, and merge separate tables into a single database.
* **Visualization (Seaborn & Matplotlib):** Used to plot trends and analyze statistical distributions directly out of our dataframes.
* **Machine Learning (Scikit-Learn):** Used for running Linear Regression and KNN Regressor models.

---

## Data Pipeline & Cleaning with Pandas

The pipeline uses Pandas to pull data from three completely different sources, clean up their structural inconsistencies, and join them together using standard U.S. State abbreviations.

### 1. The Datasets
* **Alternative Fuel Stations:** A large CSV (96,448 rows) containing tracking data for alternative refueling stations. I parsed this into a Pandas DataFrame to isolate charger levels (Level 1, Level 2, DC Fast), connector types, and network operators.
* **State EV Registrations:** Extracted via BeautifulSoup from the AFDC HTML table, then converted straight into a structured Pandas DataFrame to get raw consumer EV ownership counts by state.
* **Transportation Emissions:** Gathered via the Energy Information Administration (EIA) SEDS API, which returns annual state-level transportation energy stats and carbon outputs. The nested JSON response was flattened directly into a DataFrame.

### 2. Cleaning and Fixing Inconsistencies
To join the datasets reliably without breaking the models, I used Pandas to handle several major data quality issues:
* **Handling Missing Port Counts:** For operational stations with blank or "N/A" entries in their port counts, I used a conditional row application to default the missing count to 1, preserving the location without introducing NaN errors.
* **Status Filtering:** The raw data included future planned locations. I used Pandas string filtering to strip out planned sites and keep only existing, operational locations.
* **String Sanitization:** Cleaned up formatting commas and footnotes from the scraped HTML registration tables using regular expressions (`.str.replace()`) so the values could be safely cast to integers.
* **Unit Standardization (The Physics Fix):** The API returned emission values in mixed units (Billion Btu vs. Million Metric Tons of CO2). I applied a mathematical conversion factor across the DataFrame column to standardize everything into carbon emissions mass.
* **The Master Merge:** Once all three dataframes were individually cleaned, I used a Pandas outer join to merge them together into a unified, state-by-state database (`merged_ev_data.csv`).

---

## Modeling and Results

Using the final merged Pandas DataFrame, I split the data to run two separate predictive models to
