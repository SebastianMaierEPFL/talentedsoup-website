---
layout: article
title: Extraversion vs Introversion
permalink: /e_i/
---
## Emotional Index (EI): Definition and Asset Personality Classification

![Emotional Index visualization](social2.png)

**Overview :** 
To characterize the social dimension of financial assets, we introduce an Emotional Index (EI) constructed exclusively from observable market dynamics. The EI is designed to capture market level expressiveness, defined as the extent to which an asset’s price responds visibly and intensely to incoming information, rather than to firm-specific fundamentals or latent investor sentiment.
Each asset’s social profile is described through three complementary components volatility, jump frequency, and a turnover proxy—computed from historical price and volume data. Together, these components capture the intensity, discontinuity, and liquidity of market reactions, respectively.
Volatility is defined as the annualized standard deviation of daily log-returns and reflects the magnitude of price fluctuations . 
Jump frequency measures the proportion of trading days on which absolute returns exceed a fixed multiple of the asset’s own volatility, capturing abrupt and event-driven price movements.
Turnover is computed as average daily dollar volume and serves as a proxy for trading intensity and liquidity.
To enable cross-sectional comparison, all three components are standardized using z-score normalization. The Emotional Index is then defined as a weighted linear combination:
EI score = 0.5 x Normalised Volatility +0.2 x Normalised Jump Frequency +0.3 x Normalized Turnover
This formulation emphasizes price variability and discontinuities while retaining liquidity as a complementary signal. The EI score therefore measures an asset’s relative behavioral activity with respect to the market as a whole.

**Personality Classification Rule :**
Volatility is defined as the annualized standard deviation of daily log-returns and reflects the overall magnitude of price fluctuations.
Jump frequency measures the proportion of trading days on which absolute returns exceed a fixed multiple of the asset’s own return volatility, capturing abrupt and event-driven price movements.
Turnover is computed as average daily dollar volume and serves as a proxy for trading intensity and market participation.
To enable cross-sectional comparability, all three components are standardized using z-score normalization. The Emotional Index is then defined as a weighted linear combination:
EI score =
0.5
×
Volatility
z
+
0.2
×
Jump Frequency
z
+
0.3
×
Turnover
z
EI score=0.5×Volatility 
z
​	
 +0.2×Jump Frequency 
z
​	
 +0.3×Turnover 
z
​	
 
This specification places greater emphasis on price variability and discontinuities, while retaining liquidity as a complementary signal of market engagement. The resulting EI score measures an asset’s relative behavioral activity with respect to the broader market.

**Personality Classification Rule**
An asset’s social orientation is determined via a binary classification applied to the standardized EI score:
Assets with a positive EI score are labeled Extroverted
Assets with a negative EI score are labeled Introverted
This classification reflects relative, rather than absolute, behavioral positioning within the asset universe.

**Hypotheses :**

1) Hypothesis 1 


  Asset extroversion reflects the extent to which an asset functions as a conduit for market-level information aggregation and transmission. Extroverted assets exhibit rapid and pronounced price responses to news, regime shifts, and changes in investor positioning.


2) Hypothesis 2
   
  Asset introversion is characterized by below-average market expressiveness, with comparatively stable price dynamics and muted responses to information. Such assets tend to absorb market signals smoothly rather than amplifying short-term shocks.
Here are some data observation for further social analysis of assets :

![Stock and ETF Personality Maps](social1.png)
*Figure 1 : Side-by-side scatter plots of stocks and ETFs in the normalized personality space defined by volatility and jump frequency.*

*Most Extroverted ETFs*

| Asset | EI<sub>z</sub> | Vol | Jump | Turnover |  
|------:|--------------:|----:|-----:|---------:|
| PLC | 13.39 | 35.88 | 0.062 | 1.36e+08 |
| RTL | 12.77 | 31.87 | 0.105 | 8.05e+04 |
| SPY | 8.25 | 0.19 | 0.048 | 1.10e+10 |
| QUS  | 6.54  | 17.36 | 0.061 | 8.99e+05 |
| NJAN | 6.06  | 12.25 | 0.124 | 2.24e+05 |

*Figure 2: Highly extroverted ETFs (e.g., PLC, RTL, SPY) exhibit pronounced volatility and frequent jump activity, consistent with elevated market expressiveness. PLC and RTL display strong, discontinuous reactions around announcements and regime shifts, reflecting episodes in which market participants concentrate information flow and repositioning through these assets. SPY exhibits sustained extroversion as a central vehicle for trading, hedging, and price discovery, continuously transmitting market-wide information. QUS and NJAN show conditional extroversion, becoming expressive primarily during factor rotations or calendar-driven reallocations. Overall, these patterns are consistent with H1, confirming that extroversion corresponds to heightened market-level information processing and transmission.*

*Most Introverted ETFs*

| Asset | EI<sub>z</sub> | Vol | Jump | Turnover |
|------:|--------------:|----:|-----:|---------:|
| TFLO | -0.96 | 0.028 | 0.0045 | 1.82e+06 |
| GSY | -0.96 | 0.052 | 0.0043 | 7.25e+06 |
| NEAR | -0.89 | 0.029 | 0.0067 | 3.05e+07 |
| FFIU | -0.87 | 0.467 | 0.0015 | 1.38e+05 |
| FLOT | -0.87 | 0.044 | 0.0072 | 4.17e+07 |

