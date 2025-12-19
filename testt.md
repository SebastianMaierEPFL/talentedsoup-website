---
layout: article
title: Stock Personality Test
permalink: /testt/
---

<style>
/* =========================
   BASIC LAYOUT STYLES
   ========================= */
.test-wrap { max-width: 980px; margin: 0 auto; }
.test-hero { margin: 1.2rem 0 1rem; }
.test-hero h1 { margin-bottom: 0.3rem; }

.test-controls{
  display:grid;
  grid-template-columns: 1fr 180px 160px;
  gap:0.75rem;
  align-items:end;
  margin:1rem 0 1.25rem;
}
@media (max-width: 820px){ .test-controls{ grid-template-columns: 1fr; } }

.input-block label{ display:block; font-weight:600; margin-bottom:0.35rem; }
.input-block input, .input-block select, .test-controls button{
  width:100%;
  padding:0.65rem 0.75rem;
  border:1px solid rgba(0,0,0,0.18);
  border-radius:10px;
  font-size:1rem;
}
.test-controls button{ cursor:pointer; font-weight:700; }
.hint{ font-size:0.92rem; opacity:0.8; margin-top:0.35rem; }

/* =========================
   LOADING OVERLAY
   ========================= */
#loadingOverlay{
  position:fixed; inset:0;
  background:rgba(255,255,255,0.86);
  display:flex; justify-content:center; align-items:center;
  z-index:999;
}
.hidden{ display:none !important; }
.loading-card{
  width:min(520px, 92vw);
  border:1px solid rgba(0,0,0,0.15);
  border-radius:14px;
  padding:1.2rem;
  background:white;
  box-shadow:0 12px 36px rgba(0,0,0,0.08);
}
.spinner{
  width:28px; height:28px;
  border-radius:999px;
  border:3px solid rgba(0,0,0,0.15);
  border-top-color:rgba(0,0,0,0.55);
  animation:spin 0.9s linear infinite;
  margin-bottom:0.7rem;
}
@keyframes spin{ to{ transform:rotate(360deg);} }

/* =========================
   RESULT PANEL
   ========================= */
#resultPanel{
  border:1px solid rgba(0,0,0,0.14);
  border-radius:14px;
  padding:1.2rem;
  background:rgba(0,0,0,0.02);
}
.badge{
  display:inline-block;
  font-weight:800;
  letter-spacing:0.02em;
  font-size:1.2rem;
  padding:0.25rem 0.6rem;
  border:1px solid rgba(0,0,0,0.18);
  border-radius:999px;
  background:white;
}
.result-sub{ opacity:0.85; }

.result-identity{
  display:flex;
  gap:1rem;
  align-items:center;
  margin-bottom:1rem;
}

#avatarImg{
  width:400px; height:600px;
  border-radius:16px;
  border:1px solid rgba(0,0,0,0.15);
  background:white;
  object-fit:contain;
}

.desc-box{
  margin-top:0.65rem;
  background:white;
  border:1px solid rgba(0,0,0,0.12);
  border-radius:12px;
  padding:0.9rem 0.95rem;
}
.desc-box details summary{ cursor:pointer; font-weight:700; margin-top:0.55rem; }

/* =========================
   SLIDERS
   ========================= */
.sliders{
  display:grid;
  grid-template-columns:1fr 1fr;
  gap:0.9rem 1.2rem;
  margin-top:1.1rem;
}
@media (max-width: 820px){ .sliders{ grid-template-columns:1fr; } }

.slider-row label{
  display:flex;
  justify-content:space-between;
  gap:0.75rem;
  font-weight:700;
  margin-bottom:0.25rem;
}
.slider-row input[type="range"]{ width:100%; }
.small-note{ font-size:0.92rem; opacity:0.8; margin-top:0.9rem; }

/* =========================
   ERROR
   ========================= */
#errorBox{
  border:1px solid rgba(200,0,0,0.25);
  background:rgba(200,0,0,0.06);
  padding:0.85rem 0.9rem;
  border-radius:12px;
  margin:0.8rem 0;
}
</style>

