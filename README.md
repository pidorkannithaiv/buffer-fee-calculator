# buffer-fee-calculator
Clutch Buffer Fee Calculator 
[clutch-buffer-fee.html](https://github.com/user-attachments/files/26762333/clutch-buffer-fee.html)
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>Clutch — Buffer Fee Calculator</title>
<link href="https://fonts.googleapis.com/css2?family=Barlow:wght@300;400;500;600&family=Barlow+Condensed:wght@700;800&display=swap" rel="stylesheet"/>
<style>
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

:root {
  --bg: #FFFFFF;
  --surface: #F7F7F7;
  --surface2: #EFEFEF;
  --border: #DDDDDD;
  --accent: #FF464C;
  --accent2: #FF464C;
  --white: #000000;
  --muted: #777777;
  --t1-bg: #edfaf2;
  --t2-bg: #edf3fd;
  --t3-bg: #fdedf8;
  --t4-bg: #fdf6ed;
  --t1-accent: #1db954;
  --t2-accent: #3b82f6;
  --t3-accent: #c026d3;
  --t4-accent: #f59e0b;
}

body {
  background: var(--bg);
  color: #000000;
  font-family: 'Barlow', sans-serif;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  overflow-x: hidden;
  position: relative;
}

/* Noise texture overlay */
body::after {
  content: '';
  position: fixed;
  inset: 0;
  background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 256 256' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noise'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noise)' opacity='0.04'/%3E%3C/svg%3E");
  pointer-events: none;
  z-index: 100;
  opacity: 0.5;
}

/* Diagonal accent line */
.bg-line {
  position: fixed;
  top: -10%;
  right: 15%;
  width: 1px;
  height: 130%;
  background: linear-gradient(180deg, transparent, var(--accent) 40%, transparent);
  opacity: 0.15;
  transform: rotate(8deg);
  pointer-events: none;
}

.bg-line2 {
  position: fixed;
  top: -10%;
  right: 25%;
  width: 1px;
  height: 130%;
  background: linear-gradient(180deg, transparent, var(--accent) 60%, transparent);
  opacity: 0.04;
  transform: rotate(8deg);
  pointer-events: none;
}

/* ── HEADER ── */
header {
  width: 100%;
  max-width: 560px;
  padding: 3.5rem 2rem 0;
  text-align: center;
  align-items: center;
  display: flex;
  flex-direction: column;
  animation: slideDown 0.7s cubic-bezier(0.16, 1, 0.3, 1) both;
}

.top-bar {
  display: flex;
  justify-content: center; gap: 2rem;
  align-items: center;
  margin-bottom: 0.75rem;
  padding-bottom: 0.75rem;
  border-bottom: 1px solid var(--border);
}

.tag {
  font-family: 'Barlow Condensed', sans-serif;
  font-size: 11px;
  font-weight: 700;
  letter-spacing: 0.2em;
  text-transform: uppercase;
  color: var(--accent);
  background: rgba(255,70,76,0.08);
  border: 1px solid rgba(255,60,0,0.2);
  padding: 4px 10px;
}

.version {
  font-family: 'Barlow', monospace;
  font-size: 11px;
  color: var(--muted);
  letter-spacing: 0.1em;
}

/* BIG HEADLINE — Circular Bold style via Bebas Neue */
.headline-wrap {
  position: relative;
  line-height: 0.88;
  margin-bottom: 1.5rem;
}

.headline {
  font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; font-weight: 700;
  font-size: clamp(72px, 14vw, 110px);
  letter-spacing: 0.02em;
  color: #FF464C;
  display: block;
  position: relative;
}

.headline-sub {
  font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; font-weight: 700;
  font-size: clamp(30px, 5.5vw, 52px);
  color: #000000;
  letter-spacing: 0.02em;
  display: block;
  margin-top: 0.2em;
}

.headline-desc {
  font-size: 14px;
  color: var(--muted);
  font-weight: 300;
  letter-spacing: 0.05em;
  margin-top: 1rem;
  max-width: 400px;
  line-height: 1.6;
}

/* ── MAIN ── */
main {
  width: 100%;
  max-width: 560px;
  padding: 2.5rem 2rem 4rem;
  display: grid;
  grid-template-columns: 1fr;
  gap: 1.5rem;
  animation: fadeUp 0.8s cubic-bezier(0.16, 1, 0.3, 1) 0.2s both;
}

@media (max-width: 640px) {
  main { grid-template-columns: 1fr; }
}

