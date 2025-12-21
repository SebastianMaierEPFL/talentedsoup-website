---
layout: article
title: Extraversion vs Introversion
permalink: /e_i/
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
    color: #fbfbfbff !important; /* or any color you prefer */
  }
</style>

## Extrovertness/Introvertness Index (EI): Definition and Asset Personality Classification
Financial assets differ not only in their fundamentals, but also in how visibly they react to market information. This "reaction" behavior can be described by the social side of assets; extrovert and introvertness; just like human kind. Aseet's reactions, price mouvements are market dependent; just like humans being impacted by their social environment . 
<!-- ![Emotional Index visualization](social10.png) -->
<img src="{{ 'social10.png' | relative_url }}"
     alt="Emotional Index visualization"
     style="float: right; max-width: 75%; height: auto; margin: 0 0 1em 1.5em;">
But, how to measure this social behavior like an asset or like a characteristic that we can define through a metric, personality trait of humans, what is the socialness measure, score, or the threshold that makes an asset introvert or extrovert ? 
To capture the social dimension of assets, we build social index called "EI Index" (Extrovertness/Introvertness Index ) . This index is based on three measurable values of the assets throught adjusted price time series ; volatility, turnover provy and jump frequency . 
Volatility is a measurement of how varied the returns of a given security or market index are over time, while turnover proxy is an operational measure of trading activity used to approximate how intensively an asset is traded and jump frequency measures the share of trading days on which absolute returns exceed a fixed multiple of the asset’s own return volatility, symbolizing event-driven side per asset . Together, they reflect the intensity, discontinuity, and participation level of price responses observed in the market . Higher volatility is an indicator of large and frequent price movements, lower volatility indicates the asset's value does not fluctuate dramatically and tends to be steadier . High jump frequency exhibits sharp and irregular price mouvements , low jump frequency indicates large return realizations are rare, and asset prices over time is smooth . High turnover proxy indicates large amoutns of capital are exhanged, and the asset attracts active market participation, low turnover proxy is the indicator of the trading activity to be sparse and small in value . In short, the socialness measure triad is composed of intensity (Volatility), discontinuity (Jump Frequency) and participation (turnover proxy) aspects of the asset. 
Further more, it is interesting to question also the social relations between different assets, if they take decision together, being correlated or correlation values are just symbol of random distribution, what are the communities in the market ? By definition ETFs  pool a group of securities into a fund and can be traded like an individual stock on an exchange. Thus it is highly possible to observe correlation between ETFs from same markets, as they get influence by the same stock. For individual stocks, correlations can arise from sector affiliation, shared macroeconomic sensitivities and common investor flows. If such mechanisms dominate, the resulting correlation network should exhibit community structure, where assets with similar economic characteristics are more strongly connected to one another than to the rest of the market.

<!-- ![Emotional Index visualization](social9.png) -->
<img src="{{ 'social9.png' | relative_url }}"
     alt="Emotional Index visualization"
     style="float: left; max-width: 75%; height: auto; margin: 0 0 1em 1.5em;">

To allow meaningful cross-sectional comparison, all three components are standardized using z-score normalization. The Emotional Index is then defined as a weighted combination of the standardized components:
<img width="529" height="89" alt="image" src="https://github.com/user-attachments/assets/9c9be9ea-bdc3-4815-800d-0d2435d19f99" />
​
Assets are classified according to the sign of their standardized EI score: for assets that have positive EI scores, it is labeled as "Extroverted" else Introverted.

**Hypotheses Study :**

H1 : Extroversion
<!-- ![Emotional Index visualization](social7.png) -->
<img src="{{ 'social7.png' | relative_url }}"
     alt="Emotional Index visualization"
     style="float: center; max-width: 75%; height: auto; margin: 0 0 1em 1.5em;">
Extroverted assets exhibit expressive behaviour in market shocks and in regime changements, amplified behaviour during market shocks. 

H2 : Introversion
![Emotional Index visualization](social8.png)
Introverted assets display below-average expressiveness, smoother price dynamics and muted reactions to new information. 

H3 : Structured Co-movement
![Emotional Index visualization](social11.png)
Asset returns exhibit systematic clustering

Here are some data observation for further social analysis of assets :

![Stock and ETF Personality Maps](social1.png)
*Figure 1 : Side-by-side scatter plots of stocks and ETFs in the normalized personality space defined by volatility and jump frequency.*

<img width="291" height="305" alt="image" src="https://github.com/user-attachments/assets/3f4aacab-1323-46d9-b007-e4cdf5fc863a" />

