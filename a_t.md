---
layout: article
title: Assertive vs Turbulent
permalink: /a_t/
---

## Motivation: Why Assertive vs Turbulent?

Financial markets do not react to external shocks in a uniform way.  
Even when faced with the same macroeconomic event, different assets — and sometimes the market as a whole — can exhibit fundamentally different *reaction dynamics*.

In this project, we frame market reactions using a **fight-or-flight metaphor**, inspired by behavioral finance and stress-response systems:

- **Flight (panic / shock absorption)**  
  How abruptly and how strongly prices or volatility react immediately after an event.

- **Fight (recovery / stabilization)**  
  Whether the asset absorbs the shock and stabilizes, or whether stress persists and amplifies.

Under this view:

- **Assertive behaviour** corresponds to assets that  
  absorb shocks quickly, limit amplification, and recover efficiently.
- **Turbulent behaviour** corresponds to assets that  
  amplify stress, react violently, and exhibit slow or incomplete recovery.

Importantly, this is **not a psychological label**, but a **structural one**:
it describes *how an asset processes information and stress*, not investor sentiment.

---

## What Does Assertive vs Turbulent Mean in This Setting?

In our framework, an asset’s response to an event is characterized by:

1. **Magnitude of reaction**  
   How large the abnormal response is relative to the market baseline.

2. **Timing of reaction (flight)**  
   How quickly the peak reaction occurs after the event.

3. **Persistence of stress**  
   Whether abnormal behaviour dissipates or remains elevated.

4. **Recovery dynamics (fight)**  
   How long it takes for the asset to revert toward a “normal” state.

An **assertive** asset:
- exhibits smaller abnormal responses,
- reaches its peak reaction quickly,
- and recovers toward baseline within a short horizon.

A **turbulent** asset:
- exhibits larger abnormal responses,
- amplifies stress over time,
- and recovers slowly or not at all within the event window.

This interpretation naturally applies both to **returns** (price impact)
and to **volatility** (stress propagation).

---

## Quantifying Market Behaviour: Metrics Used

We use **event-study–based features** computed around predefined macro or market events.

### 1. Abnormal Returns and CAR (Stocks)

For individual stocks, we focus on **idiosyncratic price reactions**:

- **CAR (Cumulative Abnormal Return)**  
  Measures how much a stock outperforms or underperforms the market following an event.

From CAR, we extract:

- **MinCAR**  
  The deepest drawdown after the event (panic intensity).

- **T_flight**  
  Time to reach the trough (speed of panic).

- **T_fight**  
  Time needed to recover half of the drawdown (recovery speed).

- **CAR_end**  
  Residual abnormal return at the end of the event window (persistence).

These metrics directly capture *price-based fight-or-flight behaviour*.

---

### 2. Abnormal Volatility and CAV (ETFs)

For ETFs, price-based CAR is often less informative due to diversification.
Instead, we focus on **volatility transmission and amplification**.

- **CAV (Cumulative Abnormal Volatility)**  
  Measures how volatility deviates from market volatility after an event.

From CAV, we extract:

- **MaxCAV**  
  Peak volatility amplification (stress magnitude).

- **T_vol_flight**  
  Time to peak volatility (speed of stress propagation).

- **VolRecovery**  
  Time needed for volatility to drop below half of its peak (stability).

- **CAV_end**  
  Residual abnormal volatility at the end of the window (stress persistence).

These metrics describe **structural robustness**, not return direction.

---

## Experimental Design

### Stock Experiments

For stocks, we:

1. Identified a universe of stocks with sufficient historical coverage.
2. Computed CAR-based fight-or-flight metrics for each event.
3. Aggregated metrics across events to obtain per-stock behavioural profiles.
4. Standardized features and applied **K-Means clustering (k=2)**.
5. Interpreted clusters as:
   - **Assertive**: shallower drawdowns, faster recovery.
   - **Turbulent**: deeper drawdowns, persistent underperformance.

Stocks with incomplete event coverage were still assigned labels using
mean-imputation, allowing predictions even under partial data.

---

### ETF Experiments

For ETFs, the methodology was adapted to reflect structural differences:

1. ETFs were filtered by event availability to ensure meaningful volatility estimation.
2. Event-level **CAV-based features** were computed.
3. ETF-level features were aggregated across events.
4. Robust scaling was applied to account for heavy tails and leveraged products.
5. K-Means clustering was used to identify **structural volatility regimes**.

Crucially:
- ETFs were **trained on a high-confidence subset** (multiple events),
- but **predicted on a broader universe**, allowing classification with as little as one usable event.

For ETFs, the Assertive/Turbulent labels reflect:
- **volatility absorption vs amplification**,  
not price direction.

---

## Interpretation and Limitations

- Assertive vs Turbulent is **relative**, not absolute.
- Labels depend on:
  - event definitions,
  - window length,
  - and feature aggregation choices.
- Assets with fewer events have **lower confidence** labels.

Despite these limitations, the framework provides a **consistent, interpretable way**
to compare how different assets structurally respond to market stress.

---

## Summary

This project proposes a unified, event-based framework to characterize market behaviour as:

- **Assertive**: controlled, resilient, stabilizing.
- **Turbulent**: amplified, persistent, destabilizing.

By separating **price reactions (stocks)** from **volatility transmission (ETFs)**,
we obtain behaviourally meaningful classifications that go beyond traditional risk metrics.

The result is not a prediction of returns,
but a **map of how assets process shocks**.
