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

Here are some data observation for further social analysis of assets :

![Stock and ETF Personality Maps](social1.png)
*Figure 1 : Side-by-side scatter plots of stocks and ETFs in the normalized personality space defined by volatility and jump frequency.*

<table style="width:100%; border-collapse: collapse;">
  <tr>

    <!-- LEFT COLUMN -->
    <td style="vertical-align: top; width:50%; padding: 8px;">

      <strong style="color:#d63384;">Most Extroverted Stocks</strong>

      <table style="border-collapse: collapse; width:100%; background-color:#fde6ef;">
        <thead>
          <tr style="background-color:#f8cadd;">
            <th>Asset</th>
            <th>EI score</th>
            <th>Volatility</th>
            <th>Jump frequency</th>
            <th>Turnover</th>
          </tr>
        </thead>
        <tbody>
          <tr><td>DTP</td><td>16.70</td><td>66.89</td><td>0.163</td><td>4.73e+05</td></tr>
          <tr><td>MEC</td><td>14.17</td><td>60.60</td><td>0.108</td><td>8.39e+06</td></tr>
          <tr><td>HMI</td><td>10.82</td><td>42.43</td><td>0.132</td><td>6.65e+05</td></tr>
          <tr><td>PBC</td><td>9.89</td><td>48.82</td><td>0.025</td><td>2.83e+07</td></tr>
          <tr><td>EIC</td><td>8.80</td><td>35.29</td><td>0.109</td><td>2.16e+06</td></tr>
        </tbody>
      </table>

    </td>

    <!-- RIGHT COLUMN -->
    <td style="vertical-align: top; width:50%; padding: 8px;">

      <strong style="color:#c2255c;">Most Introverted Stocks</strong>

      <table style="border-collapse: collapse; width:100%; background-color:#fde6ef;">
        <thead>
          <tr style="background-color:#f8cadd;">
            <th>Asset</th>
            <th>EI score</th>
            <th>Volatility</th>
            <th>Jump frequency</th>
            <th>Turnover</th>
          </tr>
        </thead>
        <tbody>
          <tr><td>AAN</td><td>-15.21</td><td>0.413</td><td>0.052</td><td>-2.82e+25</td></tr>
          <tr><td>EVSTC</td><td>-0.99</td><td>0.343</td><td>0.001</td><td>2.00e+05</td></tr>
          <tr><td>EVLMC</td><td>-0.99</td><td>0.347</td><td>0.001</td><td>1.92e+05</td></tr>
        </tbody>
      </table>

    </td>

  </tr>
</table>

