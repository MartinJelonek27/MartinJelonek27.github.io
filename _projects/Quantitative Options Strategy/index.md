---
layout: post
title: Apple WWDC Options Strategy
description: Tested whether selling options on Apple before its WWDC keynote makes money. It does, but no more than on any other day, and the big move usually comes the day after.
skills:
- Python
- pandas
- Options Pricing
- Backtesting
- Statistics
main-image: "https://upload.wikimedia.org/wikipedia/commons/thumb/5/5a/Aerial_view_of_Apple_Park_dllu.jpg/1280px-Aerial_view_of_Apple_Park_dllu.jpg"
---

## Overview

Every June, Apple shows off its new software at the WWDC keynote. A common options trade bets that a stock will move less on a big event day than the options market expects. I tested that bet on Apple: sell an at-the-money straddle (a call and a put at the same strike) the night before the keynote, and close it at the end of keynote day. It makes money if the stock moves less than the options priced in.

## Approach

- Used Apple's daily closing prices from 2015 to 2026 (Yahoo Finance) and the Cboe Apple volatility index (VXAPL), which measures how much the options market expects Apple to move.
- Turned the night-before VXAPL into an expected one-day move with the standard at-the-money straddle rule, 0.8 x volatility x √time (Brenner and Subrahmanyam, 1988).
- Scored each trade as the expected move minus the actual move, as a percentage of the stock price.
- Ran the same trade on every trading day from 2015 to 2025 to see if keynote days are special, and kept WWDC 2026 out of the test to check it afterward.

## Challenge and insight

My first version said this was a great trade: 8 wins in 11 years and a Sharpe ratio of 12.8. That was too good to be true. I had typed the keynote-day returns in by hand, and when I checked them against real prices, 9 of the 11 were wrong. The Sharpe ratio was also about 16 times too high, because I annualized it as if I traded every day instead of once a year.

With real data, the trade still won 7 of 11 years. The insight came from asking: compared to what? Selling the same straddle on every trading day since 2015 won 68% of the time and made +0.23% a day on average, the same as WWDC's +0.24%. The edge has nothing to do with WWDC. Options usually cost a little more than the moves they end up paying for, because buyers pay extra for protection, and that is true almost every day (Carr and Wu, 2009).

## Result

| Check | Result |
|---|---|
| First version (hand-typed data) | 8 of 11 wins, Sharpe ratio 12.8 |
| Real data, WWDC keynote day | 7 of 11 wins, +0.24% of the stock price per trade |
| Every trading day, 2015 to 2025 | 68% wins, +0.23% per trade, worst day -11.8% |
| Day after the keynote | Bigger moves (1.6% on average vs 1.1% on keynote day), and the trade loses 0.28% per trade |
| WWDC 2026 (not in the backtest) | The stock fell 1.9% vs 1.4% expected, a loss of 0.53%, then fell another 3.6% the next day |

![Expected move against the actual move on keynote day and the day after, 2015 to 2026](/_projects/Quantitative%20Options%20Strategy/wwdc_moves.svg)

The keynote itself rarely moves the stock much. The bigger reaction often comes the next day, like the 7.3% jump after Apple Intelligence was announced in 2024.

![Profit from the trade on every trading day, with the WWDC days marked](/_projects/Quantitative%20Options%20Strategy/pnl_vs_all_days.svg)

The WWDC trades (blue) sit right in the middle of every other day. Most days make a little money, but a few lose a lot.

## Things to know

- **The expected move is an estimate.** VXAPL measures volatility over the next 30 days, not the keynote day alone, so it misses any extra price options charge for the event. Real one-day option prices would be better but are not free.
- **No trading costs, and one-day options aren't always available.** The backtest ignores bid-ask spreads and commissions, and a real trade would hold the nearest weekly options instead of a one-day straddle.
- **Selling straddles has unlimited risk.** Most days win a little, but one bad day (9 April 2025) lost 11.8% of the stock price. That is why I dropped the Kelly position sizing from my first version, since it assumes losses are limited.

## Sources

1. Cboe, [Cboe Apple VIX Index (VXAPL)](https://www.cboe.com/us/indices/dashboard/vxapl/) and [*Volatility Index Methodology: Selected Broad-Based Index, Equity and ETF Volatility Indices*](https://cdn-api.cboe.com/api/global/us_indices/governance/Volatility_Index_Methodology_Selected_Broad_Based_Index_Equity_and_ETF_Volatility_Indices.pdf).
2. Yahoo Finance, [AAPL historical prices](https://finance.yahoo.com/quote/AAPL/history/).
3. M. Brenner and M. G. Subrahmanyam, "A Simple Formula to Compute the Implied Standard Deviation," *Financial Analysts Journal*, 1988.
4. P. Carr and L. Wu, ["Variance Risk Premiums,"](https://ideas.repec.org/a/oup/rfinst/v22y2009i3p1311-1341..html) *Review of Financial Studies*, vol. 22, no. 3, pp. 1311-1341, 2009.
5. J. C. Hull, *Options, Futures, and Other Derivatives*, Pearson.

*Cover photo: ["Aerial view of Apple Park"](https://commons.wikimedia.org/wiki/File:Aerial_view_of_Apple_Park_dllu.jpg) by Daniel L. Lu, [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/), via Wikimedia Commons.*
