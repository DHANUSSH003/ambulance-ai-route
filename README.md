# SETOS — Smart Emergency Traffic Orchestration System
A browser-based traffic simulation that shows how emergency vehicles can reach their destination faster by automatically alerting and coordinating nearby vehicles in real time.

# How to Run
Just open index.html in any browser. No installations needed.

# Controls

 Button                         Action

▶ Start Simulation --->           Dispatches the ambulance

⚠ Add Traffic--->                 Adds a new vehicle to the road

↺ Reset-->                       Restarts the simulation

# What Happens

Ambulance enters the road and moves forward
A detection ring scans for nearby vehicles
Detected vehicles receive alerts and move out of the lane
A green corridor forms ahead of the ambulance
Ambulance speeds up as the path clears
Reaches the  hospital at full speed


# Built With
HTML · CSS · JavaScript — no frameworks, no libraries.

#Code:
```
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>SETOS — Smart Emergency Traffic Orchestration System</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Rajdhani:wght@400;600;700&family=Share+Tech+Mono&family=Orbitron:wght@700;900&display=swap');

  :root {
    --bg: #050a10;
    --surface: #0a1520;
    --surface2: #0f1f30;
    --border: #1a3a5c;
    --accent: #00d4ff;
    --accent2: #ff3b3b;
    --green: #00ff88;
    --yellow: #ffd700;
    --text: #c8e0f0;
    --dim: #4a7a9b;
  }

  * { margin: 0; padding: 0; box-sizing: border-box; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: 'Rajdhani', sans-serif;
    height: 100vh;
    display: flex;
    flex-direction: column;
    overflow: hidden;
  }

  /* ── HEADER ── */
  header {
    padding: 12px 28px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    border-bottom: 1px solid var(--border);
    background: linear-gradient(90deg, #050a10 0%, #0a1520 100%);
    flex-shrink: 0;
  }
  .logo {
    font-family: 'Orbitron', monospace;
    font-size: 1.1rem;
    font-weight: 900;
    color: var(--accent);
    letter-spacing: 3px;
    text-shadow: 0 0 20px rgba(0,212,255,0.5);
  }
  .logo span { color: var(--accent2); }
  .tagline {
    font-family: 'Share Tech Mono', monospace;
    font-size: 0.68rem;
    color: var(--dim);
    letter-spacing: 1.5px;
  }
  .status-bar { display: flex; gap: 20px; align-items: center; }
  .status-dot {
    width: 8px; height: 8px;
    border-radius: 50%;
    background: var(--green);
    box-shadow: 0 0 10px var(--green);
    animation: pulse 1.5s infinite;
  }
  @keyframes pulse { 0%,100%{opacity:1} 50%{opacity:0.3} }
  .status-label {
    font-family: 'Share Tech Mono', monospace;
    font-size: 0.7rem;
    color: var(--green);
    letter-spacing: 1px;
  }

  /* ── LAYOUT ── */
  main {
    display: grid;
    grid-template-columns: 260px 1fr 220px;
    flex: 1;
    min-height: 0;
  }

  /* ── PANELS ── */
  .panel {
    background: var(--surface);
    border-right: 1px solid var(--border);
    padding: 20px 16px;
    display: flex;
    flex-direction: column;
    gap: 16px;
    overflow-y: auto;
  }
  .panel.right { border-right: none; border-left: 1px solid var(--border); }
  .panel-title {
    font-family: 'Orbitron', monospace;
    font-size: 0.65rem;
    color: var(--dim);
    letter-spacing: 3px;
    text-transform: uppercase;
    padding-bottom: 10px;
    border-bottom: 1px solid var(--border);
  }

  /* ── BUTTONS ── */
  .btn {
    width: 100%;
    padding: 11px 16px;
    border: 1px solid var(--border);
    background: var(--surface2);
    color: var(--text);
    font-family: 'Rajdhani', sans-serif;
    font-size: 0.95rem;
    font-weight: 600;
    letter-spacing: 1.5px;
    cursor: pointer;
    transition: all 0.2s;
    display: flex;
    align-items: center;
    gap: 10px;
    text-transform: uppercase;
  }
  .btn:hover { background: var(--border); color: #fff; }
  .btn.primary { border-color: var(--accent); color: var(--accent); }
  .btn.primary:hover { background: rgba(0,212,255,0.12); box-shadow: 0 0 20px rgba(0,212,255,0.2); }
  .btn.danger  { border-color: var(--accent2); color: var(--accent2); }
  .btn.danger:hover  { background: rgba(255,59,59,0.12); }
  .btn:disabled { opacity: 0.35; cursor: not-allowed; }

  /* ── STAT CARDS ── */
  .stat-card { background: var(--surface2); border: 1px solid var(--border); padding: 14px; }
  .stat-label {
    font-family: 'Share Tech Mono', monospace;
    font-size: 0.62rem;
    color: var(--dim);
    letter-spacing: 1.5px;
    margin-bottom: 6px;
  }
  .stat-value {
    font-family: 'Orbitron', monospace;
    font-size: 1.8rem;
    font-weight: 700;
    color: var(--accent);
  }
  .stat-value.green  { color: var(--green); }
  .stat-value.red    { color: var(--accent2); }
  .stat-value.yellow { color: var(--yellow); }

  /* ── ALERT LOG ── */
  .alert-log {
    flex: 1; overflow-y: auto;
    display: flex; flex-direction: column; gap: 6px;
  }
  .alert-item {
    background: var(--surface2);
    border-left: 3px solid var(--accent);
    padding: 8px 10px;
    font-family: 'Share Tech Mono', monospace;
    font-size: 0.65rem;
    color: var(--text);
    animation: slideIn 0.3s ease;
  }
  .alert-item.warn    { border-color: var(--yellow); }
  .alert-item.danger  { border-color: var(--accent2); }
  .alert-item.success { border-color: var(--green); }
  @keyframes slideIn { from{transform:translateX(-10px);opacity:0} to{transform:translateX(0);opacity:1} }

  /* speed gauge */
  .speed-gauge { display: flex; flex-direction: column; gap: 4px; }
  .gauge-bar { height: 6px; background: var(--surface2); border: 1px solid var(--border); overflow: hidden; }
  .gauge-fill { height: 100%; background: linear-gradient(90deg, var(--green), var(--accent)); transition: width 0.5s ease; }

  .pill {
    display: inline-flex; align-items: center; gap: 6px;
    padding: 4px 10px; border: 1px solid var(--border);
    font-family: 'Share Tech Mono', monospace; font-size: 0.62rem; color: var(--dim); letter-spacing: 1px;
  }
  .pill .dot { width: 6px; height: 6px; border-radius: 50%; background: var(--accent); }

  /* ── SIM AREA ── */
  .sim-area {
    position: relative;
    background: var(--bg);
    overflow: hidden;
    display: flex;
    flex-direction: column;
  }
  .sim-area::before {
    content: '';
    position: absolute; inset: 0;
    background: repeating-linear-gradient(0deg, transparent, transparent 2px, rgba(0,212,255,0.015) 2px, rgba(0,212,255,0.015) 4px);
    pointer-events: none; z-index: 10;
  }

  /* ── ROAD VIEWPORT (clips the scrolling world) ── */
  .road-viewport {
    position: relative;
    flex: 1;
    overflow: hidden;
    border-top: 2px solid #1a3a5c;
    border-bottom: 2px solid #1a3a5c;
  }

  /* ── ROAD WORLD (long, scrolled by JS) ── */
  .road-world {
    position: absolute;
    top: 0; left: 0;
    height: 100%;
    /* width set by JS */
    background: linear-gradient(180deg, #070f1a 0%, #0a1422 50%, #070f1a 100%);
  }

  /* Lane dividers (inside world) */
  .lane-line {
    position: absolute;
    left: 0; right: 0;
    height: 2px;
    background: repeating-linear-gradient(90deg, #ffd700 0px, #ffd700 30px, transparent 30px, transparent 60px);
    opacity: 0.25;
    pointer-events: none;
  }

  /* fixed lane labels (left side of viewport) */
  .lane-label {
    position: absolute;
    left: 14px;
    font-family: 'Share Tech Mono', monospace;
    font-size: 0.6rem;
    color: rgba(74,122,155,0.5);
    letter-spacing: 2px;
    transform: translateY(-50%);
    z-index: 8;
    pointer-events: none;
  }

  /* ── VEHICLE ── */
  .vehicle {
    position: absolute;
    width: 54px; height: 28px;
    border-radius: 5px;
    display: flex; align-items: center; justify-content: center;
    font-size: 18px;
    /* only top transitions (lane change), left is instant (world scrolls) */
    transition: top 0.5s cubic-bezier(.4,0,.2,1), background 0.3s, border 0.3s;
    z-index: 5;
    user-select: none;
  }
  .vehicle .alert-badge {
    position: absolute;
    top: -22px; left: 50%; transform: translateX(-50%);
    background: var(--accent2); color: #fff;
    font-family: 'Share Tech Mono', monospace; font-size: 0.55rem;
    padding: 2px 6px; border-radius: 2px;
    white-space: nowrap; opacity: 0; transition: opacity 0.3s;
    pointer-events: none; z-index: 20; letter-spacing: 1px;
  }
  .vehicle.alerted .alert-badge { opacity: 1; }
  .vehicle .instruction-badge {
    position: absolute;
    bottom: -22px; left: 50%; transform: translateX(-50%);
    background: var(--yellow); color: #000;
    font-family: 'Share Tech Mono', monospace; font-size: 0.55rem;
    padding: 2px 6px; border-radius: 2px;
    white-space: nowrap; opacity: 0; transition: opacity 0.3s;
    pointer-events: none; z-index: 20; letter-spacing: 1px;
  }
  .vehicle.moving .instruction-badge { opacity: 1; }

  /* ── AMBULANCE ── */
  .ambulance {
    position: absolute;
    width: 64px; height: 32px;
    border-radius: 4px;
    display: flex; align-items: center; justify-content: center;
    font-size: 22px;
    z-index: 8;
    transition: top 0.5s ease;
    filter: drop-shadow(0 0 12px rgba(255,59,59,0.8));
  }
  .ambulance.active { animation: ambGlow 0.6s infinite alternate; }
  @keyframes ambGlow {
    from { filter: drop-shadow(0 0 8px rgba(255,59,59,0.6)); }
    to   { filter: drop-shadow(0 0 22px rgba(255,59,59,1)); }
  }
  .ambulance::before, .ambulance::after {
    content:''; position:absolute; top:3px;
    width:5px; height:5px; border-radius:50%;
    animation: fl 0.4s infinite alternate;
  }
  .ambulance::before { left:4px; background:#0044ff; box-shadow:0 0 5px #0044ff; }
  .ambulance::after  { right:4px; background:#ff3b3b; animation-delay:.2s; box-shadow:0 0 5px #ff3b3b; }
  @keyframes fl { 0%{opacity:1} 100%{opacity:.05} }

  /* ── CLEAR PATH GLOW ── */
  .clear-path {
    position: absolute; top: 0; bottom: 0;
    background: linear-gradient(90deg, transparent, rgba(0,255,136,0.06), rgba(0,255,136,0.1), rgba(0,255,136,0.06), transparent);
    pointer-events: none; z-index: 3;
    transition: left 0.5s ease, width 0.5s ease, opacity 0.5s, top 0.4s, height 0.4s;
    opacity: 0;
  }

  /* ── DETECTION RING ── */
  .detection-ring {
    position: absolute;
    border: 1.5px dashed rgba(0,212,255,0.3);
    border-radius: 50%;
    pointer-events: none; z-index: 4;
    transform: translate(-50%, -50%);
    animation: ringPulse 2s infinite;
    display: none;
  }
  @keyframes ringPulse {
    0%,100%{opacity:0.4;transform:translate(-50%,-50%) scale(1)}
    50%{opacity:0.15;transform:translate(-50%,-50%) scale(1.05)}
  }

  /* ── MINIMAP ── */
  .minimap {
    position: absolute;
    bottom: 12px; left: 50%; transform: translateX(-50%);
    width: 240px; height: 20px;
    background: rgba(10,21,32,0.9);
    border: 1px solid var(--border);
    z-index: 20; pointer-events: none;
    display: none;
  }
  .mm-label {
    position: absolute; top: -14px; left:0; right:0; text-align:center;
    font-family:'Share Tech Mono',monospace; font-size:.42rem; color:var(--dim); letter-spacing:2px;
  }
  .mm-vp {
    position:absolute;top:0;bottom:0;
    background:rgba(0,212,255,.08);border:1px solid rgba(0,212,255,.25);
    transition:left .3s;
  }
  .mm-amb {
    position:absolute;top:50%;transform:translateY(-50%);
    width:9px;height:9px;border-radius:50%;
    background:var(--accent2);box-shadow:0 0 6px var(--accent2);
    transition:left .1s;
  }

  /* ── PROGRESS BAR ── */
  .progress-bar-wrap { width:100%; height:4px; background:var(--surface2); border-top:1px solid var(--border); }
  .progress-bar-fill { height:100%; background:linear-gradient(90deg,var(--accent2),var(--accent)); transition:width 0.3s ease; }

  /* ── OVERLAYS ── */
  .overlay-msg {
    position: absolute; inset: 0;
    display: flex; align-items: center; justify-content: center;
    z-index: 30;
    background: rgba(5,10,16,0.75); backdrop-filter: blur(2px);
    flex-direction: column; gap: 12px;
  }
  .overlay-msg h2 {
    font-family: 'Orbitron', monospace; font-size: 1.5rem;
    color: var(--accent); text-shadow: 0 0 30px rgba(0,212,255,0.5); letter-spacing: 4px;
  }
  .overlay-msg p { font-family:'Share Tech Mono',monospace; font-size:.75rem; color:var(--dim); letter-spacing:2px; }

  .complete-banner {
    display: none;
    position: absolute; top:50%; left:50%; transform:translate(-50%,-50%);
    background: var(--surface); border: 1px solid var(--green);
    padding: 24px 40px; text-align: center; z-index: 40;
    box-shadow: 0 0 60px rgba(0,255,136,0.2);
  }
  .complete-banner h3 { font-family:'Orbitron',monospace; color:var(--green); font-size:1.1rem; letter-spacing:3px; margin-bottom:8px; }
  .complete-banner p  { font-family:'Share Tech Mono',monospace; font-size:.7rem; color:var(--dim); letter-spacing:2px; }

  ::-webkit-scrollbar{width:4px;}
  ::-webkit-scrollbar-track{background:var(--surface);}
  ::-webkit-scrollbar-thumb{background:var(--border);border-radius:2px;}
</style>
</head>
<body>

<header>
  <div>
    <div class="logo">SE<span>T</span>OS</div>
    <div class="tagline">SMART EMERGENCY TRAFFIC ORCHESTRATION SYSTEM</div>
  </div>
  <div class="status-bar">
    <div class="status-dot" id="sysDot"></div>
    <div class="status-label" id="sysLabel">SYSTEM READY</div>
  </div>
  <div class="tagline" style="text-align:right">ClearPath AI &nbsp;|&nbsp; Logistics Domain</div>
</header>

<main>

  <!-- LEFT PANEL -->
  <div class="panel">
    <div class="panel-title">Mission Control</div>
    <button class="btn primary" id="btnStart"   onclick="startSim()">▶ &nbsp;START SIMULATION</button>
    <button class="btn"         id="btnTraffic" onclick="addTraffic()" disabled>⚠ &nbsp;ADD TRAFFIC</button>
    <button class="btn danger"                  onclick="resetSim()">↺ &nbsp;RESET</button>

    <div class="panel-title" style="margin-top:8px">System Legend</div>
    <div style="display:flex;flex-direction:column;gap:6px;">
      <div style="display:flex;align-items:center;gap:8px;font-family:'Share Tech Mono',monospace;font-size:.6rem;color:var(--dim)">
        <div style="width:18px;height:11px;background:#ff3b3b;border-radius:2px"></div>Ambulance
      </div>
      <div style="display:flex;align-items:center;gap:8px;font-family:'Share Tech Mono',monospace;font-size:.6rem;color:var(--dim)">
        <div style="width:18px;height:11px;background:#1a3a6c;border:1px solid #2a5a9c;border-radius:2px"></div>Normal Car
      </div>
      <div style="display:flex;align-items:center;gap:8px;font-family:'Share Tech Mono',monospace;font-size:.6rem;color:var(--dim)">
        <div style="width:18px;height:11px;background:#5a2a00;border:1px solid #ff8800;border-radius:2px"></div>Alert Zone
      </div>
      <div style="display:flex;align-items:center;gap:8px;font-family:'Share Tech Mono',monospace;font-size:.6rem;color:var(--dim)">
        <div style="width:18px;height:11px;background:rgba(0,255,136,.2);border:1px solid #00ff88;border-radius:2px"></div>Clear Path
      </div>
    </div>

    <div class="panel-title" style="margin-top:8px">Ambulance Speed</div>
    <div class="speed-gauge">
      <div style="display:flex;justify-content:space-between;font-family:'Share Tech Mono',monospace;font-size:.6rem;color:var(--dim)">
        <span>0 km/h</span><span id="speedVal">0 km/h</span><span>120 km/h</span>
      </div>
      <div class="gauge-bar"><div class="gauge-fill" id="speedFill" style="width:0%"></div></div>
    </div>

    <div class="panel-title" style="margin-top:8px">Detection Radius</div>
    <div class="pill"><div class="dot"></div><span id="detectionLabel">INACTIVE</span></div>

    <div style="flex:1"></div>
    <div class="pill" style="font-size:.55rem;color:var(--dim)">
      <div class="dot" style="background:var(--dim)"></div>HACKATHON DEMO — CLEARPATH AI
    </div>
  </div>

  <!-- CENTER SIM -->
  <div class="sim-area" id="simArea">

    <!-- road viewport -->
    <div class="road-viewport" id="roadViewport">

      <!-- the long scrolling world -->
      <div class="road-world" id="roadWorld">
        <div class="lane-line" id="laneLine1"></div>
        <div class="lane-line" id="laneLine2"></div>
        <div class="clear-path"    id="clearPath"></div>
        <div class="detection-ring" id="detectionRing"></div>
      </div>

      <!-- fixed labels over viewport -->
      <div class="lane-label" id="laneLabel1">LANE 1</div>
      <div class="lane-label" id="laneLabel2">LANE 2</div>
      <div class="lane-label" id="laneLabel3">LANE 3</div>

      <!-- minimap -->
      <div class="minimap" id="minimap">
        <div class="mm-label">ROUTE PROGRESS</div>
        <div class="mm-vp"  id="mmVp"></div>
        <div class="mm-amb" id="mmAmb"></div>
      </div>

      <!-- overlays -->
      <div class="overlay-msg" id="overlayMsg">
        <h2>SETOS READY</h2>
        <p>PRESS START SIMULATION TO BEGIN</p>
      </div>
      <div class="complete-banner" id="completeBanner">
        <h3>✓ PATH CLEARED</h3>
        <p>AMBULANCE REACHED DESTINATION</p>
      </div>

    </div>

    <!-- progress bar -->
    <div class="progress-bar-wrap">
      <div class="progress-bar-fill" id="progressBar" style="width:0%"></div>
    </div>

  </div>

  <!-- RIGHT PANEL -->
  <div class="panel right">
    <div class="panel-title">Live Analytics</div>
    <div class="stat-card"><div class="stat-label">VEHICLES ALERTED</div><div class="stat-value yellow" id="statAlerted">0</div></div>
    <div class="stat-card"><div class="stat-label">VEHICLES MOVED</div><div class="stat-value green"  id="statMoved">0</div></div>
    <div class="stat-card"><div class="stat-label">TIME SAVED (EST.)</div><div class="stat-value"      id="statTime">0s</div></div>
    <div class="stat-card"><div class="stat-label">PATH CLEAR %</div><div class="stat-value green"    id="statClear">0%</div></div>

    <div class="panel-title" style="margin-top:4px">Alert Log</div>
    <div class="alert-log" id="alertLog">
      <div class="alert-item" style="color:var(--dim)">// Awaiting simulation...</div>
    </div>
  </div>

</main>

<script>
// ─── CONFIG ─────────────────────────────────────────────────────────
const WORLD_W       = 3000;   // total road length in pixels
const AMB_W         = 64;
const AMB_H         = 32;
const VEH_W         = 54;
const VEH_H         = 28;
const DETECT_THRESH = 240;    // detection radius ahead of ambulance
const LANE_PCT      = [0.165, 0.50, 0.833]; // lane centres as % of road height
const VEH_EMOJIS    = ['🚗','🚙','🚐','🚌','🚚','🚕'];

let RH = 0;   // road height, set on start
const LC = l => RH * LANE_PCT[l];  // lane centre Y

// state
let vehicles = [], ambulance = null;
let simRunning = false, simInterval = null;
let alertedCount = 0, movedCount = 0, timeSaved = 0, clearPct = 0, ambSpeed = 0;
let camX = 0;   // camera offset (how far we've scrolled)

// ─── ROAD SETUP ─────────────────────────────────────────────────────
function setupRoad() {
  const vp = document.getElementById('roadViewport');
  RH = vp.offsetHeight;

  const world = document.getElementById('roadWorld');
  world.style.width = WORLD_W + 'px';

  // Lane dividers at 1/3 and 2/3 height
  document.getElementById('laneLine1').style.top = (RH / 3) + 'px';
  document.getElementById('laneLine2').style.top = (RH * 2 / 3) + 'px';

  // Fixed lane labels (left of viewport, positioned by top)
  document.getElementById('laneLabel1').style.top = LC(0) + 'px';
  document.getElementById('laneLabel2').style.top = LC(1) + 'px';
  document.getElementById('laneLabel3').style.top = LC(2) + 'px';

  // Distance markers along the world
  for (let x = 200; x < WORLD_W - 100; x += 250) {
    const mk = document.createElement('div');
    mk.style.cssText = `position:absolute;left:${x}px;top:0;width:1px;height:100%;
      background:rgba(255,255,255,.04);pointer-events:none;z-index:1;`;
    world.appendChild(mk);
  }

  // Hospital marker at the end
  const dest = document.createElement('div');
  dest.style.cssText = `position:absolute;left:${WORLD_W - 100}px;top:0;bottom:0;width:3px;
    background:linear-gradient(180deg,var(--green),transparent,var(--green));
    box-shadow:0 0 18px var(--green);opacity:.6;pointer-events:none;z-index:2;`;
  world.appendChild(dest);
  const dlbl = document.createElement('div');
  dlbl.style.cssText = `position:absolute;left:${WORLD_W - 98}px;top:6px;
    font-family:'Share Tech Mono',monospace;font-size:.5rem;
    color:var(--green);letter-spacing:1px;pointer-events:none;z-index:3;`;
  dlbl.textContent = '🏥 HOSPITAL';
  world.appendChild(dlbl);
}

// ─── INIT VEHICLES ───────────────────────────────────────────────────
function initVehicles() {
  const world = document.getElementById('roadWorld');

  // Spread vehicles across the full world length in all 3 lanes
  const configs = [
    // lane, worldX fraction
    { lane:0, xF:0.18 },
    { lane:0, xF:0.32 },
    { lane:0, xF:0.48 },
    { lane:0, xF:0.62 },
    { lane:0, xF:0.76 },
    { lane:0, xF:0.88 },

    { lane:1, xF:0.15 },
    { lane:1, xF:0.28 },
    { lane:1, xF:0.42 },
    { lane:1, xF:0.56 },
    { lane:1, xF:0.70 },
    { lane:1, xF:0.84 },

    { lane:2, xF:0.20 },
    { lane:2, xF:0.35 },
    { lane:2, xF:0.50 },
    { lane:2, xF:0.65 },
    { lane:2, xF:0.79 },
    { lane:2, xF:0.91 },
  ];

  configs.forEach((cfg, i) => {
    const el = document.createElement('div');
    el.className = 'vehicle';
    el.id = `veh-${i}`;
    const emoji = VEH_EMOJIS[i % VEH_EMOJIS.length];
    el.innerHTML = `${emoji}
      <span class="alert-badge">🚨 ALERT</span>
      <span class="instruction-badge" id="inst-${i}">MOVE</span>`;
    el.style.background = '#1a3a6c';
    el.style.border = '1px solid #2a5a9c';
    el.style.boxShadow = '0 2px 12px rgba(0,80,200,0.4)';

    const wx = WORLD_W * cfg.xF;
    const ty = LC(cfg.lane) - VEH_H / 2;
    el.style.left = wx + 'px';
    el.style.top  = ty + 'px';
    el.style.width  = VEH_W + 'px';
    el.style.height = VEH_H + 'px';

    world.appendChild(el);
    vehicles.push({
      el, i,
      lane: cfg.lane,
      worldX: wx,
      cy: LC(cfg.lane),
      alerted: false,
      moved: false,
    });
  });
}

// ─── INIT AMBULANCE ──────────────────────────────────────────────────
function initAmbulance() {
  const world = document.getElementById('roadWorld');
  const el = document.createElement('div');
  el.className = 'ambulance';
  el.id = 'ambulance';
  el.innerHTML = '🚑';
  el.style.background = 'linear-gradient(135deg,#5a0000,#8a0000)';
  el.style.border = '2px solid #ff3b3b';
  el.style.width  = AMB_W + 'px';
  el.style.height = AMB_H + 'px';
  el.style.left = '30px';
  el.style.top  = (LC(1) - AMB_H / 2) + 'px';
  world.appendChild(el);
  ambulance = { el, worldX: 30, cy: LC(1), lane: 1, speed: 3 };
}

// ─── HELPERS ────────────────────────────────────────────────────────
function distAhead(v) {
  return v.worldX - ambulance.worldX;
}

function addLog(msg, type = 'info') {
  const log = document.getElementById('alertLog');
  const item = document.createElement('div');
  item.className = `alert-item ${type}`;
  const ts = new Date().toLocaleTimeString('en-GB',{hour12:false});
  item.textContent = `[${ts}] ${msg}`;
  log.insertBefore(item, log.firstChild);
  if (log.children.length > 24) log.removeChild(log.lastChild);
}

function updateStats() {
  document.getElementById('statAlerted').textContent = alertedCount;
  document.getElementById('statMoved').textContent   = movedCount;
  document.getElementById('statTime').textContent    = timeSaved + 's';
  document.getElementById('statClear').textContent   = clearPct + '%';
  document.getElementById('speedVal').textContent    = Math.round(ambSpeed * 12) + ' km/h';
  document.getElementById('speedFill').style.width   = Math.min(ambSpeed * 12, 100) + '%';
}

function getInstruction(veh) {
  if (veh.lane === 0) return 'STAY LEFT';
  if (veh.lane === 2) return 'STAY RIGHT';
  return veh.worldX > WORLD_W * 0.5 ? 'PULL LEFT' : 'SLOW DOWN';
}

// ─── CAMERA FOLLOW ───────────────────────────────────────────────────
function updateCamera() {
  const vw = document.getElementById('roadViewport').offsetWidth;
  // Keep ambulance at ~30% of viewport width
  const target = ambulance.worldX - vw * 0.30;
  camX = Math.max(0, Math.min(target, WORLD_W - vw));
  document.getElementById('roadWorld').style.transform = `translateX(${-camX}px)`;
}

// ─── MINIMAP ────────────────────────────────────────────────────────
function updateMinimap() {
  const mm  = document.getElementById('minimap');
  const mma = document.getElementById('mmAmb');
  const mmv = document.getElementById('mmVp');
  mm.style.display = 'block';
  const scale = 240 / WORLD_W;
  const vw = document.getElementById('roadViewport').offsetWidth;
  mma.style.left = (ambulance.worldX * scale - 4) + 'px';
  mmv.style.left  = (camX * scale) + 'px';
  mmv.style.width = (vw * scale) + 'px';
}

// ─── DETECTION & ALERT ───────────────────────────────────────────────
function detectAndAlert() {
  // Detection ring: positioned relative to viewport
  const ring = document.getElementById('detectionRing');
  const ambScreenX = ambulance.worldX - camX + AMB_W / 2;
  ring.style.display = 'block';
  ring.style.width  = (DETECT_THRESH * 2) + 'px';
  ring.style.height = (DETECT_THRESH * 2) + 'px';
  ring.style.left = ambScreenX + 'px';
  ring.style.top  = ambulance.cy + 'px';
  document.getElementById('detectionLabel').textContent = DETECT_THRESH + 'px RADIUS';

  vehicles.forEach((veh, i) => {
    const dist = distAhead(veh);
    if (dist > 0 && dist < DETECT_THRESH && !veh.alerted) {
      veh.alerted = true;
      alertedCount++;
      veh.el.style.background = '#5a2a00';
      veh.el.style.border     = '1px solid #ff8800';
      veh.el.classList.add('alerted');
      const inst = getInstruction(veh);
      document.getElementById(`inst-${i}`).textContent = inst;
      addLog(`V-${i+1} alerted → ${inst}`, 'warn');
      setTimeout(() => moveVehicle(veh, i), 600);
    }
  });
}

// ─── MOVE VEHICLE ────────────────────────────────────────────────────
function moveVehicle(veh, i) {
  if (veh.moved) return;
  veh.moved = true;
  movedCount++;

  let newLane = veh.lane;
  if (veh.lane === 1) {
    newLane = i % 2 === 0 ? 0 : 2;
  }
  // outer lanes: stay but change colour to show they yielded

  veh.lane = newLane;
  const newY = LC(newLane) - VEH_H / 2;
  veh.el.style.top = newY + 'px';
  veh.el.classList.remove('alerted');
  veh.el.classList.add('moving');
  veh.el.style.background = '#0a3a1a';
  veh.el.style.border     = '1px solid #00ff88';
  timeSaved += 2;

  addLog(`V-${i+1} cleared → LANE ${newLane+1}`, 'success');
  setTimeout(() => veh.el.classList.remove('moving'), 2000);

  clearPct = Math.min(Math.round((movedCount / vehicles.length) * 100), 100);
}

// ─── CLEAR PATH GLOW ────────────────────────────────────────────────
function updateClearPath() {
  const cp = document.getElementById('clearPath');
  if (movedCount > 0) {
    const lh = RH / 3;
    cp.style.opacity = '1';
    cp.style.left    = (ambulance.worldX - 10) + 'px';
    cp.style.width   = (WORLD_W - ambulance.worldX + 20) + 'px';
    cp.style.top     = (ambulance.lane * lh) + 'px';
    cp.style.height  = lh + 'px';
  }
}

// ─── TICK ───────────────────────────────────────────────────────────
function tick() {
  if (!simRunning) return;

  // Speed increases as more vehicles clear
  const clearRatio = movedCount / Math.max(vehicles.length, 1);
  ambSpeed = 3 + clearRatio * 5;

  ambulance.worldX += ambSpeed;
  ambulance.el.style.left = ambulance.worldX + 'px';
  ambulance.el.classList.add('active');

  // Progress bar
  document.getElementById('progressBar').style.width =
    Math.min((ambulance.worldX / (WORLD_W - 120)) * 100, 100) + '%';

  detectAndAlert();
  updateClearPath();
  updateCamera();
  updateMinimap();
  updateStats();

  if (ambulance.worldX >= WORLD_W - 120) endSim();
}

// ─── CONTROLS ───────────────────────────────────────────────────────
function startSim() {
  if (simRunning) return;
  setupRoad();
  document.getElementById('overlayMsg').style.display = 'none';
  document.getElementById('btnStart').disabled   = true;
  document.getElementById('btnTraffic').disabled = false;

  document.getElementById('sysDot').style.background  = '#ff3b3b';
  document.getElementById('sysDot').style.boxShadow   = '0 0 10px #ff3b3b';
  document.getElementById('sysLabel').textContent = 'SIMULATION ACTIVE';
  document.getElementById('sysLabel').style.color = '#ff3b3b';

  simRunning = true;
  initVehicles();
  initAmbulance();

  addLog('🚑 Ambulance dispatched — Route tracking active', 'danger');
  addLog('📡 Detection engine initialized', 'info');

  simInterval = setInterval(tick, 80);
}

function addTraffic() {
  const world = document.getElementById('roadWorld');
  const laneIdx = Math.floor(Math.random() * 3);
  const xPos = ambulance.worldX + DETECT_THRESH * 0.6 + Math.random() * (WORLD_W - ambulance.worldX - DETECT_THRESH * 0.6 - 200);
  const i = vehicles.length;

  const el = document.createElement('div');
  el.className = 'vehicle';
  el.id = `veh-${i}`;
  const emoji = VEH_EMOJIS[i % VEH_EMOJIS.length];
  el.innerHTML = `${emoji}
    <span class="alert-badge">🚨 ALERT</span>
    <span class="instruction-badge" id="inst-${i}">MOVE</span>`;
  el.style.background = '#1a3a6c';
  el.style.border     = '1px solid #2a5a9c';
  el.style.boxShadow  = '0 2px 12px rgba(0,80,200,0.4)';
  el.style.width  = VEH_W + 'px';
  el.style.height = VEH_H + 'px';
  el.style.left = xPos + 'px';
  el.style.top  = (LC(laneIdx) - VEH_H / 2) + 'px';

  world.appendChild(el);
  vehicles.push({
    el, i, lane: laneIdx,
    worldX: xPos, cy: LC(laneIdx),
    alerted: false, moved: false,
  });
  addLog(`New vehicle V-${i+1} added to LANE ${laneIdx+1}`, 'warn');
}

function endSim() {
  clearInterval(simInterval);
  simRunning = false;
  clearPct = 100;
  document.getElementById('statClear').textContent = '100%';
  document.getElementById('completeBanner').style.display = 'block';
  document.getElementById('sysDot').style.background  = 'var(--green)';
  document.getElementById('sysDot').style.boxShadow   = '0 0 10px var(--green)';
  document.getElementById('sysLabel').textContent = 'PATH CLEARED';
  document.getElementById('sysLabel').style.color = 'var(--green)';
  document.getElementById('detectionRing').style.display = 'none';
  ambulance.el.classList.remove('active');
  addLog('✅ Ambulance reached hospital — Mission complete!', 'success');
  addLog(`📊 ${movedCount} vehicles coordinated | ${timeSaved}s saved`, 'success');
  updateStats();
}

function resetSim() {
  clearInterval(simInterval);
  simRunning = false;
  alertedCount = movedCount = timeSaved = clearPct = ambSpeed = 0;
  camX = 0;

  vehicles.forEach(v => v.el.remove());
  vehicles = [];
  if (ambulance) { ambulance.el.remove(); ambulance = null; }

  // Clear the world (keep lane lines & overlays)
  const world = document.getElementById('roadWorld');
  world.style.transform = 'translateX(0)';
  world.style.width = '';
  // Remove only vehicle/ambulance children (keep named elements)
  [...world.children].forEach(c => {
    const keep = ['laneLine1','laneLine2','clearPath','detectionRing'];
    if (!keep.includes(c.id)) c.remove();
  });

  document.getElementById('clearPath').style.opacity  = '0';
  document.getElementById('detectionRing').style.display = 'none';
  document.getElementById('progressBar').style.width  = '0%';
  document.getElementById('overlayMsg').style.display = 'flex';
  document.getElementById('completeBanner').style.display = 'none';
  document.getElementById('minimap').style.display    = 'none';
  document.getElementById('btnStart').disabled   = false;
  document.getElementById('btnTraffic').disabled = true;
  document.getElementById('alertLog').innerHTML =
    '<div class="alert-item" style="color:var(--dim)">// Awaiting simulation...</div>';
  document.getElementById('detectionLabel').textContent = 'INACTIVE';
  document.getElementById('sysDot').style.background  = 'var(--green)';
  document.getElementById('sysDot').style.boxShadow   = '0 0 10px var(--green)';
  document.getElementById('sysLabel').textContent = 'SYSTEM READY';
  document.getElementById('sysLabel').style.color = 'var(--green)';
  updateStats();
}

updateStats();
</script>
</body>
</html>
```