/* ── INPUT PANEL ── */
.input-panel {
  background: var(--surface);
  border: 1px solid var(--border);
  padding: 2rem;
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
  position: relative;
  overflow: hidden;
}

.input-panel::before {
  content: '';
  position: absolute;
  top: 0; left: 0; right: 0;
  height: 2px;
  background: linear-gradient(90deg, var(--accent), transparent);
}

.panel-label {
  font-family: 'Barlow Condensed', sans-serif;
  font-size: 11px;
  font-weight: 700;
  letter-spacing: 0.2em;
  text-transform: uppercase;
  color: var(--muted);
}

.input-row {
  display: flex;
  align-items: stretch;
  border: 1px solid var(--border);
  background: var(--surface2);
  transition: border-color 0.2s;
}

.input-row:focus-within {
  border-color: var(--accent);
  box-shadow: 0 0 0 1px var(--accent);
}

.dollar {
  font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; font-weight: 700;
  font-size: 2rem;
  padding: 0.6rem 1rem;
  color: var(--accent);
  border-right: 1px solid var(--border);
  display: flex;
  align-items: center;
  background: rgba(255,70,76,0.06);
  user-select: none;
}

input[type="number"] {
  flex: 1;
  background: transparent;
  border: none;
  outline: none;
  font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; font-weight: 700;
  font-size: 2.2rem;
  color: #000000;
  padding: 0.6rem 1rem;
  letter-spacing: 0.05em;
  -moz-appearance: textfield;
  width: 100%;
}

input[type="number"]::-webkit-inner-spin-button,
input[type="number"]::-webkit-outer-spin-button { -webkit-appearance: none; }

