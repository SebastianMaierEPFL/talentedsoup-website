---
layout: article
title: Perceiving vs Judging
permalink: /p_j/
---
---
layout: article
title: Getting the Pulse of the Market
permalink: /heartbeat/
---

Can Fourier analysis reveal whether sector returns pulse like a heart under different market stresses?


## Heartbeat Analogy

We can think of market returns like a heartbeat: Fourier spectra trace the pulse, while sector–ETF correlations show how synchronized the organs are. 
In resting (pre-crisis) periods, we would expect stable, low-variance rhythms; under stress (crisis), frequencies intensify and coordination spikes or fractures; during recovery (post-crisis), we can test whether dominant cycles re-emerge and sectors resynchronize—or settle into a new baseline.


## Research Questions

- Do sector and industry returns hide recurring cycles?
- Which rhythms persist, fade, or spike during crises?
- Can ETF benchmarks and flagship stocks stay in sync when the market is under stress?


## Fourier Analysis Foundations

Starting from adjusted prices $P_t$ we construct log returns

$$
r_t = \ln P_t - \ln P_{t-1}
= \ln\!\left(\frac{P_t}{P_{t-1}}\right).
$$

which stabilise variance and make consecutive windows additive. Each analysis window then treats the demeaned series $r_t$ sampled at a uniform interval $\Delta t$ (expressed in years) and applies the discrete Fourier transform (DFT)

$$
\hat{R}_k = \sum_{t=0}^{N-1} (r_t - \bar{r}) e^{-2\pi i k t / N},
$$

where $N$ is the number of observations in the window and $k$ indexes the frequency bin. The associated cyclic tempo is

$$
f_k = \frac{k}{N \Delta t}\; \text{cycles per year},
$$

with an equivalent period $T_k = 1 / f_k$ that we also express in trading days and months. The power spectrum $P_k = |\hat{R}_k|^2$ highlights which frequencies carry the strongest energy, allowing us to identify dominant market heartbeats. Filtering to the highest-power components yields the summary tables and plots used throughout the notebook.


## Loading the Shared Toolkit

```python
import importlib.util
import os
import sys
from pathlib import Path
import tempfile
from tqdm import tqdm

from IPython.display import Image, Markdown, display
import matplotlib.pyplot as plt
import matplotlib.patheffects as pe
import numpy as np
import pandas as pd
import seaborn as sns

BASE_DIR = Path.cwd() # path to the directory containing this notebook

if str(BASE_DIR) not in sys.path:
    sys.path.insert(0, str(BASE_DIR))

# import the shared modules
import market_fourier
import heartbeat_visuals as hv
import features
import clustering
import plotting
import save

sns.set_theme(style="whitegrid") # setting a consistent style for plots

WINDOW_ORDER = list(market_fourier.CRISIS_WINDOWS.keys())
CRISIS_GROUPS = hv.CRISIS_GROUPS

print("Crisis windows in order:")
print(WINDOW_ORDER)
print("\nSector ETF proxies:")
for sector, ticker in market_fourier.SECTOR_ETFS.items():
    print(f"- {sector}: {ticker}")
print("\nStock mapping:")
for sector, tickers in hv.DEFAULT_STOCKS.items():
    print(f"- {sector}: {', '.join(tickers)}")
```

    Crisis windows in order:
    ['dotcom_pre', 'dotcom_peak', 'dotcom_recovery', 'gfc_pre', 'gfc_peak', 'gfc_recovery', 'brexit_pre', 'brexit_vote', 'brexit_post', 'covid_pre', 'covid_crisis']
    
    Sector ETF proxies:
    - Consumer Discretionary: XLY
    - Financials: XLF
    - Health Care: XLV
    - Technology: XLK
    
    Stock mapping:
    - Consumer Discretionary: AMZN, HD
    - Financials: JPM, BAC
    - Health Care: JNJ, UNH
    - Technology: AAPL, MSFT


## Checking the Pulse Inputs

We start by pulling prices and returns with `market_fourier` to verify coverage and sampling.


We will use the technology sector to verify prices and log returns before scaling the analysis to every sector and window.

### Technology pulse diagnostic

A quick look at XLK and AAPL to confirm the data are clean before we explore every sector-window combination.


```python
etf_prices = market_fourier.load_price_series("XLK", kind="etfs")
etf_returns = market_fourier.get_returns("XLK", kind="etfs")
stock_prices = market_fourier.load_price_series("AAPL", kind="stocks")
stock_returns = market_fourier.get_returns("AAPL", kind="stocks")

print("\n### XLK price history (head)")
print(etf_prices.head())

print("\n### AAPL price history (head)")
print(stock_prices.head())

print("\n### XLK log returns (summary)")
print(etf_returns.describe())

print("\n### AAPL log returns (summary)")
print(stock_returns.describe())

```

    
    ### XLK price history (head)
    date
    1998-12-22    25.008554
    1998-12-23    25.606020
    1998-12-24    25.508471
    1998-12-28    25.581635
    1998-12-29    25.654800
    Name: XLK, dtype: float64
    
    ### AAPL price history (head)
    date
    1980-12-12    0.406782
    1980-12-15    0.385558
    1980-12-16    0.357260
    1980-12-17    0.366103
    1980-12-18    0.376715
    Name: AAPL, dtype: float64
    
    ### XLK log returns (summary)
    count    5551.000000
    mean        0.000202
    std         0.016150
    min        -0.148662
    25%        -0.006030
    50%         0.000484
    75%         0.007241
    max         0.149296
    Name: XLK, dtype: float64
    
    ### AAPL log returns (summary)
    count    10253.000000
    mean         0.000623
    std          0.028693
    min         -0.731247
    25%         -0.012703
    50%          0.000000
    75%          0.014035
    max          0.286891
    Name: AAPL, dtype: float64


## Dominant Cycles Across Crises

`heartbeat_visuals.build_sector_cycle_table` sweeps every sector/window pair and records the strongest FFT components: our stand-in for heartbeat spikes.

**Table columns**

`sector` — ETF’s sector pulse.  
`window` — crisis phase (pre, peak, recovery).  
`cycles_per_year` — dominant rhythm (beats per year).  
`period_days` — equivalent cycle length in trading days.  
`approx_months` — same period translated into months for intuition.  
`power` — FFT power.

