---
layout: post
title: "Two New Games!"
date: 2026-03-29 12:00:00 -0400
categories: Games
---

Hello, Me and Phillip just made 2 more games! But there is a few things I have to say:
1. The Among Us movie got canceled due to file saving.
2. We are in the middle of making a pilot for a new show: Trapped in the Intenet. More info may come soon.
3. Enjoy The Games we made today!

---

## Don't Step on the Ant!

<style>
#gw { position: relative; height: 480px; overflow: hidden; cursor: none; border-radius: 12px; border: 0.5px solid var(--color-border-tertiary); }
#gc { display: block; }
#gh { margin-top: 8px; font-size: 12px; color: var(--color-text-secondary); text-align: center; }
</style>

<div id="gw"><canvas id="gc"></canvas></div>
<p id="gh">move your mouse over the game area to start</p>

<script>
// {% raw %}
(function(){
  const wrap = document.getElementById('gw');
  const c = document.getElementById('gc');
  const x = c.getContext('2d');
  c.width = wrap.clientWidth || 640;
  c.height = 480;
  c.style.width = '100%'; c.style.height = '480px';
  window.addEventListener('resize', () => { c.width = wrap.clientWidth; });

  let state = 'start', score = 0, ants = [], mx = c.width/2, my = c.height/2;
  let fvis = false, lastT = 0, antTimer = 0, flashAlpha = 0;

  const decor = Array.from({length:35}, (_, i) => ({
    x: (i*137.5)%1, y: (i*97.3)%1,
    t: i%3, r: 2+((i*31)%4),
    c: ['#fff','#ffd700','#ff9999'][i%3]
  }));

  class Ant {
    constructor(){
      let ax, ay, tries=0;
      do { ax = 40+Math.random()*(c.width-80); ay = 40+Math.random()*(c.height-80); tries++; }
      while(Math.hypot(ax-mx, ay-my)<120 && tries<15);
      this.x=ax; this.y=ay;
      this.angle = Math.random()*Math.PI*2;
      this.speed = 1.3;
      this.phase = Math.random()*Math.PI*2;
      this.turnCooldown = 0;
      this.sz = 10;
    }
    update(dt, spd){
      this.turnCooldown -= dt;
      if(this.turnCooldown <= 0){
        this.angle += (Math.random()-0.5)*1.8;
        this.turnCooldown = 0.4 + Math.random()*0.8;
      }
      this.x += Math.cos(this.angle)*spd;
      this.y += Math.sin(this.angle)*spd;
      this.phase += dt*14;
      const p=14;
      if(this.x<p){this.x=p; this.angle=Math.atan2(Math.sin(this.angle),-Math.cos(this.angle));}
      if(this.x>c.width-p){this.x=c.width-p; this.angle=Math.atan2(Math.sin(this.angle),-Math.cos(this.angle));}
      if(this.y<p){this.y=p; this.angle=Math.atan2(-Math.sin(this.angle),Math.cos(this.angle));}
      if(this.y>c.height-p){this.y=c.height-p; this.angle=Math.atan2(-Math.sin(this.angle),Math.cos(this.angle));}
    }
    draw(){
      x.save(); x.translate(this.x, this.y); x.rotate(this.angle+Math.PI/2);
      const s=this.sz, ph=this.phase;
      x.strokeStyle='#1a1205'; x.lineWidth=1.1;
      const lpos=[-0.32, 0, 0.32];
      for(let side=-1;side<=1;side+=2){
        for(let i=0;i<3;i++){
          const lx=side*s*0.32, ly=lpos[i]*s;
          const wag=Math.sin(ph+i*2.1)*0.5;
          const ex=lx+side*Math.cos(0.9+wag)*s*0.85;
          const ey=ly+Math.sin(0.9+wag)*s*0.7;
          x.beginPath(); x.moveTo(lx,ly); x.lineTo(ex,ey); x.stroke();
        }
      }
      x.fillStyle='#140e02';
      x.beginPath(); x.ellipse(0, s*0.52, s*0.42, s*0.62, 0, 0, Math.PI*2); x.fill();
      x.fillStyle='#251804';
      x.beginPath(); x.ellipse(0, -s*0.04, s*0.27, s*0.3, 0, 0, Math.PI*2); x.fill();
      x.fillStyle='#140e02';
      x.beginPath(); x.ellipse(0, -s*0.62, s*0.3, s*0.27, 0, 0, Math.PI*2); x.fill();
      x.fillStyle='#ffe44a';
      x.beginPath(); x.arc(-s*0.11,-s*0.67,s*0.07,0,Math.PI*2); x.arc(s*0.11,-s*0.67,s*0.07,0,Math.PI*2); x.fill();
      x.strokeStyle='#140e02'; x.lineWidth=0.9;
      const at=[[-.1,-.88,-.48,-1.38],[.1,-.88,.48,-1.38]];
      for(const [x1,y1,x2,y2] of at){
        x.beginPath(); x.moveTo(x1*s,y1*s); x.lineTo(x2*s,y2*s); x.stroke();
        x.fillStyle='#140e02'; x.beginPath(); x.arc(x2*s,y2*s,s*.08,0,Math.PI*2); x.fill();
      }
      x.restore();
    }
    hits(bx,by){ return Math.hypot(this.x-bx, this.y-(by+28)) < 28; }
  }

  function drawBoot(bx,by){
    x.save(); x.translate(bx, by);
    const w=44, h=62;
    x.fillStyle='#6b3010';
    x.beginPath();
    x.moveTo(-w*.33,-h*.5); x.lineTo(w*.33,-h*.5);
    x.lineTo(w*.36,-h*.06); x.lineTo(w*.44, h*.14);
    x.lineTo(-w*.44, h*.14); x.lineTo(-w*.36,-h*.06);
    x.closePath(); x.fill();
    x.fillStyle='#7a3818';
    x.beginPath();
    x.moveTo(-w*.44, h*.14); x.lineTo(w*.44, h*.14);
    x.lineTo(w*.58, h*.34); x.lineTo(w*.48, h*.5);
    x.lineTo(-w*.48, h*.5); x.lineTo(-w*.44, h*.14);
    x.closePath(); x.fill();
    x.fillStyle='#2e1408';
    x.beginPath();
    x.moveTo(-w*.5, h*.46); x.lineTo(w*.55, h*.46);
    x.lineTo(w*.62, h*.56); x.lineTo(-w*.5, h*.56);
    x.closePath(); x.fill();
    x.fillStyle='#b8916a';
    x.fillRect(-w*.06, -h*.5, w*.12, h*.08);
    x.strokeStyle='#d4b48c'; x.lineWidth=1.4;
    for(let i=0;i<4;i++){
      const ly=-h*.38+i*11;
      x.beginPath(); x.moveTo(-w*.23,ly); x.lineTo(w*.23,ly); x.stroke();
      x.fillStyle='#a07840';
      x.beginPath(); x.arc(-w*.26,ly,2,0,Math.PI*2); x.arc(w*.26,ly,2,0,Math.PI*2); x.fill();
    }
    x.strokeStyle='#4a2008'; x.lineWidth=2;
    x.beginPath(); x.moveTo(-w*.33,-h*.5); x.lineTo(w*.33,-h*.5); x.stroke();
    x.restore();
  }

  function drawBG(){
    x.fillStyle='#5a9e3f'; x.fillRect(0,0,c.width,c.height);
    x.fillStyle='#4e8e35';
    for(let i=0;i<40;i++){
      const gx=(i*137.5)%c.width, gy=(i*91.3)%c.height;
      x.fillRect(gx,gy,12,18);
    }
    x.fillStyle='#68b14a';
    for(let i=0;i<25;i++){
      const gx=(i*173.7)%c.width, gy=(i*119.1)%c.height;
      x.fillRect(gx,gy,6,12);
    }
    for(const d of decor){
      const dx=d.x*c.width, dy=d.y*c.height;
      if(d.t===0){
        x.fillStyle=d.c;
        for(let p=0;p<5;p++){
          const a=p*Math.PI*2/5;
          x.beginPath(); x.ellipse(dx+Math.cos(a)*d.r*1.6, dy+Math.sin(a)*d.r*1.6, d.r, d.r*.7, a, 0, Math.PI*2); x.fill();
        }
        x.fillStyle='#ffe500'; x.beginPath(); x.arc(dx,dy,d.r*.6,0,Math.PI*2); x.fill();
      } else if(d.t===1){
        x.fillStyle='rgba(0,0,0,0.12)';
        x.beginPath(); x.ellipse(dx,dy,d.r*1.4,d.r*.8,0,0,Math.PI*2); x.fill();
      } else {
        x.fillStyle='rgba(0,80,0,0.2)';
        x.beginPath(); x.arc(dx,dy,d.r,0,Math.PI*2); x.fill();
      }
    }
  }

  function drawHUD(){
    x.fillStyle='rgba(0,0,0,0.42)';
    x.fillRect(12,12,154,52);
    x.fillStyle='#fff'; x.font='bold 13px sans-serif'; x.textAlign='left';
    x.fillText('Time: '+Math.floor(score)+'s', 24, 34);
    x.fillText('Ants: '+ants.length, 24, 55);
  }

  function drawOverlay(lines, subLines, btnText){
    x.fillStyle='rgba(0,0,0,0.58)'; x.fillRect(0,0,c.width,c.height);
    const cx=c.width/2, cy=c.height/2;
    x.fillStyle='#fff'; x.textAlign='center';
    x.font='bold 26px sans-serif'; x.fillText(lines[0], cx, cy-55);
    if(lines[1]){ x.font='bold 20px sans-serif'; x.fillStyle='#ffdddd'; x.fillText(lines[1], cx, cy-22); }
    x.fillStyle='#ccc'; x.font='15px sans-serif';
    for(let i=0;i<subLines.length;i++) x.fillText(subLines[i], cx, cy+12+i*24);
    x.fillStyle='#7eff7e'; x.font='bold 16px sans-serif'; x.fillText(btnText, cx, cy+18+subLines.length*24+28);
  }

  function spawnAnt(){
    if(ants.length >= 14) return;
    ants.push(new Ant());
  }

  function startGame(){
    score=0; ants=[]; antTimer=0; flashAlpha=0;
    for(let i=0;i<3;i++) spawnAnt();
    state='playing';
  }

  wrap.addEventListener('mousemove', e=>{
    const r=c.getBoundingClientRect();
    mx=(e.clientX-r.left)*(c.width/r.width);
    my=(e.clientY-r.top)*(c.height/r.height);
    fvis=true;
    if(state==='start') startGame();
    else if(state==='gameover') startGame();
  });
  wrap.addEventListener('touchmove', e=>{
    e.preventDefault();
    const r=c.getBoundingClientRect();
    mx=(e.touches[0].clientX-r.left)*(c.width/r.width);
    my=(e.touches[0].clientY-r.top)*(c.height/r.height);
    fvis=true;
    if(state==='start') startGame();
    else if(state==='gameover') startGame();
  },{passive:false});
  wrap.addEventListener('click', ()=>{ if(state==='gameover') startGame(); });

  function loop(ts){
    const dt=Math.min((ts-lastT)/1000, 0.05);
    lastT=ts;
    drawBG();

    if(state==='start'){
      drawOverlay(
        ["Don't Step on the Ant!"],
        ["move your mouse to control the boot","ants keep coming - how long can you last?"],
        "move mouse here to start"
      );
    } else if(state==='playing'){
      score+=dt; antTimer+=dt;
      const interval=Math.max(2.5, 6-Math.floor(score/20)*0.5);
      if(antTimer>=interval){ spawnAnt(); antTimer=0; }
      const spd=1.4+score*0.015;
      for(const a of ants) a.update(dt, spd);
      for(const a of ants) a.draw();
      if(fvis) drawBoot(mx,my);
      if(fvis){
        for(const a of ants){
          if(a.hits(mx,my)){
            state='gameover'; flashAlpha=1; break;
          }
        }
      }
      if(flashAlpha>0){
        x.fillStyle=`rgba(220,0,0,${flashAlpha})`;
        x.fillRect(0,0,c.width,c.height);
        flashAlpha=Math.max(0,flashAlpha-dt*4);
      }
      drawHUD();
    } else {
      if(flashAlpha>0){
        x.fillStyle=`rgba(180,0,0,${flashAlpha*.4})`;
        x.fillRect(0,0,c.width,c.height);
        flashAlpha=Math.max(0,flashAlpha-dt*2);
      }
      for(const a of ants) a.draw();
      if(fvis) drawBoot(mx,my);
      drawOverlay(
        ["You stepped on an ant!","SQUISH"],
        ["survived: "+Math.floor(score)+" seconds","ants on screen: "+ants.length],
        "move mouse or click to try again"
      );
    }
    requestAnimationFrame(loop);
  }
  requestAnimationFrame(loop);
})();
// {% endraw %}
</script>