<div class="test-wrap">

  <div class="test-hero">
    <h1>Stock & ETF Personality Test</h1>
    <p class="result-sub">
      Type a symbol, click <b>Take test</b>, and we’ll display its personality + percentile sliders for each axis.
    </p>
  </div>

  <!-- =========================
       CONTROLS
       ========================= -->
  <div class="test-controls">

    <div class="input-block">
      <label for="symbolInput">Symbol</label>
      <input id="symbolInput" list="symbolsList" placeholder="e.g., AAPL, SPY, TSLA" autocomplete="off" />
      <datalist id="symbolsList">
        {%- for kv in site.data.personality_data.assets -%}
          <option value="{{ kv[0] }}"></option>
        {%- endfor -%}
      </datalist>
      <div class="hint">Tip: start typing, then pick from autocomplete.</div>
    </div>

    <div class="input-block">
      <label for="kindSelect">Universe</label>
      <select id="kindSelect">
        <option value="all" selected>All</option>
        <option value="stock">Stocks</option>
        <option value="etf">ETFs</option>
      </select>
      <div class="hint">Filters results by dataset type.</div>
    </div>

    <div class="input-block">
      <label>&nbsp;</label>
      <button id="takeTestBtn">Take test</button>
      <div class="hint">Pen and paper is provided.</div>
    </div>

  </div>

  <div id="errorBox" class="hidden"></div>

  <!-- =========================
       RESULT PANEL
       ========================= -->
  <div id="resultPanel" class="hidden">

    <!-- Avatar + identity -->
    <div class="result-identity">
      <img id="avatarImg" src="" alt="Personality avatar" />
      <div>
        <div>
          <span class="badge" id="typeBadge">—</span>
          <span class="result-sub" id="symbolLine" style="margin-left:0.5rem;">—</span>
        </div>
        <div class="result-sub" id="typeNameLine" style="margin-top:0.35rem;">—</div>
      </div>
    </div>

    <div class="desc-box">
      <div id="descShort">—</div>
      <details>
        <summary>Read full description</summary>
        <div id="descLong" style="margin-top:0.55rem; white-space:pre-line;">—</div>
      </details>
    </div>

    <div class="sliders" id="slidersGrid"></div>

    <div class="small-note">
      Percentiles are shown as 0–100 positions on each axis (relative to the dataset used to compute them).
    </div>

  </div>

</div>

<!-- =========================
     LOADING OVERLAY
     ========================= -->
<div id="loadingOverlay" class="hidden">
  <div class="loading-card">
    <div class="spinner"></div>
    <div style="font-weight:800;">Analyzing intrinsic behavior…</div>
    <div style="opacity:0.75; margin-top:0.25rem;">
      (Lookup + UI rendering)
    </div>
  </div>
</div>

<script>
/**
 * ===========================================
 * DATA INJECTION FROM JEKYLL (build-time)
 * ===========================================
 * We keep your JSON "as-is":
 * - personality_data.json has metadata + an `assets` object
 * - personality_descriptions.json is keyed by 4-letter types (e.g., "IPAO")
 */
const personalityRoot = {{ site.data.personality_data | jsonify }};
const personalityData = personalityRoot.assets || {}; // <-- IMPORTANT
const descriptions = {{ site.data.personality_descriptions | jsonify }};

/**
 * Axes order / labels
 * These keys must match asset.axes.<KEY>.pct and asset.axes.<KEY>.letter
 *
 * NOTE:
 * - You requested Organized ↔ Chaotic (reversed label compared to before)
 * - Keep Reactive ↔ Stable as-is
 * - Keep order as it was
 */
const AXES = [
  { key: "EI", label: "Introvert ↔ Extrovert",  left: "I", right: "E" },
  { key: "PF", label: "Fluid ↔ Persistent",    left: "P", right: "F" },
  { key: "AR", label: "Restrained ↔ Assertive",left: "R", right: "A" },
  { key: "OC", label: "Organized ↔ Chaotic",   left: "O", right: "C" },
  { key: "SR", label: "Reactive ↔ Stable",     left: "R", right: "S" }
];

const elInput   = document.getElementById("symbolInput");
const elKind    = document.getElementById("kindSelect");
const elBtn     = document.getElementById("takeTestBtn");

const elOverlay = document.getElementById("loadingOverlay");
const elError   = document.getElementById("errorBox");

const elPanel   = document.getElementById("resultPanel");
const elType    = document.getElementById("typeBadge");
const elSymbol  = document.getElementById("symbolLine");
const elName    = document.getElementById("typeNameLine");
const elShort   = document.getElementById("descShort");
const elLong    = document.getElementById("descLong");
const elSliders = document.getElementById("slidersGrid");
const elAvatar  = document.getElementById("avatarImg");

function showLoading(on){ elOverlay.classList.toggle("hidden", !on); }

