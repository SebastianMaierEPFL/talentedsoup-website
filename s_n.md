---
layout: article
title: Persistent vs Fluid
permalink: /s_n/
---

<style>
  .next-page-container {
    text-align: center;
    margin: 3rem 0 1rem;
  }

  .next-page-button {
    display: inline-block;
    padding: 0.75rem 1.5rem;
    background: #9c5ec5ff;
    color: #fbfbfbff;
    border-radius: 9999px;
    text-decoration: none;
    font-weight: 600;
  }

  .next-page-button:hover {
    background: #774796ff;
  }

  .next-page-button,
  .next-page-button:visited,
  .next-page-button:hover,
  .next-page-button:active {
    color: #fbfbfbff !important;
  }
  /* Legend styles */
  .regime-legend {
    display: flex;
    flex-wrap: wrap;
    gap: 0.75rem 1.5rem;
    margin: 1rem 0 2rem;
    font-size: 0.9rem;
  }

  regime-legend__item {
    display: inline-flex;
    align-items: center;
    gap: 0.35rem;
  }

  .regime-legend__swatch {
    display: inline-block;
    width: 30px;
    height: 14px;
    border-radius: 3px;
    border: 1px solid rgba(0,0,0,0.2);
  }

  /* Adjust these colors to match your plots */
  .regime-legend__swatch--bull {
    background: #2ecc70c8;
  }

  .regime-legend__swatch--bear {
    background: #e74d3cbb;
  }

  .regime-legend__swatch--correction {
    background: #e67d22b6;
  }

  .regime-legend__swatch--recovery {
    background: #f1c40fb3;
  }
</style>

# Do Markets have Persistent "Moods"?

<img src="{{ '/assets/images/persistent.png' | relative_url }}"
     alt="persistent image"
     style="float: right; max-width: 50%; height: auto; margin: 0 0 1em 1.5em;">

When we say that markets are "calm", "panicking", or "euphoric", we are implicitly claiming that prices move through a small set of recurring and qualitatively distinct regimes. In this section, we make that idea precise and show how to test whether a given stock or ETF behaves in a **persistent** or **fluid** way with respect to these regimes.

Concretely, we work with four major ETFs:

- SPY - broad U.S. equity market (S&P 500)
- QQQ – tech‐heavy Nasdaq 100
- XLY – Consumer Discretionary (cyclical “risk‑on” sector)
- GLD – Gold (typical “risk‑off” asset)

<div style="clear: both;"></div>

<div style="text-align: center;">
    <img src="{{ '/assets/images/fluid.png' | relative_url }}"
        alt="fluid image"
       style="max-width: 70%; height: auto;">
</div>

We ask two questions:

1. Can we uncover a small number of hidden regimes that behave like “market moods” (bull, bear, correction, recovery)?
2. For each ETF, are these regimes persistent (long‑lived, stable, few switches) or fluid (short‑lived, frequently changing)?


# From Prices to "Mood Signals"

Raw prices are not directly interpretable as emotions. To get closer to "mood", we engineer a set of daily features that capture return, risk, momentum, and activity. 

For each ETF, we start from standard daily OHLCV data and compute:

- Log Return – day‑to‑day log price change (Adj Close)
- Rolling Volatility (21 days) – annualized standard deviation of log returns over the last month
- Rolling Momentum (21 days) – average log return over the last month
- Volume Change (21 days) – log change in trading volume vs. 21 days ago
- Volatility Change (21 days) – change in rolling volatility vs. 21 days ago

To make these features comparable over time, we apply a rolling z-score using a 252-day (approximatively 1-year) window, so each feature is locally standardized.

# Letting the Data Discover Regimes: Hidden Markov Models

We do not hand‑label bull or bear markets. Instead, we let the data identify a small set of latent regimes by fitting a Hidden Markov Model (HMM) on the engineered features.

For each ETF (SPY, QQQ, XLY, GLD):

1. We feed the feature matrix (5 features per day) into an HMM with 4 hidden states.
2. Each state emits observations from a multivariate Gaussian distribution with its own mean and covariance.
3. The hidden state process follows a Markov chain with a transition matrix describing how likely the system is to stay in or switch between regimes from day to day.

Intuitively:
- Each state corresponds to a characteristic pattern of return, volatility, and volume (e.g. low volatility & positive momentum vs. high volatility & negative returns).
- The transition matrix tells us how these regimes evolve over time (e.g. calm periods occasionally jump into stressed regimes, but stressed regimes are short‑lived).

# Making the Regimes Concrete

### Visualizing the Regimes on Price Charts

To see that the inferred regimes are meaningful, we overlay the decoded states onthe price series for each ETF.

