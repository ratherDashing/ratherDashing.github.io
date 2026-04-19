---
layout: post
title: "Draw an Animal & Billboard 200 Studio"
date: 2026-04-19 12:00:00 -0400
categories: Games
---

Two new games! Draw your own animal and release it into the wild, or build your own album and see where it lands on the Billboard 200.

---

## 🎨 Draw an Animal

Draw any animal, choose where it lives and what it eats, then release it into a world with sky, land, and water. Tap the scene to drop food!

<div id="draw-animal-game">

<h1>🎨 Draw an Animal</h1>

<div id="drawPhase" class="phase">
  <canvas id="drawCanvas" class="draw-canvas" width="400" height="400"></canvas>
  <div class="colors" id="colorPicker"></div>
  <div class="controls">
    <button class="size-btn" data-size="4">• Small</button>
    <button class="size-btn active" data-size="10">● Medium</button>
    <button class="size-btn" data-size="22">⬤ Big</button>
    <button class="size-btn" id="eraserBtn">Eraser</button>
  </div>
  <div class="controls">
    <button class="action secondary" id="clearBtn">Clear</button>
    <button class="action secondary hidden" id="cancelDrawBtn">Cancel</button>
    <button class="action primary" id="doneDrawingBtn">I'm Done! →</button>
  </div>
</div>

<div id="classifyPhase" class="phase hidden">
  <h2>Where does your animal live?</h2>
  <div class="habitat-grid" id="habitatGrid">
    <div class="choice" data-value="sky"><div class="emoji">☁️</div><div class="label">Sky</div></div>
    <div class="choice" data-value="land"><div class="emoji">🌳</div><div class="label">Land</div></div>
    <div class="choice" data-value="water"><div class="emoji">🌊</div><div class="label">Water</div></div>
    <div class="choice" data-value="both"><div class="emoji">🏝️</div><div class="label">Land + Water</div></div>
  </div>
  <h2>What does it eat?</h2>
  <div class="diet-grid" id="dietGrid">
    <div class="choice" data-value="meat"><div class="emoji">🍖</div><div class="label">Meat</div></div>
    <div class="choice" data-value="plants"><div class="emoji">🌿</div><div class="label">Plants</div></div>
    <div class="choice" data-value="both"><div class="emoji">🍽️</div><div class="label">Both</div></div>
  </div>
  <div class="controls">
    <button class="action secondary" id="backToDrawBtn">← Back</button>
    <button class="action primary" id="playBtn" disabled>Release! →</button>
  </div>
</div>

<div id="playPhase" class="phase hidden">
  <div class="score-bar">
    <div class="score">🐾 Animals: <span id="animalCount">0</span></div>
    <div class="score">🌟 Yum: <span id="scoreVal">0</span></div>
  </div>
  <canvas id="gameCanvas" class="game-canvas" width="400" height="533"></canvas>
  <div class="hint">Tap the scene to drop food 🍎</div>
  <div class="controls">
    <button class="action primary" id="addAnimalBtn">➕ Add Animal</button>
    <button class="action danger" id="resetZooBtn">🗑️ New Zoo</button>
  </div>
</div>

</div>

<style>
#draw-animal-game * {
  box-sizing: border-box;
  -webkit-user-select: none;
  user-select: none;
  -webkit-tap-highlight-color: transparent;
}
#draw-animal-game {
  font-family: ui-rounded, "SF Pro Rounded", "Arial Rounded MT Bold", system-ui, sans-serif;
  font-weight: 800;
  background:
    radial-gradient(circle at 15% 20%, #fde68a 0%, transparent 40%),
    radial-gradient(circle at 85% 80%, #fca5a5 0%, transparent 40%),
    radial-gradient(circle at 50% 50%, #bbf7d0 0%, transparent 60%),
    linear-gradient(135deg, #fef3c7, #fed7aa);
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 14px 12px 30px;
  color: #451a03;
  border-radius: 12px;
  overflow: hidden;
  margin: 0 0 24px;
}
#draw-animal-game h1 {
  font-size: 30px;
  color: #7c2d12;
  margin-bottom: 10px;
  letter-spacing: -0.5px;
  text-shadow: 3px 3px 0 #fde047;
}
#draw-animal-game h2 {
  font-size: 19px;
  color: #7c2d12;
  margin: 14px 0 8px;
  text-align: center;
}
#draw-animal-game .phase { width: 100%; max-width: 440px; display: flex; flex-direction: column; align-items: center; }
#draw-animal-game canvas {
  background: white;
  border: 5px solid #7c2d12;
  border-radius: 22px;
  box-shadow: 0 6px 0 #7c2d12, 0 8px 20px rgba(0,0,0,0.15);
  touch-action: none;
  display: block;
}
#draw-animal-game .draw-canvas { width: 100%; max-width: 400px; aspect-ratio: 1; }
#draw-animal-game .controls { display: flex; gap: 8px; flex-wrap: wrap; justify-content: center; margin: 10px 0; }
#draw-animal-game .colors { display: flex; gap: 7px; flex-wrap: wrap; justify-content: center; margin: 12px 0 6px; max-width: 380px; }
#draw-animal-game .color-btn {
  width: 34px; height: 34px; border-radius: 50%;
  border: 3px solid white;
  box-shadow: 0 2px 0 #7c2d12, 0 3px 6px rgba(0,0,0,0.2);
  cursor: pointer; transition: transform 0.1s;
}
#draw-animal-game .color-btn.active { border-color: #7c2d12; transform: scale(1.2) translateY(-2px); }
#draw-animal-game .size-btn {
  padding: 8px 14px; border-radius: 12px; background: white;
  border: 3px solid #7c2d12; cursor: pointer;
  font-weight: 800; color: #7c2d12; font-size: 14px;
  box-shadow: 0 3px 0 #7c2d12; font-family: inherit;
}
#draw-animal-game .size-btn.active { background: #7c2d12; color: #fde047; transform: translateY(2px); box-shadow: 0 1px 0 #7c2d12; }
#draw-animal-game button.action {
  padding: 14px 22px; font-size: 17px; font-weight: 800;
  border: 3px solid #7c2d12; border-radius: 16px; cursor: pointer;
  box-shadow: 0 4px 0 #7c2d12; font-family: inherit;
  transition: transform 0.1s, box-shadow 0.1s;
}
#draw-animal-game button.action.primary { background: #16a34a; color: white; }
#draw-animal-game button.action.secondary { background: white; color: #7c2d12; }
#draw-animal-game button.action.fun { background: #f59e0b; color: white; }
#draw-animal-game button.action.danger { background: #dc2626; color: white; }
#draw-animal-game button.action:active { transform: translateY(4px); box-shadow: 0 0 0 #7c2d12; }
#draw-animal-game button.action:disabled { opacity: 0.4; cursor: not-allowed; }
#draw-animal-game .habitat-grid {
  display: grid; grid-template-columns: repeat(2, 1fr); gap: 10px;
  width: 100%; max-width: 400px; margin-bottom: 8px;
}
#draw-animal-game .diet-grid {
  display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px;
  width: 100%; max-width: 400px; margin-bottom: 8px;
}
#draw-animal-game .choice {
  aspect-ratio: 1.3; border: 4px solid #7c2d12; border-radius: 18px;
  background: white; display: flex; flex-direction: column;
  align-items: center; justify-content: center; gap: 4px;
  cursor: pointer; box-shadow: 0 4px 0 #7c2d12; padding: 8px;
  transition: transform 0.1s, box-shadow 0.1s, background 0.2s;
}
#draw-animal-game .diet-grid .choice { aspect-ratio: 1; }
#draw-animal-game .choice .emoji { font-size: 36px; line-height: 1; }
#draw-animal-game .choice .label { font-weight: 800; color: #7c2d12; font-size: 13px; }
#draw-animal-game .choice.selected {
  background: #fde047; transform: translateY(4px) scale(1.02);
  box-shadow: 0 0 0 #7c2d12, 0 0 0 6px #16a34a;
}
#draw-animal-game .choice:active { transform: translateY(2px); }
#draw-animal-game .game-canvas { width: 100%; max-width: 400px; aspect-ratio: 3 / 4; }
#draw-animal-game .score-bar { display: flex; gap: 8px; flex-wrap: wrap; justify-content: center; margin: 10px 0; }
#draw-animal-game .score {
  font-size: 18px; font-weight: 800; color: #7c2d12;
  background: #fde047; padding: 8px 14px;
  border: 3px solid #7c2d12; border-radius: 20px;
  box-shadow: 0 3px 0 #7c2d12;
}
#draw-animal-game .hint {
  font-size: 13px; color: #7c2d12; margin-top: 6px;
  background: rgba(255,255,255,0.75); padding: 5px 12px; border-radius: 12px;
}
#draw-animal-game .hidden { display: none !important; }
@keyframes da-pop {
  0% { transform: scale(0); }
  60% { transform: scale(1.2); }
  100% { transform: scale(1); }
}
#draw-animal-game .pop-in { animation: da-pop 0.4s ease-out; }
</style>

