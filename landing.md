---
layout: landing
title: Does the Stock Market have a Personality?
# excerpt: >
#   Giving the Stock Market Human Emotions
permalink: /

article_header:
  height: 60vh
  theme: default
  background_color: "#afafafff"
  background_image:
    gradient: "linear-gradient(rgba(255, 255, 255, 0), rgba(255, 255, 255, 0))"
    src: /assets/images/commander.svg
  actions:
    - text: Data Analysis
      type: error
      url: /e_i/
    - text: Test Stock
      type: outline-theme-default
      url: /testt/


data:
  sections: []           # optional sections below the hero; leave empty for now
---



<!-- <div style="max-width: 720px; margin: 0 auto; text-align: center;">

  <h1>Introduction</h1>
  <p>
    Does market have a personality? Are there hidden similarities between human and stocks? Is it possible to distinguish two different stocks since they may react differently in the grand market? Our project aims to do a detailed analysis of the stock characteristics, coming from 5 different angles, dive into different traits of a stock and MAYBE, stock does have a personality. 
  </p>

</div> -->

<!-- <div style="max-width: 720px; margin: 0 auto; padding: 0 1.5rem; text-align: left;">

  <h1>Put buttons at the end to direct to the start of the analysis</h1>
  <p>
    This is some intro text for the landing page. You can write a short
    description of what TalentedSoup is about here.
  </p>

</div>

<div style="max-width: 960px; margin: 0 auto; padding: 0 1.5rem; text-align: left;">

  <h1>Welcome to TalentedSoup</h1>
  <p>
    This is some intro text for the landing page. You can write a short
    description of what TalentedSoup is about here. Would the rest of the text be put in the bottom? 
  </p>

</div> -->

# Is the Market Alive — What Is the Stock Market’s Personality?
## Into the Psychophysiology of NASDAQ

## Abstract  
We treat the NASDAQ market as a living system: it reacts to shocks, shifts moods, forms social alliances, and exhibits rhythms that speed up, slow down, or collapse under stress. 

Our main goal is to build one unified behavioral diagnosis based on five different perspectives: a market-wide “personality profile” that is measurable from price/volume data and interpretable in human terms.

---

## The Market Personality Framework (MBTI-inspired)  

Instead of stopping at “the market was volatile,” we classify assets (stocks & ETFs) using a 4-axis personality code: 

<div style="max-width: 720px; margin: 0 auto; text-align: center;">
  <p>[I/E] [P/F] [A/R] [O/C]</p>
</div>

* **I / E — Introverted / Extroverted**: how strongly an asset “couples” to the market or peers (market expressiveness / social coupling).
* **P / F — Persistent / Fluid**: whether behavior stays stable in long regimes or switches often.
* **A / R — Assertive / Restrained**: how decisively it moves directionally (strong trends vs cautious drift).
* **O / C — Organized / Chaotic**: whether its spectrum has a clear “heartbeat” (dominant cycles) or looks like noise.
  
### Stress modifier: Stable vs Reactive
On top of the four letters, we add a stress-response tag:
* **Stable (S)**: rebounds toward baseline quickly after shocks
* **Reactive (R)**: slow/incomplete recovery; shocks reshape behavior longer-term

This lets two assets share a baseline personality but behave very differently under crisis conditions.

---

## How the five analyses become one personality  

Each module is a “vital sign” that feeds the personality framework: 

### 1. Event Impact → Stable / Reactive (S/R)
We implement an event-study framework to quantify how major global shocks shape collective market dynamics. We first identify a set of widely recognized crisis events and define event windows around each one. Within each window, we compute abnormal returns (relative to a baseline/expected return) and aggregate them into cumulative abnormal returns (CAR) to capture the full impact-and-recovery trajectory. We then compare CAR response profiles across events to test whether markets show consistent patterns—such as synchronized panic followed by recovery—and interpret the post-event rebound (quick return toward baseline vs prolonged deviation) as a Stable vs Reactive stress-response label for the market and/or assets.

### 2. Behavioral Kinematics → Assertive / Restrained (A/R)
To capture “market behavior” directly from price motion, we use a humanized, derivative-based framework. Closing prices are first smoothed (to suppress day-to-day noise), then we compute the first derivative (a velocity-like proxy for momentum) and second derivative (an acceleration-like proxy for shifts in momentum). The sign and magnitude of these two signals define four intrinsic behavioral regimes:

