Based on the uploaded notebook structure, here’s a draft markdown file to present your project on GitHub Pages. The markdown includes sections for the introduction, detailed steps for code and analysis, and conclusions, following the requirements:

---

# **Egg Prices Analysis Across Countries**

## **Introduction**

Eggs are a staple in many households worldwide. For this project, we explored the global prices of eggs and analyzed how they are influenced by an external factor: sales tax rates. This analysis allows us to understand price variations across countries and assess whether economic policies such as sales taxes significantly affect the cost of this essential product.

---

## **Steps and Analysis**

### **1. Importing Necessary Libraries**
```python
from datascience import *
import numpy as np

%matplotlib inline
import matplotlib.pyplot as plots
plots.style.use('fivethirtyeight')
```
*Motivation*: Setting up the environment with required libraries like `datascience`, `numpy`, and `matplotlib` for data manipulation and visualization.

### **2. Web Scraping Functions**
```python
import requests
from bs4 import BeautifulSoup
import pandas as pd
from IPython.core.display import display, HTML

def scrape_table(table):
    df = pd.read_html(str(table))
    df = pd.DataFrame(df[0])
    return Table.from_df(df)
```
*Motivation*: The `scrape_table` function is designed to extract HTML tables from web pages and convert them into pandas DataFrames for further processing.

### **3. Scraping Data**
```python
egg_price_url = "https://www.globalproductprices.com/rankings/egg_prices/"
tax_rate_url = "https://tradingeconomics.com/country-list/sales-tax-rate"
currency_url = "https://taxsummaries.pwc.com/glossary/currency-codes"
```
*Motivation*: These URLs provide data for egg prices, sales tax rates, and currency codes, which are essential for this analysis.

### **4. Cleaning and Preparing Data**
(Include brief explanation and code snippets for cleaning and aligning data like handling missing values, ensuring consistent currency conversions, etc.)
Our biggest challenge was the country names between the web-scraped tables that contained different ways of naming certain countries. To tackle this we imported rapidfuzz to fuzzy match country names and standardize the country names across all three tables.

We set a high matching threshold of 80. This allows rapidfuzz to take our elements and assign a score of match between all three tables. The one with the highest match will be standarized. Of course, not all our countries were given high scores of matches, we left those countries to remain as they are as we did not have many to cause issues.

```python
all_countries = list(all_countries)  # Convert to a list for iteration
for country in all_countries:
    match = process.extractOne(country, all_countries, scorer=fuzz.token_sort_ratio)
    if match and match[1] >= threshold:
        # High-confidence match
        mapping[country] = match[0]
    else:
        # No match or low confidence, retain original value
        mapping[country] = country

# Standardize each table using the mapping
def standardize_country(name):
    return mapping.get(name, name)

egg_price_table = egg_price_table.with_column('Standardized_Country', egg_price_table.apply(standardize_country, 'Countries'))
tax_price_table = tax_price_table.with_column('Standardized_Country', tax_price_table.apply(standardize_country, 'Country'))
currency_table = currency_table.with_column('Standardized_Country', currency_table.apply(standardize_country, 'Territory'))
```
We then cleaned, relabeled the tables that, when joined, gave us our dataset: 

![Screenshot 2024-12-03 130529](https://github.com/user-attachments/assets/481f12aa-d0bd-4223-90c0-2efec3e1f080)

We also included our external factor of electricity prices and made our necessary or relabelling, filterting & converting to CAD

```python
electricity_url = 'https://raw.githubusercontent.com/hoangr1010/CMPUT-191---A3/refs/heads/main/data/cost-of-electricity-by-country-2024.csv'
electricity_data = Table.read_table(electricity_url)
electricity_data = electricity_data.drop("CostOfElectricity_ElectricityCost_USDPerkWh_2023March", "CostOfElectricity_ElectricityCost_USDPerkWh_2022Sept")
electricity_data = electricity_data.relabeled("CostOfElectricity_ElectricityCost_USDPerkWh_2024March", "Electricity Cost (USD/kWh) - 03/2024")
electricity_data = electricity_data.relabeled("country", "Country")
electricity_data.where('Country', are.equal_to('Canada'))
```
This gave us our final data ready for visualization:

![image](https://github.com/user-attachments/assets/bf32299e-d8fc-48e7-8466-0b3653da8e77)


### **5. Visualizing Price Distributions**
```python
plots.figure(figsize=(10, 6))
plots.hist(egg_prices.column('Price'), bins=20, alpha=0.7)
plots.title('Distribution of Egg Prices Worldwide')
plots.xlabel('Price (USD)')
plots.ylabel('Frequency')
plots.show()
```
*Graph*: Include the generated histogram.


---

## **External Factor Analysis**
(Explain the analysis performed to correlate sales tax rates with egg prices, including visualizations like scatter plots or correlation metrics.)

---

## **Conclusions**

1. **Most Expensive Country**: (Insert result here)
2. **Cheapest Country**: (Insert result here)
3. **Impact of Sales Tax Rates**: The analysis indicates that (insert findings on whether sales tax significantly affects prices).

### **Challenges and Observations**
- Scraping accurate data from dynamic websites posed challenges.
- Aligning data from multiple sources required rigorous cleaning.

### **Interesting Insights**
- (Highlight any trends or anomalies observed during the analysis, e.g., "Countries with higher GDPs tend to have lower egg prices despite higher sales tax rates.")

---

This markdown file provides a clear structure to present your analysis, meeting all the requirements for your GitHub Pages site. Let me know if you need help refining specific sections or adding more details!