---

## Music Maker Studio

<link href="https://fonts.googleapis.com/css2?family=Bebas+Neue&family=Space+Mono:ital,wght@0,400;0,700;1,400&display=swap" rel="stylesheet">
<style>
.mm *{box-sizing:border-box;margin:0;padding:0}
.mm {
  --pink:#ff2d8e;--cyan:#00e5ff;--yellow:#ffe600;--green:#39ff14;--orange:#ff6b35;
  --bg:#09090f;--panel:#111118;--border:#202030;--text:#f0e6ff;--muted:#6666aa;
  background:var(--bg);color:var(--text);
  font-family:'Space Mono',monospace;
  display:flex;flex-direction:column;
  align-items:center;padding:28px 16px 60px;
  border-radius: 12px;
}

/* HEADER */
.mm .logo{
  font-family:'Bebas Neue',sans-serif;font-size:clamp(3rem,10vw,5.5rem);
  letter-spacing:6px;text-align:center;
  background:linear-gradient(135deg,var(--pink) 0%,var(--cyan) 100%);
  -webkit-background-clip:text;-webkit-text-fill-color:transparent;background-clip:text;
  line-height:1;
}
.mm .tagline{font-size:10px;letter-spacing:4px;color:var(--muted);text-align:center;margin:6px 0 28px;text-transform:uppercase}

