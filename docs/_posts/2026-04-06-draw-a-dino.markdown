---
layout: post
title: "Draw a Dino, Be a Dino"
date: 2026-04-06 12:00:00 -0400
categories: Games
---

Play Draw a Dino, be a Dino

<div id="drawadino-root" style="position:relative; width:100%; max-width:900px; margin:0 auto;">
</div>

<style>
  @import url('https://fonts.googleapis.com/css2?family=Bungee+Shade&family=Fredoka:wght@400;600&display=swap');
  #drawadino-root * { margin: 0; padding: 0; box-sizing: border-box; }
  #drawadino-root {
    font-family: 'Fredoka', sans-serif;
    user-select: none;
    background: #1a1a2e;
    border-radius: 12px;
    overflow: hidden;
    position: relative;
  }
  #drawadino-root #ui-overlay {
    position: absolute; top: 0; left: 0; right: 0; z-index: 100;
    display: flex; align-items: center; justify-content: space-between;
    padding: 10px 16px;
    background: linear-gradient(180deg, rgba(10,10,30,0.92) 60%, transparent 100%);
    pointer-events: none; flex-wrap: wrap; gap: 6px;
  }
  #drawadino-root #ui-overlay > * { pointer-events: auto; }
  #drawadino-root h1 {
    font-family: 'Bungee Shade', cursive;
    color: #7fff6a; font-size: 18px;
    text-shadow: 2px 2px 0 #0a0a1e;
  }
  #drawadino-root .toolbar { display: flex; gap: 6px; align-items: center; flex-wrap: wrap; }
  #drawadino-root .toolbar label { color: #aaa; font-size: 12px; }
  #drawadino-root .btn {
    font-family: 'Fredoka', sans-serif; font-weight: 600;
    border: none; padding: 7px 14px; border-radius: 8px;
    cursor: pointer; font-size: 13px; transition: transform 0.1s;
  }
  #drawadino-root .btn:active { transform: scale(0.95); }
  #drawadino-root .btn-go { background: #7fff6a; color: #1a1a2e; box-shadow: 0 0 12px #7fff6a44; }
  #drawadino-root .btn-clear { background: #ff6a6a; color: #fff; }
  #drawadino-root .btn-back { background: #6ac5ff; color: #1a1a2e; }
  #drawadino-root .color-swatch {
    width: 24px; height: 24px; border-radius: 50%;
    border: 3px solid transparent; cursor: pointer;
    transition: border-color 0.15s, transform 0.15s;
  }
  #drawadino-root .color-swatch.active { border-color: #fff; transform: scale(1.2); }
  #drawadino-root .brush-size { width: 60px; accent-color: #7fff6a; }
  #drawadino-root #game-canvas { display: block; width: 100%; cursor: crosshair; }
  #drawadino-root #game-canvas.play-mode { cursor: none; }
  #drawadino-root #instructions {
    position: absolute; bottom: 16px; left: 50%; transform: translateX(-50%);
    color: #ccc; font-size: 14px; text-align: center;
    pointer-events: none; z-index: 100;
    background: rgba(10,10,30,0.75); padding: 8px 20px; border-radius: 10px;
  }
  #drawadino-root #score-display {
    position: absolute; top: 56px; right: 16px;
    color: #ffd700; font-weight: 600; font-size: 18px;
    z-index: 100; display: none;
    text-shadow: 0 0 10px #ffd70055;
  }
  #drawadino-root .food-counter { color: #ff6a6a; font-size: 15px; margin-top: 2px; }
  #drawadino-root #dino-pad-overlay {
    display: none; position: absolute; inset: 0; z-index: 200;
    background: rgba(10,10,30,0.94);
    flex-direction: column; align-items: center; justify-content: center; gap: 14px;
  }
  #drawadino-root #dino-pad-overlay.visible { display: flex; }
  #drawadino-root #dino-pad-container {
    position: relative; border: 3px dashed #7fff6a55; border-radius: 12px;
    background: repeating-conic-gradient(#252535 0% 25%, #20202e 0% 50%) 50% / 16px 16px;
  }
  #drawadino-root #dino-canvas { display: block; border-radius: 10px; cursor: crosshair; }
  #drawadino-root .dino-tools { display: flex; gap: 6px; align-items: center; flex-wrap: wrap; justify-content: center; }
  #drawadino-root #dino-pad-overlay h2 { font-family: 'Bungee Shade', cursive; color: #7fff6a; font-size: 22px; }
  #drawadino-root #dino-pad-overlay p { color: #999; font-size: 13px; max-width: 350px; text-align: center; }
  #drawadino-root .eraser-btn { background: #444; color: #fff; }
  #drawadino-root .eraser-btn.active { background: #fff; color: #222; }
