---
layout: article
title: Persistent vs Fluid
permalink: /s_n/
---

# Do Markets have Persistent "Moods"?

When we say that markets are "calm", "panicking", or "euphoric", we are implicitly claiming that prices move through a small set of recurring, qualitatively distinct regimes. In this section, we make that idea precise and show how to test whether a given stock or ETF behaves in a **persistent** or **fluid** way with respect to these regimes.

Concretely, we work with four major ETFs:

- SPY - broad U.S. equity market (S&P 500)
- QQQ – tech‐heavy Nasdaq 100
- XLY – Consumer Discretionary (cyclical “risk‑on” sector)
- GLD – Gold (typical “risk‑off” asset)

We ask two questions:

1. Can we uncover a small number of hidden regimes that behave like “market moods” (bull, bear, correction, calm)?
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

(Maybe insert graph to show what the features look like)

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
![SPY regimes](/assets/images/SPY_colors.png)
#### QQQ:
![QQQ regimes](/assets/images/QQQ_colors.png)
#### XLY:
![XLY regimes](/assets/images/XLY_colors.png)
#### GLD:
![GLD regimes](/assets/images/GLD_colors.png)

We can see that a high-volatility, negative-return regime often aligns with crisis periods and sharp drawdowns. A low-volatility, positive-return tracks steady bull markets. For GLD, some regimes that are "defensive" (e.g. strong when equities are weak) tend to appear when SPY/QQQ are in stressed or correction-type states.

This qualitative alignment is the first sanity check that HMM states are economically interpretable. 

# Are These "Moods" Statistically Distinct?

We test whether the regimes are not just cosmetic labels by comparing their return distributions. For each ETF, and for each pair of states ( i, j ), we perform Kolmogorov–Smirnov (KS) tests on daily log returns:

- Null hypothesis ( H_0 ): returns in regime ( i ) and regime ( j ) come from the same distribution.
- We typically observe small p‑values (e.g. ( p < 0.01 )) for most state pairs, especially those that visually look different (bull vs. bear, calm vs. crisis).

