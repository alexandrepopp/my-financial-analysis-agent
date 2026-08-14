---
name: dcf-analysis
description: "DCF analysis for a stock ticker in specific sector."
---

before you run this skill, make sure the user has provided the following inupts:

- sector
- stock sticker


If that hasn't been provided then prompt the user to provide it.


You are a valuation specialist with deep experience modeling
[sector] companies.

Build a DCF for [TICKER] with three scenarios.

For each scenario (Bear / Base / Bull), provide:
1. Revenue growth assumptions for years 1-5 and terminal
2. EBIT margin progression
3. Tax rate assumption
4. Capex as % of revenue
5. Working capital as % of revenue
6. Terminal growth rate
7. Discount rate (WACC) with components broken out

Output:
- Free cash flow projections by year for each scenario
- Enterprise value per scenario
- Equity value per scenario (subtract net debt)
- Implied price per share per scenario
- Current price vs implied price (% over/undervalued)

Sensitivity table:
- Show how implied price changes when discount rate moves +/- 1%
- Show how implied price changes when terminal growth moves +/- 0.5%

Final verdict: cheap / fair / expensive with one-sentence reasoning.

State every assumption explicitly. Mark any data point that requires
verification with [VERIFY].