</style>

<script>
// {% raw %}
(function() {
  var root = document.getElementById('drawadino-root');
  root.innerHTML = '<div id="ui-overlay">' +
    '<h1 id="title">DRAW YOUR WORLD</h1>' +
    '<div class="toolbar" id="draw-tools">' +
      '<div class="color-swatch active" style="background:#4a7a3b;" data-color="#4a7a3b"></div>' +
      '<div class="color-swatch" style="background:#8B7355;" data-color="#8B7355"></div>' +
      '<div class="color-swatch" style="background:#5577aa;" data-color="#5577aa"></div>' +
      '<div class="color-swatch" style="background:#888;" data-color="#888"></div>' +
      '<div class="color-swatch" style="background:#2d5a1e;" data-color="#2d5a1e"></div>' +
      '<div class="color-swatch" style="background:#e8d44d;" data-color="#e8d44d"></div>' +
      '<label>Size</label>' +
      '<input type="range" class="brush-size" id="brush-size" min="5" max="80" value="30">' +
      '<button class="btn btn-clear" id="clear-map-btn">Clear</button>' +
      '<button class="btn btn-go" id="open-dino-btn">NEXT: DRAW DINO</button>' +
    '</div>' +
    '<div class="toolbar" id="play-tools" style="display:none;">' +
      '<button class="btn btn-back" id="edit-world-btn">Edit World</button>' +
      '<button class="btn btn-back" style="background:#d9a0ff;color:#1a1a2e;" id="edit-dino-btn">Edit Dino</button>' +
    '</div>' +
  '</div>' +
  '<div id="score-display">' +
    '<div>Score: <span id="score">0</span></div>' +
    '<div class="food-counter">Food left: <span id="food-count">0</span></div>' +
  '</div>' +
  '<div id="instructions">Paint your world, then draw your dino!</div>' +
  '<canvas id="game-canvas"></canvas>' +
  '<div id="dino-pad-overlay">' +
    '<h2>DRAW YOUR DINO</h2>' +
    '<p>Draw your dinosaur facing right. This will be your character!</p>' +
    '<div id="dino-pad-container">' +
      '<canvas id="dino-canvas" width="200" height="200"></canvas>' +
    '</div>' +
    '<div class="dino-tools">' +
      '<div class="color-swatch active" style="background:#3daa2e;" data-dino-color="#3daa2e"></div>' +
      '<div class="color-swatch" style="background:#ff4444;" data-dino-color="#ff4444"></div>' +
      '<div class="color-swatch" style="background:#4488ff;" data-dino-color="#4488ff"></div>' +
      '<div class="color-swatch" style="background:#ffcc00;" data-dino-color="#ffcc00"></div>' +
      '<div class="color-swatch" style="background:#ff66cc;" data-dino-color="#ff66cc"></div>' +
      '<div class="color-swatch" style="background:#ffffff;" data-dino-color="#ffffff"></div>' +
      '<div class="color-swatch" style="background:#111111;" data-dino-color="#111111"></div>' +
      '<div class="color-swatch" style="background:#ff8833;" data-dino-color="#ff8833"></div>' +
      '<label>Size</label>' +
      '<input type="range" class="brush-size" id="dino-brush-size" min="2" max="30" value="8">' +
      '<button class="btn eraser-btn" id="eraser-btn">Eraser</button>' +
      '<button class="btn btn-clear" id="clear-dino-btn">Clear</button>' +
      '<button class="btn btn-go" id="start-play-btn">BE THE DINO!</button>' +
    '</div>' +
  '</div>';

  var canvas = document.getElementById('game-canvas');
  var ctx = canvas.getContext('2d');
  var dinoCanvas = document.getElementById('dino-canvas');
  var dinoCtx = dinoCanvas.getContext('2d');

  var W, H;
  var drawing = false, brushColor = '#4a7a3b', brushSize = 30;
  var mapData = null, mode = 'draw', dinoImage = null;
  var dino = { x: 0, y: 0, targetX: 0, targetY: 0, angle: 0, speed: 3.5 };
  var score = 0, foods = [], particles = [], footprints = [], frameId = null;
  var dinoBrushColor = '#3daa2e', dinoBrushSize = 8, dinoDrawing = false, eraserMode = false;
  var lastPos = null;

  function resize() {
    var rect = root.getBoundingClientRect();
    W = canvas.width = rect.width;
    H = canvas.height = Math.max(500, window.innerHeight * 0.7);
    root.style.height = H + 'px';
    if (mode === 'draw') {
      if (mapData) ctx.putImageData(mapData, 0, 0);
      else { ctx.fillStyle = '#2a2a4a'; ctx.fillRect(0, 0, W, H); }
    }
  }
  window.addEventListener('resize', resize);
  resize();

  // --- MAP DRAWING ---
  root.querySelectorAll('[data-color]').forEach(function(s) {
    s.addEventListener('click', function() {
      root.querySelectorAll('[data-color]').forEach(function(x) { x.classList.remove('active'); });
      s.classList.add('active'); brushColor = s.dataset.color;
    });
  });
  document.getElementById('brush-size').addEventListener('input', function(e) { brushSize = +e.target.value; });

  function getCanvasPos(e) {
    var rect = canvas.getBoundingClientRect();
    return { x: e.clientX - rect.left, y: e.clientY - rect.top };
  }

  canvas.addEventListener('mousedown', function(e) {
    if (mode !== 'draw') return;
    drawing = true; var p = getCanvasPos(e); lastPos = p; mapBrush(p.x, p.y);
  });
  canvas.addEventListener('mousemove', function(e) {
    var p = getCanvasPos(e);
    if (mode === 'draw' && drawing) {
      mapLine(lastPos.x, lastPos.y, p.x, p.y);
      lastPos = p;
    }
    if (mode === 'play') { dino.targetX = p.x; dino.targetY = p.y; }
  });
  canvas.addEventListener('mouseup', function() { drawing = false; lastPos = null; });
  canvas.addEventListener('mouseleave', function() { drawing = false; lastPos = null; });
  canvas.addEventListener('click', function(e) {
    if (mode === 'play') {
      for (var i = 0; i < 8; i++) {
        var a = Math.random()*Math.PI*2, sp = 2+Math.random()*4;
        particles.push({x:dino.x,y:dino.y, vx:Math.cos(a)*sp, vy:Math.sin(a)*sp, life:1, color:'#ff6a6a', text:'ROAR!'[i%5]});
      }
    }
  });

  function mapBrush(x,y) { ctx.beginPath(); ctx.arc(x,y,brushSize,0,Math.PI*2); ctx.fillStyle=brushColor; ctx.fill(); }
  function mapLine(x1,y1,x2,y2) {
    var d=Math.hypot(x2-x1,y2-y1), s=Math.max(1,Math.floor(d/(brushSize*0.3)));
    for(var i=0;i<=s;i++){var t=i/s; mapBrush(x1+(x2-x1)*t, y1+(y2-y1)*t);}
  }
  function clearMapCanvas() { ctx.fillStyle='#2a2a4a'; ctx.fillRect(0,0,W,H); mapData=null; }

  document.getElementById('clear-map-btn').addEventListener('click', clearMapCanvas);
  document.getElementById('open-dino-btn').addEventListener('click', openDinoPad);
  document.getElementById('edit-world-btn').addEventListener('click', backToDraw);
  document.getElementById('edit-dino-btn').addEventListener('click', openDinoPad);

  // --- DINO PAD ---
  root.querySelectorAll('[data-dino-color]').forEach(function(s) {
    s.addEventListener('click', function() {
      eraserMode = false; document.getElementById('eraser-btn').classList.remove('active');
      root.querySelectorAll('[data-dino-color]').forEach(function(x) { x.classList.remove('active'); });
      s.classList.add('active'); dinoBrushColor = s.dataset.dinoColor;
    });
  });
  document.getElementById('dino-brush-size').addEventListener('input', function(e) { dinoBrushSize = +e.target.value; });
  document.getElementById('eraser-btn').addEventListener('click', function() {
    eraserMode=!eraserMode; document.getElementById('eraser-btn').classList.toggle('active',eraserMode);
  });

  var dLastPos = null;
  dinoCanvas.addEventListener('mousedown', function(e) {
    dinoDrawing=true; var r=dinoCanvas.getBoundingClientRect();
    dLastPos={x:e.clientX-r.left, y:e.clientY-r.top}; dinoBrush(dLastPos.x, dLastPos.y);
  });
  dinoCanvas.addEventListener('mousemove', function(e) {
    if(!dinoDrawing) return; var r=dinoCanvas.getBoundingClientRect();
    var nx=e.clientX-r.left, ny=e.clientY-r.top;
    dinoLine(dLastPos.x, dLastPos.y, nx, ny); dLastPos={x:nx,y:ny};
  });
  dinoCanvas.addEventListener('mouseup', function() { dinoDrawing=false; dLastPos=null; });
  dinoCanvas.addEventListener('mouseleave', function() { dinoDrawing=false; dLastPos=null; });

  function dinoBrush(x,y) {
    if(eraserMode) {
      dinoCtx.save(); dinoCtx.globalCompositeOperation='destination-out';
      dinoCtx.beginPath(); dinoCtx.arc(x,y,dinoBrushSize,0,Math.PI*2); dinoCtx.fill(); dinoCtx.restore();
    } else {
      dinoCtx.beginPath(); dinoCtx.arc(x,y,dinoBrushSize,0,Math.PI*2);
      dinoCtx.fillStyle=dinoBrushColor; dinoCtx.fill();
    }
  }
  function dinoLine(x1,y1,x2,y2) {
    var d=Math.hypot(x2-x1,y2-y1), s=Math.max(1,Math.floor(d/(dinoBrushSize*0.3)));
    for(var i=0;i<=s;i++){var t=i/s; dinoBrush(x1+(x2-x1)*t, y1+(y2-y1)*t);}
  }
  function clearDinoCanvas() { dinoCtx.clearRect(0,0,200,200); }
  document.getElementById('clear-dino-btn').addEventListener('click', clearDinoCanvas);

  function openDinoPad() {
    if(mode==='draw') mapData = ctx.getImageData(0,0,W,H);
    if(mode==='play' && frameId) cancelAnimationFrame(frameId);
    document.getElementById('dino-pad-overlay').classList.add('visible');
    document.getElementById('instructions').textContent = 'Draw your dinosaur facing right!';
  }

  // --- PLAY ---
  document.getElementById('start-play-btn').addEventListener('click', startPlay);

  function startPlay() {
    dinoImage = new Image();
    dinoImage.src = dinoCanvas.toDataURL();
    document.getElementById('dino-pad-overlay').classList.remove('visible');
    mode = 'play';
    document.getElementById('draw-tools').style.display='none';
    document.getElementById('play-tools').style.display='flex';
    document.getElementById('score-display').style.display='block';
    document.getElementById('instructions').textContent='Move mouse to walk - Click to ROAR!';
    canvas.classList.add('play-mode');
    document.getElementById('title').textContent='BE THE DINO!';

    if(!foods.length) {
      dino.x=W/2; dino.y=H/2; dino.targetX=W/2; dino.targetY=H/2;
      score=0; foods=[]; particles=[]; footprints=[];
      spawnFoods(15);
    }
    updateScore();
    gameLoop();
  }

  function backToDraw() {
    mode='draw';
    if(frameId) cancelAnimationFrame(frameId);
    document.getElementById('draw-tools').style.display='flex';
    document.getElementById('play-tools').style.display='none';
    document.getElementById('score-display').style.display='none';
    document.getElementById('instructions').textContent='Paint your world, then draw your dino!';
    canvas.classList.remove('play-mode');
    document.getElementById('title').textContent='DRAW YOUR WORLD';
    if(mapData) ctx.putImageData(mapData,0,0);
  }

  function spawnFoods(n) {
    for(var i=0;i<n;i++) foods.push({
      x:60+Math.random()*(W-120), y:80+Math.random()*(H-140),
      type:Math.random()>0.5?'leaf':'meat', bob:Math.random()*Math.PI*2, size:8+Math.random()*6
    });
    updateScore();
  }
  function updateScore() {
    document.getElementById('score').textContent=score;
    document.getElementById('food-count').textContent=foods.length;
  }

  function isWater(x,y) {
    if(!mapData||x<0||y<0||x>=W||y>=H) return false;
    var i=(Math.floor(y)*W+Math.floor(x))*4;
    return mapData.data[i+2]>120 && mapData.data[i]<100 && mapData.data[i+1]<140;
  }

  function gameLoop() {
    var dx=dino.targetX-dino.x, dy=dino.targetY-dino.y, dist=Math.hypot(dx,dy);
    var spd=dino.speed; if(isWater(dino.x,dino.y)) spd*=0.4;
    if(dist>5) {
      dino.angle=Math.atan2(dy,dx);
      dino.x+=dx/dist*spd; dino.y+=dy/dist*spd;
      if(Math.random()<0.12) footprints.push({x:dino.x-Math.cos(dino.angle)*20, y:dino.y-Math.sin(dino.angle)*20, life:1});
    }
    for(var i=foods.length-1;i>=0;i--) {
      var f=foods[i], fd=Math.hypot(f.x-dino.x, f.y-dino.y);
      if(fd<50) {
        score+=f.type==='meat'?15:10;
        for(var j=0;j<6;j++){var a=Math.random()*Math.PI*2;
          particles.push({x:f.x,y:f.y,vx:Math.cos(a)*2,vy:Math.sin(a)*2,life:1,color:f.type==='meat'?'#ff6a6a':'#7fff6a',text:'+'});}
        foods.splice(i,1); updateScore();
        if(foods.length<5) spawnFoods(8);
      }
    }
    particles.forEach(function(p){p.x+=p.vx;p.y+=p.vy;p.life-=0.02;p.vy-=0.02;});
    particles=particles.filter(function(p){return p.life>0;});
    footprints.forEach(function(f){f.life-=0.005;});
    footprints=footprints.filter(function(f){return f.life>0;});
    render();
    frameId=requestAnimationFrame(gameLoop);
  }

  function render() {
    if(mapData) ctx.putImageData(mapData,0,0);
    footprints.forEach(function(f){
      ctx.globalAlpha=f.life*0.3; ctx.fillStyle='#333';
      ctx.beginPath(); ctx.ellipse(f.x,f.y,4,3,0,0,Math.PI*2); ctx.fill();
    });
    ctx.globalAlpha=1;

    var t=performance.now()/1000;
    foods.forEach(function(f){
      var by=Math.sin(t*2+f.bob)*3;
      if(f.type==='leaf'){
        ctx.fillStyle='#4ae03a'; ctx.beginPath();
        ctx.ellipse(f.x,f.y+by,f.size,f.size*0.6,0.3,0,Math.PI*2); ctx.fill();
        ctx.fillStyle='#2d8a22'; ctx.fillRect(f.x-1,f.y+by-f.size*0.8,2,f.size*0.5);
      } else {
        ctx.fillStyle='#cc3333'; ctx.beginPath();
        ctx.arc(f.x,f.y+by,f.size*0.7,0,Math.PI*2); ctx.fill();
        ctx.fillStyle='#eee'; ctx.fillRect(f.x-f.size,f.y+by-2,f.size*2,4);
      }
    });

    if(dinoImage&&dinoImage.complete) {
      ctx.save(); ctx.translate(dino.x,dino.y);
      var flip=Math.abs(dino.angle)>Math.PI/2?-1:1;
      ctx.scale(flip,1);
      var bob=Math.sin(performance.now()/150)*2;
      ctx.drawImage(dinoImage,-50,-50+bob,100,100);
      ctx.restore();
    }

    particles.forEach(function(p){
      ctx.globalAlpha=p.life; ctx.fillStyle=p.color;
      ctx.font='600 16px Fredoka'; ctx.fillText(p.text,p.x,p.y);
    });
    ctx.globalAlpha=1;
  }
})();
// {% endraw %}
</script>
