# Firefly-Algorithm-Project-Game
Firefly-Algorithm-Project-Game
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Firefly Algorithm — Live Optimizer</title>
<link href="https://fonts.googleapis.com/css2?family=Space+Mono:wght@400;700&family=Syne:wght@400;500;600;700;800&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #0a0a0f;
    --surface: #13131a;
    --surface2: #1c1c28;
    --border: rgba(255,255,255,0.07);
    --border2: rgba(255,255,255,0.13);
    --amber: #EF9F27;
    --amber-dim: rgba(239,159,39,0.15);
    --red: #E24B4A;
    --teal: #1D9E75;
    --text: #f0ede8;
    --text2: #8b8891;
    --text3: #55525f;
    --font-display: 'Syne', sans-serif;
    --font-mono: 'Space Mono', monospace;
  }

  * { box-sizing: border-box; margin: 0; padding: 0; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: var(--font-display);
    min-height: 100vh;
    overflow-x: hidden;
  }

  /* Background stars */
  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background-image:
      radial-gradient(1px 1px at 20% 15%, rgba(255,200,80,0.4) 0%, transparent 100%),
      radial-gradient(1px 1px at 80% 25%, rgba(255,200,80,0.3) 0%, transparent 100%),
      radial-gradient(1.5px 1.5px at 45% 70%, rgba(255,200,80,0.35) 0%, transparent 100%),
      radial-gradient(1px 1px at 15% 80%, rgba(255,200,80,0.25) 0%, transparent 100%),
      radial-gradient(1px 1px at 90% 60%, rgba(255,200,80,0.3) 0%, transparent 100%),
      radial-gradient(1px 1px at 60% 10%, rgba(255,200,80,0.2) 0%, transparent 100%),
      radial-gradient(1px 1px at 35% 45%, rgba(255,200,80,0.15) 0%, transparent 100%),
      radial-gradient(1.5px 1.5px at 70% 85%, rgba(255,200,80,0.3) 0%, transparent 100%);
    pointer-events: none;
    z-index: 0;
  }

  .wrapper {
    position: relative;
    z-index: 1;
    max-width: 860px;
    margin: 0 auto;
    padding: 2rem 1.5rem 3rem;
  }

  /* Header */
  .header {
    display: flex;
    align-items: flex-start;
    justify-content: space-between;
    margin-bottom: 2rem;
    gap: 1rem;
  }

  .header-left h1 {
    font-size: clamp(22px, 4vw, 34px);
    font-weight: 800;
    letter-spacing: -0.02em;
    line-height: 1.1;
    background: linear-gradient(135deg, var(--amber) 0%, #fff6d0 50%, var(--amber) 100%);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
  }

  .header-left p {
    font-size: 13px;
    color: var(--text2);
    margin-top: 6px;
    font-family: var(--font-mono);
    letter-spacing: 0.03em;
  }

  .badge-group {
    display: flex;
    gap: 6px;
    flex-wrap: wrap;
    margin-top: 10px;
  }

  .badge {
    font-size: 10px;
    font-family: var(--font-mono);
    padding: 3px 10px;
    border-radius: 99px;
    border: 0.5px solid;
    letter-spacing: 0.05em;
    text-transform: uppercase;
  }
  .badge-amber { border-color: var(--amber); color: var(--amber); background: rgba(239,159,39,0.1); }
  .badge-teal  { border-color: var(--teal); color: var(--teal); background: rgba(29,158,117,0.1); }
  .badge-red   { border-color: var(--red); color: var(--red); background: rgba(226,75,74,0.1); }

  /* Metrics */
  .metrics {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 10px;
    margin-bottom: 1.2rem;
  }

  .metric {
    background: var(--surface);
    border: 0.5px solid var(--border);
    border-radius: 12px;
    padding: 14px 16px;
    text-align: center;
    position: relative;
    overflow: hidden;
    transition: border-color 0.3s;
  }
  .metric::before {
    content: '';
    position: absolute;
    bottom: 0; left: 0; right: 0;
    height: 2px;
    background: var(--amber);
    transform: scaleX(0);
    transform-origin: left;
    transition: transform 0.4s ease;
  }
  .metric.active::before { transform: scaleX(1); }

  .metric-label {
    font-size: 10px;
    color: var(--text3);
    letter-spacing: 0.1em;
    text-transform: uppercase;
    font-family: var(--font-mono);
    margin-bottom: 6px;
  }
  .metric-val {
    font-size: 22px;
    font-weight: 700;
    color: var(--text);
    font-family: var(--font-mono);
    letter-spacing: -0.02em;
  }
  .metric-val.running { color: var(--amber); }
  .metric-val.converged { color: var(--teal); }

  /* Canvas area */
  .canvas-wrap {
    position: relative;
    border-radius: 16px;
    overflow: hidden;
    border: 0.5px solid var(--border2);
    background: #07070d;
    margin-bottom: 1.2rem;
  }

  canvas {
    display: block;
    width: 100%;
    height: auto;
  }

  .canvas-overlay {
    position: absolute;
    top: 12px; left: 12px;
    display: flex;
    gap: 6px;
    pointer-events: none;
  }

  .canvas-tag {
    font-size: 10px;
    font-family: var(--font-mono);
    padding: 3px 8px;
    border-radius: 6px;
    background: rgba(10,10,15,0.8);
    border: 0.5px solid var(--border2);
    color: var(--text2);
    letter-spacing: 0.05em;
  }

  /* Controls */
  .controls-panel {
    background: var(--surface);
    border: 0.5px solid var(--border);
    border-radius: 16px;
    padding: 1.2rem 1.4rem;
    margin-bottom: 1.2rem;
  }

  .controls-grid {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr;
    gap: 1rem;
    margin-bottom: 1rem;
  }

  .ctrl {
    display: flex;
    flex-direction: column;
    gap: 6px;
  }

  .ctrl-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  .ctrl-label {
    font-size: 11px;
    color: var(--text2);
    font-family: var(--font-mono);
    letter-spacing: 0.05em;
    text-transform: uppercase;
  }

  .ctrl-val {
    font-size: 12px;
    font-weight: 700;
    color: var(--amber);
    font-family: var(--font-mono);
  }

  input[type=range] {
    width: 100%;
    height: 4px;
    -webkit-appearance: none;
    appearance: none;
    border-radius: 99px;
    background: var(--border2);
    outline: none;
    cursor: pointer;
  }
  input[type=range]::-webkit-slider-thumb {
    -webkit-appearance: none;
    width: 16px; height: 16px;
    border-radius: 50%;
    background: var(--amber);
    cursor: pointer;
    border: 2px solid var(--bg);
    box-shadow: 0 0 8px rgba(239,159,39,0.5);
    transition: box-shadow 0.2s;
  }
  input[type=range]:hover::-webkit-slider-thumb {
    box-shadow: 0 0 14px rgba(239,159,39,0.8);
  }

  .landscape-row {
    display: flex;
    align-items: center;
    gap: 10px;
    margin-top: 0.2rem;
  }
  .landscape-row label {
    font-size: 11px;
    font-family: var(--font-mono);
    color: var(--text2);
    text-transform: uppercase;
    letter-spacing: 0.05em;
    white-space: nowrap;
  }

  select {
    flex: 1;
    height: 36px;
    background: var(--surface2);
    border: 0.5px solid var(--border2);
    border-radius: 8px;
    color: var(--text);
    font-family: var(--font-mono);
    font-size: 12px;
    padding: 0 10px;
    cursor: pointer;
    outline: none;
    transition: border-color 0.2s;
  }
  select:focus { border-color: var(--amber); }
  option { background: var(--surface2); }

  /* Buttons */
  .btn-row {
    display: flex;
    gap: 8px;
    flex-wrap: wrap;
  }

  .btn {
    height: 38px;
    padding: 0 20px;
    border-radius: 10px;
    font-family: var(--font-mono);
    font-size: 12px;
    font-weight: 700;
    letter-spacing: 0.04em;
    cursor: pointer;
    border: 0.5px solid var(--border2);
    background: var(--surface2);
    color: var(--text2);
    transition: all 0.18s;
    text-transform: uppercase;
  }
  .btn:hover { color: var(--text); border-color: var(--border); background: var(--surface); }
  .btn:active { transform: scale(0.97); }

  .btn-start {
    background: var(--amber);
    color: #0a0a0f;
    border-color: var(--amber);
  }
  .btn-start:hover { background: #f5b84a; color: #0a0a0f; border-color: #f5b84a; }
  .btn-start.paused {
    background: transparent;
    color: var(--amber);
    border-color: var(--amber);
  }

  .btn-step { color: var(--teal); border-color: rgba(29,158,117,0.4); }
  .btn-step:hover { background: rgba(29,158,117,0.1); color: var(--teal); border-color: var(--teal); }

  .btn-reset { color: var(--red); border-color: rgba(226,75,74,0.35); }
  .btn-reset:hover { background: rgba(226,75,74,0.1); color: var(--red); border-color: var(--red); }

  /* Legend */
  .legend {
    display: flex;
    gap: 16px;
    flex-wrap: wrap;
    margin-top: 1rem;
    padding-top: 1rem;
    border-top: 0.5px solid var(--border);
  }

  .legend-item {
    display: flex;
    align-items: center;
    gap: 6px;
    font-size: 11px;
    color: var(--text3);
    font-family: var(--font-mono);
  }

  .legend-dot {
    width: 10px; height: 10px;
    border-radius: 50%;
    flex-shrink: 0;
  }

  /* Info box */
  .info-box {
    background: var(--surface);
    border: 0.5px solid var(--border);
    border-left: 2px solid var(--amber);
    border-radius: 12px;
    padding: 14px 16px;
    font-size: 13px;
    color: var(--text2);
    line-height: 1.75;
    font-family: var(--font-mono);
    transition: all 0.4s;
  }
  .info-box strong { color: var(--amber); font-weight: 700; }

  /* Algo steps */
  .algo-steps {
    display: grid;
    grid-template-columns: repeat(3,1fr);
    gap: 10px;
    margin-bottom: 1.2rem;
  }

  .step-card {
    background: var(--surface);
    border: 0.5px solid var(--border);
    border-radius: 12px;
    padding: 14px;
    transition: border-color 0.3s;
  }
  .step-card.active { border-color: var(--amber); }

  .step-num {
    font-size: 10px;
    font-family: var(--font-mono);
    color: var(--text3);
    letter-spacing: 0.1em;
    margin-bottom: 6px;
  }

  .step-title {
    font-size: 13px;
    font-weight: 600;
    color: var(--text);
    margin-bottom: 4px;
  }

  .step-desc {
    font-size: 11px;
    color: var(--text2);
    font-family: var(--font-mono);
    line-height: 1.6;
  }

  /* Pulse animation for running */
  @keyframes pulse-border {
    0%, 100% { border-color: rgba(239,159,39,0.3); }
    50% { border-color: rgba(239,159,39,0.8); }
  }

  .canvas-wrap.running {
    animation: pulse-border 1.5s ease-in-out infinite;
  }

  @media(max-width: 600px) {
    .metrics { grid-template-columns: repeat(2,1fr); }
    .controls-grid { grid-template-columns: 1fr 1fr; }
    .algo-steps { grid-template-columns: 1fr; }
  }
</style>
</head>
<body>
<div class="wrapper">

  <div class="header">
    <div class="header-left">
      <h1>Firefly Algorithm</h1>
      <p>// swarm intelligence optimizer</p>
      <div class="badge-group">
        <span class="badge badge-amber">Nature-Inspired</span>
        <span class="badge badge-teal">Metaheuristic</span>
        <span class="badge badge-red">Live Simulation</span>
      </div>
    </div>
  </div>

  <!-- Algo steps -->
  <div class="algo-steps">
    <div class="step-card" id="step1">
      <div class="step-num">STEP 01</div>
      <div class="step-title">Initialize Swarm</div>
      <div class="step-desc">Place N fireflies randomly in the search space. Each has a brightness = fitness score.</div>
    </div>
    <div class="step-card" id="step2">
      <div class="step-num">STEP 02</div>
      <div class="step-title">Attract & Move</div>
      <div class="step-desc">Dimmer fireflies move toward brighter ones. Attraction decays with distance (γ).</div>
    </div>
    <div class="step-card" id="step3">
      <div class="step-num">STEP 03</div>
      <div class="step-title">Converge</div>
      <div class="step-desc">With random noise (α) the swarm escapes local optima and finds the global peak.</div>
    </div>
  </div>

  <!-- Metrics -->
  <div class="metrics">
    <div class="metric" id="m-gen">
      <div class="metric-label">Generation</div>
      <div class="metric-val" id="val-gen">0</div>
    </div>
    <div class="metric" id="m-best">
      <div class="metric-label">Best Fitness</div>
      <div class="metric-val" id="val-best">—</div>
    </div>
    <div class="metric" id="m-pop">
      <div class="metric-label">Fireflies</div>
      <div class="metric-val" id="val-pop">20</div>
    </div>
    <div class="metric" id="m-status">
      <div class="metric-label">Status</div>
      <div class="metric-val" id="val-status">Idle</div>
    </div>
  </div>

  <!-- Canvas -->
  <div class="canvas-wrap" id="canvasWrap">
    <canvas id="c"></canvas>
    <div class="canvas-overlay">
      <span class="canvas-tag" id="land-tag">MULTI-PEAK</span>
      <span class="canvas-tag" id="gen-tag">GEN 0</span>
    </div>
  </div>

  <!-- Controls -->
  <div class="controls-panel">
    <div class="controls-grid">
      <div class="ctrl">
        <div class="ctrl-header">
          <span class="ctrl-label">Population (N)</span>
          <span class="ctrl-val" id="v-pop">20</span>
        </div>
        <input type="range" id="sl-pop" min="5" max="50" value="20" step="1">
      </div>
      <div class="ctrl">
        <div class="ctrl-header">
          <span class="ctrl-label">Absorption (γ)</span>
          <span class="ctrl-val" id="v-gamma">0.5</span>
        </div>
        <input type="range" id="sl-gamma" min="1" max="30" value="5" step="1">
      </div>
      <div class="ctrl">
        <div class="ctrl-header">
          <span class="ctrl-label">Randomness (α)</span>
          <span class="ctrl-val" id="v-alpha">0.08</span>
        </div>
        <input type="range" id="sl-alpha" min="1" max="30" value="8" step="1">
      </div>
    </div>

    <div class="landscape-row" style="margin-bottom:1rem">
      <label>Landscape</label>
      <select id="landscape">
        <option value="multimodal">Multi-peak (hard)</option>
        <option value="unimodal">Single peak (easy)</option>
        <option value="rastrigin">Rastrigin (very hard)</option>
        <option value="rosenbrock">Rosenbrock (tricky)</option>
      </select>
    </div>

    <div class="btn-row">
      <button class="btn btn-start" id="btn-start" onclick="startFA()">▶ Start</button>
      <button class="btn btn-step" onclick="stepOnce()">⏭ Step</button>
      <button class="btn btn-reset" onclick="resetFA()">↺ Reset</button>
      <button class="btn" onclick="newRandom()">⊞ New Random</button>
    </div>

    <div class="legend">
      <div class="legend-item">
        <span class="legend-dot" style="background:var(--amber)"></span>
        Firefly (size = brightness)
      </div>
      <div class="legend-item">
        <span class="legend-dot" style="background:var(--red)"></span>
        Global best ★
      </div>
      <div class="legend-item">
        <span class="legend-dot" style="background:rgba(239,159,39,0.3);border-radius:2px;width:14px;height:10px"></span>
        Fitness landscape (heat)
      </div>
    </div>
  </div>

  <!-- Info -->
  <div class="info-box" id="infoBox">
    <strong>How it works:</strong> Each firefly has a position (x,y) and brightness = fitness at that point. Dimmer fireflies move toward brighter ones — closer and brighter targets pull stronger. Random noise α prevents getting stuck. Press <strong>Start</strong> to watch the swarm converge on the global optimum.
  </div>

</div>

<script>
const canvas = document.getElementById('c');
const ctx = canvas.getContext('2d');
let W, H, SCALE, OX, OY;
let fireflies = [], bestFF = null;
let running = false, genCount = 0, animId = null;
let heatCache = null;

function resizeCanvas() {
  const pw = canvas.parentElement.clientWidth;
  W = pw; H = Math.round(pw * 0.48);
  canvas.width = W; canvas.height = H;
  SCALE = Math.min(W, H) * 0.44;
  OX = W / 2; OY = H / 2;
  heatCache = null;
}

function getP() {
  return {
    n: parseInt(document.getElementById('sl-pop').value),
    gamma: parseInt(document.getElementById('sl-gamma').value) / 10,
    alpha: parseInt(document.getElementById('sl-alpha').value) / 100
  };
}

function fitness(x, y) {
  const land = document.getElementById('landscape').value;
  if (land === 'unimodal') return Math.exp(-0.5 * (x*x + y*y));
  if (land === 'multimodal') {
    return 0.55*Math.exp(-((x-0.5)*(x-0.5)+(y-0.5)*(y-0.5))*3.5)
      + 0.75*Math.exp(-((x+0.6)*(x+0.6)+(y-0.4)*(y-0.4))*4.5)
      + 0.42*Math.exp(-((x+0.2)*(x+0.2)+(y+0.7)*(y+0.7))*5.5)
      + 0.6*Math.exp(-((x-0.7)*(x-0.7)+(y+0.3)*(y+0.3))*3);
  }
  if (land === 'rastrigin') {
    const A = 0.3;
    return 1-(A*2+(x*x-A*Math.cos(2*Math.PI*x*3))+(y*y-A*Math.cos(2*Math.PI*y*3)));
  }
  if (land === 'rosenbrock') {
    return 1/(1+(1-x*1.5)*(1-x*1.5)*0.2+100*(y*1.5-(x*1.5)*(x*1.5))*(y*1.5-(x*1.5)*(x*1.5))*0.005);
  }
  return 0;
}

function initFireflies() {
  const { n } = getP();
  fireflies = [];
  for (let i = 0; i < n; i++) {
    const x = (Math.random()*2-1)*0.92, y = (Math.random()*2-1)*0.92;
    fireflies.push({ x, y, fit: fitness(x, y) });
  }
  updateBest();
  genCount = 0;
  updateUI();
}

function updateBest() {
  bestFF = fireflies.reduce((a, b) => b.fit > a.fit ? b : a, fireflies[0]);
}

function updateUI() {
  document.getElementById('val-gen').textContent = genCount;
  document.getElementById('val-best').textContent = bestFF ? bestFF.fit.toFixed(3) : '—';
  document.getElementById('val-pop').textContent = fireflies.length || getP().n;
  document.getElementById('gen-tag').textContent = 'GEN ' + genCount;
}

function faStep() {
  const { gamma, alpha } = getP();
  const n = fireflies.length;
  for (let i = 0; i < n; i++) {
    for (let j = 0; j < n; j++) {
      if (i === j) continue;
      if (fireflies[j].fit > fireflies[i].fit) {
        const dx = fireflies[j].x - fireflies[i].x;
        const dy = fireflies[j].y - fireflies[i].y;
        const r2 = dx*dx + dy*dy;
        const beta = Math.exp(-gamma * r2);
        fireflies[i].x = Math.max(-1, Math.min(1, fireflies[i].x + beta*dx + alpha*(Math.random()-0.5)));
        fireflies[i].y = Math.max(-1, Math.min(1, fireflies[i].y + beta*dy + alpha*(Math.random()-0.5)));
      }
    }
    fireflies[i].fit = fitness(fireflies[i].x, fireflies[i].y);
  }
  genCount++;
  updateBest();
  updateUI();
}

function tx(x) { return OX + x * SCALE; }
function ty(y) { return OY - y * SCALE; }

function buildHeatmap() {
  const res = 100;
  const tmp = document.createElement('canvas');
  tmp.width = W; tmp.height = H;
  const tc = tmp.getContext('2d');
  const sw = W / res, sh = H / res;
  for (let px = 0; px < res; px++) {
    for (let py = 0; py < res; py++) {
      const wx = (px / res) * 2 - 1;
      const wy = 1 - (py / res) * 2;
      const f = Math.max(0, Math.min(1, fitness(wx, wy)));
      const r = Math.round(30 + f * 180);
      const g = Math.round(f * 100);
      const b = Math.round(f * 20);
      tc.fillStyle = `rgba(${r},${g},${b},${0.1 + f*0.3})`;
      tc.fillRect(px * sw, py * sh, sw + 1, sh + 1);
    }
  }
  heatCache = tmp;
}

function drawFrame() {
  ctx.clearRect(0, 0, W, H);
  ctx.fillStyle = '#07070d';
  ctx.fillRect(0, 0, W, H);

  if (!heatCache) buildHeatmap();
  ctx.drawImage(heatCache, 0, 0);

  // Grid
  ctx.strokeStyle = 'rgba(255,255,255,0.04)';
  ctx.lineWidth = 0.5;
  ctx.setLineDash([4, 8]);
  for (let v = -1; v <= 1; v += 0.5) {
    ctx.beginPath(); ctx.moveTo(tx(v), 0); ctx.lineTo(tx(v), H); ctx.stroke();
    ctx.beginPath(); ctx.moveTo(0, ty(v)); ctx.lineTo(W, ty(v)); ctx.stroke();
  }
  ctx.setLineDash([]);

  // Axes
  ctx.strokeStyle = 'rgba(255,255,255,0.1)';
  ctx.lineWidth = 0.5;
  ctx.beginPath(); ctx.moveTo(OX, 0); ctx.lineTo(OX, H); ctx.stroke();
  ctx.beginPath(); ctx.moveTo(0, OY); ctx.lineTo(W, OY); ctx.stroke();

  if (!fireflies.length) return;

  const minF = Math.min(...fireflies.map(f => f.fit));
  const maxF = Math.max(...fireflies.map(f => f.fit));
  const range = maxF - minF || 1;

  // Draw attraction lines to best (subtle)
  if (bestFF) {
    const bx2 = tx(bestFF.x), by2 = ty(bestFF.y);
    fireflies.forEach(ff => {
      if (ff === bestFF) return;
      const norm = (ff.fit - minF) / range;
      if (norm < 0.3) {
        const fx2 = tx(ff.x), fy2 = ty(ff.y);
        const grad = ctx.createLinearGradient(fx2, fy2, bx2, by2);
        grad.addColorStop(0, 'rgba(239,159,39,0)');
        grad.addColorStop(1, `rgba(239,159,39,${0.05 + (1-norm)*0.06})`);
        ctx.strokeStyle = grad;
        ctx.lineWidth = 0.5;
        ctx.beginPath(); ctx.moveTo(fx2, fy2); ctx.lineTo(bx2, by2); ctx.stroke();
      }
    });
  }

  // Draw fireflies
  fireflies.forEach(ff => {
    const cx2 = tx(ff.x), cy2 = ty(ff.y);
    const norm = (ff.fit - minF) / range;
    const r = 3 + norm * 10;

    // Outer glow
    const grd = ctx.createRadialGradient(cx2, cy2, 0, cx2, cy2, r * 2.5);
    grd.addColorStop(0, `rgba(239,159,39,${0.25 + norm * 0.25})`);
    grd.addColorStop(1, 'rgba(239,159,39,0)');
    ctx.beginPath();
    ctx.arc(cx2, cy2, r * 2.5, 0, Math.PI * 2);
    ctx.fillStyle = grd;
    ctx.fill();

    // Body
    ctx.beginPath();
    ctx.arc(cx2, cy2, r, 0, Math.PI * 2);
    ctx.fillStyle = `rgba(239,159,39,${0.6 + norm * 0.4})`;
    ctx.fill();

    // Core
    ctx.beginPath();
    ctx.arc(cx2, cy2, r * 0.4, 0, Math.PI * 2);
    ctx.fillStyle = `rgba(255,245,180,${0.8 + norm * 0.2})`;
    ctx.fill();
  });

  // Best marker
  if (bestFF) {
    const bx2 = tx(bestFF.x), by2 = ty(bestFF.y);

    // Pulse ring
    ctx.beginPath();
    ctx.arc(bx2, by2, 18, 0, Math.PI * 2);
    ctx.strokeStyle = 'rgba(226,75,74,0.25)';
    ctx.lineWidth = 1.5;
    ctx.stroke();

    ctx.beginPath();
    ctx.arc(bx2, by2, 12, 0, Math.PI * 2);
    ctx.strokeStyle = 'rgba(226,75,74,0.7)';
    ctx.lineWidth = 1.5;
    ctx.stroke();

    ctx.beginPath();
    ctx.arc(bx2, by2, 7, 0, Math.PI * 2);
    ctx.fillStyle = 'rgba(226,75,74,0.95)';
    ctx.fill();

    ctx.fillStyle = 'rgba(255,255,255,0.95)';
    ctx.font = 'bold 8px sans-serif';
    ctx.textAlign = 'center';
    ctx.textBaseline = 'middle';
    ctx.fillText('★', bx2, by2);
  }
}

let frameSkip = 0;
function loop() {
  if (!running) return;
  frameSkip++;
  faStep();
  if (frameSkip % 1 === 0) drawFrame();
  if (genCount < 250) {
    animId = requestAnimationFrame(loop);
  } else {
    running = false;
    document.getElementById('canvasWrap').classList.remove('running');
    document.getElementById('val-status').textContent = 'Done';
    document.getElementById('val-status').className = 'metric-val converged';
    document.getElementById('btn-start').textContent = '▶ Start';
    document.getElementById('btn-start').classList.remove('paused');
    document.getElementById('infoBox').innerHTML =
      `<strong>Converged!</strong> Completed <strong>${genCount}</strong> generations. Best fitness: <strong>${bestFF ? bestFF.fit.toFixed(4) : ''}</strong> at position (<strong>${bestFF ? bestFF.x.toFixed(3) : ''}</strong>, <strong>${bestFF ? bestFF.y.toFixed(3) : ''}</strong>). The red ★ marks the global optimum found.`;
    [1,2,3].forEach(i => document.getElementById('step'+i).classList.remove('active'));
    document.getElementById('step3').classList.add('active');
  }
}

function startFA() {
  if (running) {
    running = false;
    cancelAnimationFrame(animId);
    document.getElementById('canvasWrap').classList.remove('running');
    document.getElementById('val-status').textContent = 'Paused';
    document.getElementById('val-status').className = 'metric-val';
    document.getElementById('btn-start').textContent = '▶ Resume';
    document.getElementById('btn-start').classList.add('paused');
    return;
  }
  if (!fireflies.length) initFireflies();
  running = true;
  document.getElementById('canvasWrap').classList.add('running');
  document.getElementById('val-status').textContent = 'Running';
  document.getElementById('val-status').className = 'metric-val running';
  document.getElementById('btn-start').textContent = '⏸ Pause';
  document.getElementById('btn-start').classList.remove('paused');
  document.getElementById('m-gen').classList.add('active');
  document.getElementById('step2').classList.add('active');
  loop();
}

function stepOnce() {
  if (running) { startFA(); return; }
  if (!fireflies.length) initFireflies();
  faStep();
  drawFrame();
  document.getElementById('val-status').textContent = 'Step';
  document.getElementById('val-status').className = 'metric-val';
  document.getElementById('step2').classList.add('active');
}

function resetFA() {
  running = false;
  cancelAnimationFrame(animId);
  fireflies = []; bestFF = null; genCount = 0; heatCache = null;
  document.getElementById('canvasWrap').classList.remove('running');
  document.getElementById('val-gen').textContent = '0';
  document.getElementById('val-best').textContent = '—';
  document.getElementById('val-status').textContent = 'Idle';
  document.getElementById('val-status').className = 'metric-val';
  document.getElementById('btn-start').textContent = '▶ Start';
  document.getElementById('btn-start').classList.remove('paused');
  document.getElementById('gen-tag').textContent = 'GEN 0';
  [1,2,3].forEach(i => document.getElementById('step'+i).classList.remove('active'));
  ctx.clearRect(0, 0, W, H);
  ctx.fillStyle = '#07070d';
  ctx.fillRect(0, 0, W, H);
  document.getElementById('infoBox').innerHTML =
    '<strong>How it works:</strong> Each firefly has a position (x,y) and brightness = fitness at that point. Dimmer fireflies move toward brighter ones — closer and brighter targets pull stronger. Random noise α prevents getting stuck. Press <strong>Start</strong> to watch the swarm converge on the global optimum.';
}

function newRandom() {
  resetFA();
  setTimeout(() => { initFireflies(); drawFrame(); document.getElementById('step1').classList.add('active'); }, 100);
}

// Slider bindings
document.getElementById('sl-pop').oninput = function() {
  document.getElementById('v-pop').textContent = this.value;
  document.getElementById('val-pop').textContent = this.value;
};
document.getElementById('sl-gamma').oninput = function() {
  document.getElementById('v-gamma').textContent = (parseInt(this.value)/10).toFixed(1);
};
document.getElementById('sl-alpha').oninput = function() {
  document.getElementById('v-alpha').textContent = (parseInt(this.value)/100).toFixed(2);
};
document.getElementById('landscape').onchange = function() {
  const names = { multimodal:'MULTI-PEAK', unimodal:'SINGLE PEAK', rastrigin:'RASTRIGIN', rosenbrock:'ROSENBROCK' };
  document.getElementById('land-tag').textContent = names[this.value] || this.value.toUpperCase();
  heatCache = null;
  if (!running && fireflies.length) drawFrame();
};

window.addEventListener('resize', () => { resizeCanvas(); heatCache = null; if (!running && fireflies.length) drawFrame(); });

resizeCanvas();
initFireflies();
drawFrame();
document.getElementById('step1').classList.add('active');
</script>
</body>
</html>

