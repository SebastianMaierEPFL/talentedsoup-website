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


<div style="display: flex; gap: 16px; align-items: flex-start;">

  <div style="width: 50%;">
    <strong>Most Extroverted Stocks</strong>
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>EI score_z</th>
      <th>volatility</th>
      <th>jump_frequency</th>
      <th>turnover</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>DTP</th>
      <td>16.697536</td>
      <td>66.888366</td>
      <td>0.162593</td>
      <td>4.725967e+05</td>
    </tr>
    <tr>
      <th>MEC</th>
      <td>14.174816</td>
      <td>60.603661</td>
      <td>0.107685</td>
      <td>8.385282e+06</td>
    </tr>
    <tr>
      <th>HMI</th>
      <td>10.817338</td>
      <td>42.428938</td>
      <td>0.132069</td>
      <td>6.652723e+05</td>
    </tr>
    <tr>
      <th>PBC</th>
      <td>9.890218</td>
      <td>48.816051</td>
      <td>0.025493</td>
      <td>2.832842e+07</td>
    </tr>
    <tr>
      <th>EIC</th>
      <td>8.801261</td>
      <td>35.289560</td>
      <td>0.109217</td>
      <td>2.156440e+06</td>
    </tr>
    <tr>
      <th>SAF</th>
      <td>8.721624</td>
      <td>36.854253</td>
      <td>0.089969</td>
      <td>4.197363e+07</td>
    </tr>
    <tr>
      <th>CCX</th>
      <td>8.208543</td>
      <td>27.128067</td>
      <td>0.162727</td>
      <td>2.823380e+05</td>
    </tr>
    <tr>
      <th>PAE</th>
      <td>7.725117</td>
      <td>28.820747</td>
      <td>0.123425</td>
      <td>2.088036e+07</td>
    </tr>
    <tr>
      <th>LBC</th>
      <td>7.385851</td>
      <td>28.204702</td>
      <td>0.113764</td>
      <td>2.556197e+05</td>
    </tr>
    <tr>
      <th>DTW</th>
      <td>7.048594</td>
      <td>29.927414</td>
      <td>0.080963</td>
      <td>3.230028e+05</td>
    </tr>
  </tbody>
</table>
</div>
  </div>

  <div style="width: 50%;">
    <strong>Most Introverted Stocks</strong>
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>EI score_z</th>
      <th>volatility</th>
      <th>jump_frequency</th>
      <th>turnover</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>AAN</th>
      <td>-15.209337</td>
      <td>0.412560</td>
      <td>0.052458</td>
      <td>-2.822350e+25</td>
    </tr>
    <tr>
      <th>EVSTC</th>
      <td>-0.989738</td>
      <td>0.342753</td>
      <td>0.000970</td>
      <td>2.001387e+05</td>
    </tr>
    <tr>
      <th>EVLMC</th>
      <td>-0.988156</td>
      <td>0.347439</td>
      <td>0.000997</td>
      <td>1.921985e+05</td>
    </tr>
    <tr>
      <th>EVGBC</th>
      <td>-0.988154</td>
      <td>0.347446</td>
      <td>0.000997</td>
      <td>1.769137e+05</td>
    </tr>
    <tr>
      <th>CARR#</th>
      <td>-0.922201</td>
      <td>0.575074</td>
      <td>0.001803</td>
      <td>2.237525e+04</td>
    </tr>
    <tr>
      <th>COW</th>
      <td>-0.907907</td>
      <td>0.276639</td>
      <td>0.005433</td>
      <td>1.215234e+06</td>
    </tr>
    <tr>
      <th>ARGD</th>
      <td>-0.900715</td>
      <td>0.271351</td>
      <td>0.005820</td>
      <td>2.661384e+05</td>
    </tr>
    <tr>
      <th>MFO</th>
      <td>-0.874994</td>
      <td>0.575399</td>
      <td>0.003996</td>
      <td>1.912662e+05</td>
    </tr>
    <tr>
      <th>JHY</th>
      <td>-0.869945</td>
      <td>0.809668</td>
      <td>0.001904</td>
      <td>2.622306e+05</td>
    </tr>
    <tr>
      <th>THGA</th>
      <td>-0.869640</td>
      <td>0.260875</td>
      <td>0.007370</td>
      <td>3.355198e+05</td>
    </tr>
  </tbody>