```python
sector_cycles = hv.build_sector_cycle_table(
    market_fourier, top_n=3, window_order=WINDOW_ORDER
)
print(sector_cycles.head())
print(f"Total records: {len(sector_cycles)}")
```

                       sector       window  cycles_per_year  period_days  \
    0  Consumer Discretionary   dotcom_pre        18.000000    20.291667   
    1  Consumer Discretionary   dotcom_pre        31.500000    11.595238   
    2  Consumer Discretionary   dotcom_pre        36.000000    10.145833   
    3  Consumer Discretionary  dotcom_peak        37.503212     9.739166   
    4  Consumer Discretionary  dotcom_peak        40.740899     8.965192   
    
       approx_months     power  
    0       0.666667  0.021220  
    1       0.380952  0.020505  
    2       0.333333  0.008299  
    3       0.319973  1.560739  
    4       0.294544  1.427863  
    Total records: 132


### Where the Market Beats Fastest

The heatmap highlights the highest-power frequency (dominant frequency - cycles/year) for each sector and crisis window. Darker shades mark faster heartbeats.

```python
hv.plot_sector_heatmap(sector_cycles, window_order=WINDOW_ORDER)
display(Image(filename=str(hv.OUTPUT_DIR / "sector_frequency_heatmap.png")))
```

    [saved] figure: /Users/magdaturcanu/Desktop/SV/ADA/outputs/sector_frequency_heatmap.png



    
![png](heartbeat_files/output_13_1.png)
    


Just by looking at this heatmap, we can sketch how markets move in the frequency domain—and it’s clear that crises don’t uniformly “speed up” markets.

During the dot-com peak, most sectors jump into the high-frequency band (~37–47 cycles/year). Technology leads (~46.9), Financials also accelerate (~40.7), and Consumer Discretionary moves up strongly (~37.5). The pattern is broadly consistent with market-wide acceleration—though Health Care is slightly less aligned, easing down to ~33.5 instead of rising.

At the GFC peak, the heatmap shows a split tempo. Health Care stays very fast (~39.6) while Consumer Discretionary collapses to ~2.9 cycles/year, indicating long, slow swings. Financials sit in the middle (~32.6) and Technology slows (~18.0). Same crisis, radically different rhythms by sector—evidence that stress can bifurcate the market rather than push everything into overdrive.

In the COVID window, many sectors settle into mid–low frequencies (~16–32). In the crisis slice, Consumer Discretionary, Health Care, and Technology converge around ~15.8, while Financials remains higher (~31.5). Instead of rapid oscillations, the pattern points to more persistent, extended moves for most sectors—though the divergence in Financials suggests the shock is not fully uniform, and more data is required to actually confirm it.

### Sector Family Panels

Do flagship stocks track the same heartbeat as their sector ETF?

```python
corr_df = hv.compute_window_correlations(
    market_fourier,
    stocks_map=hv.FAMILY_STOCKS,  
    windows=WINDOW_ORDER,
    min_obs=30,
)

hv.plot_sector_family_panels(market_fourier, WINDOW_ORDER)
hv.plot_correlation_heatmaps(corr_df, window_order=WINDOW_ORDER)
```

    [saved] figure: /Users/magdaturcanu/Desktop/SV/ADA/outputs/sector_family_xly.png
    [saved] figure: /Users/magdaturcanu/Desktop/SV/ADA/outputs/sector_family_xlf.png
    [saved] figure: /Users/magdaturcanu/Desktop/SV/ADA/outputs/sector_family_xlv.png
    [saved] figure: /Users/magdaturcanu/Desktop/SV/ADA/outputs/sector_family_xlk.png
    [saved] figure: /Users/magdaturcanu/Desktop/SV/ADA/outputs/corr_xly.png
    [saved] figure: /Users/magdaturcanu/Desktop/SV/ADA/outputs/corr_xlf.png
    [saved] figure: /Users/magdaturcanu/Desktop/SV/ADA/outputs/corr_xlv.png
    [saved] figure: /Users/magdaturcanu/Desktop/SV/ADA/outputs/corr_xlk.png


```python
# Health Care (XLV)
img_path = hv.OUTPUT_DIR / "sector_family_xlv.png"
corr_path = hv.OUTPUT_DIR / "corr_xlv.png"

display(Markdown("#### Health Care (XLV)"))
display(Image(filename=str(img_path)))
display(Image(filename=str(corr_path)))
```


#### Health Care (XLV)



    
![png](heartbeat_files/output_17_1.png)
    



    
![png](heartbeat_files/output_17_2.png)
    


Health Care behaves like a defensive **metronome**: it rarely hits extremes, and XLV (red) usually sits near the middle of its stocks, so the ETF really does track the “central beat” of the family. The correlation heatmap backs this up: ETF–stock Pearson $r$ stays **positive in every window**, ranging from about **0.23 to 0.92**, with **all p-values $\approx 0.000$** except the weakest link (UNH in dot-com pre: $r \approx 0.34$, $p = 0.011$).

* **Dot-com:** everyone starts mid–fast, then **speeds up together into the peak** in the spectra. Correlations in the pre window are already moderate for the big pharma names (JNJ/MRK/PFE/TMO around **0.50–0.60**, $p \approx 0.000$), while UNH is only loosely tied to XLV ($r \approx 0.34$, $p = 0.011$). At the peak, synchrony actually loosens (several $r$ values drop toward **0.23–0.35**), and in recovery it tightens again for most stocks (PFE $r \approx 0.76$, MRK $r \approx 0.57$), matching the picture of **uneven cooling and partial de-synchronization**.

* **GFC:** pre-crisis spectra sit in a **calm mid-band**, and correlations are steady (roughly **0.43–0.67**). At the peak you see the spectral **two-speed pattern** (one very slow name vs faster peers), but statistically everyone locks onto XLV: JNJ and PFE reach about **0.76–0.81**, TMO/MRK/UNH around **0.64–0.72** (all $p \approx 0.000$). Recovery keeps them high (**0.63–0.79**), so the group **re-clusters around mid frequencies** with XLV dampening extremes rather than amplifying them.

* **Brexit:** spectrally you get a **mixed pre-vote lineup**, then the vote triggers **short-lived spikes** in a couple of names while XLV barely moves. Correlations pre-vote are already strong (most **0.61–0.73**), dip slightly around the vote (UNH drops to $r \approx 0.44$, others in the **0.57–0.77** range), then recover into the **0.65–0.75** band post-vote. That matches the idea of a **brief shock with quick re-synchronization**.

* **COVID:** before the shock, the spectra show the group is **noticeably up-tempo**, and ETF–stock $r$ is solid but not extreme (**0.53–0.66**). During the crisis, the panels show **all names, including XLV, shift down to mid–low frequencies together**, and the correlations spike: JNJ/MRK/PFE/TMO all sit around **0.83–0.89**, UNH peaks at **$r \approx 0.92$**, all with $p \approx 0.000$. So in COVID, Health Care not only slows its heartbeat, it does so **almost perfectly in sync with XLV**, reinforcing the “defensive metronome” story.

