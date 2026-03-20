# Brent Crude & U.S. Retail Gas Price Analysis

I was inspired to make this project in response to oil shocks resulting from the US involvment in Iran. The oil market is a global market and it is said that supply changes in one location can impact supply everywhere. In the 1970s, the US was a net importer of oil and was hit very hard by the OPEC embargo. Since then, they have managed to diversify their oil sources and have become a net exporter with the help of technological advancements in techniques such as fracking. With the distance in mind, I researched the impact of more recent oil shocks to see how the American oil market was impatced by global oil price fluctations. I am not trying to predict the price of oil, but merely tracking the impact that brent 

This project investigates the relationship between Brent crude oil prices and U.S. retail gasoline prices from 1987 to 2026. Drawing on frameworks from the St. Louis Fed, EIA, and IMF, it applies a consistent methodology across three geopolitical shocks: 2008 financial crisis, 2022 Ukraine war, and the 2026 Iran conflict. It compares how each shock transmitted to the consumer pump price.

A key focus is the **rockets and feathers** phenomenon: the well-documented asymmetry where pump prices rise faster when crude rises than they fall when crude drops.

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

> **Why Brent and not WTI?** After 2010, WTI diverged from Brent due to transportation bottlenecks at Cushing, Oklahoma — lower WTI prices did not result in lower U.S. gas prices. EIA research confirms that Brent is the stronger predictor of U.S. retail pump prices, so it was chosen as the crude benchmark throughout this analysis.

---

## Visualizations

### U.S. Retail Gas vs Brent Crude
![US Retail Gas vs Brent](https://raw.githubusercontent.com/npearsonat/brent_us_oil_price/main/charts/us_retail_gas_vs_brent.png)

### U.S. Gas vs Brent — Annotated
![US Gas vs Brent](https://raw.githubusercontent.com/npearsonat/brent_us_oil_price/main/charts/us_gas_vs_brent.png)

### Retail-to-Crude Ratio
![US Retail Crude Ratio](https://raw.githubusercontent.com/npearsonat/brent_us_oil_price/main/charts/us_retail_crude_ratio.png)

### Rockets & Feathers
![Rockets and Feathers](https://raw.githubusercontent.com/npearsonat/brent_us_oil_price/main/charts/rockets_and_feathers.png)

### Correlation Between Brent & U.S. Prices
![Correlation Between Brent and US Prices](https://raw.githubusercontent.com/npearsonat/brent_us_oil_price/main/charts/corr_between_brent%2Bus_prices.png)

---

## Methodology

All data is stored in a local **SQLite** database (`energy_prices.db`) with two tables:

- `eia_us_prices` — weekly U.S. retail prices
- `brent_crude` — monthly Brent spot prices

Analysis is performed in a **Python / Jupyter notebook** using SQL queries via `pandas.read_sql_query`, with visualizations built in `matplotlib`. Weekly EIA data is aggregated to monthly averages before joining with Brent to ensure consistent granularity across all analyses.

---

## Analysis & Findings

**Price Trend Overview** — Time series of U.S. pump prices and Brent crude, annotated with key shock events (2008, COVID, Ukraine War, Iran 2026).

**Retail-to-Crude Ratio** — The pump-to-Brent ratio (adjusted to a per-barrel basis using the 42-gallon conversion) tracks what fraction of crude cost passes through to consumers. The ratio typically sits above 1.0, reflecting refining, distribution, and taxes layered on top of crude.

**Rockets & Feathers** — Month-over-month changes in Brent plotted against month-over-month changes in pump price, broken into quadrants. The slope when crude rises is steeper than when crude falls — consistent with asymmetric pass-through. This replicates and extends findings from the [St. Louis Fed FRED Blog](https://fredblog.stlouisfed.org).

**Lag Correlation Analysis** — Cross-correlation of monthly Brent changes against pump price changes reveals a ~2 month lag — crude price movements take approximately two months to fully transmit to the retail pump.

**Shock Comparison (2008 / 2022 / 2026)** — Each shock is indexed to its pre-shock baseline and percentage changes are compared across shocks to quantify magnitude and speed of transmission.

---

## Tech Stack

- **Python** — pandas, matplotlib, sqlite3
- **SQLite** — local data storage and query layer
- **Jupyter Notebook** — analysis and documentation
- **EIA Open Data API** — primary data source

---

## References

- St. Louis Fed FRED Blog — *Rockets and Feathers* (asymmetric price transmission)
- EIA — *This Week in Petroleum* and historical shock analyses
- World Bank — *What Explains Global Inflation* (oil shocks and pass-through)
- IMF — oil price shock and inflation impact estimates

---

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
