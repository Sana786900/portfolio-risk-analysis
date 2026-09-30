# portfolio-risk-analysis
<div align="center">

# Portfolio Risk & Return Analysis

**A five asset investment portfolio taken apart in SQL: returns, correlations, risk, and a rebalancing recommendation.**

![SQL](https://img.shields.io/badge/SQL-MySQL_8-1E3A5F?style=for-the-badge&logo=mysql&logoColor=6BA3D0)
![Python](https://img.shields.io/badge/Python-yfinance-1E3A5F?style=for-the-badge&logo=python&logoColor=4B8BBE)
![Rows](https://img.shields.io/badge/📊_PRICE_RECORDS-4,340-3E6B8A?style=for-the-badge&labelColor=1E3A5F)
![Window](https://img.shields.io/badge/🗓️_WINDOW-3.5_YEARS-475569?style=for-the-badge&labelColor=1E3A5F)

</div>

<img src="https://img.shields.io/badge/-C0704A?style=flat-square" width="100%" height="3">

## The question

A portfolio is spread across five assets. Tech equity, broad growth equity, treasuries, real estate, and gold. It has never been examined as a *system*, only as five separate line items.

The owner wants to know four things. What has it actually returned? How much risk is it carrying? Do the holdings move together, which would mean the diversification is an illusion? And what should change?

Those are not four separate questions. They are one question asked four times, and the answer to each depends on the one before it.

## The approach

Everything runs in SQL. No spreadsheet, no notebook, no statistics package. The point was to see whether portfolio theory holds up when you have to express it in window functions and self joins, and it does.

| | Question | What it takes |
|:--|:--|:--|
| `Q1` | What did each asset and the portfolio return over 12, 18, and 24 months? | Point to point returns off the most recent trading date, then a weighted sum for the portfolio |
| `Q2` | Do these assets actually move independently? | Pearson correlation across all ten pairs, computed inline: `(AVG(xy) - AVG(x)AVG(y)) / (STD(x)STD(y))` |
| `Q3` | How much risk is each position carrying? | Daily return volatility annualized as `STD(daily_return) × √252`, at both asset and portfolio level |
| `Q4` | What should be bought, sold, or held? | Return, sigma, and Sharpe ratio joined into one decision table. Sharpe as `AVG(daily_ror) / STD(daily_ror) × √252` |
| `Q5` | What changes if the portfolio is rebalanced? | Current weights against a proposed target, compared year by year on return, risk, and diversification benefit |

The interesting one is `Q3`. Portfolio sigma is not the weighted average of the individual sigmas, because correlation absorbs part of the risk. Computing it on the weighted daily portfolio return rather than on the components is what makes the diversification benefit visible instead of theoretical.

## What's in here

```
sql/portfolio_analysis.sql     Schema, 4,340 price records, and all five analyses. Runs top to bottom.
scripts/generate_portfolio_sql.py   Pulls daily prices from Yahoo Finance and regenerates the SQL
scripts/build_final_sql.py          Rebuilds the same script from a local dump, no network needed
```

## Running it

```bash
mysql -u root -p < sql/portfolio_analysis.sql
```

The script creates its own schema, loads the data, and prints each analysis in order. Nothing to configure.

To rebuild against current market data:

```bash
pip install yfinance
python scripts/generate_portfolio_sql.py
```

## Data

Daily OHLC prices for **IXN**, **QQQ**, **IEF**, **VNQ**, and **GLD** from Yahoo Finance. 868 trading days per ticker, January 2023 through June 2026. Weights are 17.5%, 22.1%, 28.5%, 8.9%, and 23.0% respectively.

The portfolio itself is a case study, not a live account.

<img src="https://img.shields.io/badge/-C0704A?style=flat-square" width="100%" height="3">

<div align="center">

Built by [Juan Felipe Betancourt](https://github.com/jfelipeb1)

<sub>Part of a portfolio of commercial and analytical work. See the <a href="https://github.com/jfelipeb1">profile</a> for the rest.</sub>

</div>