#### SPY:
<!-- ![SPY regimes](/assets/images/SPY_colors.png) -->
![SPY regimes]({{ '/assets/images/SPY_colors.png' | relative_url }})
#### QQQ:
<!-- ![QQQ regimes](/assets/images/QQQ_colors.png) -->
![QQQ regimes]({{ '/assets/images/QQQ_colors.png' | relative_url }})

#### XLY:
<!-- ![XLY regimes](/assets/images/XLY_colors.png) -->
![XLY regimes]({{ '/assets/images/XLY_colors.png' | relative_url }})
#### GLD:
<!-- ![GLD regimes](/assets/images/GLD_colors.png) -->
![GLD regimes]({{ '/assets/images/GLD_colors.png' | relative_url }})

<div class="regime-legend">
  <div class="regime-legend__item">
    <span class="regime-legend__swatch regime-legend__swatch--bull"></span>
    Bull
  </div>
  <div class="regime-legend__item">
    <span class="regime-legend__swatch regime-legend__swatch--bear"></span>
    Bear
  </div>
  <div class="regime-legend__item">
    <span class="regime-legend__swatch regime-legend__swatch--correction"></span>
    Correction
  </div>
  <div class="regime-legend__item">
    <span class="regime-legend__swatch regime-legend__swatch--recovery"></span>
    Recovery
  </div>
</div>

We can see that a high-volatility, negative-return regime often aligns with crisis periods and sharp drawdowns. A low-volatility, positive-return tracks steady bull markets. For GLD, some regimes that are "defensive" (e.g. strong when equities are weak) tend to appear when SPY/QQQ are in stressed or correction-type states.

This qualitative alignment is the first sanity check that HMM states are economically interpretable. 

# Are These "Moods" Statistically Distinct?

We test whether the regimes are not just cosmetic labels by comparing their return distributions. For each ETF, and for each pair of states ( i, j ), we perform Kolmogorov–Smirnov (KS) tests on daily log returns:

- Null hypothesis ( H_0 ): returns in regime ( i ) and regime ( j ) come from the same distribution.
- We typically observe small p‑values (e.g. ( p < 0.05 )) for most state pairs, especially those that visually look different (bull vs. bear, calm vs. crisis).

<table>
  <tr>
    <td align="center">
      <strong>SPY</strong><br>
      <img src="{{'/assets/images/pval_spy.png' | relative_url}}" alt="SPY p-values">
    </td>
    <td align="center">
      <strong>QQQ</strong><br>
      <img src="{{'/assets/images/pval_qqq.png' | relative_url}}" alt="QQQ p-values">
    </td>
  </tr>
  <tr>
    <td align="center">
      <strong>XLY</strong><br>
      <img src="{{'/assets/images/pval_xly.png' | relative_url}}" alt="XLY p-values">
    </td>
    <td align="center">
      <strong>GLD</strong><br>
      <img src="{{'/assets/images/pval_gld.png' | relative_url}}" alt="GLD p-values">
    </td>
  </tr>
</table>

Across SPY, QQQ, XLY, and GLD, KS tests on regime‑conditioned returns strongly reject the hypothesis that regimes share the same return distribution. This confirms that our “emotional states” are not arbitrary and that they correspond to statistically distinct patterns of market behavior.

# How Regimes Transition: Persistence vs Fluidity

To talk about persistence and fluidity, we move from “what regimes look like” to “how they evolve over time”.

Each HMM yields a transition matrix ( $P$ ), where ( $P_{ij}$ ) is the probability of going from state ( $i$ ) today to state ( $j$ ) tomorrow. The diagonal entries ( $P_{ii}$ ) capture self‑transition probabilities.

- High ( $P_{ij}$ ): once in the state ( $i$ ), the process tends to stay there (persistent).
- Low ( $P_{ij}$ ): state ( $i$ ) is short-lived. The process quickly leaves it.

We can visualize this matrix for each ETF.

<table>
  <tr>
    <td align="center">
      <strong>SPY</strong><br>
      <img src="{{'/assets/images/transition_spy.png' | relative_url}}" alt="SPY transition">
    </td>
    <td align="center">
      <strong>QQQ</strong><br>
      <img src="{{'/assets/images/transition_qqq.png' | relative_url}}" alt="QQQ transition">
    </td>
  </tr>
  <tr>
    <td align="center">
      <strong>XLY</strong><br>
      <img src="{{'/assets/images/transition_xly.png' | relative_url}}" alt="XLY transition">
    </td>
    <td align="center">
      <strong>GLD</strong><br>
      <img src="{{'/assets/images/transition_gld.png' | relative_url}}" alt="GLD transition">
    </td>
  </tr>
</table>

We cap the color map at a low number to be able to view the transition probabilities between different states.

We can see that the transition heatmaps are similar between the three first stocks and that the GLD stock differs slighty. Implying that similar stocks do have similar state transition probabilities. We observe that there are higher probabilities of going from a state into a Bull or Bear regime with the Recovery and Correction states not transitioning between eachother. 