</table>
</div>
  </div>

</div>

<div style="display: flex; gap: 16px; align-items: flex-start;">

  <div style="width: 50%;">
    <strong>Most Extroverted ETFs</strong>
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>EI score_z</th>
      <th>volatility</th>
      <th>jump_frequency</th>
      <th>turnover</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>PLC</th>
      <td>13.389846</td>
      <td>35.877917</td>
      <td>0.062042</td>
      <td>1.360358e+08</td>
    </tr>
    <tr>
      <th>RTL</th>
      <td>12.772375</td>
      <td>31.873318</td>
      <td>0.105247</td>
      <td>8.049316e+04</td>
    </tr>
    <tr>
      <th>SPY</th>
      <td>8.248032</td>
      <td>0.188193</td>
      <td>0.047647</td>
      <td>1.097140e+10</td>
    </tr>
    <tr>
      <th>QUS</th>
      <td>6.544810</td>
      <td>17.363645</td>
      <td>0.060750</td>
      <td>8.987473e+05</td>
    </tr>
    <tr>
      <th>NJAN</th>
      <td>6.059744</td>
      <td>12.248183</td>
      <td>0.123967</td>
      <td>2.242616e+05</td>
    </tr>
    <tr>
      <th>COM</th>
      <td>5.951998</td>
      <td>17.553615</td>
      <td>0.030213</td>
      <td>1.608322e+06</td>
    </tr>
    <tr>
      <th>OLD</th>
      <td>5.265350</td>
      <td>14.306479</td>
      <td>0.052916</td>
      <td>6.165391e+04</td>
    </tr>
    <tr>
      <th>CEZ</th>
      <td>3.556033</td>
      <td>9.337837</td>
      <td>0.056634</td>
      <td>1.656747e+07</td>
    </tr>
    <tr>
      <th>QQQ</th>
      <td>3.050288</td>
      <td>0.279764</td>
      <td>0.057925</td>
      <td>3.690442e+09</td>
    </tr>
    <tr>
      <th>CSB</th>
      <td>3.017281</td>
      <td>10.347451</td>
      <td>0.015413</td>
      <td>1.870201e+06</td>
    </tr>
  </tbody>
</table>
</div>
  </div>

  <div style="width: 50%;">
    <strong>Most Introverted ETFs</strong>
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>EI score_z</th>
      <th>volatility</th>
      <th>jump_frequency</th>
      <th>turnover</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>TFLO</th>
      <td>-0.962381</td>
      <td>0.027782</td>
      <td>0.004516</td>
      <td>1.819025e+06</td>
    </tr>
    <tr>
      <th>GSY</th>
      <td>-0.955023</td>
      <td>0.052438</td>
      <td>0.004255</td>
      <td>7.253978e+06</td>
    </tr>
    <tr>
      <th>NEAR</th>
      <td>-0.892219</td>
      <td>0.029407</td>
      <td>0.006732</td>
      <td>3.054644e+07</td>
    </tr>
    <tr>
      <th>FFIU</th>
      <td>-0.869383</td>
      <td>0.466919</td>
      <td>0.001517</td>
      <td>1.380672e+05</td>
    </tr>
    <tr>
      <th>FLOT</th>
      <td>-0.867753</td>
      <td>0.043740</td>
      <td>0.007237</td>
      <td>4.165771e+07</td>
    </tr>
    <tr>
      <th>OPER</th>
      <td>-0.861710</td>
      <td>0.027271</td>
      <td>0.009217</td>
      <td>3.732792e+05</td>
    </tr>
    <tr>
      <th>GIGB</th>
      <td>-0.833360</td>
      <td>0.566259</td>
      <td>0.001453</td>
      <td>1.975068e+06</td>
    </tr>
    <tr>
      <th>GUDB</th>
      <td>-0.832089</td>
      <td>0.365607</td>
      <td>0.004934</td>
      <td>6.186122e+04</td>
    </tr>
    <tr>
      <th>SPSB</th>
      <td>-0.828362</td>
      <td>0.029831</td>
      <td>0.010085</td>
      <td>1.850067e+07</td>
    </tr>
    <tr>
      <th>MINC</th>
      <td>-0.812451</td>
      <td>0.037702</td>
      <td>0.011293</td>
      <td>9.650548e+05</td>
    </tr>
  </tbody>
</table>
</div>
  </div>

</div>