```python
# Consumer Discretionary (XLY)
img_path = hv.OUTPUT_DIR / 'sector_family_xly.png'
corr_path = hv.OUTPUT_DIR / "corr_xly.png"

display(Markdown("#### Consumer Discretionary (XLY)"))
display(Image(filename=str(img_path)))
display(Image(filename=str(corr_path)))
```


#### Consumer Discretionary (XLY)



    
![png](heartbeat_files/output_19_1.png)
    



    
![png](heartbeat_files/output_19_2.png)
    


Consumer Discretionary shows a more **uneven and reactive rhythm** than Health Care, with its ETF (red, XLY) often **pulsing ahead of or behind its members**, reflecting the sector’s sensitivity to economic mood swings. The correlation heatmap supports this view: ETF–stock Pearson $r$ values remain **consistently positive** across windows, ranging roughly from **0.21 to 0.94**, with **most $p$-values $\approx 0.000$**, except AMZN in the dot-com pre window ($r \approx 0.21$, $p = 0.119$), showing weak or insignificant coupling at that time.

* **Dot-com:** pre-bubble, most names beat slowly while **a few pulse fast**, showing weak synchronization. Correlations span from **low for AMZN** ($r = 0.21$, $p = 0.119$) to **moderate for HD and MCD** ($r \approx 0.48\text{–}0.59$, $p \approx 0.000$). At the peak, **the whole family quickens**, and XLY captures that acceleration (HD $r = 0.75$, $p \approx 0.000$). In recovery, **tempos spread apart again**, with correlations returning to the **0.43–0.70** range, consistent with a fractured, uneven rebound.

* **GFC:** XLY leads with a **strong, rapid pulse pre-crash**, backed by moderate–high synchrony ($r \approx 0.47\text{–}0.71$, $p \approx 0.000$). At the peak, the group **splinters spectrally**, yet correlation intensifies for HD, NKE, and SBUX ($r \approx 0.77\text{–}0.84$, $p \approx 0.000$). During recovery, **rhythms re-align toward mid frequencies**, and correlations remain strong ($r \approx 0.59\text{–}0.76$), suggesting the sector regains a steady collective pace.

* **Brexit:** pre-vote, **mixed tempos** correspond to mid–high correlations ($r \approx 0.57\text{–}0.66$, $p \approx 0.000$). During the vote, **sharp bursts from HD and MCD** dominate while others stay moderate; synchrony dips briefly (MCD $r = 0.40$, $p \approx 0.000$). Post-vote, correlations rebound—AMZN reaches ($r = 0.80$, $p \approx 0.000$)—and the family settles back into a cohesive mid-band rhythm.

* **COVID:** before the pandemic, frequencies cluster in the **mid–high range**, forming a **brisk, confident beat** with solid correlations ($r \approx 0.52\text{–}0.81$, $p \approx 0.000$). During the crisis, **everything slows sharply and together**: XLY–stock correlations surge to **very high values** ($r \approx 0.78\text{–}0.94$, $p \approx 0.000$), as the sector’s heartbeat **drops into a synchronized low tempo**, signaling contraction under pressure.


```python
# Financials (XLF)
img_path = hv.OUTPUT_DIR / 'sector_family_xlf.png'
corr_path = hv.OUTPUT_DIR / "corr_xlf.png"

display(Markdown("#### Financials (XLF)"))
display(Image(filename=str(img_path)))
display(Image(filename=str(corr_path)))

```


#### Financials (XLF)



    
![png](heartbeat_files/output_21_1.png)
    



    
![png](heartbeat_files/output_21_2.png)
    


Financials show a **choppy, crisis-sensitive rhythm**. XLF often **sits near the middle of the pack**, but single names (especially GS and MS) throw off strong “extra beats” around major shocks. Statistically, ETF–stock Pearson $r$ is **high in almost every window**, mostly between **$0.71$ and $0.98$**, with only a brief dip to about **$0.42$–$0.47$** around the Brexit vote; **all reported $p$-values are $\approx 0.000$**, and the single missing cell (GS in dot-com pre) simply reflects data availability rather than weak linkage.

* **Dot-com:** pre-bubble, **mid-tempo across the board** with XLF slightly above peers, and **strong synchrony** ($r \approx 0.71$–$0.82$, $p \approx 0.000$). At the peak, **everyone lifts into the high band together**, and correlations tighten further (C/GS/JPM/MS mostly **$0.73$–$0.84$**), so the whole cluster moves as a unit. In recovery, **all series remain very fast and tightly clustered**, and $r$ rises again (roughly **$0.76$–$0.88$**), consistent with a fully synchronized, high-frequency regime.

* **GFC:** pre-crisis, **most banks are mid-tempo but GS is unusually slow**, an early outlier in the spectra. Yet even there, ETF–stock $r$ is already high (about **$0.76$–$0.83$**). At the peak, **frequencies converge into the low–mid 30s**, and correlations climb further: BAC/C/GS sit around **$0.79$–$0.89$**, while JPM reaches **$r \approx 0.91$** (all $p \approx 0.000$). Recovery stays **very strongly coupled** (most names **$0.80$–$0.90$**), even though the panels show **MS spiking to the top of the range while JPM drops to low frequency**—XLF ends up averaging a highly fractured heartbeat that is still statistically tight.

* **Brexit:** pre-vote, **BAC–C sit mid–high, JPM is slow, MS is fast**, a visibly split pattern, but correlations with XLF remain robust (roughly **$0.83$–$0.87$**). The vote itself produces **a neat cluster around $\sim 30$ cycles for all banks**, while **XLF jumps even higher**; at the same time, $r$ briefly weakens to **$0.42$–$0.47$** for BAC/C/GS/JPM/MS, the only real soft spot in the heatmap. Post-vote, **GS explodes to very high frequency while the others sink to low levels**, yet correlations bounce back into the **$0.82$–$0.93$** band, with XLF in the mid-band and **under-representing GS’s surge** but still moving in step overall.

* **COVID:** before COVID, **GS again runs hot while the rest stay slow–mid**, so the family beat is skewed by one fast drummer, but ETF–stock $r$ stays very high (**$0.87$–$0.91$**). In the crisis window, **frequencies rise across almost all names and re-cluster in the mid–high band**, and the synchrony becomes extreme: BAC/JPM/MS all reach **$r \approx 0.96$–$0.98$**, C/GS about **$0.96$**, with $p \approx 0.000$. XLF thus **captures a near-perfect re-synchronization after the initial shock**, even as the time-domain panels remain choppy and crisis-sensitive.