# Sojourn Durations: How Long Do Regimes Last?

Another way to characterize persistence is to look directly at sojourn durations: the number of consecutive days the process spends in a given state before switching.

For each ETF and each state:

1. Extract all contiguous runs in that state.
2. Record their lengths in days.

<table>
  <tr>
    <td align="center">
      <strong>SPY</strong><br>
      <img src="{{'/assets/images/sojourn_spy.png' | relative_url}}" alt="SPY sojourn" style="max-width: 85%; height: auto;">
    </td>
    <td align="center">
      <strong>QQQ</strong><br>
      <img src="{{'/assets/images/sojourn_qqq.png' | relative_url}}" alt="QQQ sojourn" style="max-width: 85%; height: auto;">
    </td>
  </tr>
  <tr>
    <td align="center">
      <strong>XLY</strong><br>
      <img src="{{'/assets/images/sojourn_xly.png' | relative_url}}" alt="XLY sojourn" style="max-width: 85%; height: auto;">
    </td>
    <td align="center">
      <strong>GLD</strong><br>
      <img src="{{'/assets/images/sojourn_gld.png' | relative_url}}" alt="GLD sojourn" style="max-width: 85%; height: auto;">
    </td>
  </tr>
</table>

We observe that across the ETFs, the sojourns in of each same state ressemble eachother. We can see that the distribution of the Recovery state has a more flat distribution, meaning that the number of days it takes to get out of this state has more variance than other states.

# Cross-Asset Co-Movements of Regimes

Finally, to show that these regimes make economic sense across assets, we can inspect how the regimes of SPY and GLD (for example) co-occur.

<div class="flourish-embed flourish-heatmap" data-src="visualisation/26907567"><script src="https://public.flourish.studio/resources/embed.js"></script><noscript><img src="https://public.flourish.studio/visualisation/26907567/thumbnail" width="100%" alt="heatmap visualization" /></noscript></div>


From this cross table, we can observe that GLD acts as a safe haven when the SPY index is in a weak state, such as when the SPY is in a recovery state, it is probable that the GLD is in a Bull state. And when the SPY is in a strong state, then the GLD is in a Recovery state. This shows the interaction between a risk-on vs a risk-off indexes.

# Persistency vs Fluidity Index

To finally give an emotional attribute to each stock and ETF, we define a metric. The question becomes: "What should 'P' measure?"

For each stock / ETF 'P' should capute:

- High P (persistent):
    - Long average time in a given state
    - High self-transition probabilities
    - Few regime switches per year
- Low P (fluid):
    - Short sojourns
    - Low self-transition probabilities
    - frequent switching between states

For each stock and ETF's HMM, we compute the following features:
- mean self-transition probability
- minimum self-transition probability
- mean expected duration (days)
- number of regime switches per year
- mean coefficient of variation (CV) of sojourn durations


$$
\mathrm{CV} = \frac{s}{\bar{d}}
$$

where
$\bar{d}$ is the sample mean of the sojourn durations $d_1, \dots, d_n$,

$s$ is the sample standard deviation of those durations:

$$
\bar{d} = \frac{1}{n}\sum_{i=1}^{n} d_i,
\qquad
s = \sqrt{\frac{1}{n-1}\sum_{i=1}^{n}\left(d_i - \bar{d}\right)^2}
$$

So, explicitly:

$$
\mathrm{CV}
= \frac{\sqrt{\frac{1}{n-1}\sum_{i=1}^{n}\left(d_i - \bar{d}\right)^2}}
{\bar{d}}
$$

We first invert the values of the number of switches per year and the mean CV, since more swicthes and more a stock being more erratic implies a lower P. We then standardize the features to ensure equal contributions between the features, and take the mean of each feature for every datapoint to give us a scalar value. The higher the P score is for a stock, the more persistent it is.

<div class="flourish-embed flourish-scatter" data-src="visualisation/26916318"><script src="https://public.flourish.studio/resources/embed.js"></script><noscript><img src="https://public.flourish.studio/visualisation/26916318/thumbnail" width="100%" alt="scatter visualization" /></noscript></div>

Let's take a look at the collection of stocks and see their P type. From the plot above, we can see that most of the persistent stocks have a low number of switches per year and a high mean expected duration. Some fluid stocks also lie in the region of the persistent stocks. This could be due to a low minimum self-transition probability and CV duration mean, which could drag the final decision to being a fluid stock. 

Finally, to get a decision between having a Persistent or Fluid stock, we take the distribution of the $\text{P Score}$ and take the top 75 percentile to being Fluid stocks and the rest are Persistent stocks. 

<div class="next-page-container">
  <a class="next-page-button"
     href="{{ '/f_t/' | relative_url }}">
    Next page →
  </a>
</div>