Highly extroverted ETFs such as PLC, RTL, SPY exhibit pronounced volatility and frequent jump activity, consistent with elevated market expressiveness. PLC and RTL display strong, discontinuous reactions around announcements and regime shifts, reflecting episodes in which market participants concentrate information flow and repositioning through these assets. SPY exhibits sustained extroversion as a central vehicle for trading, hedging, and price discovery, continuously transmitting market-wide information. QUS and NJAN show conditional extroversion, becoming expressive primarily during factor rotations or calendar-driven reallocations. Overall, these patterns are consistent with H1, confirming that extroversion corresponds to heightened market-level information processing and transmission. Extroverted stocks such as DTP, MEC, and HMI exhibit extreme volatility and frequent jump activity, indicating high market expressiveness. Rather than reflecting intrinsic firm characteristics, this behavior captures episodes in which market participants concentrate information processing and repositioning through these assets. As a result, they function as conduits for idiosyncratic information flow, transmitting shocks rapidly via large and discontinuous price movements.

<img width="330" height="302" alt="image" src="https://github.com/user-attachments/assets/f541aa2b-cdbc-4b86-ad8c-ab22d838f69c" />

* Introverted ETFs exhibit muted price dynamics and rare discontinuities, consistent with below-average market expressiveness. Even in the presence of non-trivial trading activity, price adjustments remain smooth and stable, indicating subdued reactions to new information. This behavior reflects a market-level form of restraint: these assets tend to absorb information quietly and remain comparatively insulated from short-term market fluctuations, rather than amplifying shocks. Overall, the observed patterns support H2, confirming that introversion captures restrained market expressiveness rather than illiquidity, neglect, or mechanical price rigidity. Introverted stocks exhibit persistently low Emotional Index (EI) scores, reflecting below-average market expressiveness. Their price dynamics are comparatively stable, with few abrupt movements and muted reactions to new information, even when trading activity is non-trivial. This behavior is consistent with a market-level form of restraint, whereby information is absorbed smoothly rather than transmitted through sharp price adjustments. In isolated cases (e.g., AAN), extreme EI values arise from data sparsity or structural trading constraints rather than genuine behavioral intensity, and are therefore interpreted as measurement artifacts rather than deviations from the underlying pattern.*

<div class="flourish-embed flourish-chart" data-src="visualisation/26818399"></div>
<script src="https://public.flourish.studio/resources/embed.js"></script>
<noscript>
  <img src="https://public.flourish.studio/visualisation/26818399/thumbnail"
       width="100%" alt="chart visualization" />
</noscript>

* Figure 2: Asset behavior across three market metrics. Higher values indicate more reactive assets, while lower values reflect more stable behavior, illustrating how the Emotional Index (EI) relates to underlying market dynamics. The figure shows a subset of 30 assets for clarity.*

<div class="flourish-embed flourish-network" data-src="visualisation/26824006"></div>
<script src="https://public.flourish.studio/resources/embed.js"></script>
<noscript>
  <img src="https://public.flourish.studio/visualisation/26824006/thumbnail"
       width="100%" alt="network visualization" />
</noscript>

* Figure 3: Friendship network illustration for selected stocks*
<div class="flourish-embed flourish-network" data-src="visualisation/26824327"></div>
<script src="https://public.flourish.studio/resources/embed.js"></script>
<noscript>
  <img src="https://public.flourish.studio/visualisation/26824327/thumbnail"
       width="100%" alt="network visualization" />
</noscript>

*Figure 4. Friendship network illustration for selected ETFs*

Figure 3 and Figure 4 are constructed from log-returns computed using adjusted closing prices. For each pair of assets, the Pearson correlation of returns is computed; if the absolute correlation exceeds 0.5, the assets are considered connected, sharing a comouvement due to friendship related exhange
The resulting networks display non-uniform connectivity patterns, characterized by densely connected subsets of assets and a limited number of cross-group links. Such structures are inconsistent with a random correlation model, in which strong correlations would be sparsely and evenly distributed. Instead, the observed concentration of links indicates that certain assets repeatedly move together, reflecting shared exposures, common information channels, or coordinated trading behavior, proving hypothesis 3. 

<img width="794" height="791" alt="image" src="https://github.com/user-attachments/assets/470c9f7a-b3f7-466e-b941-7939a5faa452" />
<img width="793" height="791" alt="image" src="https://github.com/user-attachments/assets/c733b5b5-87fc-4bc2-962b-1a3d28366ed0" />

On the clustering developed based on more assets, it is shown that ETFs are more correlated than stocks as they might have more assets in common under their title .

<div class="next-page-container">
  <a class="next-page-button"
     href="{{ '/s_n/' | relative_url }}">
    Next page →
  </a>
</div>