```python
# Technology (XLK)
img_path = hv.OUTPUT_DIR / 'sector_family_xlk.png'
corr_path = hv.OUTPUT_DIR / "corr_xlk.png"

display(Markdown("#### Technology (XLK)"))
display(Image(filename=str(img_path)))
display(Image(filename=str(corr_path)))

```


#### Technology (XLK)



    
![png](heartbeat_files/output_23_1.png)
    



    
![png](heartbeat_files/output_23_2.png)
    


Technology appears to have a **regime-switching heartbeat**: it spends a lot of time at fast tempos, with XLK usually **following the big platforms** but sometimes missing the more extreme pulses in names like NVDA or ADBE.
The correlation heatmap shows ETF–stock Pearson $r$ **almost always positive**, typically in the **$0.4$–$0.9$** range with $p \approx 0.000$. Early on there are a couple of genuine decouplings — **NVDA in dot-com pre** ($r \approx 0.02$, $p = 0.922$) and **GOOGL in dot-com recovery** ($r \approx 0.04$, $p = 0.676$) — but by the COVID crisis **all names lock tightly to XLK** (AAPL/ADBE/GOOGL/NVDA around $r \approx 0.92$–$0.95$, MSFT at $r \approx 0.98$, all $p \approx 0.000$).

* **Dot-com:** pre-bubble, **AAPL/MSFT run mid–fast while NVDA/ADBE are much slower**, so XLK sits in the middle of a split family. Correlations mirror this: MSFT is already tightly tied to the ETF ($r \approx 0.81$, $p = 0.000$), AAPL only moderately so ($r \approx 0.52$), ADBE weaker still ($r \approx 0.43$, $p = 0.001$), and NVDA basically uncoupled ($r \approx 0.02$, $p = 0.922$). At the peak, **XLK, AAPL and MSFT all jump to very high frequencies**, a classic overheated tech beat, and $r$ lifts into the $0.54$–$0.79$ band for these names, while NVDA just catches up to a modest correlation ($r \approx 0.55$). In recovery, **MSFT and ADBE remain fast but XLK and others drop to mid/low**, leaving a scattered rhythm: ETF–stock $r$ is mid for AAPL/ADBE/NVDA ($\approx 0.51$–$0.67$), but GOOGL is still almost uncorrelated ($r \approx 0.04$, $p = 0.676$), highlighting how incomplete the index linkage was in that early period.

* **GFC:** pre-crisis, **AAPL is extremely fast, CSCO mid, NVDA slow**, and XLK only mid-band, so the ETF underplays AAPL’s surge. Statistically, though, correlations are already coherent across the group: AAPL/ADBE/GOOGL/MSFT/NVDA sit between $r \approx 0.40$ and $0.59$ (all $p \approx 0.000$). At the peak, **all five names crowd into the high 30–50 range**, a tightly synchronized, stressed heartbeat that XLK reflects; correspondingly, $r$ tightens into **$0.67$–$0.81$** for every stock. Recovery then **drops everyone back to low–mid frequencies together**, and $r$ stays firmly positive ($\approx 0.62$–$0.70$ for AAPL/ADBE/MSFT/NVDA, $0.66$ for GOOGL), a clear downshift after the storm but still a well-coupled tech block.

* **Brexit:** pre-vote, **XLK and NVDA both beat very fast while AAPL is unusually slow**, producing an uneven lineup. Correlations, however, are already strong: AAPL/ADBE/GOOGL/MSFT/NVDA all lie around $r \approx 0.56$–$0.69$ ($p \approx 0.000$). During the vote, **the family converges into a mid–high cluster**—XLK, AAPL, MSFT, CSCO, ADBE all around 25–35 cycles—while NVDA briefly dips; the heatmap shows this as generally high $r$ ($\approx 0.64$–$0.79$) with NVDA the laggard at $r \approx 0.42$. Post-vote, **AAPL and ADBE spike to the top of the range while XLK falls to a modest mid tempo**, meaning the ETF smooths over the strongest individual surges; nonetheless, $r$ remains high across the board ($\approx 0.70$–$0.89$), so the “smoothed” index is still tightly tied to its stars.

* **COVID:** before COVID, **all six series sit in a tight mid–high band**, a strong, synchronized tech “cruise control” phase that XLK tracks closely: ETF–stock $r$ is already elevated ($\approx 0.64$–$0.87$, all $p \approx 0.000$). In the crisis window, **frequencies drop across the board**, with **MSFT especially slow while AAPL and ADBE stay relatively higher**, and XLK settling at a moderate low–mid tempo—**a cooled but still beating tech heart rather than a total collapse**. The heatmap shows this as **near-perfect co-movement**: AAPL/ADBE/GOOGL/NVDA reach $r \approx 0.92$–$0.95$, and MSFT climbs to $r \approx 0.98$ (all $p \approx 0.000$), confirming that in COVID the ETF moves almost one-for-one with its megacaps.

## Crisis Pulse vs Baseline

We now step through each crisis and compare ETF returns (top row) with multiple stocks alongside their spectra, shown in right columns. Vertical dashed lines shown in the time domain separate pre-crisis, crisis, and recovery, while those in the frequency domain mark reference tempos: roughly 5 cycles/year for quarterly rhythms, 12 for monthly pulses, and 52 for weekly beats, useful baselines for spotting whether crisis spectra race ahead or slow down relative to the market’s usual cadence.

```python
# Dot-com
crisis = "dotcom"
windows = CRISIS_GROUPS[crisis]
for sector in market_fourier.SECTOR_ETFS:
    sector_ticker = market_fourier.SECTOR_ETFS[sector]
    stock_list = hv.DEFAULT_STOCKS.get(sector, [])
    hv.plot_crisis_comparison(
        market_fourier,
        crisis,
        windows,
        sector,
        sector_ticker,
        stock_list,
    )
    fig_name = f"{crisis}_{sector_ticker.lower()}_comparison.png"
    path = hv.OUTPUT_DIR / fig_name
    if path.exists():
        display(Markdown(f"#### {sector} ({sector_ticker}) vs stocks"))
        display(Image(filename=str(path)))

```

    [saved] figure: /Users/magdaturcanu/Desktop/SV/ADA/outputs/dotcom_xly_comparison.png



#### Consumer Discretionary (XLY) vs stocks



    
![png](heartbeat_files/output_26_2.png)
    


    [saved] figure: /Users/magdaturcanu/Desktop/SV/ADA/outputs/dotcom_xlf_comparison.png