*Figure 3: Introverted ETFs exhibit muted price dynamics and rare discontinuities, consistent with below-average market expressiveness. Even in the presence of non-trivial trading activity, price adjustments remain smooth and stable, indicating subdued reactions to new information. This behavior reflects a market-level form of restraint: these assets tend to absorb information quietly and remain comparatively insulated from short-term market fluctuations, rather than amplifying shocks. Overall, the observed patterns support H2, confirming that introversion captures restrained market expressiveness rather than illiquidity, neglect, or mechanical price rigidity.*

*Most Extroverted Stocks*

| Asset | EI<sub>z</sub> | Vol | Jump | Turnover |
|------:|--------------:|----:|-----:|---------:|
| DTP | 16.70 | 66.89 | 0.163 | 4.73e+05 |
| MEC | 14.17 | 60.60 | 0.108 | 8.39e+06 |
| HMI | 10.82 | 42.43 | 0.132 | 6.65e+05 |
| PBC | 9.89 | 48.82 | 0.025 | 2.83e+07 |
| EIC | 8.80 | 35.29 | 0.109 | 2.16e+06 |

*Figure 4 : Extroverted stocks such as DTP, MEC, and HMI exhibit extreme volatility and frequent jump activity, indicating high market expressiveness. Rather than reflecting intrinsic firm characteristics, this behavior captures episodes in which market participants concentrate information processing and repositioning through these assets. As a result, they function as conduits for idiosyncratic information flow, transmitting shocks rapidly via large and discontinuous price movements.*

*Most Introverted Stocks*

| Asset | EI<sub>z</sub> | Vol | Jump | Turnover |
|------:|--------------:|----:|-----:|---------:|
| AAN   | -15.21 | 0.413 | 0.052 | -2.82e+25 |
| EVSTC | -0.99  | 0.343 | 0.001 | 2.00e+05 |
| EVLMC | -0.99  | 0.347 | 0.001 | 1.92e+05 |
| EVGBC | -0.99  | 0.347 | 0.001 | 1.77e+05 |
| CARR# | -0.92  | 0.575 | 0.002 | 2.24e+04 |

*Figure 5 : Introverted stocks exhibit persistently low Emotional Index (EI) scores, reflecting below-average market expressiveness. Their price dynamics are comparatively stable, with few abrupt movements and muted reactions to new information, even when trading activity is non-trivial. This behavior is consistent with a market-level form of restraint, whereby information is absorbed smoothly rather than transmitted through sharp price adjustments. In isolated cases (e.g., AAN), extreme EI values arise from data sparsity or structural trading constraints rather than genuine behavioral intensity, and are therefore interpreted as measurement artifacts rather than deviations from the underlying pattern.* 

<div class="flourish-embed flourish-chart" data-src="visualisation/26818399"></div>
<script src="https://public.flourish.studio/resources/embed.js"></script>
<noscript>
  <img src="https://public.flourish.studio/visualisation/26818399/thumbnail"
       width="100%" alt="chart visualization" />
</noscript>

*Figure 6. Asset behavior across three market metrics. Higher values indicate more reactive assets, while lower values reflect more stable behavior, illustrating how the Emotional Index (EI) relates to underlying market dynamics. The figure shows a subset of 30 assets for clarity.*

<div class="flourish-embed flourish-network" data-src="visualisation/26824006"></div>
<script src="https://public.flourish.studio/resources/embed.js"></script>
<noscript>
  <img src="https://public.flourish.studio/visualisation/26824006/thumbnail"
       width="100%" alt="network visualization" />
</noscript>

*Figure 7. Friendship network illustration for selected stocks, constructed from log-returns computed using adjusted closing prices. For each pair of assets, the Pearson correlation of returns is computed; if the absolute correlation exceeds 0.5, the assets are considered connected.*
<div class="flourish-embed flourish-network" data-src="visualisation/26824327"></div>
<script src="https://public.flourish.studio/resources/embed.js"></script>
<noscript>
  <img src="https://public.flourish.studio/visualisation/26824327/thumbnail"
       width="100%" alt="network visualization" />
</noscript>

*Figure 8. Friendship network illustration for selected ETFs, constructed from log-returns computed using adjusted closing prices. For each pair of assets, the Pearson correlation of returns is computed; if the absolute correlation exceeds 0.5, the assets are considered connected.*

**Network Perspective**
Correlation-based “friendship” networks for stocks and ETFs (Figures 6–7) reveal that extroverted assets tend to occupy structurally influential positions, often acting as hubs during market-wide movements. Introverted assets, by contrast, appear more peripheral, reinforcing their role as market stabilizers rather than shock propagators.

**Conclusion**
This study demonstrates that asset personality can be meaningfully defined using market observables alone. The Emotional Index captures a persistent dimension of market behavior—expressiveness versus restraint—that is orthogonal to corporate fundamentals and investor sentiment.
Extroverted assets amplify information, propagate shocks, and dominate price discovery.
Introverted assets absorb information smoothly, contributing to market stability.
These findings support the hypothesis that asset behavior reflects how markets process information.  The EI framework provides a unified, interpretable lens for analyzing behavioral heterogeneity across stocks and ETFs, with direct implications for portfolio construction, risk management, and network-based market analysis.