input::placeholder { color: #CCCCCC; }

/* ── RESULT BOX ── */
.result-box {
  margin-top: auto;
  border: 1px solid var(--border);
  padding: 1.5rem;
  background: var(--surface2);
  min-height: 120px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  position: relative;
  transition: border-color 0.3s;
}

.result-box.has-result {
  border-color: var(--active-accent, var(--accent));
}

.result-box.has-result::after {
  content: '';
  position: absolute;
  bottom: 0; left: 0; right: 0;
  height: 2px;
  background: var(--active-accent, var(--accent));
}

.result-idle {
  font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; font-weight: 700;
  font-size: 1.1rem;
  color: #CCCCCC;
  letter-spacing: 0.1em;
  text-align: center;
}

.result-main { display: none; }
.result-main.show { display: block; animation: popIn 0.3s cubic-bezier(0.34,1.56,0.64,1) both; }

.result-tier-label {
  font-family: 'Barlow Condensed', sans-serif;
  font-size: 11px;
  font-weight: 700;
  letter-spacing: 0.2em;
  text-transform: uppercase;
  color: var(--active-accent, var(--accent));
  margin-bottom: 0.25rem;
}

.result-fee {
  font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; font-weight: 700;
  font-size: clamp(3rem, 8vw, 4.5rem);
  line-height: 1;
  color: #000000;
  letter-spacing: 0.02em;
}

.result-range {
  font-size: 12px;
  color: var(--muted);
  margin-top: 0.25rem;
  letter-spacing: 0.05em;
}

.result-oor {
  display: none;
  font-family: 'Barlow Condensed', sans-serif;
  font-size: 13px;
  color: var(--accent);
  letter-spacing: 0.1em;
  text-transform: uppercase;
  text-align: center;
}

.result-oor.show { display: block; }

/* ── TIERS PANEL ── */
.tiers-panel {
  display: flex;
  flex-direction: column;
  gap: 0.6rem;
}

.tier-card {
  background: var(--surface);
  border: 1px solid var(--border);
  padding: 1.2rem 1.5rem;
  display: flex;
  justify-content: center; gap: 2rem;
  align-items: center;
  position: relative;
  overflow: hidden;
  transition: border-color 0.25s, background 0.25s, transform 0.2s;
  cursor: default;
}

.tier-card::before {
  content: '';
  position: absolute;
  left: 0; top: 0; bottom: 0;
  width: 3px;
  background: var(--tier-color);
  opacity: 0;
  transition: opacity 0.25s;
}

.tier-card.active {
  border-color: var(--tier-color);
  background: var(--tier-bg);
  transform: translateX(6px);
}

.tier-card.active::before { opacity: 1; }

.tier-card.t1 { --tier-color: var(--t1-accent); --tier-bg: var(--t1-bg); }
.tier-card.t2 { --tier-color: var(--t2-accent); --tier-bg: var(--t2-bg); }
.tier-card.t3 { --tier-color: var(--t3-accent); --tier-bg: var(--t3-bg); }
.tier-card.t4 { --tier-color: var(--t4-accent); --tier-bg: var(--t4-bg); }

.tier-left {}

.tier-num {
  font-family: 'Barlow Condensed', sans-serif;
  font-size: 10px;
  font-weight: 700;
  letter-spacing: 0.2em;
  text-transform: uppercase;
  color: var(--muted);
  margin-bottom: 2px;
}

.tier-card.active .tier-num { color: var(--tier-color); }

.tier-range-txt {
  font-family: 'Barlow', sans-serif;
  font-size: 14px;
  font-weight: 500;
  color: var(--muted);
  transition: color 0.25s;
}

.tier-card.active .tier-range-txt { color: var(--white); }

.tier-fee-txt {
  font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; font-weight: 700;
  font-size: 2rem;
  letter-spacing: 0.05em;
  color: #CCCCCC;
  transition: color 0.25s;
}

.tier-card.active .tier-fee-txt { color: var(--tier-color); }

/* ── FOOTER ── */
footer {
  width: 100%;
  max-width: 560px;
  padding: 0 2rem 2.5rem;
  display: flex;
  justify-content: center; gap: 2rem;
  align-items: center;
  border-top: 1px solid var(--border);
  padding-top: 1.25rem;
  animation: fadeUp 0.8s cubic-bezier(0.16,1,0.3,1) 0.4s both;
}

.footer-brand {
  font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; font-weight: 700;
  font-size: 1.1rem;
  letter-spacing: 0.12em;
  color: var(--muted);
}

.footer-note {
  font-size: 11px;
  color: #AAAAAA;
  letter-spacing: 0.05em;
}

/* ── ANIMATIONS ── */
@keyframes slideDown {
  from { opacity: 0; transform: translateY(-24px); }
  to   { opacity: 1; transform: translateY(0); }
}

@keyframes fadeUp {
  from { opacity: 0; transform: translateY(20px); }
  to   { opacity: 1; transform: translateY(0); }
}

@keyframes popIn {
  from { opacity: 0; transform: scale(0.9); }
  to   { opacity: 1; transform: scale(1); }
}

@media (max-width: 480px) {
  .tier-card.active { transform: translateX(3px); }
  .result-fee { font-size: 3rem; }
}
</style>
</head>
<body>

<div class="bg-line"></div>
<div class="bg-line2"></div>

<header>
  <div class="top-bar">
    <span class="tag">Fee Calculator</span>
    <span class="version">v1.0 · 2026</span>
  </div>
  <div class="headline-wrap">
    <span class="headline">Clutch</span>
    <span class="headline-sub">Buffer Fee Calculator</span>
  </div>
  <p class="headline-desc">Enter your per diem interest amount below to instantly determine the applicable buffer fee tier.</p>
</header>

<main>

  <!-- Left: Input + Result -->
  <div class="input-panel">
    <div>
      <div class="panel-label" style="margin-bottom:0.75rem;">Per Diem Interest</div>
      <div class="input-row">
        <span class="dollar">$</span>
        <input type="number" id="perDiemInput" min="0" step="0.01" placeholder="0.00" autocomplete="off"/>
      </div>
    </div>

    <div>
      <div class="panel-label" style="margin-bottom:0.75rem;">Buffer Fee Result</div>
      <div class="result-box" id="resultBox">
        <div class="result-idle" id="resultIdle">AWAITING INPUT</div>
        <div class="result-main" id="resultMain">
          <div class="result-tier-label" id="resultTierLabel">Tier 1</div>
          <div class="result-fee" id="resultFee">$100</div>
          <div class="result-range" id="resultRangeTxt">Per diem under $5.00</div>
        </div>
        <div class="result-oor" id="resultOor">Outside defined range — review manually</div>
      </div>
    </div>
  </div>

  <!-- Right: Tier cards -->
  <div class="tiers-panel" style="display:none;">
    <div class="tier-card t1" id="tc1">
      <div class="tier-left">
        <div class="tier-num">Tier 1</div>
        <div class="tier-range-txt">Under $5.00</div>
      </div>
      <div class="tier-fee-txt">$100</div>
    </div>
    <div class="tier-card t2" id="tc2">
      <div class="tier-left">
        <div class="tier-num">Tier 2</div>
        <div class="tier-range-txt">$5.00 – $9.99</div>
      </div>
      <div class="tier-fee-txt">$150</div>
    </div>
    <div class="tier-card t3" id="tc3">
      <div class="tier-left">
        <div class="tier-num">Tier 3</div>
        <div class="tier-range-txt">$10.00 – $14.99</div>
      </div>
      <div class="tier-fee-txt">$200</div>
    </div>
    <div class="tier-card t4" id="tc4">
      <div class="tier-left">
        <div class="tier-num">Tier 4</div>
        <div class="tier-range-txt">$15.00 – $19.99</div>
      </div>
      <div class="tier-fee-txt">$250</div>
    </div>
  </div>

</main>

<div style="width:100%; max-width:900px; padding: 0 2rem 1.5rem; animation: fadeUp 0.8s cubic-bezier(0.16,1,0.3,1) 0.35s both;">
  <div style="border: 1.5px solid #FF464C; background: rgba(255,70,76,0.04); padding: 1rem 1.5rem; display: flex; align-items: flex-start; gap: 12px;">
    <span style="font-size: 18px; line-height:1; margin-top:2px;">⚠️</span>
    <p style="font-family: 'Barlow', sans-serif; font-size: 13px; color: #000000; line-height: 1.6; margin:0;">
      <strong style="font-weight:600;">Note:</strong> If the per diem is greater than $20, please consult with the payments team.
    </p>
  </div>
</div>

<footer>
  <span class="footer-brand">Clutch &copy; 2026</span>
  <span class="footer-note">Tiers defined for per diem interest $0.00 – $19.99</span>
</footer>

<script>
const input = document.getElementById('perDiemInput');
const resultBox = document.getElementById('resultBox');
const resultIdle = document.getElementById('resultIdle');
const resultMain = document.getElementById('resultMain');
const resultFee = document.getElementById('resultFee');
const resultTierLabel = document.getElementById('resultTierLabel');
const resultRangeTxt = document.getElementById('resultRangeTxt');
const resultOor = document.getElementById('resultOor');
const tierCards = [
  document.getElementById('tc1'),
  document.getElementById('tc2'),
  document.getElementById('tc3'),
  document.getElementById('tc4'),
];

const tiers = [
  { fee: 100, label: 'Tier 1', range: 'Per diem under $5.00',       accent: '#1db954', idx: 0 },
  { fee: 150, label: 'Tier 2', range: 'Per diem $5.00 – $9.99',     accent: '#3b82f6', idx: 1 },
  { fee: 200, label: 'Tier 3', range: 'Per diem $10.00 – $14.99',   accent: '#c026d3', idx: 2 },
  { fee: 250, label: 'Tier 4', range: 'Per diem $15.00 – $19.99',   accent: '#f59e0b', idx: 3 },
];

function getTier(v) {
  if (v < 5)  return tiers[0];
  if (v < 10) return tiers[1];
  if (v < 15) return tiers[2];
  if (v < 20) return tiers[3];
  return null;
}

function clearAll() {
  tierCards.forEach(c => c.classList.remove('active'));
  resultBox.classList.remove('has-result');
  resultBox.style.removeProperty('--active-accent');
}

input.addEventListener('input', function () {
  const raw = this.value.trim();
  clearAll();

  if (raw === '' || isNaN(parseFloat(raw)) || parseFloat(raw) < 0) {
    resultIdle.style.display = 'block';
    resultMain.classList.remove('show');
    resultOor.classList.remove('show');
    return;
  }

  const val = parseFloat(raw);
  resultIdle.style.display = 'none';
  const tier = getTier(val);

  if (!tier) {
    resultMain.classList.remove('show');
    resultOor.classList.add('show');
    return;
  }

  resultOor.classList.remove('show');

  // Activate tier card
  tierCards[tier.idx].classList.add('active');

  // Update result box
  resultBox.classList.add('has-result');
  resultBox.style.setProperty('--active-accent', tier.accent);

  resultTierLabel.textContent = tier.label;
  resultTierLabel.style.color = tier.accent;
  resultFee.textContent = '$' + tier.fee;
  resultRangeTxt.textContent = tier.range;

  // Re-trigger animation
  resultMain.classList.remove('show');
  void resultMain.offsetWidth;
  resultMain.classList.add('show');
});
</script>
</body>
</html>