#### Financials (XLF) vs stocks



    
![png](heartbeat_files/output_26_5.png)
    


    [saved] figure: /Users/magdaturcanu/Desktop/SV/ADA/outputs/dotcom_xlv_comparison.png



#### Health Care (XLV) vs stocks



    
![png](heartbeat_files/output_26_8.png)
    


    [saved] figure: /Users/magdaturcanu/Desktop/SV/ADA/outputs/dotcom_xlk_comparison.png



#### Technology (XLK) vs stocks



    
![png](heartbeat_files/output_26_11.png)
    


### Dotcom

### Consumer Discretionary – XLY vs AMZN, HD

* **XLY:** In the **pre** window, power is scattered with only modest bumps near the **monthly (~12 c/yr)** and **weekly (~52 c/yr)** guides. At the **peak**, those regions fill in: you see **stronger clusters between ~40–60 c/yr**, right around the weekly band, matching the visibly higher volatility. **Recovery** softens those peaks but doesn’t erase them—mid/high frequencies remain active.
* **AMZN:** Already has **large spikes near ~10–15 c/yr and 45–60 c/yr** in the **pre** period; at the **peak** these get much taller, especially around the **weekly band**, showing a much faster “heartbeat” than the ETF. **Recovery** tones this down but still keeps more power near those guides than XLY.
* **HD:** Power is lower overall; the main features are **modest bumps near 10–20 c/yr** and only small structure near the weekly line, so it behaves more like a **mid-tempo constituent**.

---

### Financials – XLF vs JPM, BAC

* **XLF:** Pre-crisis, peaks are modest and mostly below **~40 c/yr**. At the **peak**, the spectrum fills in with many stems **from ~15 up to 60 c/yr**, including activity around the **52 c/yr weekly line**—the ETF clearly speeds up. Recovery leaves **residual bumps in the 20–40 c/yr band**, so it doesn’t go back to very slow cycles.
* **JPM / BAC:** Both show **pronounced peaks near ~10–15 c/yr and 45–60 c/yr** in the **peak** window, much stronger than pre. Recovery scales them down but still keeps **noticeable power near the monthly region**, consistent with an elevated but cooling heartbeat.

---

### Health Care – XLV vs JNJ, UNH

* **XLV:** Pre and peak spectra show **moderate peaks near ~10–20 c/yr**, with only small structures close to the weekly line; the move from pre → peak is **a gentle lift, not an explosion**. In **recovery**, power redistributes but stays mostly in **low–mid bands (<40 c/yr)**.
* **JNJ:** Has a rising slope of power into **20–40 c/yr**, plus some spikes near **~50–60 c/yr** at the **peak**, but none dominate.
* **UNH:** Richer spectrum with **frequent peaks between ~15 and 50 c/yr**, yet the change across phases is gradual—supporting the **“defensive metronome”** view: tempos nudge up around the bubble but stay within a midrange.

---

### Technology – XLK vs AAPL, MSFT

* **XLK:** Pre-dotcom, a few lines around **10–20 c/yr** and one near **~50 c/yr**. At the **peak**, the entire **20–60 c/yr region lights up**, with several large spikes close to the **weekly guide**, reflecting a sharp acceleration. **Recovery** lowers the amplitudes but still leaves **mid-band (~20–40 c/yr)** structure.
* **AAPL:** Already has strong peaks near **~25–30 c/yr and around 50–60 c/yr** in the **pre** panel; at the **peak**, those get taller and more numerous, especially near the weekly band—**very fast heartbeat**. Recovery cuts the heights roughly back toward pre levels, but **still richer than XLK**.
* **MSFT:** Similar story: **clear peaks near the monthly (~12 c/yr) and weekly (~52 c/yr) guides** grow at the **peak**, then shrink but remain in **recovery**—Tech stocks stay more rhythmic than the ETF.

```python
crisis = "gfc"
windows = CRISIS_GROUPS[crisis]
for sector in market_fourier.SECTOR_ETFS:
    sector_ticker = market_fourier.SECTOR_ETFS[sector]
    stock_list = hv.DEFAULT_STOCKS.get(sector, [])
    hv.plot_crisis_comparison(
        market_fourier,
        crisis,
        windows,
        sector,
        sector_ticker,
        stock_list,
    )
    fig_name = f"{crisis}_{sector_ticker.lower()}_comparison.png"
    path = hv.OUTPUT_DIR / fig_name
    if path.exists():
        display(Markdown(f"#### {sector} ({sector_ticker}) vs stocks"))
        display(Image(filename=str(path)))

```

    [saved] figure: /Users/magdaturcanu/Desktop/SV/ADA/outputs/gfc_xly_comparison.png



#### Consumer Discretionary (XLY) vs stocks



    
![png](heartbeat_files/output_28_2.png)
    


    [saved] figure: /Users/magdaturcanu/Desktop/SV/ADA/outputs/gfc_xlf_comparison.png



#### Financials (XLF) vs stocks



    
![png](heartbeat_files/output_28_5.png)
    


    [saved] figure: /Users/magdaturcanu/Desktop/SV/ADA/outputs/gfc_xlv_comparison.png



#### Health Care (XLV) vs stocks



    
![png](heartbeat_files/output_28_8.png)
    


    [saved] figure: /Users/magdaturcanu/Desktop/SV/ADA/outputs/gfc_xlk_comparison.png



#### Technology (XLK) vs stocks



    
![png](heartbeat_files/output_28_11.png)
    


### Global Financial Crisis

### Consumer Discretionary – XLY vs AMZN, HD

* **XLY:** From **pre → peak**, the spectrum thickens, with many stems between **~10–40 c/yr** and a visible ridge around the **monthly band (~12 c/yr)**. A few peaks reach into the **40–60 c/yr** zone near the weekly line, matching the sharp volatility spike in 2008–2009. **Recovery** keeps some power around **10–20 c/yr**, but the high-frequency energy above **40 c/yr** is clearly lower.
* **AMZN:** Already busy pre-crisis; at the **peak**, you get taller spikes near both **~10–15 c/yr** and **around 50–60 c/yr**, so AMZN is strongly engaged with both the monthly and weekly tempos. In **recovery**, the weekly-region peak is still there but with smaller amplitude, indicating a slower, but still fast, rhythm.
* **HD:** Shows a milder version: a noticeable lift in **10–30 c/yr** power at the **peak**, but less emphasis right on 52 c/yr. Recovery moderates those mid-band peaks, so HD behaves more like a mid-tempo retailer.

---

### Financials – XLF vs JPM, BAC