<script>
// {% raw %}
(function(){
const drawCanvas = document.getElementById('drawCanvas');
const drawCtx = drawCanvas.getContext('2d');
drawCtx.lineCap = 'round';
drawCtx.lineJoin = 'round';

let drawing = false;
let currentColor = '#1f2937';
let brushSize = 10;
let isEraser = false;
let lastX = 0, lastY = 0;

const colors = ['#1f2937', '#ef4444', '#f97316', '#f59e0b', '#eab308', '#22c55e', '#14b8a6', '#3b82f6', '#8b5cf6', '#ec4899', '#8b4513', '#f9a8d4'];
const colorPicker = document.getElementById('colorPicker');
colors.forEach((c, i) => {
  const btn = document.createElement('div');
  btn.className = 'color-btn' + (i === 0 ? ' active' : '');
  btn.style.background = c;
  btn.addEventListener('click', () => {
    document.querySelectorAll('.color-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    currentColor = c;
    isEraser = false;
    document.getElementById('eraserBtn').classList.remove('active');
  });
  colorPicker.appendChild(btn);
});

document.querySelectorAll('.size-btn[data-size]').forEach(btn => {
  btn.addEventListener('click', () => {
    document.querySelectorAll('.size-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    brushSize = parseInt(btn.dataset.size);
    isEraser = false;
  });
});

document.getElementById('eraserBtn').addEventListener('click', (e) => {
  document.querySelectorAll('.size-btn').forEach(b => b.classList.remove('active'));
  e.currentTarget.classList.add('active');
  isEraser = true;
});

function getPos(e, canvas) {
  const rect = canvas.getBoundingClientRect();
  const sx = canvas.width / rect.width;
  const sy = canvas.height / rect.height;
  const pt = e.touches ? e.touches[0] : e;
  return { x: (pt.clientX - rect.left) * sx, y: (pt.clientY - rect.top) * sy };
}

function startDraw(e) {
  e.preventDefault();
  drawing = true;
  const p = getPos(e, drawCanvas);
  lastX = p.x; lastY = p.y;
  drawCtx.globalCompositeOperation = isEraser ? 'destination-out' : 'source-over';
  drawCtx.fillStyle = currentColor;
  drawCtx.beginPath();
  drawCtx.arc(p.x, p.y, brushSize / 2, 0, Math.PI * 2);
  drawCtx.fill();
}
function moveDraw(e) {
  if (!drawing) return;
  e.preventDefault();
  const p = getPos(e, drawCanvas);
  drawCtx.globalCompositeOperation = isEraser ? 'destination-out' : 'source-over';
  drawCtx.strokeStyle = currentColor;
  drawCtx.lineWidth = brushSize;
  drawCtx.beginPath();
  drawCtx.moveTo(lastX, lastY);
  drawCtx.lineTo(p.x, p.y);
  drawCtx.stroke();
  lastX = p.x; lastY = p.y;
}
function endDraw() { drawing = false; }

drawCanvas.addEventListener('mousedown', startDraw);
drawCanvas.addEventListener('mousemove', moveDraw);
window.addEventListener('mouseup', endDraw);
drawCanvas.addEventListener('touchstart', startDraw, { passive: false });
drawCanvas.addEventListener('touchmove', moveDraw, { passive: false });
drawCanvas.addEventListener('touchend', endDraw);

document.getElementById('clearBtn').addEventListener('click', () => {
  drawCtx.clearRect(0, 0, drawCanvas.width, drawCanvas.height);
});

const drawPhase = document.getElementById('drawPhase');
const classifyPhase = document.getElementById('classifyPhase');
const playPhase = document.getElementById('playPhase');

let habitat = null, diet = null;
let pendingImg = null;

function goToDraw(showCancel) {
  playPhase.classList.add('hidden');
  classifyPhase.classList.add('hidden');
  drawPhase.classList.remove('hidden');
  drawCtx.clearRect(0, 0, drawCanvas.width, drawCanvas.height);
  document.getElementById('cancelDrawBtn').classList.toggle('hidden', !showCancel);
}

function goToClassify() {
  drawPhase.classList.add('hidden');
  classifyPhase.classList.remove('hidden');
  classifyPhase.classList.add('pop-in');
  habitat = null; diet = null;
  document.querySelectorAll('.choice').forEach(c => c.classList.remove('selected'));
  document.getElementById('playBtn').disabled = true;
  setTimeout(() => classifyPhase.classList.remove('pop-in'), 400);
}

function goToPlay() {
  classifyPhase.classList.add('hidden');
  drawPhase.classList.add('hidden');
  playPhase.classList.remove('hidden');
}

document.getElementById('doneDrawingBtn').addEventListener('click', () => {
  const img = new Image();
  img.onload = () => { pendingImg = img; goToClassify(); };
  img.src = drawCanvas.toDataURL('image/png');
});

document.getElementById('backToDrawBtn').addEventListener('click', () => {
  classifyPhase.classList.add('hidden');
  drawPhase.classList.remove('hidden');
});

document.getElementById('cancelDrawBtn').addEventListener('click', () => {
  drawPhase.classList.add('hidden');
  playPhase.classList.remove('hidden');
  if (!daAnimId && daAnimals.length > 0) daGameLoop();
});

function updatePlayBtn() {
  document.getElementById('playBtn').disabled = !(habitat && diet);
}

document.querySelectorAll('#habitatGrid .choice').forEach(c => {
  c.addEventListener('click', () => {
    document.querySelectorAll('#habitatGrid .choice').forEach(x => x.classList.remove('selected'));
    c.classList.add('selected');
    habitat = c.dataset.value;
    updatePlayBtn();
  });
});
document.querySelectorAll('#dietGrid .choice').forEach(c => {
  c.addEventListener('click', () => {
    document.querySelectorAll('#dietGrid .choice').forEach(x => x.classList.remove('selected'));
    c.classList.add('selected');
    diet = c.dataset.value;
    updatePlayBtn();
  });
});

document.getElementById('playBtn').addEventListener('click', () => {
  daAnimals.push(createAnimal(pendingImg, habitat, diet));
  document.getElementById('animalCount').textContent = daAnimals.length;
  pendingImg = null;
  goToPlay();
  if (!daAnimId) daGameLoop();
  if (!daSpawnTimer) daSpawnTimer = setInterval(autoSpawnFood, 900);
});

document.getElementById('addAnimalBtn').addEventListener('click', () => { goToDraw(true); });

document.getElementById('resetZooBtn').addEventListener('click', () => {
  daStopGame();
  daAnimals.length = 0;
  daFoods.length = 0;
  daParticles.length = 0;
  daScore = 0;
  document.getElementById('scoreVal').textContent = '0';
  document.getElementById('animalCount').textContent = '0';
  goToDraw(false);
});

const gameCanvas = document.getElementById('gameCanvas');
const gameCtx = gameCanvas.getContext('2d');
const GW = gameCanvas.width, GH = gameCanvas.height;

const SKY_TOP = 20, SKY_BOT = GH * 0.38;
const LAND_TOP = GH * 0.38, LAND_BOT = GH * 0.68;
const WATER_TOP = GH * 0.68, WATER_BOT = GH - 20;

const daAnimals = [];
const daFoods = [];
let daParticles = [];
let daScore = 0;
let daAnimId = null;
let daSpawnTimer = null;
let daNextId = 0;

const FOOD_TABLE = {
  sky: { meat: ['🦋', '🐛', '🐝', '🪲'], plant: ['🌾', '🌰', '🌱'] },
  land: { meat: ['🍖', '🥩', '🍗', '🦴'], plant: ['🍎', '🥕', '🥬', '🍇', '🌽', '🍓', '🍌', '🍒'] },
  water: { meat: ['🐟', '🦐', '🦑', '🪼'], plant: ['🌿', '🪸', '🍃'] }
};

function getZoneBounds(zone) {
  if (zone === 'sky') return { top: SKY_TOP, bot: SKY_BOT };
  if (zone === 'land') return { top: LAND_TOP, bot: LAND_BOT };
  return { top: WATER_TOP, bot: WATER_BOT };
}
function getValidZones(h) {
  if (h === 'sky') return ['sky'];
  if (h === 'land') return ['land'];
  if (h === 'water') return ['water'];
  return ['land', 'water'];
}
function randomPosInZones(zones) {
  const zone = zones[Math.floor(Math.random() * zones.length)];
  const b = getZoneBounds(zone);
  return { x: 50 + Math.random() * (GW - 100), y: b.top + 15 + Math.random() * (b.bot - b.top - 30) };
}
function createAnimal(img, habitat, diet) {
  const zones = getValidZones(habitat);
  const p = randomPosInZones(zones);
  return { id: daNextId++, img, habitat, diet, x: p.x, y: p.y, vx: 0, vy: 0, facing: 1, wanderAngle: Math.random() * Math.PI * 2, wanderTimer: 0, size: 60 + Math.random() * 20, bobPhase: Math.random() * Math.PI * 2 };
}
function canEat(a, f) {
  if (a.diet === 'meat' && f.type !== 'meat') return false;
  if (a.diet === 'plants' && f.type !== 'plant') return false;
  return getValidZones(a.habitat).includes(f.zone);
}
function pickFoodType(a) {
  if (a.diet === 'meat') return 'meat';
  if (a.diet === 'plants') return 'plant';
  return Math.random() < 0.5 ? 'meat' : 'plant';
}
function autoSpawnFood() {
  if (daAnimals.length === 0 || daFoods.length > 20) return;
  const a = daAnimals[Math.floor(Math.random() * daAnimals.length)];
  const zones = getValidZones(a.habitat);
  const zone = zones[Math.floor(Math.random() * zones.length)];
  const type = pickFoodType(a);
  const p = randomPosInZones([zone]);
  addFood(p.x, p.y, zone, type);
}
function addFood(x, y, zone, type) {
  const pool = FOOD_TABLE[zone][type];
  const emoji = pool[Math.floor(Math.random() * pool.length)];
  daFoods.push({ x, y, zone, type, emoji, bob: Math.random() * Math.PI * 2, scale: 0 });
}
function zoneAt(y) {
  if (y < SKY_BOT) return 'sky';
  if (y < LAND_BOT) return 'land';
  return 'water';
}

let daLastTap = 0;
function handleGameTap(e) {
  e.preventDefault();
  const now = Date.now();
  if (now - daLastTap < 80) return;
  daLastTap = now;
  if (daAnimals.length === 0) return;
  const p = getPos(e, gameCanvas);
  const zone = zoneAt(p.y);
  const eligible = daAnimals.filter(a => getValidZones(a.habitat).includes(zone));
  if (eligible.length === 0) return;
  const n = 2 + Math.floor(Math.random() * 2);
  for (let i = 0; i < n; i++) {
    const a = eligible[Math.floor(Math.random() * eligible.length)];
    const type = pickFoodType(a);
    const b = getZoneBounds(zone);
    const fx = Math.max(30, Math.min(GW - 30, p.x + (Math.random() - 0.5) * 50));
    const fy = Math.max(b.top + 10, Math.min(b.bot - 10, p.y + (Math.random() - 0.5) * 30));
    addFood(fx, fy, zone, type);
  }
}
gameCanvas.addEventListener('click', handleGameTap);
gameCanvas.addEventListener('touchstart', handleGameTap, { passive: false });

function updateAnimal(a) {
  let nearest = null, nearestD2 = Infinity;
  for (const f of daFoods) {
    if (!canEat(a, f)) continue;
    const dx = f.x - a.x, dy = f.y - a.y;
    const d2 = dx * dx + dy * dy;
    if (d2 < nearestD2) { nearestD2 = d2; nearest = f; }
  }
  const nearestD = Math.sqrt(nearestD2);
  let tx, ty;
  if (nearest) {
    tx = nearest.x; ty = nearest.y;
  } else {
    a.wanderTimer--;
    if (a.wanderTimer <= 0) { a.wanderAngle += (Math.random() - 0.5) * 1.8; a.wanderTimer = 40 + Math.random() * 40; }
    tx = a.x + Math.cos(a.wanderAngle) * 80;
    ty = a.y + Math.sin(a.wanderAngle) * 50;
  }
  tx = Math.max(35, Math.min(GW - 35, tx));
  const zones = getValidZones(a.habitat);
  const bounds = zones.map(getZoneBounds);
  const yMin = Math.min(...bounds.map(b => b.top + 15));
  const yMax = Math.max(...bounds.map(b => b.bot - 15));
  ty = Math.max(yMin, Math.min(yMax, ty));
  const dx = tx - a.x, dy = ty - a.y;
  const d = Math.sqrt(dx * dx + dy * dy) || 1;
  const speed = nearest ? 2.2 : 0.9;
  a.vx += ((dx / d) * speed - a.vx) * 0.12;
  a.vy += ((dy / d) * speed - a.vy) * 0.12;
  a.x += a.vx; a.y += a.vy;
  a.x = Math.max(35, Math.min(GW - 35, a.x));
  a.y = Math.max(yMin, Math.min(yMax, a.y));
  if (Math.abs(a.vx) > 0.25) a.facing = a.vx < 0 ? -1 : 1;
  if (nearest && nearestD < 34) {
    const idx = daFoods.indexOf(nearest);
    if (idx >= 0) {
      daFoods.splice(idx, 1);
      daScore++;
      document.getElementById('scoreVal').textContent = daScore;
      for (let i = 0; i < 10; i++) {
        const ang = (i / 10) * Math.PI * 2;
        daParticles.push({ x: nearest.x, y: nearest.y, vx: Math.cos(ang) * 3, vy: Math.sin(ang) * 3 - 1, life: 28, maxLife: 28, emoji: ['✨', '⭐', '💛'][Math.floor(Math.random() * 3)] });
      }
    }
  }
}

function drawBackground(t) {
  const sky = gameCtx.createLinearGradient(0, 0, 0, SKY_BOT);
  sky.addColorStop(0, '#7dd3fc'); sky.addColorStop(1, '#bae6fd');
  gameCtx.fillStyle = sky; gameCtx.fillRect(0, 0, GW, SKY_BOT);
  gameCtx.fillStyle = '#fde047'; gameCtx.beginPath(); gameCtx.arc(GW - 50, 50, 24, 0, Math.PI * 2); gameCtx.fill();
  gameCtx.strokeStyle = '#f59e0b'; gameCtx.lineWidth = 3; gameCtx.stroke();
  gameCtx.fillStyle = 'rgba(255,255,255,0.9)';
  drawCloud(70 + Math.sin(t * 0.5) * 12, 60, 1);
  drawCloud(230 + Math.cos(t * 0.3) * 15, 95, 0.75);
  drawCloud(140 + Math.sin(t * 0.4 + 1) * 10, 135, 0.6);
  const land = gameCtx.createLinearGradient(0, LAND_TOP, 0, LAND_BOT);
  land.addColorStop(0, '#86efac'); land.addColorStop(0.6, '#4ade80'); land.addColorStop(1, '#92400e');
  gameCtx.fillStyle = land; gameCtx.fillRect(0, LAND_TOP, GW, LAND_BOT - LAND_TOP);
  gameCtx.fillStyle = '#166534';
  for (let i = 0; i < 14; i++) { const gx = (i * 30 + 8) % GW; const gy = LAND_TOP + 2 + (i % 3); gameCtx.beginPath(); gameCtx.moveTo(gx, gy + 7); gameCtx.lineTo(gx + 3, gy - 6); gameCtx.lineTo(gx + 6, gy + 7); gameCtx.fill(); }
  const water = gameCtx.createLinearGradient(0, WATER_TOP, 0, WATER_BOT);
  water.addColorStop(0, '#38bdf8'); water.addColorStop(0.5, '#0ea5e9'); water.addColorStop(1, '#1e3a8a');
  gameCtx.fillStyle = water; gameCtx.fillRect(0, WATER_TOP, GW, GH - WATER_TOP);
  gameCtx.strokeStyle = 'rgba(255,255,255,0.8)'; gameCtx.lineWidth = 3; gameCtx.beginPath();
  for (let x = 0; x <= GW; x += 6) { const y = WATER_TOP + Math.sin((x + t * 30) / 26) * 4; if (x === 0) gameCtx.moveTo(x, y); else gameCtx.lineTo(x, y); }
  gameCtx.stroke();
  gameCtx.fillStyle = 'rgba(255,255,255,0.35)';
  for (let i = 0; i < 6; i++) { const bx = (i * 63 + Math.sin(t + i) * 14) % GW; const by = GH - 10 - ((t * 28 + i * 60) % (WATER_BOT - WATER_TOP - 10)); gameCtx.beginPath(); gameCtx.arc(bx, by, 3 + (i % 3) * 2, 0, Math.PI * 2); gameCtx.fill(); }
  gameCtx.fillStyle = '#166534';
  for (let i = 0; i < 4; i++) { const sx = i * 100 + 30 + Math.sin(t * 1.5 + i) * 3; gameCtx.fillRect(sx, GH - 32 - i * 4, 6, 32 + i * 4); }
}

function drawCloud(x, y, scale) {
  gameCtx.beginPath();
  gameCtx.arc(x, y, 14 * scale, 0, Math.PI * 2);
  gameCtx.arc(x + 16 * scale, y - 5 * scale, 16 * scale, 0, Math.PI * 2);
  gameCtx.arc(x + 32 * scale, y, 13 * scale, 0, Math.PI * 2);
  gameCtx.arc(x + 18 * scale, y + 7 * scale, 13 * scale, 0, Math.PI * 2);
  gameCtx.fill();
}

function daGameLoop() {
  const t = Date.now() / 1000;
  gameCtx.clearRect(0, 0, GW, GH);
  drawBackground(t);
  for (const f of daFoods) if (f.scale < 1) f.scale += 0.1;
  daFoods.forEach(f => {
    const yb = f.y + Math.sin(t * 2 + f.bob) * 3;
    gameCtx.save(); gameCtx.translate(f.x, yb); gameCtx.scale(f.scale, f.scale);
    gameCtx.font = '32px sans-serif'; gameCtx.textAlign = 'center'; gameCtx.textBaseline = 'middle';
    gameCtx.fillText(f.emoji, 0, 0); gameCtx.restore();
  });
  for (const a of daAnimals) updateAnimal(a);
  const sorted = daAnimals.slice().sort((a, b) => a.y - b.y);
  for (const a of sorted) {
    const bob = Math.sin(t * 4 + a.bobPhase) * 2;
    gameCtx.save(); gameCtx.translate(a.x, a.y + bob); gameCtx.scale(a.facing, 1);
    gameCtx.shadowColor = 'rgba(0,0,0,0.35)'; gameCtx.shadowBlur = 10; gameCtx.shadowOffsetY = 4;
    gameCtx.drawImage(a.img, -a.size / 2, -a.size / 2, a.size, a.size); gameCtx.restore();
  }
  daParticles = daParticles.filter(p => {
    p.x += p.vx; p.y += p.vy; p.vy += 0.15; p.life--;
    gameCtx.save(); gameCtx.globalAlpha = Math.max(0, p.life / p.maxLife);
    gameCtx.font = '18px sans-serif'; gameCtx.textAlign = 'center'; gameCtx.textBaseline = 'middle';
    gameCtx.fillText(p.emoji, p.x, p.y); gameCtx.restore();
    return p.life > 0;
  });
  daAnimId = requestAnimationFrame(daGameLoop);
}

function daStopGame() {
  if (daAnimId) cancelAnimationFrame(daAnimId);
  if (daSpawnTimer) clearInterval(daSpawnTimer);
  daAnimId = null; daSpawnTimer = null;
}
})();
// {% endraw %}
</script>

---

## 🎵 Billboard 200 Studio

Name your album, add your tracks (mark the explicit ones!), then publish it to see where it debuts on the Billboard 200!

<div id="gr" style="background:#06061a;min-height:600px;color:#7070aa;font-family:'Courier New',Courier,monospace"></div>

<style>
#gr *{box-sizing:border-box}
#gr input{background:rgba(0,200,255,0.06);border:1px solid #00ccff;color:#fff;font-family:'Courier New',Courier,monospace;font-size:15px;padding:12px 16px;border-radius:3px;outline:none;width:100%}
#gr input:focus{border-color:#ff10f0}
#gr button{cursor:pointer;font-family:'Courier New',Courier,monospace;border-radius:3px;transition:background 0.15s}
#gr .bp{background:transparent;border:1px solid #ff10f0;color:#ff10f0;font-size:11px;font-weight:bold;letter-spacing:3px;padding:11px 22px;text-transform:uppercase}
#gr .bp:hover{background:rgba(255,16,240,0.1)}
#gr .bc{background:transparent;border:1px solid #00ffff;color:#00ffff;font-size:11px;font-weight:bold;letter-spacing:3px;padding:11px 22px;text-transform:uppercase}
#gr .bc:hover{background:rgba(0,255,255,0.1)}
#gr .bg{background:transparent;border:1px solid #39ff14;color:#39ff14;font-size:11px;font-weight:bold;letter-spacing:3px;padding:11px 22px;text-transform:uppercase}
#gr .bg:hover{background:rgba(57,255,20,0.1)}
#gr .by{background:transparent;border:1px solid #ffe600;color:#ffe600;font-size:11px;font-weight:bold;letter-spacing:3px;padding:11px 22px;text-transform:uppercase}
#gr .by:hover{background:rgba(255,230,0,0.1)}
#gr .bpu{background:transparent;border:1px solid #bf5fff;color:#bf5fff;font-size:11px;font-weight:bold;letter-spacing:3px;padding:11px 22px;text-transform:uppercase}
#gr .bpu:hover{background:rgba(191,95,255,0.1)}
#gr .scr{display:flex;flex-direction:column;align-items:center;justify-content:center;min-height:540px;text-align:center;padding:24px 20px}
#gr .sc{animation:bbFadeIn 0.3s}
#gr .crow{display:flex;align-items:center;padding:8px;border-radius:4px;cursor:pointer;border:1px solid transparent;gap:10px;margin-bottom:2px}
#gr .crow:hover{background:rgba(255,255,255,0.04);border-color:rgba(255,255,255,0.06)}
#gr .si{display:flex;align-items:center;padding:9px 0;border-bottom:1px solid rgba(255,255,255,0.05);gap:10px}
#gr .eb{font-size:10px;font-weight:bold;padding:2px 5px;background:rgba(255,16,240,0.15);border:1px solid #ff10f0;color:#ff10f0;border-radius:2px;letter-spacing:1px;white-space:nowrap}
#gr .yb{font-size:9px;font-weight:bold;padding:2px 6px;border-radius:2px;letter-spacing:2px;vertical-align:middle;margin-left:5px}
#gr .athumb{width:44px;height:44px;object-fit:cover;border-radius:4px;flex-shrink:0;display:block}
#gr .athumb-ph{width:44px;height:44px;border-radius:4px;flex-shrink:0;background:#080818;border:1px solid rgba(255,255,255,0.06)}
@keyframes bbBarDance{0%{height:4px}100%{height:36px}}
@keyframes bbFadeIn{from{opacity:0;transform:translateY(8px)}to{opacity:1;transform:translateY(0)}}
@keyframes bbSpin{to{transform:rotate(360deg)}}
</style>

<script>
// {% raw %}
(function(){
const BB=[
{p:1,t:"ARIRANG",a:"BTS"},{p:2,t:"Bully",a:"Ye"},{p:3,t:"Hades",a:"Melanie Martinez"},
{p:4,t:"I'm The Problem",a:"Morgan Wallen"},{p:5,t:"ADL",a:"Yeat"},{p:6,t:"The Way I Am",a:"Luke Combs"},
{p:7,t:"The Art Of Loving",a:"Olivia Dean"},{p:8,t:"Octane",a:"Don Toliver"},
{p:9,t:"Debi Tirar Mas Fotos",a:"Bad Bunny"},{p:10,t:"Kiss All The Time. Disco, Occasionally.",a:"Harry Styles"},
{p:11,t:"This Music May Contain Hope.",a:"RAYE"},{p:12,t:"The Romantic",a:"Bruno Mars"},
{p:13,t:"The Life Of A Showgirl",a:"Taylor Swift"},{p:14,t:"One Thing At A Time",a:"Morgan Wallen"},
{p:15,t:"SOS",a:"SZA"},{p:16,t:"KPop Demon Hunters",a:"Soundtrack"},{p:17,t:"Stick Season",a:"Noah Kahan"},
{p:18,t:"You'll Be Alright, Kid",a:"Alex Warren"},{p:19,t:"Dangerous: The Double Album",a:"Morgan Wallen"},
{p:20,t:"DINASTIA",a:"Peso Pluma"},{p:21,t:"The Diamond Collection",a:"Post Malone"},
{p:22,t:"Wor$t Girl In America",a:"Slayyyter"},{p:23,t:"So Close To What",a:"Tate McRae"},
{p:24,t:"Un Verano Sin Ti",a:"Bad Bunny"},{p:25,t:"Take Care",a:"Drake"},
{p:26,t:"Number Ones",a:"Michael Jackson"},{p:27,t:"Rumours",a:"Fleetwood Mac"},
{p:28,t:"30 Number One Hits",a:"Jason Aldean"},{p:29,t:"Short n Sweet",a:"Sabrina Carpenter"},
{p:30,t:"Hungover",a:"Ella Langley"},{p:31,t:"Engines Of Demolition",a:"Black Label Society"},
{p:32,t:"With Heaven On Top",a:"Zach Bryan"},{p:33,t:"Cloud 9",a:"Megan Moroney"},
{p:34,t:"Man's Best Friend",a:"Sabrina Carpenter"},{p:35,t:"The Tortured Poets Department",a:"Taylor Swift"},
{p:36,t:"Am I The Drama",a:"Cardi B"},{p:37,t:"Greatest Hits",a:"Queen"},
{p:38,t:"Blonde",a:"Frank Ocean"},{p:39,t:"Views",a:"Drake"},{p:40,t:"I Barely Know Her",a:"sombr"},
{p:41,t:"Tour Collection",a:"Ed Sheeran"},{p:42,t:"Mutiny After Midnight",a:"Johnny Blue Skies"},
{p:43,t:"Diamonds",a:"Elton John"},{p:44,t:"The Fall-Off",a:"J. Cole"},
{p:45,t:"Hit Me Hard And Soft",a:"Billie Eilish"},{p:46,t:"Whatever's Clever",a:"Charlie Puth"},
{p:47,t:"Chronicle Greatest Hits",a:"Creedence Clearwater Revival"},
{p:48,t:"Graduation",a:"Kanye West"},{p:49,t:"Ctrl",a:"SZA"},{p:50,t:"Nevermind",a:"Nirvana"},
{p:51,t:"Don't Mind If I Do",a:"Riley Green"},{p:52,t:"Animal",a:"Kesha"},
{p:53,t:"Some Sexy Songs 4 U",a:"PARTYNEXTDOOR Drake"},{p:54,t:"Fancy Some More",a:"PinkPantheress"},
{p:55,t:"The Best Of Nickelback Volume 1",a:"Nickelback"},{p:56,t:"Sour",a:"Olivia Rodrigo"},
{p:57,t:"ANTI",a:"Rihanna"},{p:58,t:"This One's For You",a:"Luke Combs"},
{p:59,t:"Curtain Call The Hits",a:"Eminem"},{p:60,t:"Sweet Boy",a:"Malcolm Todd"},
{p:61,t:"Certified Lover Boy",a:"Drake"},{p:62,t:"Teenage Dream",a:"Katy Perry"},
{p:63,t:"Where I've Been Isn't Where I'm Going",a:"Shaboozey"},{p:64,t:"Greatest Hits",a:"Pitbull"},
{p:65,t:"The Rise And Fall Of A Midwest Princess",a:"Chappell Roan"},
{p:66,t:"American Heartbreak",a:"Zach Bryan"},{p:67,t:"good kid maad city",a:"Kendrick Lamar"},
{p:68,t:"The Fame",a:"Lady Gaga"},{p:69,t:"The Collection",a:"OneRepublic"},
{p:70,t:"GNX",a:"Kendrick Lamar"},{p:71,t:"Channel Orange",a:"Frank Ocean"},
{p:72,t:"Good Girl Gone Bad",a:"Rihanna"},{p:73,t:"Folklore",a:"Taylor Swift"},
{p:74,t:"Lover",a:"Taylor Swift"},{p:75,t:"Journey's Greatest Hits",a:"Journey"},
{p:76,t:"Legend The Best Of Bob Marley",a:"Bob Marley"},{p:77,t:"Guts",a:"Olivia Rodrigo"},
{p:78,t:"Scorpion",a:"Drake"},{p:79,t:"Slime Cry",a:"YoungBoy Never Broke Again"},
{p:80,t:"If I Know Me",a:"Morgan Wallen"},{p:81,t:"Doo-Wops and Hooligans",a:"Bruno Mars"},
{p:82,t:"Zach Bryan",a:"Zach Bryan"},{p:83,t:"Greatest Hits",a:"Tom Petty"},
{p:84,t:"Currents",a:"Tame Impala"},{p:85,t:"Meteora",a:"Linkin Park"},
{p:86,t:"Swag",a:"Justin Bieber"},{p:87,t:"111XPANTIA",a:"Fuerza Regida"},
{p:88,t:"Sublime",a:"Sublime"},{p:89,t:"35 Biggest Hits",a:"Toby Keith"},
{p:90,t:"Don't Forget About Me Demos",a:"Dominic Fike"},
{p:91,t:"Thank Me Later",a:"Drake"},{p:92,t:"For All The Dogs",a:"Drake"},
{p:93,t:"My Turn",a:"Lil Baby"},{p:94,t:"50 Number Ones",a:"George Strait"},
{p:95,t:"Midnights",a:"Taylor Swift"},{p:96,t:"Por Si Manana No Estoy",a:"Omar Courtz"},
{p:97,t:"Hybrid Theory",a:"Linkin Park"},{p:98,t:"Deadbeat",a:"Tame Impala"},
{p:99,t:"Greatest Hits Vol I and II",a:"Billy Joel"},{p:100,t:"The Secret Of Us",a:"Gracie Abrams"}
];

const TR={
"SOS|SZA":["SOS","!Kill Bill","Seek & Destroy","Low","Love Language","Blind","Gone Girl","Used","!Smoking on My Ex Pack","Forgiveless","Special","Too Late","Far","F2F","Nobody Gets Me","Conceited","!Shirt","!I Hate U","Good Days"],
"Stick Season|Noah Kahan":["Northern Attitude","Stick Season","Dial Drunk","Homesick","Everywhere, Everything","Paul Revere","All My Love","Call Your Mom","She Calls Me Back","Orange Juice","Strawberry Wine","Growing Sideways","Anyway"],
"Dangerous: The Double Album|Morgan Wallen":["Sand In My Boots","More Than My Hometown","Wasted On You","7 Summers","Somebody's Problem","Livin' The Dream","Don't Think Jesus","Covered In Country","Quittin' Season","Whatcha Think of Country","This Bar","Talkin' Tennessee","Warning","Still Goin' Down","Half Of Me","Neon Eyes","Rednecks","Chasin' You","Whiskey Friends","98 Braves"],
"One Thing At A Time|Morgan Wallen":["Last Night","Cowgirls","98 Braves","Everything I Love","Tennessee Fan","Thought About You","Just Look Up","Country Again","Down Home","High Note","You Proof","More Surprised Than Me","Smile"],
"Un Verano Sin Ti|Bad Bunny":["El Apagon","!Titi Me Pregunto","Ojitos Lindos","Party","Moscow Mule","Un Coco","Neverita","!Me Porto Bonito","Efecto","La Corriente","Los Unicos","Dos Mil 16","Andrea","Aguacero","Caro","Agosto","Callaita"],
"Take Care|Drake":["Over My Dead Body","Shot for Me","Headlines","Crew Love","Take Care","Marvins Room","Buried Alive","Underground Kings","We'll Be Fine","Practice","Look What You've Done","Doing It Wrong","The Real Her","Make Me Proud","Lord Knows","Cameras / Good Ones Go Interlude","Hate Sleeping Alone","Furthest Thing"],
"Number Ones|Michael Jackson":["Don't Stop 'Til You Get Enough","Rock with You","Billie Jean","Beat It","Thriller","Bad","The Way You Make Me Feel","Man in the Mirror","Smooth Criminal","Black or White","You Are Not Alone","Earth Song","You Rock My World","Butterflies"],
"Rumours|Fleetwood Mac":["Second Hand News","Dreams","Never Going Back Again","Don't Stop","Go Your Own Way","Songbird","The Chain","You Make Loving Fun","I Don't Want to Know","Oh Daddy","Gold Dust Woman"],
"Short n' Sweet|Sabrina Carpenter":["Taste","Espresso","Please Please Please","Coincidence","Slim Pickins","Dumb & Poetic","Juno","Lie to Girls","Don't Smile","Good Graces","Bishop","Mirror","Bad Reviews","Bed Chem","Looking at Me","Feather"],
"The Tortured Poets Department|Taylor Swift":["Fortnight","The Tortured Poets Department","My Boy Only Breaks His Favorite Toys","Down Bad","So Long London","But Daddy I Love Him","Fresh Out The Slammer","Florida!!!","Guilty as Sin?","Who's Afraid of Little Old Me?","I Can Fix Him (No Really I Can)","loml","I Can Do It With a Broken Heart","The Smallest Man Who Ever Lived","The Alchemy","Clara Bow"],
"Greatest Hits|Queen":["Bohemian Rhapsody","Radio Ga Ga","I Was Born to Love You","Invisible Man","Killer Queen","Don't Stop Me Now","Under Pressure","Another One Bites the Dust","We Will Rock You","We Are the Champions","You're My Best Friend","A Kind of Magic","I Want to Break Free"],
"Blonde|Frank Ocean":["Nikes","Ivy","Pink + White","Be Yourself","Solo","Skyline To","Self Control","Good Guy","Nights","Solo (Reprise)","Pretty Sweet","Facebook Story","Close to You","White Ferrari","Seigfried","Godspeed","Futura Free"],
"Views|Drake":["Keep the Family Close","9","Hype","Weston Road Flows","Redemption","With You","Feel No Ways","Fire & Desire","One Dance","Controlla","Too Good","Faithful","Still Here","Hotline Bling","Summers Over Interlude","Child's Play","Pop Style","Got the Keys","Grammys"],
"Hit Me Hard And Soft|Billie Eilish":["Skinny","Lunch","Chihiro","Birds of a Feather","Wildflower","The Greatest","L'Amour de Ma Vie","The Diner","Bittersuite","Blue"],
"Graduation|Kanye West":["!Good Morning","!Champion","!Stronger","!I Wonder","!Good Life","!Can't Tell Me Nothing","!Barry Bonds","!Drunk and Hot Girls","!Flashing Lights","!Everything I Am","!The Glory","!Homecoming","!Big Brother"],
"Ctrl|SZA":["Supermodel","!Love Galore","!Doves in the Wind","Drew Barrymore","Prom","!The Weekend","Go Gina","Broken Clocks","Deja Vu","Normal Girl","Pretty Little Birds","20 Something"],
"Nevermind|Nirvana":["!Smells Like Teen Spirit","In Bloom","Come as You Are","Breed","Lithium","Polly","!Territorial Pissings","Drain You","Lounge Act","Stay Away","On a Plain","Something in the Way"],
"Sour|Olivia Rodrigo":["brutal","traitor","drivers license","deja vu","good 4 u","enough for you","happier","favorite crime","1 step forward, 3 steps back","hope ur ok"],
"ANTI|Rihanna":["Consideration","James Joint","Kiss It Better","!Work","Desperado","Woo","!Needed Me","Yeah I Said It","Same Ol' Mistakes","Never Ending","Love on the Brain","Close to You"],
"Curtain Call: The Hits|Eminem":["!Lose Yourself","!Cleanin' Out My Closet","!Without Me","!Business","!Just Lose It","!Ass Like That","Mockingbird","Hailie's Song","When I'm Gone","!Till I Collapse"],
"Teenage Dream|Katy Perry":["Teenage Dream","Last Friday Night (T.G.I.F.)","California Gurls","Firework","E.T.","The One That Got Away","Peacock","Circle the Drain","Part of Me","Hummingbird Heartbeat","Pearl","Who Am I Living For?","Not Like the Movies","I Kissed a Girl"],
"The Rise And Fall Of A Midwest Princess|Chappell Roan":["Femininomenon","Red Wine Supernova","Casual","Naked in Manhattan","My Kink is Karma","After Midnight","Kaleidoscope","Super Graphic Ultra Modern Girl","Guilty Pleasure","Notion","Always Never Yours","The Giver","Dress Up"],
"good kid, m.A.A.d city|Kendrick Lamar":["!Sherane a.k.a Master Splinter's Daughter","!Bitch, Don't Kill My Vibe","!Backseat Freestyle","!The Art of Peer Pressure","!Money Trees","!Poetic Justice","!good kid","!m.A.A.d city","!Swimming Pools (Drank)","!Sing About Me, I'm Dying of Thirst","!Real","Compton","The Recipe"],
"The Fame|Lady Gaga":["Just Dance","LoveGame","Paparazzi","Poker Face","Eh Eh (Nothing Else I Can Say)","Beautiful Dirty Rich","The Fame","Money Honey","Starstruck","Boys Boys Boys","Paper Gangsta","Brown Eyes"],
"GNX|Kendrick Lamar":["!wacced out murals","!squabble up","!luther","!man at the garden","!hey now","!reincarnated","!tv off","!dodger blue","!peekaboo","!gnx"],
"Channel Orange|Frank Ocean":["Thinkin Bout You","Fertilizer","Sierra Leone","Sweet Life","Not Just Money","Super Rich Kids","Pilot Jones","Crack Rock","Pyramids","Lost","White","Monks","Bad Religion","Pink Matter","Forrest Gump","End / Golden Girl"],
"Good Girl Gone Bad|Rihanna":["Umbrella","Push Up On Me","Don't Stop the Music","Breakin' Dishes","Shut Up and Drive","Hate That I Love You","Say It","Rehab","Question Existing","Good Girl Gone Bad","We Ride","Lemme Get That"],
"Folklore|Taylor Swift":["the 1","cardigan","the last great american dynasty","exile","my tears ricochet","mirrorball","seven","august","this is me trying","illicit affairs","invisible string","mad woman","epiphany","betty","peace","hoax"],
"Lover|Taylor Swift":["I Forgot That You Existed","Cruel Summer","Lover","The Man","The Archer","I Think He Knows","Miss Americana & the Heartbreak Prince","Paper Rings","Cornelia Street","Death By A Thousand Cuts","London Boy","Soon You'll Get Better","False God","You Need to Calm Down","Afterglow","ME!","It's Nice to Have a Friend","Daylight"],
"Guts|Olivia Rodrigo":["vampire","bad idea right?","get him back!","lacy","ballad of a homeschooled girl","making the bed","logical","all-american bitch","pretty isn't pretty","teenage dream","the grudge","love is embarrassing"],
"Scorpion|Drake":["Survival","!Nonstop","Elevate","!Emotionless","!God's Plan","I'm Upset","8 Out of 10","Talk Up","Is There More","Sandra's Rose","Jaded","!Nice for What","Finesse","!In My Feelings","Don't Matter to Me","After Dark","Final Fantasy","March 14"],
"Doo-Wops and Hooligans|Bruno Mars":["Grenade","Just the Way You Are","Our First Time","Runaway Baby","The Lazy Song","Marry You","Talking to the Moon","Liquor Store Blues","Count on Me","The Other Side","Somewhere in Brooklyn"],
"Currents|Tame Impala":["Let It Happen","Nangs","The Moment","Yes I'm Changing","Eventually","Gossip","The Less I Know the Better","Past Life","New Person, Same Old Mistakes"],
"Meteora|Linkin Park":["Foreword","Don't Stay","Somewhere I Belong","Lying from You","Hit the Floor","Easier to Run","Faint","Figure.09","Breaking the Habit","From the Inside","Nobody's Listening","Session","Numb"],
"Midnights|Taylor Swift":["Lavender Haze","Marjorie","Anti-Hero","Snow on the Beach","Midnight Rain","Question...?","Vigilante Shit","Bejeweled","Labyrinth","Karma","Sweet Nothing","Mastermind","The Great War","Bigger Than the Whole Sky","Paris","High Infidelity","Glitch","Would've, Could've, Should've","Dear Reader"],
"Hybrid Theory|Linkin Park":["Papercut","One Step Closer","With You","Points of Authority","Crawling","Runaway","By Myself","In the End","A Place for My Head","Forgotten","Cure for the Itch","Pushing Me Away"],
"The Secret Of Us|Gracie Abrams":["I Love You, I'm Sorry","That's So True","Risk","Free Now","Block Me Out","Difficult","I Should Hate You","Fault Line","Full Machine","Right Now","Where Do We Go Now?","21","The While"],
"Thank Me Later|Drake":["Fireworks","Karaoke","The Resistance","Light Up","Up All Night","Show Me a Good Time","!Miss Me","!Unforgettable","Find Your Love","!Thank Me Now","Shut It Down","Fancy","Free Spirit","Closer"]
};

const NC=['#ff10f0','#00ffff','#39ff14','#ffe600','#ff4d00','#bf5fff','#00ff9f','#ff6b6b','#4ec9ff','#ffaa00'];
function sH(s){let h=5381;for(let i=0;i<s.length;i++)h=((h<<5)+h)^s.charCodeAt(i);return Math.abs(h);}
function pick(h,n){return NC[Math.abs(h+n)%NC.length];}

function cssArt(title,artist,sz){
  const h=sH(title+artist),s=sz||44,bg='#030310';
  const c1=pick(h,0),c2=pick(h,4),c3=pick(h,8),t=h%5;
  let i='';
  if(t===0){const r=(h>>10)%30-15;i=`<div style="position:absolute;width:80%;height:90%;background:${c1};top:-5%;left:5%;transform:rotate(${r}deg)"></div><div style="position:absolute;width:50%;height:50%;border-radius:50%;background:${c2};top:25%;left:25%"></div><div style="position:absolute;width:25%;height:25%;background:${c3};top:8%;right:5%"></div>`;}
  else if(t===1){const ag=(h%36)-18;i=[0,1,2,3,4,5].map(n=>`<div style="position:absolute;width:250%;height:${s*.18}px;background:${pick(h,n)};top:${n*19-5}%;left:-75%;transform:rotate(${ag}deg)"></div>`).join('');}
  else if(t===2){i=`<div style="position:absolute;inset:0;border-radius:50%;background:${c1}"></div><div style="position:absolute;width:72%;height:72%;border-radius:50%;background:${bg};top:14%;left:14%"></div><div style="position:absolute;width:44%;height:44%;border-radius:50%;background:${c2};top:28%;left:28%"></div><div style="position:absolute;width:18%;height:18%;border-radius:50%;background:${c3};top:41%;left:41%"></div>`;}
  else if(t===3){i=`<div style="position:absolute;inset:0;background:${c1}"></div><div style="position:absolute;width:100%;height:100%;background:${c2};clip-path:polygon(100% 0,100% 100%,0 100%)"></div>`;}
  else{i=[0,1,2,3,4,5,6,7,8].map(n=>`<div style="position:absolute;width:33.4%;height:33.4%;background:${pick(h,n*3)};top:${Math.floor(n/3)*33.3}%;left:${(n%3)*33.3}%;opacity:${.25+((h>>(n*2))&3)*.22}"></div>`).join('');}
  return `<div style="width:${s}px;height:${s}px;background:${bg};position:relative;overflow:hidden;flex-shrink:0;border-radius:4px;border:1px solid rgba(255,255,255,0.08)">${i}</div>`;
}

const artCache={};
const thumbCache={};

async function fetchArt(title,artist){
  const key=`${title}|${artist}`;
  if(artCache[key]!==undefined)return artCache[key];
  try{
    const q=encodeURIComponent(`${title} ${artist}`);
    const r=await fetch(`https://itunes.apple.com/search?term=${q}&entity=album&limit=1&media=music`);
    if(!r.ok){artCache[key]=null;return null;}
    const d=await r.json();
    if(d.results&&d.results.length>0){
      const url=d.results[0].artworkUrl100.replace('100x100bb','600x600bb');
      artCache[key]=url;
      thumbCache[key]=d.results[0].artworkUrl100;
      return url;
    }
  }catch(e){}
  artCache[key]=null;return null;
}

async function fetchThumb(title,artist,imgEl,phEl){
  const key=`${title}|${artist}`;
  if(thumbCache[key]){if(imgEl){imgEl.src=thumbCache[key];imgEl.style.display='block';if(phEl)phEl.style.display='none';}return;}
  try{
    const q=encodeURIComponent(`${title} ${artist}`);
    const r=await fetch(`https://itunes.apple.com/search?term=${q}&entity=album&limit=1&media=music`);
    if(!r.ok)return;
    const d=await r.json();
    if(d.results&&d.results.length>0){
      const url=d.results[0].artworkUrl100;
      thumbCache[key]=url;artCache[key]=d.results[0].artworkUrl100.replace('100x100bb','600x600bb');
      if(imgEl){imgEl.src=url;imgEl.style.display='block';if(phEl)phEl.style.display='none';}
    }
  }catch(e){}
}

let S={screen:'name',albumName:'',artistName:'',songs:[],songInput:'',isExplicit:false,chartPos:null,selAlbum:null,err:''};

function wPos(){const r=Math.random();if(r<0.05)return Math.floor(Math.random()*10)+1;if(r<0.3)return Math.floor(Math.random()*40)+11;return Math.floor(Math.random()*50)+51;}
function rInfo(p){if(p<=10)return{l:'LEGENDARY',c:'#ffe600',bc:'by',d:'TOP 10 - 5% CHANCE'};if(p<=50)return{l:'UNCOMMON',c:'#bf5fff',bc:'bpu',d:'TOP 50 - 25% CHANCE'};return{l:'COMMON',c:'#00ffff',bc:'bc',d:'#51-100 - 70% CHANCE'};}
function esc(s){return String(s||'').replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;');}
function buildChart(){const u={p:S.chartPos,t:S.albumName||'My Album',a:S.artistName||'You',isUser:true,songs:S.songs};const r=[];let ins=false;for(const x of BB){if(!ins&&x.p>=S.chartPos){r.push(u);ins=true;}r.push(x);}if(!ins)r.push(u);return r;}

function nameScreen(){return `<div class="scr sc"><div style="font-size:10px;letter-spacing:8px;color:#ff10f0;margin-bottom:6px">BILLBOARD 200 STUDIO</div><h1 style="font-size:clamp(18px,4vw,36px);color:#fff;letter-spacing:2px;margin-bottom:4px;font-weight:bold">NAME YOUR ALBUM</h1><div style="width:2px;height:26px;background:#ff10f0;margin:0 auto 26px"></div><div style="width:100%;max-width:440px"><input id="ia" type="text" placeholder="Album title..." value="${esc(S.albumName)}" style="margin-bottom:12px;font-size:17px;text-align:center;border-color:#ff10f0" maxlength="80"><input id="ib" type="text" placeholder="Your artist name..." value="${esc(S.artistName)}" style="margin-bottom:24px;text-align:center" maxlength="60"><button class="bp" id="btn-ns" style="width:100%">NEXT: ADD SONGS &gt;&gt;</button></div></div>`;}

function songsScreen(){const sl=S.songs.length===0?`<div style="text-align:center;color:#1e1e38;padding:28px;font-size:11px;letter-spacing:4px">NO TRACKS YET</div>`:S.songs.map((s,i)=>`<div class="si"><span style="color:#1e1e38;font-size:11px;min-width:26px">${String(i+1).padStart(2,'0')}</span><span style="flex:1;font-size:13px;color:#ddd;overflow:hidden;text-overflow:ellipsis;white-space:nowrap">${esc(s.t)}</span>${s.ex?`<span class="eb">E</span>`:''}<button onclick="bbRemoveSong(${i})" style="background:transparent;border:1px solid #1e1e38;color:#3a3a6a;font-size:10px;padding:3px 8px;border-radius:2px">X</button></div>`).join('');
return `<div class="sc" style="max-width:570px;margin:0 auto;padding:20px 14px 36px"><div style="text-align:center;margin-bottom:20px;padding-top:10px"><div style="font-size:10px;letter-spacing:6px;color:#00ffff;margin-bottom:4px">ADDING TRACKS TO</div><div style="font-size:clamp(15px,3vw,24px);color:#fff;font-weight:bold">${esc(S.albumName)}</div><div style="font-size:11px;color:${S.songs.length>=75?'#ff4444':'#1e1e38'};margin-top:5px;letter-spacing:3px">${S.songs.length} / 75 TRACKS</div></div><div style="border:1px solid rgba(255,16,240,0.2);border-radius:4px;padding:12px;margin-bottom:16px"><div style="display:flex;gap:8px;flex-wrap:wrap"><input id="is" type="text" placeholder="Song title..." value="${esc(S.songInput)}" style="flex:1;min-width:140px;border-color:rgba(255,16,240,0.5)" maxlength="100"><button class="bg" id="btn-as" style="padding:11px 16px;flex-shrink:0;font-size:11px">+ ADD</button></div><div style="display:flex;align-items:center;gap:10px;margin-top:12px;cursor:pointer" onclick="bbToggleEx()"><div style="width:40px;height:20px;border-radius:10px;border:1px solid ${S.isExplicit?'#ff10f0':'#1e1e38'};background:${S.isExplicit?'rgba(255,16,240,0.2)':'transparent'};position:relative;flex-shrink:0"><div style="width:14px;height:14px;border-radius:50%;background:#fff;position:absolute;top:2px;left:${S.isExplicit?'23px':'3px'};transition:left 0.15s"></div></div><span style="font-size:11px;letter-spacing:3px;color:${S.isExplicit?'#ff10f0':'#2a2a4a'}">${S.isExplicit?'[E] EXPLICIT':'CLEAN'}</span></div>${S.err?`<div style="color:#ff4444;font-size:11px;margin-top:8px;letter-spacing:2px">${esc(S.err)}</div>`:''}</div><div style="min-height:60px;margin-bottom:16px">${sl}</div><div style="display:flex;gap:10px;justify-content:center"><button class="bc" onclick="bbGo('name')">BACK</button><button class="bp" onclick="bbGo('review')">REVIEW ALBUM &gt;&gt;</button></div></div>`;}

function reviewScreen(){const ex=S.songs.filter(s=>s.ex).length;return `<div class="scr sc"><div style="font-size:10px;letter-spacing:6px;color:#39ff14;margin-bottom:6px">READY TO DROP?</div><div style="margin-bottom:16px">${cssArt(S.albumName,S.artistName,140)}</div><h2 style="font-size:clamp(16px,3.5vw,30px);color:#fff;font-weight:bold;margin-bottom:4px">${esc(S.albumName)}</h2><div style="font-size:13px;color:#333;margin-bottom:24px">${esc(S.artistName||'Unknown Artist')}</div><div style="border:1px solid rgba(255,255,255,0.07);border-radius:6px;padding:22px 36px;margin-bottom:24px"><div style="font-size:10px;letter-spacing:5px;color:#1e1e38;margin-bottom:4px">TRACKS</div><div style="font-size:50px;font-weight:bold;color:#fff;line-height:1">${S.songs.length}</div>${ex>0?`<div style="font-size:11px;color:#ff10f0;margin-top:6px;letter-spacing:2px">${ex} EXPLICIT</div>`:''}</div><div style="display:flex;gap:12px"><button class="bc" onclick="bbGo('songs')">BACK</button><button class="bg" onclick="bbPubAlbum()">** PUBLISH ALBUM **</button></div></div>`;}

function loadingScreen(){return `<div class="scr sc"><div style="font-size:10px;letter-spacing:6px;color:#1e1e38;margin-bottom:12px">SUBMITTING TO CHARTS...</div><div id="sn" style="font-size:clamp(56px,12vw,110px);font-weight:bold;color:#fff;line-height:1;border:2px solid #1a1a2e;border-radius:8px;padding:10px 20px;min-width:180px;text-align:center;transition:color 0.4s,border-color 0.4s;margin-bottom:16px">#??</div><div id="ss" style="font-size:12px;font-weight:bold;letter-spacing:6px;color:#1a1a2e;margin-bottom:6px;transition:color 0.4s">CALCULATING...</div><div id="sd" style="font-size:10px;color:#1a1a2e;letter-spacing:3px;margin-bottom:24px;min-height:14px;transition:color 0.4s"></div><div style="display:flex;gap:4px;align-items:flex-end;height:42px;margin-bottom:24px">${Array.from({length:18}).map((_,i)=>`<div style="width:8px;border-radius:2px 2px 0 0;background:hsl(${i*22},100%,55%);animation:bbBarDance ${0.4+i*0.04}s ease-in-out infinite alternate;animation-delay:${i*0.06}s"></div>`).join('')}</div><div id="vcb" style="display:none;animation:bbFadeIn 0.5s forwards"></div></div>`;}

function chartScreen(){
  const ch=buildChart();const ri=rInfo(S.chartPos);
  const rows=ch.map((a,idx)=>{
    const isu=a.isUser;const pc=isu?ri.c:(idx+1<=10?'#ffe600':(idx+1<=50?'#bf5fff':'#2a2a5a'));
    const bs=isu?`border-color:${ri.c}44;background:rgba(255,255,255,0.018)`:'';
    const key=`${a.t}|${a.a}`;const cached=thumbCache[key];
    const thumb=isu?cssArt(a.t,a.a,44):cached?`<img class="athumb" src="${cached}" alt="">`:`<div class="athumb-ph" id="ph-${idx}">${cssArt(a.t,a.a,44)}</div>`;
    return `<div class="crow" style="${bs}" data-i="${idx}" id="${isu?'ur':'r'+idx}"><span style="min-width:32px;font-size:${idx<9?'18px':'14px'};font-weight:bold;color:${pc}">${idx<9?'0':''}${idx+1}</span>${thumb}<div style="flex:1;min-width:0"><div style="font-size:13px;color:${isu?'#fff':'#aaa'};overflow:hidden;text-overflow:ellipsis;white-space:nowrap;font-weight:${isu?'bold':'normal'}">${esc(a.t)}${isu?`<span class="yb" style="background:${ri.c}18;border:1px solid ${ri.c};color:${ri.c}">YOU</span>`:''}</div><div style="font-size:11px;color:#1e1e38;margin-top:1px">${esc(a.a)}</div></div></div>`;
  }).join('');
  return `<div class="sc" style="max-width:640px;margin:0 auto;padding:0 6px 36px"><div style="padding:12px 8px 10px;border-bottom:1px solid rgba(255,16,240,0.12);margin-bottom:8px;display:flex;align-items:center;justify-content:space-between;flex-wrap:wrap;gap:8px"><div><div style="font-size:9px;letter-spacing:5px;color:#ff10f0">BILLBOARD 200</div><div style="font-size:9px;color:#1e1e38;letter-spacing:2px;margin-top:2px">WEEK OF APR 11, 2026</div></div><div style="text-align:right"><div style="font-size:9px;letter-spacing:3px;color:${ri.c}">YOUR DEBUT</div><div style="font-size:20px;font-weight:bold;color:${ri.c}">#${S.chartPos} ${ri.l}</div></div></div><div style="display:flex;gap:8px;justify-content:center;margin-bottom:8px;flex-wrap:wrap"><button class="${ri.bc}" style="font-size:10px;padding:7px 14px" onclick="bbScrollToU()">JUMP TO MY ALBUM</button><button class="bc" style="font-size:10px;padding:7px 14px" onclick="bbNewAlbum()">NEW ALBUM</button></div><div style="font-size:9px;color:#1a1a2e;text-align:center;margin-bottom:8px;letter-spacing:3px">TAP ANY ALBUM TO VIEW DETAILS + TRACKS</div>${rows}</div>`;
}

function detailScreen(){
  const a=S.selAlbum;const isu=a.isUser;
  const ri=S.chartPos?rInfo(S.chartPos):{c:'#00ffff',l:'',bc:'bc'};
  const ac=isu?ri.c:'#00ffff';
  const ch=S.chartPos?buildChart():[];
  const chartPos=isu?S.chartPos:(ch.findIndex(x=>x.p===a.p&&!x.isUser)+1||a.p);
  let tl='';
  if(isu){
    tl=a.songs.length===0?`<div style="color:#1e1e38;text-align:center;padding:28px;font-size:11px;letter-spacing:4px">NO TRACKS ON THIS ALBUM</div>`:a.songs.map((s,i)=>`<div class="si"><span style="color:#2a2a4a;font-size:11px;min-width:26px">${String(i+1).padStart(2,'0')}</span><span style="flex:1;font-size:13px;color:#ddd">${esc(s.t)}</span>${s.ex?`<span class="eb">E</span>`:''}</div>`).join('');
  }else{
    const key=`${a.t}|${a.a}`;const trk=TR[key];
    tl=trk?trk.map((s,i)=>{const ex=s.startsWith('!');const title=ex?s.slice(1):s;return`<div class="si"><span style="color:#2a2a4a;font-size:11px;min-width:26px">${String(i+1).padStart(2,'0')}</span><span style="flex:1;font-size:13px;color:#ccc">${esc(title)}</span>${ex?`<span class="eb">E</span>`:''}</div>`;}).join(''):
    `<div style="color:#1e1e38;text-align:center;padding:24px;font-size:11px;letter-spacing:3px;border:1px solid rgba(255,255,255,0.04);border-radius:4px">TRACKLIST NOT IN DATABASE<br><span style="font-size:10px;color:#111133;margin-top:6px;display:block">NEW 2026 RELEASE OR COMPILATION</span></div>`;
  }
  const cachedUrl=artCache[`${a.t}|${a.a}`];
  const artHtml=isu?cssArt(a.t,a.a,200):
    `<div style="position:relative;width:200px;height:200px;flex-shrink:0">
      <div id="art-ph" style="position:absolute;inset:0">${cssArt(a.t,a.a,200)}</div>
      ${cachedUrl?`<img id="art-img" src="${cachedUrl}" style="width:200px;height:200px;object-fit:cover;border-radius:4px;display:block;position:absolute;inset:0" alt="">`:`<img id="art-img" src="" style="width:200px;height:200px;object-fit:cover;border-radius:4px;display:none;position:absolute;inset:0" alt=""><div id="art-spin" style="position:absolute;inset:0;display:flex;align-items:center;justify-content:center"><div style="width:24px;height:24px;border:2px solid rgba(255,255,255,0.1);border-top-color:${ac};border-radius:50%;animation:bbSpin 0.8s linear infinite"></div></div>`}
    </div>`;
  return `<div class="sc" style="max-width:520px;margin:0 auto;padding:20px 14px 36px"><button class="bc" style="font-size:10px;padding:7px 14px;margin-bottom:18px" onclick="bbGo('chart')">^ BACK TO CHART</button><div style="display:flex;gap:18px;align-items:flex-start;margin-bottom:22px;flex-wrap:wrap">${artHtml}<div style="flex:1;min-width:160px"><div style="font-size:11px;color:${ac};letter-spacing:3px;margin-bottom:6px">#${chartPos} ON BILLBOARD 200</div><div style="font-size:clamp(14px,3vw,22px);font-weight:bold;color:#fff;margin-bottom:6px;line-height:1.3">${esc(a.t)}</div><div style="font-size:13px;color:#444;margin-bottom:${isu?'10px':'0'}">${esc(a.a)}</div>${isu?`<div style="font-size:10px;color:${ri.c};letter-spacing:3px;margin-top:6px">${ri.l} DEBUT</div>`:''}</div></div><div style="font-size:9px;letter-spacing:4px;color:#1a1a2e;margin-bottom:8px">TRACKLIST</div>${tl}</div>`;
}

function bbGo(s){const ia=document.getElementById('ia'),ib=document.getElementById('ib'),is=document.getElementById('is');if(ia)S.albumName=ia.value;if(ib)S.artistName=ib.value;if(is)S.songInput=is.value;S.err='';S.screen=s;bbRender();}
function bbToggleEx(){const is=document.getElementById('is');if(is)S.songInput=is.value;S.isExplicit=!S.isExplicit;S.err='';S.screen='songs';bbRender();}
function bbRemoveSong(i){S.songs.splice(i,1);S.screen='songs';bbRender();}
function bbAddSong(){const el=document.getElementById('is');const v=(el?el.value:'').trim();if(!v){S.err='Enter a song title';S.screen='songs';bbRender();return;}if(S.songs.length>=75){S.err='Maximum 75 songs reached';S.screen='songs';bbRender();return;}S.songs.push({t:v,ex:S.isExplicit});S.songInput='';S.isExplicit=false;S.err='';S.screen='songs';bbRender();}
function bbPubAlbum(){S.chartPos=wPos();S.screen='loading';bbRender();bbStartSpin();}
function bbNewAlbum(){S={screen:'name',albumName:'',artistName:'',songs:[],songInput:'',isExplicit:false,chartPos:null,selAlbum:null,err:''};bbRender();}
function bbScrollToU(){const e=document.getElementById('ur');if(e)e.scrollIntoView({behavior:'smooth',block:'center'});}

function bbSelectAlbum(i){
  const ch=buildChart();S.selAlbum=ch[i];S.screen='detail';bbRender();
  const a=S.selAlbum;
  if(!a.isUser){
    fetchArt(a.t,a.a).then(url=>{
      const img=document.getElementById('art-img');
      const ph=document.getElementById('art-ph');
      const sp=document.getElementById('art-spin');
      if(img&&url){img.src=url;img.style.display='block';if(ph)ph.style.display='none';if(sp)sp.style.display='none';}
      else if(sp)sp.style.display='none';
    });
  }
}

function bbStartSpin(){
  const pos=S.chartPos;let fr=0;const tot=95;
  const iv=setInterval(()=>{
    fr++;const sn=document.getElementById('sn');if(!sn){clearInterval(iv);return;}
    let num;if(fr<tot*0.65){num=Math.floor(Math.random()*100)+1;}
    else{const pg=(fr-tot*0.65)/(tot*0.35);const jt=Math.round((1-pg)*12);num=pos+Math.floor(Math.random()*jt*2)-jt;num=Math.max(1,Math.min(100,num));}
    if(fr<tot){sn.textContent='#'+num;}
    else{clearInterval(iv);const ri=rInfo(pos);sn.textContent='#'+pos;sn.style.color=ri.c;sn.style.borderColor=ri.c;
      const ss=document.getElementById('ss');if(ss){ss.textContent=ri.l;ss.style.color=ri.c;}
      const sd=document.getElementById('sd');if(sd){sd.textContent=ri.d;sd.style.color=ri.c+'88';}
      const vcb=document.getElementById('vcb');if(vcb){vcb.innerHTML=`<button class="${ri.bc}" onclick="bbGo('chart')" style="font-size:12px;padding:13px 26px">VIEW BILLBOARD CHART &gt;&gt;</button>`;vcb.style.display='block';}}
  },58);
}

function bbRender(){
  const root=document.getElementById('gr');if(!root)return;
  switch(S.screen){
    case'name':root.innerHTML=nameScreen();break;
    case'songs':root.innerHTML=songsScreen();break;
    case'review':root.innerHTML=reviewScreen();break;
    case'loading':root.innerHTML=loadingScreen();break;
    case'chart':root.innerHTML=chartScreen();break;
    case'detail':root.innerHTML=detailScreen();break;
  }
  const as=document.getElementById('btn-as');if(as)as.onclick=bbAddSong;
  const ns=document.getElementById('btn-ns');if(ns)ns.onclick=()=>{const ia=document.getElementById('ia');if(ia)S.albumName=ia.value;if(S.albumName.trim())bbGo('songs');};
  const is=document.getElementById('is');if(is)is.addEventListener('keydown',e=>{if(e.key==='Enter')bbAddSong();});
  const ia=document.getElementById('ia');if(ia){ia.oninput=e=>S.albumName=e.target.value;ia.addEventListener('keydown',e=>{if(e.key==='Enter'&&S.albumName.trim())bbGo('songs');});}
  const ib=document.getElementById('ib');if(ib){ib.oninput=e=>S.artistName=e.target.value;ib.addEventListener('keydown',e=>{if(e.key==='Enter'&&S.albumName.trim())bbGo('songs');});}
  document.querySelectorAll('.crow').forEach(r=>{r.onclick=()=>bbSelectAlbum(parseInt(r.dataset.i));});
}
bbRender();
})();
// {% endraw %}
</script>
