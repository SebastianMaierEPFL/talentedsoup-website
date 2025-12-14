---
layout: article
title: Sensing vs iNtuition (Persistent vs Fluid)
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