* **XLF:** Pre-GFC, it shows numerous small stems < **40 c/yr**, with occasional bumps near **12 c/yr**. At the **peak**, the spectrum explodes: tall peaks from **~10 to 60 c/yr**, plus clear energy near the **weekly tempo (~52 c/yr)**. **Recovery** still has a dense forest of stems in **10–40 c/yr**, plus some residual weekly-region power—Financials never fully slow back to pre rhythms.
* **JPM / BAC:** Both banks display strong amplification at the **peak**: prominent peaks **around 10–15 c/yr (monthly to bi-monthly)** and a lot of structure **between 40–60 c/yr**, often with one or two spikes right on or just beside the 52-c/yr line. In **recovery**, peak heights drop but these bands stay active, showing that Financials keep cycling faster than before the crisis.

---

### Health Care – XLV vs JNJ, UNH

* **XLV:** Pre-GFC, power is spread modestly across **~5–35 c/yr** with some emphasis around **10–15 c/yr**; the weekly zone is lightly touched. At the **peak**, amplitudes increase a bit—especially in **10–30 c/yr**—but the pattern stays diffuse rather than dominated by a single crisis frequency. **Recovery** actually shows a slightly fuller mid-band, yet still mostly < **40 c/yr**.
* **JNJ / UNH:** Both see their power lift at the **peak**, with JNJ having clearer peaks near **~10–15 c/yr** and UNH showing many moderate stems from **~10–50 c/yr**. Spikes at exactly **52 c/yr** are present but not huge. In **recovery**, mid-band power remains but without further escalation. Health Care again looks like a **mid-tempo, resilient metronome** rather than a crisis amplifier.

---

### Technology – XLK vs AAPL, MSFT

* **XLK:** Pre-GFC, you already see stems up to **60 c/yr**, but with moderate heights. At the **peak**, the band from **~15 to 50 c/yr** becomes much more energetic, and there are clear spikes near **~50–55 c/yr**, right on top of the weekly guide. **Recovery** shifts power downward—lots of stems **<40 c/yr**, fewer large weekly peaks—so Tech cools but remains quicker than defensives.
* **AAPL:** Pre-crisis, AAPL has strong peaks around **20–40 c/yr** and a notable one at **~52 c/yr**. At the **peak**, those regions intensify, with multi-unit peaks near the weekly line. In **recovery**, amplitudes stay elevated but lower than at the peak, especially around **10–30 c/yr**—still a fast ticker.
* **MSFT:** Shows a similar pattern but with smaller absolute power: more energy near **10–20 c/yr** and **40–60 c/yr** at the **peak**, then a clearer drop in the weekly zone during **recovery**.


```python
crisis = "brexit"
windows = CRISIS_GROUPS[crisis]
for sector in market_fourier.SECTOR_ETFS:
    sector_ticker = market_fourier.SECTOR_ETFS[sector]
    stock_list = hv.DEFAULT_STOCKS.get(sector, [])
    hv.plot_crisis_comparison(
        market_fourier,
        crisis,
        windows,
        sector,
        sector_ticker,
        stock_list,
    )
    fig_name = f"{crisis}_{sector_ticker.lower()}_comparison.png"
    path = hv.OUTPUT_DIR / fig_name
    if path.exists():
        display(Markdown(f"#### {sector} ({sector_ticker}) vs stocks"))
        display(Image(filename=str(path)))

```

    [saved] figure: /Users/magdaturcanu/Desktop/SV/ADA/outputs/brexit_xly_comparison.png



#### Consumer Discretionary (XLY) vs stocks



    
![png](heartbeat_files/output_30_2.png)
    


    [saved] figure: /Users/magdaturcanu/Desktop/SV/ADA/outputs/brexit_xlf_comparison.png



#### Financials (XLF) vs stocks



    
![png](heartbeat_files/output_30_5.png)
    


    [saved] figure: /Users/magdaturcanu/Desktop/SV/ADA/outputs/brexit_xlv_comparison.png



#### Health Care (XLV) vs stocks



    
![png](heartbeat_files/output_30_8.png)
    


    [saved] figure: /Users/magdaturcanu/Desktop/SV/ADA/outputs/brexit_xlk_comparison.png



#### Technology (XLK) vs stocks



    
![png](heartbeat_files/output_30_11.png)
    


### Brexit

### Consumer Discretionary – XLY vs AMZN, HD

* **XLY:** Pre-vote, the spectrum is fairly busy from **~10–40 c/yr**, plus some spikes near **50–60 c/yr**. In the **vote** window, power *drops* almost everywhere – the whole spectrum flattens, with only tiny stems near the guides. **Post-vote**, activity returns, mainly in **10–30 c/yr**, but without huge weekly peaks.
* **AMZN:** Pre-vote, lots of power in **10–40 c/yr** and some stems around **50–60 c/yr**. The **vote** window looks very muted (short, low bars), then **post-vote** AMZN goes back to stronger peaks near **~10–20 c/yr** and occasional spikes near the **weekly line**.
* **HD:** Similar story: **pre** has moderate mid-band activity, **vote** is very quiet, **post** brings back peaks mainly in **10–30 c/yr**, with only small touches near **52 c/yr**.

---

### Financials – XLF vs JPM, BAC

* **XLF:** Pre-Brexit, many stems **10–40 c/yr**, plus some activity near **50–60 c/yr**. In the **vote** window, power is much weaker and more concentrated below **30 c/yr**. **Post-vote**, the mid band repopulates and you again see occasional spikes near the weekly line, but nothing like the GFC.
* **JPM / BAC:** Both banks show this **temporary power vacuum** around the vote: pre-vote, they have clear peaks around **10–20 c/yr** and scattered ones near **50–60 c/yr**; the vote spectra are faint; post-vote, mid-frequency power (**10–40 c/yr**) and some weekly-adjacent spikes return.

---

### Health Care – XLV vs JNJ, UNH

* **XLV:** Pre-vote, a moderate ridge from **~5–30 c/yr**, light touches near **52 c/yr**. The **vote** spectrum is very low-amplitude; **post-vote**, XLV returns to a similar low–mid-band profile with slightly more power but no dramatic new peaks.
* **JNJ / UNH:** Both are classic defensives here: pre-vote peaks mainly **<30 c/yr**, a very quiet **vote** window, and **post-vote** spectra that look like slightly noisier versions of pre, with only a few stems near the reference tempos.

---

### Technology – XLK vs AAPL, MSFT