/* STEP DOTS */
.mm .dots{display:flex;gap:10px;justify-content:center;align-items:center;margin-bottom:32px}
.mm .dot{
  width:10px;height:10px;border-radius:50%;
  background:#222;border:2px solid #333;transition:all .35s;
}
.mm .dot.active{background:var(--pink);border-color:var(--pink);box-shadow:0 0 10px rgba(255,45,142,.6)}
.mm .dot.done{background:var(--cyan);border-color:var(--cyan)}
.mm .dot-connector{width:28px;height:2px;background:#222;transition:background .35s}
.mm .dot-connector.done{background:var(--cyan)}

/* PANELS */
.mm .panel{
  background:var(--panel);border:1px solid var(--border);border-radius:20px;
  padding:32px 28px;max-width:680px;width:100%;
  display:none;animation:mmSlideIn .4s cubic-bezier(.22,.68,0,1.2);
}
.mm .panel.active{display:block}
@keyframes mmSlideIn{from{opacity:0;transform:translateY(16px)}to{opacity:1;transform:translateY(0)}}

.mm .step-label{font-size:10px;letter-spacing:3px;color:var(--pink);margin-bottom:6px;text-transform:uppercase}
.mm .step-title{
  font-family:'Bebas Neue',sans-serif;font-size:clamp(2rem,6vw,3rem);
  letter-spacing:2px;margin-bottom:24px;color:var(--text);line-height:1;
}
.mm .hint{font-size:10px;color:#444460;letter-spacing:.5px;margin:8px 0 20px}

/* INPUTS */
.mm input[type=text]{
  background:#06060d;border:2px solid var(--border);border-radius:12px;
  color:var(--text);font-family:'Space Mono',monospace;font-size:1rem;
  padding:14px 18px;width:100%;outline:none;transition:border-color .2s;
  margin-bottom:10px;
}
.mm input[type=text]:focus{border-color:var(--pink)}
.mm input[type=text]::placeholder{color:#33334a}

/* TOGGLE GROUP */
.mm .toggle-group{display:flex;gap:8px;margin-bottom:24px;flex-wrap:wrap}
.mm .tog{
  padding:10px 22px;border-radius:10px;border:2px solid var(--border);
  background:transparent;color:var(--muted);
  font-family:'Space Mono',monospace;font-size:11px;
  cursor:pointer;transition:all .2s;letter-spacing:1px;
}
.mm .tog.on{border-color:var(--cyan);color:var(--cyan);background:rgba(0,229,255,.08)}

/* BUTTONS */
.mm .btn-main{
  background:var(--pink);color:#fff;border:none;border-radius:12px;
  font-family:'Bebas Neue',sans-serif;font-size:1.3rem;letter-spacing:2px;
  padding:14px 40px;cursor:pointer;transition:all .2s;margin-top:4px;
  display:inline-block;
}
.mm .btn-main:hover{background:#ff55aa;transform:translateY(-2px);box-shadow:0 6px 24px rgba(255,45,142,.35)}
.mm .btn-main:disabled{background:#1e1e2e;color:#444460;cursor:not-allowed;transform:none;box-shadow:none}

.mm .btn-ghost{
  background:transparent;color:var(--cyan);border:2px solid var(--cyan);
  border-radius:10px;font-family:'Space Mono',monospace;font-size:11px;
  padding:10px 18px;cursor:pointer;transition:all .2s;letter-spacing:1px;
  margin-bottom:16px;
}
.mm .btn-ghost:hover{background:rgba(0,229,255,.1)}

.mm .btn-play{
  background:var(--green);color:#000;border:none;border-radius:10px;
  font-family:'Bebas Neue',sans-serif;font-size:1.15rem;letter-spacing:2px;
  padding:12px 28px;cursor:pointer;transition:all .2s;flex-shrink:0;
}
.mm .btn-play:hover{background:#66ff44}
.mm .btn-play.stop{background:var(--pink);color:#fff}

/* BEAT MAKER */
.mm .beat-controls{display:flex;align-items:center;gap:16px;margin-bottom:20px;flex-wrap:wrap}
.mm .bpm-wrap{display:flex;align-items:center;gap:10px;font-size:10px;color:var(--muted);letter-spacing:1px}
.mm input[type=range]{
  appearance:none;-webkit-appearance:none;height:4px;border-radius:2px;
  background:#222;outline:none;width:110px;cursor:pointer;
}
.mm input[type=range]::-webkit-slider-thumb{
  -webkit-appearance:none;width:16px;height:16px;border-radius:50%;
  background:var(--pink);cursor:pointer;
}
.mm .bpm-val{font-family:'Bebas Neue',sans-serif;font-size:1.2rem;color:var(--pink);min-width:36px}

.mm .beat-grid{display:flex;flex-direction:column;gap:10px;margin-bottom:10px}
.mm .beat-row{display:flex;align-items:center;gap:8px}
.mm .inst-label{font-size:9px;letter-spacing:1px;color:var(--muted);width:48px;flex-shrink:0;text-transform:uppercase}
.mm .steps-wrap{display:flex;gap:4px;flex:1}
.mm .sgap{width:6px;flex-shrink:0}

.mm .stp{
  flex:1;height:38px;border-radius:7px;border:2px solid var(--border);
  background:#0c0c18;cursor:pointer;transition:all .12s;
  min-width:0;position:relative;overflow:hidden;
}
.mm .stp:hover{border-color:#444}
.mm .stp.on.kick  {border-color:var(--orange);background:rgba(255,107,53,.22)}
.mm .stp.on.snare {border-color:var(--pink); background:rgba(255,45,142,.22)}
.mm .stp.on.hihat {border-color:var(--yellow);background:rgba(255,230,0,.15)}
.mm .stp.on.bass  {border-color:var(--green); background:rgba(57,255,20,.15)}
.mm .stp.cursor{outline:2px solid rgba(255,255,255,.7);outline-offset:-2px}

/* RELEASE */
.mm .release-wrap{text-align:center}
.mm .radio-anim{display:flex;align-items:flex-end;justify-content:center;gap:3px;height:56px;margin:16px 0}
.mm .rbar{
  width:5px;border-radius:3px;background:var(--pink);
  animation:mmRwave ease-in-out infinite;
}
@keyframes mmRwave{0%,100%{height:6px}50%{height:var(--h,30px)}}

.mm .msg-feed{margin:12px 0}
.mm .msg{
  font-family:'Bebas Neue',sans-serif;font-size:1.15rem;letter-spacing:2px;
  margin:6px 0;animation:mmPopIn .4s cubic-bezier(.22,.68,0,1.2);
}
@keyframes mmPopIn{from{opacity:0;transform:scale(.85)}to{opacity:1;transform:scale(1)}}

/* HOT 100 CHART */
.mm .chart-box{
  background:#06060d;border:1px solid var(--border);border-radius:14px;
  padding:20px 18px;margin-top:20px;text-align:left;
}
.mm .chart-hdr{
  font-family:'Bebas Neue',sans-serif;letter-spacing:4px;font-size:1rem;
  color:var(--muted);text-align:center;margin-bottom:14px;
  border-bottom:1px solid var(--border);padding-bottom:12px;
}
.mm .chart-row{display:flex;align-items:center;gap:12px;padding:9px 0;border-bottom:1px solid rgba(32,32,48,.7)}
.mm .chart-row:last-child{border-bottom:none}
.mm .rank{
  font-family:'Bebas Neue',sans-serif;font-size:1.5rem;color:var(--muted);
  width:30px;text-align:center;flex-shrink:0;
}
.mm .rank.gold{color:var(--yellow);font-size:2rem;text-shadow:0 0 12px rgba(255,230,0,.5)}
.mm .song-info{flex:1;min-width:0}
.mm .sname{font-size:12px;font-weight:700;color:var(--text);white-space:nowrap;overflow:hidden;text-overflow:ellipsis}
.mm .sname.gold{color:var(--yellow)}
.mm .sartist{font-size:10px;color:var(--muted);margin-top:2px}
.mm .badge{
  background:var(--yellow);color:#000;font-size:9px;font-weight:700;
  letter-spacing:1px;padding:3px 8px;border-radius:5px;flex-shrink:0;
}

/* STATS */
.mm .stats{display:flex;gap:12px;margin:20px 0;justify-content:center;flex-wrap:wrap}
.mm .stat-card{
  background:var(--panel);border:1px solid var(--border);border-radius:12px;
  padding:14px 20px;text-align:center;min-width:90px;
}
.mm .stat-n{font-family:'Bebas Neue',sans-serif;font-size:2rem;color:var(--cyan)}
.mm .stat-l{font-size:9px;letter-spacing:1px;color:var(--muted);text-transform:uppercase;margin-top:2px}

.mm .btn-restart{
  background:transparent;color:var(--muted);border:1px solid #333;
  border-radius:10px;font-family:'Space Mono',monospace;font-size:11px;
  padding:11px 22px;cursor:pointer;margin-top:16px;transition:all .2s;
}
.mm .btn-restart:hover{color:var(--text);border-color:#555}

/* CONFETTI */
.mm #mm-cfx{position:fixed;top:0;left:0;width:100%;height:100%;pointer-events:none;z-index:999}
.mm .cf{position:absolute;width:9px;height:9px;border-radius:2px;animation:mmCffall linear forwards}
@keyframes mmCffall{
  0%{transform:translateY(-10px) rotate(0deg);opacity:1}
  100%{transform:translateY(110vh) rotate(750deg);opacity:0}
}

/* vinyl decoration */
.mm .vinyl{
  width:70px;height:70px;border-radius:50%;
  background:repeating-radial-gradient(circle,#1a1a2a 0,#1a1a2a 3px,#111116 3px,#111116 6px);
  margin:0 auto 20px;position:relative;display:flex;align-items:center;justify-content:center;
  animation:mmSpin 4s linear infinite paused;
}
.mm .vinyl.playing{animation-play-state:running}
.mm .vinyl::after{content:'';width:12px;height:12px;border-radius:50%;background:var(--pink);position:absolute}

@keyframes mmSpin{to{transform:rotate(360deg)}}
</style>

<div class="mm">

<div class="logo">Music Maker</div>
<p class="tagline">From the studio to the charts</p>

<!-- STEP DOTS -->
<div class="dots">
  <div class="dot active" id="mm-d0"></div>
  <div class="dot-connector" id="mm-dc0"></div>
  <div class="dot" id="mm-d1"></div>
  <div class="dot-connector" id="mm-dc1"></div>
  <div class="dot" id="mm-d2"></div>
  <div class="dot-connector" id="mm-dc2"></div>
  <div class="dot" id="mm-d3"></div>
</div>

<!-- STEP 1: NAME YOUR SONG -->
<div class="panel active" id="mm-p0">
  <p class="step-label">Step 1 of 4</p>
  <h2 class="step-title">Name Your Music</h2>

  <p style="font-size:11px;color:var(--muted);margin-bottom:12px;letter-spacing:.5px">What are you dropping?</p>
  <div class="toggle-group" id="mm-type-group">
    <button class="tog on" onclick="mmSetType('Single',this)">Single</button>
    <button class="tog" onclick="mmSetType('Album',this)">Album</button>
    <button class="tog" onclick="mmSetType('EP',this)">EP</button>
    <button class="tog" onclick="mmSetType('Mixtape',this)">Mixtape</button>
  </div>

  <input type="text" id="mm-song-inp" maxlength="60" placeholder="Enter title here..." oninput="mmChk1()">
  <p class="hint">Make it iconic. Your title will be on the charts.</p>

  <button class="btn-main" id="mm-btn1" onclick="mmGotoStep(1)" disabled>Next: Build the Beat</button>
</div>

<!-- STEP 2: BEAT MAKER -->
<div class="panel" id="mm-p1">
  <p class="step-label">Step 2 of 4</p>
  <h2 class="step-title">Build Your Beat</h2>

  <div class="beat-controls">
    <button class="btn-play" id="mm-play-btn" onclick="mmTogglePlay()">PLAY</button>
    <div class="bpm-wrap">
      BPM
      <input type="range" id="mm-bpm-sl" min="60" max="180" value="95" oninput="mmSetBPM(this.value)">
      <span class="bpm-val" id="mm-bpm-v">95</span>
    </div>
    <button class="btn-ghost" onclick="mmClearBeat()" style="margin:0;padding:8px 14px;font-size:10px">CLEAR</button>
    <button class="btn-ghost" onclick="mmRandBeat()" style="margin:0;padding:8px 14px;font-size:10px;border-color:var(--orange);color:var(--orange)">SHUFFLE</button>
  </div>

  <div class="beat-grid">
    <div class="beat-row"><span class="inst-label">Kick</span><div class="steps-wrap" id="mm-g-kick"></div></div>
    <div class="beat-row"><span class="inst-label">Snare</span><div class="steps-wrap" id="mm-g-snare"></div></div>
    <div class="beat-row"><span class="inst-label">Hi-Hat</span><div class="steps-wrap" id="mm-g-hihat"></div></div>
    <div class="beat-row"><span class="inst-label">Bass</span><div class="steps-wrap" id="mm-g-bass"></div></div>
  </div>
  <p class="hint">Click steps to toggle them on/off. Hit PLAY to hear your beat live!</p>

  <button class="btn-main" onclick="mmGotoStep(2)">Next: Create Your Name</button>
</div>

<!-- STEP 3: ARTIST NAME -->
<div class="panel" id="mm-p2">
  <p class="step-label">Step 3 of 4</p>
  <h2 class="step-title">Your Artist Name</h2>

  <div class="vinyl" id="mm-vinyl"></div>

  <input type="text" id="mm-artist-inp" maxlength="40" placeholder="Your stage name..." oninput="mmChk3()">
  <button class="btn-ghost" onclick="mmRandName()">RANDOMIZE NAME</button>

  <p class="hint">This is how the world will know you. Choose wisely.</p>

  <button class="btn-main" id="mm-btn3" onclick="mmGotoStep(3)" disabled>Release It!</button>
</div>

<!-- STEP 4: RELEASE -->
<div class="panel" id="mm-p3">
  <p class="step-label">Step 4 of 4</p>
  <div class="release-wrap">
    <div id="mm-broadcast">
      <p class="msg" style="color:var(--cyan);font-size:1rem" id="mm-msg0">SENDING TO ALL STATIONS...</p>
      <div class="radio-anim" id="mm-radio-anim"></div>
      <div class="msg-feed" id="mm-msg-feed"></div>
    </div>

    <div id="mm-chart-section" style="display:none">
      <div class="chart-box">
        <div class="chart-hdr">HOT 100 - WEEK OF MARCH 29, 2026</div>
        <div id="mm-chart-rows"></div>
      </div>
      <div class="stats" id="mm-stats"></div>
      <button class="btn-restart" onclick="mmRestart()">Make Another Song</button>
    </div>
  </div>
</div>

<div id="mm-cfx"></div>

</div>

<script>
// {% raw %}
(function(){
let mmStep=0, mmSongType='Single', mmPlaying=false, mmCursor=0, mmBpm=95, mmBeatTimer=null, mmAudioCtx=null;
const MM_STEPS=16;
const mmGrid={kick:[], snare:[], hihat:[], bass:[]};

const MM_DEF={
  kick: [1,0,0,0,1,0,0,0,1,0,0,0,1,0,0,0],
  snare:[0,0,0,0,1,0,0,0,0,0,0,0,1,0,0,0],
  hihat:[1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,1],
  bass: [1,0,0,1,0,0,1,0,0,1,0,0,0,1,0,0]
};

function mmAc(){
  if(!mmAudioCtx) mmAudioCtx=new(window.AudioContext||window.webkitAudioContext)();
  return mmAudioCtx;
}

function mmKick(t){
  const c=mmAc(),o=c.createOscillator(),g=c.createGain();
  o.connect(g);g.connect(c.destination);
  o.frequency.setValueAtTime(160,t);
  o.frequency.exponentialRampToValueAtTime(0.01,t+0.4);
  g.gain.setValueAtTime(1.2,t);
  g.gain.exponentialRampToValueAtTime(0.001,t+0.45);
  o.start(t);o.stop(t+0.45);
}
function mmNoise(t,dur,vol,hi){
  const c=mmAc(),n=Math.floor(c.sampleRate*dur),buf=c.createBuffer(1,n,c.sampleRate),d=buf.getChannelData(0);
  for(let i=0;i<n;i++) d[i]=Math.random()*2-1;
  const src=c.createBufferSource();src.buffer=buf;
  const g=c.createGain();g.gain.setValueAtTime(vol,t);g.gain.exponentialRampToValueAtTime(0.001,t+dur);
  const f=c.createBiquadFilter();f.type='highpass';f.frequency.value=hi;
  src.connect(f);f.connect(g);g.connect(c.destination);
  src.start(t);src.stop(t+dur);
}
function mmSnare(t){mmNoise(t,0.14,0.75,900)}
function mmHihat(t){mmNoise(t,0.05,0.35,7000)}
function mmBass(t){
  const c=mmAc(),o=c.createOscillator(),g=c.createGain();
  o.type='sine';o.frequency.setValueAtTime(55,t);
  g.gain.setValueAtTime(0.9,t);g.gain.exponentialRampToValueAtTime(0.001,t+0.28);
  o.connect(g);g.connect(c.destination);o.start(t);o.stop(t+0.28);
}
const mmPlayFn={kick:mmKick,snare:mmSnare,hihat:mmHihat,bass:mmBass};

function mmBuildGrid(){
  ['kick','snare','hihat','bass'].forEach(inst=>{
    mmGrid[inst]=Array(MM_STEPS).fill(false);
    const wrap=document.getElementById('mm-g-'+inst);
    wrap.innerHTML='';
    for(let i=0;i<MM_STEPS;i++){
      if(i===8){const sp=document.createElement('div');sp.className='sgap';wrap.appendChild(sp)}
      const b=document.createElement('button');
      b.className='stp '+inst+(MM_DEF[inst][i]?' on':'');
      b.dataset.i=i;b.dataset.inst=inst;
      mmGrid[inst][i]=!!MM_DEF[inst][i];
      b.onclick=()=>{mmGrid[inst][i]=!mmGrid[inst][i];b.classList.toggle('on');};
      wrap.appendChild(b);
    }
  });
}

function mmTick(){
  const t=mmAc().currentTime;
  if(mmGrid.kick[mmCursor]) mmKick(t);
  if(mmGrid.snare[mmCursor]) mmSnare(t);
  if(mmGrid.hihat[mmCursor]) mmHihat(t);
  if(mmGrid.bass[mmCursor]) mmBass(t);
  ['kick','snare','hihat','bass'].forEach(inst=>{
    document.querySelectorAll('#mm-g-'+inst+' .stp').forEach((b,j)=>b.classList.toggle('cursor',j===mmCursor));
  });
  mmCursor=(mmCursor+1)%MM_STEPS;
}

window.mmTogglePlay=function(){
  if(mmPlaying){
    clearInterval(mmBeatTimer);mmPlaying=false;mmCursor=0;
    document.getElementById('mm-play-btn').textContent='PLAY';
    document.getElementById('mm-play-btn').classList.remove('stop');
    document.getElementById('mm-vinyl').classList.remove('playing');
    ['kick','snare','hihat','bass'].forEach(inst=>
      document.querySelectorAll('#mm-g-'+inst+' .stp').forEach(b=>b.classList.remove('cursor')));
  } else {
    mmAc();mmPlaying=true;
    document.getElementById('mm-play-btn').textContent='STOP';
    document.getElementById('mm-play-btn').classList.add('stop');
    document.getElementById('mm-vinyl').classList.add('playing');
    mmTick();
    const ms=(60/mmBpm/2)*1000;
    mmBeatTimer=setInterval(mmTick,ms);
  }
};

window.mmSetBPM=function(v){
  mmBpm=parseInt(v);document.getElementById('mm-bpm-v').textContent=v;
  if(mmPlaying){clearInterval(mmBeatTimer);mmBeatTimer=setInterval(mmTick,(60/mmBpm/2)*1000)}
};

window.mmClearBeat=function(){
  ['kick','snare','hihat','bass'].forEach(inst=>{
    mmGrid[inst].fill(false);
    document.querySelectorAll('#mm-g-'+inst+' .stp').forEach(b=>b.classList.remove('on'));
  });
};

window.mmRandBeat=function(){
  ['kick','snare','hihat','bass'].forEach(inst=>{
    document.querySelectorAll('#mm-g-'+inst+' .stp').forEach((b,i)=>{
      const on=Math.random()>.6;
      mmGrid[inst][i]=on;
      b.classList.toggle('on',on);
    });
  });
};

window.mmSetType=function(t,el){
  mmSongType=t;
  document.querySelectorAll('#mm-type-group .tog').forEach(b=>b.classList.remove('on'));
  el.classList.add('on');
};

window.mmChk1=function(){document.getElementById('mm-btn1').disabled=!document.getElementById('mm-song-inp').value.trim()};
window.mmChk3=function(){document.getElementById('mm-btn3').disabled=!document.getElementById('mm-artist-inp').value.trim()};

window.mmGotoStep=function(n){
  if(mmPlaying) mmTogglePlay();
  document.getElementById('mm-p'+mmStep).classList.remove('active');
  document.getElementById('mm-d'+mmStep).classList.remove('active');
  document.getElementById('mm-d'+mmStep).classList.add('done');
  if(mmStep<3) document.getElementById('mm-dc'+mmStep).classList.add('done');
  mmStep=n;
  document.getElementById('mm-p'+n).classList.add('active');
  document.getElementById('mm-d'+n).classList.add('active');
  if(n===3) mmStartRelease();
};

const mmAdj=['Midnight','Solar','Crystal','Neon','Thunder','Velvet','Cosmic','Diamond','Electric','Golden','Shadow','Silver','Blazing','Lunar','Atomic','Crimson','Spectral','Vintage','Wild','Cloud'];
const mmNou=['Echo','Wave','Drift','Pulse','Storm','Haze','Nova','Lynx','Vibe','Rush','Glow','Surge','Fox','Blaze','Frost','Rave','Clash','Lux','Arc','Zeal'];
const mmPfx=['MC ','DJ ','The ',''];

window.mmRandName=function(){
  const a=mmAdj[Math.floor(Math.random()*mmAdj.length)];
  const b=mmNou[Math.floor(Math.random()*mmNou.length)];
  const p=mmPfx[Math.floor(Math.random()*mmPfx.length)];
  const styles=[p+a,a+' '+b,a+b,p+b,a+' '+a.slice(-3).toLowerCase()+b];
  const name=styles[Math.floor(Math.random()*styles.length)];
  document.getElementById('mm-artist-inp').value=name;
  mmChk3();
};

const MM_HOT100=[
  {song:"Opalite",artist:"Taylor Swift"},
  {song:"DTMF",artist:"Bad Bunny"},
  {song:"Choosin' Texas",artist:"Ella Langley"},
  {song:"Aperture",artist:"Harry Styles"},
  {song:"I Just Might",artist:"Bruno Mars"},
  {song:"The Fate of Ophelia",artist:"Taylor Swift"},
  {song:"Beautiful Things",artist:"Benson Boone"},
  {song:"Die With A Smile",artist:"Lady Gaga & Bruno Mars"},
  {song:"A Bar Song (Tipsy)",artist:"Shaboozey"},
  {song:"Too Sweet",artist:"Hozier"},
];

function mmStartRelease(){
  const song=document.getElementById('mm-song-inp').value.trim();
  const artist=document.getElementById('mm-artist-inp').value.trim();

  const ra=document.getElementById('mm-radio-anim');ra.innerHTML='';
  for(let i=0;i<22;i++){
    const b=document.createElement('div');b.className='rbar';
    const h=6+Math.random()*48;
    b.style.setProperty('--h',h+'px');
    b.style.animationDuration=(0.35+Math.random()*.5)+'s';
    b.style.animationDelay=(Math.random()*.4)+'s';
    ra.appendChild(b);
  }

  const feed=document.getElementById('mm-msg-feed');feed.innerHTML='';
  const msgs=[
    {t:900, text:'SIGNAL LOCKED IN', color:'var(--cyan)'},
    {t:1900, text:'500+ STATIONS TUNED IN', color:'var(--cyan)'},
    {t:3000, text:'REQUESTS ARE FLOODING IN!', color:'var(--yellow)'},
    {t:4200, text:'IT\'S OFFICIAL... YOU\'RE A HIT!', color:'var(--green)'},
    {t:5400, text:'"'+song.toUpperCase()+'" HITS #1!', color:'var(--pink)'},
  ];
  msgs.forEach(m=>setTimeout(()=>{
    const d=document.createElement('p');d.className='msg';d.style.color=m.color;d.textContent=m.text;
    feed.appendChild(d);feed.scrollTop=feed.scrollHeight;
  },m.t));

  setTimeout(()=>mmShowChart(song,artist),6400);
  setTimeout(()=>mmSpawnConfetti(),6500);
}

function mmShowChart(song,artist){
  const cs=document.getElementById('mm-chart-section');cs.style.display='block';
  cs.style.animation='mmSlideIn .5s cubic-bezier(.22,.68,0,1.2)';

  const rows=document.getElementById('mm-chart-rows');rows.innerHTML='';

  const r1=document.createElement('div');r1.className='chart-row';
  r1.innerHTML='<div class="rank gold">1</div><div class="song-info"><div class="sname gold">"'+song+'"</div><div class="sartist">'+artist+' &bull; '+mmSongType+'</div></div><span class="badge">#1 HIT</span>';
  rows.appendChild(r1);

  MM_HOT100.forEach((e,i)=>{
    const r=document.createElement('div');r.className='chart-row';
    r.innerHTML='<div class="rank">'+(i+2)+'</div><div class="song-info"><div class="sname">"'+e.song+'"</div><div class="sartist">'+e.artist+'</div></div>';
    rows.appendChild(r);
  });

  const statsEl=document.getElementById('mm-stats');
  const streams=Math.floor(80+Math.random()*220);
  const plays=Math.floor(300+Math.random()*700);
  const fans=Math.floor(150+Math.random()*500);
  statsEl.innerHTML='<div class="stat-card"><div class="stat-n" id="mm-s1">0M</div><div class="stat-l">Streams</div></div><div class="stat-card"><div class="stat-n" id="mm-s2">0K</div><div class="stat-l">Radio Plays</div></div><div class="stat-card"><div class="stat-n" id="mm-s3">0K</div><div class="stat-l">New Fans</div></div>';
  mmCountUp('mm-s1',streams,'M');mmCountUp('mm-s2',plays,'K');mmCountUp('mm-s3',fans,'K');
}

function mmCountUp(id,target,suf){
  const el=document.getElementById(id);let v=0;
  const s=Math.ceil(target/28);
  const t=setInterval(()=>{v=Math.min(v+s,target);el.textContent=v+suf;if(v>=target)clearInterval(t)},45);
}

function mmSpawnConfetti(){
  const box=document.getElementById('mm-cfx');
  const cols=['#ff2d8e','#00e5ff','#ffe600','#39ff14','#ff6b35','#cc88ff','#ff8fff'];
  for(let i=0;i<80;i++){
    setTimeout(()=>{
      const c=document.createElement('div');c.className='cf';
      c.style.cssText='left:'+Math.random()*100+'%;top:-10px;background:'+cols[i%cols.length]+';animation-duration:'+(2+Math.random()*2.5)+'s;transform:rotate('+Math.random()*360+'deg)';
      box.appendChild(c);
      setTimeout(()=>c.remove(),5000);
    },i*35);
  }
}

window.mmRestart=function(){
  if(mmPlaying) mmTogglePlay();
  mmStep=0;mmSongType='Single';mmCursor=0;
  document.getElementById('mm-song-inp').value='';
  document.getElementById('mm-artist-inp').value='';
  document.getElementById('mm-btn1').disabled=true;
  document.getElementById('mm-btn3').disabled=true;
  document.querySelectorAll('#mm-type-group .tog').forEach((b,i)=>b.classList.toggle('on',i===0));
  document.getElementById('mm-chart-rows').innerHTML='';
  document.getElementById('mm-stats').innerHTML='';
  document.getElementById('mm-msg-feed').innerHTML='';
  document.getElementById('mm-msg0').textContent='SENDING TO ALL STATIONS...';
  document.getElementById('mm-chart-section').style.display='none';
  document.getElementById('mm-cfx').innerHTML='';
  for(let i=0;i<4;i++){
    document.getElementById('mm-d'+i).className='dot'+(i===0?' active':'');
    if(i<3) document.getElementById('mm-dc'+i).className='dot-connector';
  }
  document.querySelectorAll('.mm .panel').forEach((p,i)=>p.classList.toggle('active',i===0));
  ['kick','snare','hihat','bass'].forEach(inst=>{
    mmGrid[inst]=MM_DEF[inst].map(v=>!!v);
    document.querySelectorAll('#mm-g-'+inst+' .stp').forEach((b,i)=>b.classList.toggle('on',!!MM_DEF[inst][i]));
  });
  document.getElementById('mm-vinyl').classList.remove('playing');
};

mmBuildGrid();
})();
// {% endraw %}
</script>
