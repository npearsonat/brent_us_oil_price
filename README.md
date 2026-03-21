# Brent Crude & U.S. Retail Gas Price Analysis

I was inspired to make this project in response to oil shocks resulting from the US involvment in Iran. The oil market is a global market and it is said that supply changes in one location can impact supply everywhere. In the 1970s, the US was a net importer of oil and was hit very hard by the OPEC embargo. Since then, they have managed to diversify their oil sources and have become a net exporter with the help of technological advancements in techniques such as fracking. With the distance in mind, I researched the impact of more recent oil shocks to see how the American oil market was impatced by global oil price fluctations. I am not trying to predict the price of oil, but merely tracking the impact that brent. 

This project investigates the relationship between Brent crude oil prices and U.S. retail gasoline prices from 1987 to 2026. Drawing on frameworks from the St. Louis Fed, EIA, and IMF, it applies a consistent methodology across three geopolitical shocks: 2008 financial crisis, 2022 Ukraine war, and the 2026 Iran conflict. It compares how each shock transmitted to the consumer pump price.

**Main Findings:** Cross-correlation analysis reveals an approximate two month lag between Brent price changes and pump price responses, with a slight asymmetry where prices rise marginally faster than they fall. Comparing the 2008 and 2022 shocks shows that in both cases prices rose sharply before recovering, though it remains to be seen whether the 2026 Iran shock will follow the same pattern.

## Guiding Questions

- How closely does U.S. retail gas track Brent crude over time?
- What fraction of a crude price increase passes through to consumers (and how quickly)?
- Do prices rise faster than they fall — and has this asymmetry changed across shocks?
- How does the 2026 Iran shock compare in speed and magnitude to 2008 and 2022?

## Data Sources

| Dataset | Source | Coverage |
|---|---|---|
| U.S. retail regular gasoline (weekly) | [EIA Open Data API](https://www.eia.gov/opendata/) | 1990 – 2026 |
| Brent crude spot price (monthly) | [EIA — Series RBRTE](https://www.eia.gov/opendata/) | 1987 – 2026 |

## Queries

### Brent Lag Analysis

Queries were done in SQL language using sqlite3 in Google Colab. This example query computes 1-, 2-, and 3-month lagged changes in Brent crude prices to measure how long crude price movements take to transmit to the retail pump.

```python
# Query US Brent LAG
query6 = """
SELECT *,
avg_brent - LAG(avg_brent, 1) OVER (ORDER BY year_month) AS brent_change_lag1,
avg_brent - LAG(avg_brent, 2) OVER (ORDER BY year_month) AS brent_change_lag2,
avg_brent - LAG(avg_brent, 3) OVER (ORDER BY year_month) AS brent_change_lag3
FROM
(SELECT
  AVG(e.us_regular) AS avg_us,
  AVG(b.brent_usd_bbl) AS avg_brent,
  e.year_month
FROM eia_us_prices AS e
JOIN brent_crude AS b ON e.year_month = b.year_month
WHERE us_regular IS NOT NULL
GROUP BY e.year_month) AS monthly_avg
"""

result = pd.read_sql_query(query6, conn)
```

## Analysis And Visuals

### U.S. Retail Gas vs Brent Crude
![US Retail Gas vs Brent](https://raw.githubusercontent.com/npearsonat/brent_us_oil_price/main/charts/us_retail_gas_vs_brent_2.png)

Time series of U.S. pump prices and Brent crude, annotated with key shock events (2008, COVID, Ukraine War, Iran 2026). Visible in this graph is the fact that brent crude and US retail prices appear to be quite closely linked. Over time there has been some separation between the two, but they mostly follow the same trend. The shock events I have examined have lead to dramatic increases and decreases in both markets.  

### Retail-to-Crude Ratio
![US Retail Crude Ratio](https://raw.githubusercontent.com/npearsonat/brent_us_oil_price/main/charts/us_retail_crude_ratio.png)

The pump-to-Brent ratio (adjusted to a per-barrel basis using the 42-gallon conversion) tracks what fraction of crude cost passes through to consumers. The ratio typically sits above 1.0, reflecting refining, distribution, and taxes layered on top of crude. The key takeway from this graph is that the premium that Americans pay for oil and gas is relatively consistent regardless of greater world events. If anything, that premium has been steadily decreased over the past few decades. increases in brent oil prices have not disproportionately affected Americans.

### U.S. Gas vs Brent
![US Gas vs Brent](https://raw.githubusercontent.com/npearsonat/brent_us_oil_price/main/charts/us_gas_vs_brent.png)

This graph zooms on the shocks and how oil and gas prices recovered from them. It shows how much oil and gas changed from a baseline price right before it occurred. With all of these oil shocks, there was a dramatic increase followed by a decrease and a leveling out. It is important to note that this may not necessarily be the case for 2026, but it is how previous oil shocks played out.

### Rockets & Feathers
![Rockets and Feathers](https://raw.githubusercontent.com/npearsonat/brent_us_oil_price/main/charts/rockets_and_feathers_2.png)

Month-over-month changes in Brent plotted against month-over-month changes in pump price, broken into quadrants. The slope when crude rises is steeper than when crude falls, consistent with asymmetric pass-through. This replicates and extends findings from the [St. Louis Fed FRED Blog](https://fredblog.stlouisfed.org).This means that the price of oil in the US increases faster than it decreases with brent. There less of a delay when it comes to US prices adjusting to rising brent. The exact slopes are 41.43 and 39.68. 

### Correlation Between Brent & U.S. Prices
![Correlation Between Brent and US Prices](https://raw.githubusercontent.com/npearsonat/brent_us_oil_price/main/charts/corr_between_brent%2Bus_prices.png)

Cross-correlation of monthly Brent changes against pump price changes reveals a ~2 month lag. Crude price movements take approximately two months to fully transmit to the retail pump. The highest correlation relationship is 2 months by a small margin. A possible explanation for this is some delayed reaction from the US markets and the global market.

## Methodology

All data is stored in a local **SQLite** database (`energy_prices.db`) with two tables:

- `eia_us_prices` — weekly U.S. retail prices
- `brent_crude` — monthly Brent spot prices

Analysis is performed in a Python / Jupyter notebook using SQL queries via `pandas.read_sql_query`, with visualizations built in `matplotlib`. Weekly EIA data is aggregated to monthly averages before joining with Brent to ensure consistent granularity across all analyses.

---

## Tech Stack

- **Python** — pandas, matplotlib, sqlite3
- **SQLite** — local data storage and query layer
- **Jupyter Notebook** — analysis and documentation
- **EIA Open Data API** — primary data source

## References

- St. Louis Fed FRED Blog — *Rockets and Feathers* (https://fredblog.stlouisfed.org/2022/06/oil-and-gas-prices-move-together-like-rockets-and-feathers/)
- EIA — *This Week in Petroleum* and historical shock analyses (https://www.eia.gov/petroleum/weekly/)
- World Bank — *What Explains Global Inflation* (https://www.worldbank.org/en/research/brief/global-inflation)
- IMF — oil price shock and inflation impact estimates

## Getting Started

```bash
# Clone the repo
git clone https://github.com/npearsonat/brent_us_oil_price.git
cd brent_us_oil_price

# Install dependencies
pip install pandas matplotlib requests jupyter

# Get a free EIA API key at https://www.eia.gov/opendata/register.php
# Add your key to the notebook where prompted

# Launch the notebook
jupyter notebook notebooks/oil_price_analysis.ipynb
```
