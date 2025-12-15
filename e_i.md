---
layout: article
title: Extraversion vs Introversion
permalink: /e_i/
---
## Emotional Index (EI): Definition and Asset Personality Classification

To characterize the behavioral profile of financial assets, we construct an **Emotional Index (EI)** based on observable market dynamics. For each asset (stock or ETF), three complementary components are computed from historical price and volume data: **volatility**, **jump frequency**, and a **turnover proxy**. Each component captures a distinct aspect of market behavior and is computed independently for each asset.

**Volatility** is defined as the annualized standard deviation of daily log-returns and reflects the magnitude of price fluctuations.  
**Jump frequency** measures the proportion of trading days on which absolute daily returns exceed a fixed multiple of the asset’s own return volatility, capturing abrupt and event-driven price movements.  
The **turnover proxy** is computed as average daily dollar volume and serves as a measure of liquidity and trading intensity.

To enable cross-sectional comparison, all three components are standardized across assets using z-score normalization. The standardized Emotional Index is then defined as a weighted linear combination of the normalized components:

$EI_z = 0.5 \times volatility_z + 0.3 \times jumpfrequency_z + 0.2 \times turnover_z$




This formulation assigns greater importance to price variability and discontinuities while retaining liquidity as a complementary signal. The resulting EI score quantifies the relative behavioral activity of an asset with respect to the market as a whole.

Asset personality is derived through a binary classification rule applied to the standardized EI score. Assets with  
$EI_z > 0$
 are labeled **extroverted**, indicating above-average behavioral intensity, while assets with  
$EI_z > 0$
 are labeled **introverted**, corresponding to below-average market activity. No additional thresholds or nonlinear transformations are introduced; the classification boundary coincides with the cross-sectional mean.

From a conceptual standpoint, **extroverted assets** are characterized by higher volatility, more frequent jump events, and elevated trading activity, leading to stronger and more visible reactions to market information. In contrast, **introverted assets** exhibit more stable price dynamics, fewer abrupt movements, and lower trading intensity. The EI score therefore provides a single behavioral axis that positions assets relative to their peers based on consistent and observable market signals.

To ensure robustness, assets are required to satisfy minimum data availability constraints. In particular, at least **30 return observations** are necessary for reliable estimation of volatility and jump frequency. Assets with insufficient data or missing component values are excluded from scoring. Only assets with valid measurements for all three components are assigned an EI score and personality label.