* Confidence (upward motion with supportive acceleration)
* Discipline (upward motion with cooling acceleration—steady, controlled advance)
* Descent (downward motion with reinforcing acceleration—panic-like decline)
* Tenacity (downward motion with stabilizing acceleration—recovery attempts / resilience)
  
We summarize each asset by how long it spends in each regime, how frequently it transitions, and how strongly directional phases dominate. This produces an Assertive vs Restrained axis: assertive assets show clearer, longer directional episodes (strong, sustained “moves”), while restrained assets spend more time in moderated/transition dynamics where direction is weaker, shorter-lived, or quickly self-correcting.

### 3. Hidden Regime Dynamics (HMM) → Persistent / Fluid (P/F)
To test whether markets exhibit “mood swings,” we model market behavior as a sequence of latent emotional regimes. From daily NASDAQ time series, we construct a compact feature set that captures changing temperament—typically log returns, rolling volatility, momentum, and volume change. We then fit a Hidden Markov Model (HMM) that assumes these observable features are generated by an underlying, unobserved state (a “mood”) and learns both the characteristics of each regime (its typical return/volatility/volume profile), and the transition dynamics between regimes (how likely the market is to stay vs switch). The result is an interpretable regime timeline that can be read as alternating phases such as euphoria, anxiety, fear, stabilization, or recovery. We summarize each asset (or the market aggregate) by its regime persistence (long stable runs, high self-transition probabilities) versus regime fluidity (frequent switching, rapid transitions). This directly defines the Persistent vs Fluid axis: persistent assets remain in the same mood longer and transition rarely, while fluid assets flip states often, showing more frequent psychological “mood changes” in their market dynamics.

### 4. ETF / market coupling + expressiveness → Introverted / Extroverted (I/E)
We treat ETFs as a social system and visualize their relationships as a friendship network built from coordination: if two ETFs (or stocks) have sufficiently strong Pearson correlation in log-returns, they are considered “connected”. To quantify how visibly an asset reacts to market information, we compute an Emotional Index (EI) from three time-series components—volatility, jump frequency, and a turnover proxy—standardize them (z-scores), and combine them into a single expressiveness score. This lens supports three core tests: (1) whether correlation-based communities remain stable over time, (2) whether any instrument shows systematic influence rather than purely mutual coordination, and (3) whether different market regimes produce reorganized clusters (e.g., tech vs commodities). Finally, we translate these results into an Introverted vs Extroverted axis: Extroverted assets exhibit amplified, expressive behavior (higher EI; stronger coupling and information transmission), while Introverted assets show muted reactions, smoother dynamics, and weaker coupling to the market’s collective motion.

### 5. Fourier “Heartbeat” → Organized / Chaotic (O/C)
To measure the market’s rhythm in the frequency domain, we first transform adjusted prices into log returns and treat each analysis segment as a windowed signal sampled at a uniform trading cadence. For each window, we compute the discrete Fourier transform and its power spectrum, then convert frequency bins into an interpretable tempo in cycles per year (and the equivalent period in trading days/months). The dominant “heartbeat” of a sector (or asset) is defined as the highest-power frequency, which we summarize in sector × crisis heatmaps and in “family panels” that compare each sector ETF against flagship constituents (plus ETF–stock synchronization via correlations). To turn rhythm into a personality axis, we go beyond “what is the heart rate?” and ask “how healthy is the rhythm?” We characterize each ticker’s spectrum using two signal-processing diagnostics: spectral entropy (how noise-like / disordered the spectrum is) and energy concentration (how much total power is captured by the top dominant frequencies). Low entropy + high concentration corresponds to Organized (O) assets with a clear, spiky “sinus rhythm,” while high entropy + low concentration corresponds to Chaotic (C) assets whose energy is scattered broadly across frequencies (fibrillation-like).

---

## Research questions

### 1. Do shocks leave consistent signatures?
Are crisis responses similar across events (market “memory”), and which assets recover vs remain distorted?

### 2. Can behavior be inferred directly from motion?
Do price-derived dynamics reveal interpretable phases and regime transitions?

### 3. Does the market have “social structure”?
Do ETFs form stable communities, and do alliances/rivalries reorganize under stress?

### 4. Does the market have a heartbeat?
Do dominant cycles exist, and do crises synchronize or fracture rhythms across sectors?

### 5. Do these signals agree into a personality diagnosis?
Do “extroversion,” “fluidity,” “chaos,” and “reactivity” rise together under stress?