* **XLK:** Pre-vote, Tech has a healthy spread **10–50 c/yr**, with a few stems close to the **weekly guide**. The **vote** spectrum is strikingly flat – hardly any power above noise. **Post-vote**, power returns across **~10–40 c/yr**, with some peaks near **50–60 c/yr**, but magnitudes are modest.
* **AAPL / MSFT:** Both stocks echo that pattern: pre-vote, clear peaks near **10–20 c/yr** and some around **50–60 c/yr**; very quiet **vote** windows; **post-vote**, mid-band power comes back (especially AAPL, which shows stronger **15–30 c/yr** peaks) with a few weekly-ish spikes.


```python
crisis = "covid"
windows = CRISIS_GROUPS[crisis]
for sector in market_fourier.SECTOR_ETFS:
    sector_ticker = market_fourier.SECTOR_ETFS[sector]
    stock_list = hv.DEFAULT_STOCKS.get(sector, [])
    hv.plot_crisis_comparison(
        market_fourier,
        crisis,
        windows,
        sector,
        sector_ticker,
        stock_list,
    )
    fig_name = f"{crisis}_{sector_ticker.lower()}_comparison.png"
    path = hv.OUTPUT_DIR / fig_name
    if path.exists():
        display(Markdown(f"#### {sector} ({sector_ticker}) vs stocks"))
        display(Image(filename=str(path)))

```

    [saved] figure: /Users/magdaturcanu/Desktop/SV/ADA/outputs/covid_xly_comparison.png



#### Consumer Discretionary (XLY) vs stocks



    
![png](heartbeat_files/output_32_2.png)
    


    [saved] figure: /Users/magdaturcanu/Desktop/SV/ADA/outputs/covid_xlf_comparison.png



#### Financials (XLF) vs stocks



    
![png](heartbeat_files/output_32_5.png)
    


    [saved] figure: /Users/magdaturcanu/Desktop/SV/ADA/outputs/covid_xlv_comparison.png



#### Health Care (XLV) vs stocks



    
![png](heartbeat_files/output_32_8.png)
    


    [saved] figure: /Users/magdaturcanu/Desktop/SV/ADA/outputs/covid_xlk_comparison.png



#### Technology (XLK) vs stocks



    
![png](heartbeat_files/output_32_11.png)
    


### COVID

### Consumer Discretionary (XLY, AMZN, HD)

* **Pre-Covid**: XLY and AMZN have a spread of mid-band power between roughly **20–80 cycles/yr**, with a few bumps near the **monthly band (~10–15 c/yr)** and some energy around **50–60 c/yr**, i.e. near the weekly guide. HD is quieter but still shows scattered power across that midrange.
* **Crisis**: spectra collapse into a few strong spikes, mostly at **very high frequencies (>90 c/yr)**, well **above the weekly 52 c/yr guide**. There’s little persistent power near 5 or 12 c/yr – it looks like a short burst of rapid day-to-day moves rather than a new “tempo”.

---

### Financials (XLF, JPM, BAC)

* **Pre-Covid**: XLF has a cluster of peaks between **20–70 c/yr**, with a couple close to **10–15 c/yr** and **50–60 c/yr** (monthly and weekly regions). JPM and BAC also show mid-band concentration, with some distinct spikes near the weekly guide.
* **Crisis**: crisis spectra are dominated by a few tall spikes in the **80–110 c/yr** range, while energy near **5 and 12 c/yr** is almost absent. There are occasional peaks near **50–60 c/yr**, but overall Financials look like they jump to **“hyper-fast” rhythms** during the crash, consistent with intense intraday churn over a very short window.

---

### Health Care (XLV, JNJ, UNH)

* **Pre-Covid**: XLV spreads power across **20–80 c/yr**, with some structure around **10–15 c/yr** and **50–60 c/yr**. JNJ and UNH show similar, though slightly noisier, mid-band profiles, with several peaks near the **weekly band**.
* **Crisis**: spectra thin out severely; XLV has a few peaks near **40–70 c/yr** and some very high-frequency spikes beyond **90 c/yr**, but not much else. JNJ and UNH show the same pattern: **isolated high-frequency spikes** and relatively little power near the lower reference tempos.

---

### Technology (XLK, AAPL, MSFT)

* **Pre-Covid**: tech shows relatively strong mid-band structure: repeated peaks between **30–80 c/yr**, including some close to **52 c/yr** and weaker bumps near **10–15 c/yr**. AAPL’s and MSFT’s pre spectra have several peaks of comparable height in that region, suggesting a richer mix of tempos.
* **Crisis**: crisis spectra again thin out, leaving a few tall spikes **near or above 90–110 c/yr** and some power around the **weekly band (~50–60 c/yr)**. Tech’s rhythms thus **speed up sharply**, but we can’t say much about persistent cycles because the crisis window is so short.

---

Across sectors the crisis phase is characterized by **isolated high-frequency spikes (often >80–100 cycles/yr)** and **much sparser spectra** than earlier crises, exactly because the data end on **1 April 2020**. We’re essentially observing the **initial volatility explosion**, not a fully formed post-shock heartbeat.


## Market Personality: The “Arrhythmia” Test

If the market has a heartbeat, then not all pulses are created equal.

In our previous analysis, we looked for **dominant frequencies**—essentially asking, *“What is the heart rate?”* Now we ask a deeper question: *“How healthy is the rhythm?”*

In cardiology, a healthy heart beats with a distinct, orderly pattern (Normal Sinus Rhythm). It focuses its energy into specific frequencies. A stressed or failing heart, however, often descends into **fibrillation**—a chaotic state where energy is scattered randomly across all frequencies, creating noise rather than a beat.

We can apply this same diagnostic to financial assets. By analyzing the **shape of the Fourier Power Spectrum** of each asset, we can classify every stock and ETF into one of two personality types:

- **Type O (Organized)** – the *“Sinus Rhythm”* stocks.  
  Their spectrum is **spiky and concentrated**. They move in distinct, predictable waves (e.g. seasonality, business cycles). These are often defensive assets that march to a slow, steady drummer.

- **Type C (Chaos)** – the *“Fibrillation”* stocks.  
  Their spectrum is **flat and entropic** (approximating white noise). They react impulsively to news, tweets, and shocks. These are often cyclical or speculative assets that vibrate with high-frequency nervous energy.

### The Mathematics of Chaos

To quantify this, we extract two signal-processing metrics from the Fourier Power Spectrum \(P(f)\) of each ticker.

#### 1. Spectral Entropy – the “Noise” Metric

This measures the **disorder** of the signal.  
A pure sine wave has entropy close to 0. White noise has entropy close to 1.

$$
H = -\sum_{f} p(f)\,\log p(f)
$$