function showError(msg){
  elError.textContent = msg;
  elError.classList.remove("hidden");
}
function clearError(){
  elError.classList.add("hidden");
  elError.textContent = "";
}

function clamp01to100(x){
  const v = Number(x);
  if (!Number.isFinite(v)) return null;
  return Math.max(0, Math.min(100, v));
}

function normalizeSymbol(s){
  return String(s || "").trim().toUpperCase();
}

function findAsset(symbol){
  return personalityData[symbol] || null;
}

function kindMatches(assetKind, filterKind){
  // assetKind is "stock" or "etf" in your JSON
  if (filterKind === "all") return true;
  return String(assetKind || "").toLowerCase() === filterKind;
}

/**
 * Build avatar URL from the 4-letter core type.
 * Put your 16 images at: /assets/images/<TYPE4>.png
 * Example: /assets/images/EPAO.png
 */
function avatarUrlForType4(type4){
  const base = "{{ '/assets/images/' | relative_url }}";
  return `${base}${type4}.png`;
}

function renderSliders(asset){
  elSliders.innerHTML = "";

  // Your JSON: asset.axes = { EI:{letter,pct}, PF:{...}, ... }
  const axes = asset.axes || {};

  for (const ax of AXES){
    const pct = clamp01to100(axes?.[ax.key]?.pct);
    let letter = axes?.[ax.key]?.letter || "—";

    // If you still have old SR letters (e.g., T/A) and haven't converted yet,
    // you can map them on the fly for display:
    if (ax.key === "SR") {
      if (letter === "T") letter = "R"; // Reactive
      if (letter === "A") letter = "S"; // Stable (your old encoding)
    }

    const row = document.createElement("div");
    row.className = "slider-row";

    const lab = document.createElement("label");
    const left = document.createElement("span");
    left.textContent = ax.label;

    const right = document.createElement("span");
    right.textContent = (pct === null) ? `(${letter})` : `${pct.toFixed(0)}% (${letter})`;

    lab.appendChild(left);
    lab.appendChild(right);

    const slider = document.createElement("input");
    slider.type = "range";
    slider.min = 0;
    slider.max = 100;
    slider.value = (pct === null) ? 50 : pct;
    slider.disabled = true;

    row.appendChild(lab);
    row.appendChild(slider);

    elSliders.appendChild(row);
  }
}

function renderResult(symbol, asset){
  // Your JSON:
  // asset.type = "stock"/"etf"
  // asset.personality = "EPACT" (5 letters)
  const kind = asset.type ? String(asset.type) : "—";
  const personality5 = asset.personality || "—";
  const type4 = (typeof personality5 === "string" && personality5.length >= 4)
    ? personality5.slice(0,4)
    : personality5;

  // Description is keyed by the 4-letter type
  const desc = descriptions[type4] || null;

  // TEXT
  elType.textContent = personality5;
  elSymbol.textContent = `${symbol} · ${kind}`;

  elName.textContent = desc ? `${type4} — ${desc.name}` : `${type4} — (description missing)`;
  elShort.textContent = desc?.short || "No description found for this type yet.";
  elLong.textContent  = desc?.long  || "";

  // AVATAR (16 images)
  elAvatar.src = avatarUrlForType4(type4);
  elAvatar.onerror = () => {
    // optional fallback (create /assets/images/default.png if you want)
    elAvatar.src = "{{ '/assets/images/default.png' | relative_url }}";
  };

  // SLIDERS
  renderSliders(asset);

  elPanel.classList.remove("hidden");
}

async function handleTakeTest(){
  clearError();

  const symbol = normalizeSymbol(elInput.value);
  const filterKind = elKind.value;

  if (!symbol){
    showError("Please enter a symbol (e.g., AAPL, SPY).");
    elPanel.classList.add("hidden");
    return;
  }

  const asset = findAsset(symbol);
  if (!asset){
    showError(`Symbol "${symbol}" not found in personality_data.json.`);
    elPanel.classList.add("hidden");
    return;
  }

  if (!kindMatches(asset.type, filterKind)){
    showError(`Symbol "${symbol}" exists, but is not in the selected universe ("${filterKind}").`);
    elPanel.classList.add("hidden");
    return;
  }

  showLoading(true);
  await new Promise(r => setTimeout(r, 450));
  renderResult(symbol, asset);
  showLoading(false);
}

elBtn.addEventListener("click", handleTakeTest);
elInput.addEventListener("keydown", (e) => {
  if (e.key === "Enter") handleTakeTest();
});
</script>