We normalize by $\ln(N)$ so that $H \in [0, 1]$.

**Analogy:** Is the signal a clear note or static hiss?

#### 2. Energy Concentration – the “Rhythm” Metric

This measures how much of the signal’s total power is held by its **strong drivers**.  
We sum the power of the top 3 dominant frequencies:

$$
C = \sum_{i=1}^{3} P\bigl(f_{(i)}\bigr)
$$

**Analogy:** Is the heartbeat a strong *“LUB-DUB”* (high concentration) or a faint flutter (low concentration)?

```python
# Configuration
OUTPUT_FILE = "market_personality_labels.csv"

# Feature Extraction 
df = None
if os.path.exists(OUTPUT_FILE):
    print(f"Found existing {OUTPUT_FILE}. Loading data...")
    try:
        df = pd.read_csv(OUTPUT_FILE)
        # Ensure we have the right metrics
        if "Entropy" not in df.columns or "Concentration" not in df.columns:
            print("Missing metrics. Re-calculating...")
            df = None
    except:
        df = None

if df is None:
    tickers = features.get_all_tickers()
    if not tickers:
        print("Error: No data found. Check your DATA_ROOT path.")
    else:
        results = []
        print(f"Recording heartbeats for {len(tickers)} instruments...")
        for ticker, kind in tqdm(tickers):
            res = features.calculate_spectral_personality(ticker, kind)
            if res:
                results.append(res)
        df = pd.DataFrame(results)
        df.to_csv(OUTPUT_FILE, index=False)
        print("Vitals recorded.")

# Clustering
if df is not None:
    print("Diagnosing personality types...")
    df = clustering.clean_data(df)
    df = clustering.apply_mbti_clustering(df)
    
    # Save the diagnosis
    df.to_csv(OUTPUT_FILE, index=False)
    
    # Print Summary
    print("\n--- Diagnosis Summary ---")
    print(df.groupby(["Kind", "Label"]).size())
```

    Found existing market_personality_labels.csv. Loading data...
    Diagnosing personality types...
    Separating Analysis for 7001 instruments...
      -> Clustering 1763 ETFs...
      -> Clustering 5238 Stocks...
    
    --- Diagnosis Summary ---
    Kind    Label      
    etfs    C (Chaos)       957
            R (Regular)     806
    stocks  C (Chaos)      2785
            R (Regular)    2453
    dtype: int64


```python
if df is not None:
    plotting.plot_personality_map(df)

save.final_results()
```

    Plot saved to market_personality_map.png



    
![png](heartbeat_files/output_36_1.png)
    


    --- Generating Final Lists for MBTI Analysis ---
    1. Saved classified list to 'labels.csv' (7001 items)
    2. Saved dropped list to 'dropped.csv' (1048 items)


In `clean_data(df)` we apply a set of quality filters before clustering, so only instruments with a meaningful “heartbeat” are kept:

- First, we replace any `±∞` values with `NaN` and **drop rows where `Entropy` or `Concentration` is `NaN`**. This removes tickers whose Fourier spectrum could not be computed reliably (e.g. prices too flat, bad data, numerical issues).
- Next, we **only keep instruments with more than 500 trading days**. Short histories do not produce a stable Fourier spectrum, so those tickers are excluded from the personality map: $\text{Total\_Days} > 500.$
- Finally, we **filter out very low–entropy assets**: $H < 0.90.$ Very low entropy usually means the price barely moves (illiquid, inactive, or near-constant). These series don’t really have a “heartbeat”, so we drop them and focus on assets with enough variation to say something meaningful about their rhythm.

```python
# Distribution
if df is not None:
    fig, axes = plt.subplots(2, 2, figsize=(14, 10))
    palette = {"O (Organized)": "#2ca02c", "C (Chaos)": "#d62728"}
    
    # Row 1: Entropy (Disorder)
    sns.histplot(data=df[df['Kind']=='etfs'], x='Entropy', hue='Label', kde=True, ax=axes[0,0], palette=palette, bins=30)
    axes[0,0].set_title("Spectral Entropy: ETFs")
    
    sns.histplot(data=df[df['Kind']=='stocks'], x='Entropy', hue='Label', kde=True, ax=axes[0,1], palette=palette, bins=30)
    axes[0,1].set_title("Spectral Entropy: Stocks")
    
    # Row 2: Concentration (Rhythm)
    sns.histplot(data=df[df['Kind']=='etfs'], x='Concentration', hue='Label', kde=True, ax=axes[1,0], palette=palette, bins=30)
    axes[1,0].set_title("Energy Concentration: ETFs")
    
    sns.histplot(data=df[df['Kind']=='stocks'], x='Concentration', hue='Label', kde=True, ax=axes[1,1], palette=palette, bins=30)
    axes[1,1].set_title("Energy Concentration: Stocks")
    
    plt.tight_layout()
    plt.show()
```


    
![png](heartbeat_files/output_38_0.png)
    


The histograms for $H$ and $C$ reveal a fundamental feature of market physics: **Regularity is a tail event.**

1. **Entropy distribution (top row)**  
   - The **red mass** (Chaos) clusters tightly near maximum entropy, confirming the Efficient Market Hypothesis: most actively traded names behave very much like random walks. Their spectra are almost pure noise.  
   - The **green “tail”** to the left consists of outliers with significantly lower entropy – assets whose spectra are more ordered than the market norm.

2. **Concentration distribution (bottom row)**  
   - Chaos assets pile up on the **left**, with very low concentration: their energy is **diluted across many frequencies**.  
   - Organized assets form a **long green tail to the right**. These tickers have a **“loud heartbeat”** – a handful of dominant cycles (often yearly or business-cycle components) that stand out clearly above the background noise.

Just as a cardiologist looks for a clean QRS complex on an ECG, we look for **low entropy and high concentration**:

- **Organized (O)** assets are the market’s **pacemakers** – slow, rhythmic, and resistant to the fibrillation of daily news.
- **Chaos (C)** assets are the **adrenaline spikes** – powerful but prone to arrhythmia when the system is shocked.

The personality map highlights how these two types occupy **distinct regions** of
the $H\text{–}C$ plane:

The **transition band** around $H \approx 0.94$ with small $C$ is populated by borderline cases whose spectra show a weak dominant cycle floating on a noisy background. These are neither clean sinus rhythm nor pure fibrillation, but they mark the **fuzzy frontier** between Organied and Chaos.

Overall, both the histograms and the scatterplots tell a consistent story: the market’s default state is **high-entropy chaos**, and the clean, cycle-driven “heartbeats” of Type O assets live out in the tails – rare, but structurally distinct.

