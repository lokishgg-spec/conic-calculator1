<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Кривые второго порядка — 2D</title>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600&display=swap" rel="stylesheet">
<style>
*{margin:0;padding:0;box-sizing:border-box;-webkit-tap-highlight-color:transparent;}
html,body{height:100%;overflow:hidden;}
body{font-family:'Inter',sans-serif;background:#f8faff;color:#1e293b;display:flex;flex-direction:column;height:100dvh;}

header{height:50px;background:#fff;border-bottom:1px solid #e2e8f0;display:flex;align-items:center;justify-content:space-between;padding:0 20px;flex-shrink:0;box-shadow:0 1px 4px rgba(0,0,0,.05);}
.logo{font-size:.95rem;font-weight:600;color:#1e293b;}
.logo span{color:#94a3b8;font-weight:400;}
.author-badge{font-size:.72rem;color:#64748b;background:#f1f5f9;border:1px solid #e2e8f0;padding:5px 13px;border-radius:20px;white-space:nowrap;}
.author-badge strong{color:#3b82f6;font-weight:600;}
.mob-btn{display:none;background:#f1f5f9;border:1px solid #e2e8f0;border-radius:8px;padding:7px 12px;font-size:.78rem;color:#374151;cursor:pointer;}
@media(max-width:700px){.mob-btn{display:block;}.author-badge{font-size:.62rem;padding:4px 8px;}}

.app{flex:1;display:flex;overflow:hidden;position:relative;}
.canvas-wrap{flex:1;position:relative;background:#fff;}
canvas{width:100%;height:100%;display:block;cursor:grab;}
canvas:active{cursor:grabbing;}

.panel{width:300px;min-width:280px;background:#fff;border-right:1px solid #e2e8f0;display:flex;flex-direction:column;overflow-y:auto;order:-1;}
@media(max-width:700px){
  .panel{position:absolute;bottom:0;left:0;right:0;width:100%;min-width:0;border-right:none;border-top:1px solid #e2e8f0;border-radius:18px 18px 0 0;height:75vh;transform:translateY(100%);transition:transform .3s ease;z-index:15;}
  .panel.open{transform:translateY(0);}
  .fab{display:flex!important;}
}
.drag-bar{width:36px;height:4px;background:#cbd5e1;border-radius:2px;margin:10px auto 4px;cursor:pointer;flex-shrink:0;}

.surf-tabs{display:flex;border-bottom:1px solid #e2e8f0;background:#f8faff;}
.surf-tab{flex:1;padding:12px 4px;font-size:.75rem;font-weight:500;color:#94a3b8;background:transparent;border:none;cursor:pointer;transition:all .15s;border-bottom:2px solid transparent;font-family:'Inter',sans-serif;text-align:center;line-height:1.5;}
.surf-tab:hover{color:#475569;background:#f1f5f9;}
.surf-tab.active{color:#3b82f6;border-bottom-color:#3b82f6;font-weight:600;background:#fff;}

.eq-section{padding:12px 16px;border-bottom:1px solid #e2e8f0;background:#f8faff;}
.eq-box{display:flex;align-items:center;gap:10px;background:#fff;border:1.5px solid #e2e8f0;border-radius:10px;padding:10px 13px;}
.eq-dot{width:13px;height:13px;border-radius:50%;flex-shrink:0;}
.eq-formula{font-size:.9rem;color:#1e293b;font-weight:500;}

.inputs-section{padding:14px 16px;flex:1;}
.sec-title{font-size:.62rem;text-transform:uppercase;letter-spacing:1.5px;color:#94a3b8;font-weight:600;margin-bottom:12px;}

.prop-grid{display:flex;flex-direction:column;gap:10px;}
.prop-row{display:flex;align-items:center;gap:10px;}
.prop-label{font-size:.82rem;color:#374151;font-weight:600;min-width:30px;}
.prop-desc{font-size:.7rem;color:#94a3b8;flex:1;}
.prop-input{width:90px;background:#f8faff;border:1.5px solid #e2e8f0;border-radius:8px;padding:8px 10px;color:#1e293b;font-family:'Inter',sans-serif;font-size:.9rem;outline:none;transition:all .2s;text-align:center;font-weight:600;}
.prop-input:focus{border-color:#3b82f6;background:#fff;box-shadow:0 0 0 3px rgba(59,130,246,.1);}

.build-btn{margin:14px 16px 16px;padding:12px;background:#3b82f6;color:#fff;border:none;border-radius:10px;font-family:'Inter',sans-serif;font-size:.88rem;font-weight:600;cursor:pointer;transition:all .15s;box-shadow:0 3px 10px rgba(59,130,246,.25);display:block;width:calc(100% - 32px);}
.build-btn:hover{background:#2563eb;transform:translateY(-1px);}
.build-btn:active{transform:none;}

.info-sec{padding:0 16px 16px;}
.info-chips{display:grid;grid-template-columns:1fr 1fr;gap:6px;}
.chip{background:#f8faff;border:1px solid #e2e8f0;border-radius:9px;padding:8px 10px;}
.chip-k{font-size:.58rem;color:#94a3b8;font-weight:500;margin-bottom:2px;}
.chip-v{font-size:.78rem;color:#1e293b;font-weight:700;}

/* OVERLAYS */
.badge2d{position:absolute;top:12px;left:12px;background:rgba(255,255,255,.96);border:1px solid #e2e8f0;border-radius:10px;padding:8px 13px;pointer-events:none;box-shadow:0 2px 8px rgba(0,0,0,.07);}
.b2-name{font-size:.75rem;color:#1e293b;font-weight:700;display:block;margin-bottom:1px;}
.b2-eq{font-size:.65rem;color:#64748b;}

/* SCALE INDICATOR */
.scale-indicator{position:absolute;bottom:14px;left:14px;background:rgba(255,255,255,.93);border:1px solid #e2e8f0;border-radius:8px;padding:6px 12px;font-size:.65rem;color:#64748b;pointer-events:none;box-shadow:0 1px 4px rgba(0,0,0,.05);font-family:'Inter',monospace;}

.hint2d{position:absolute;bottom:14px;right:14px;background:rgba(255,255,255,.92);border:1px solid #e2e8f0;border-radius:9px;padding:7px 12px;font-size:.6rem;color:#94a3b8;pointer-events:none;line-height:2;}
@media(max-width:700px){.hint2d{display:none;}}

.fab{display:none;position:absolute;bottom:18px;left:50%;transform:translateX(-50%);background:#3b82f6;border:none;color:#fff;padding:13px 30px;border-radius:30px;font-family:'Inter',sans-serif;font-size:.85rem;font-weight:600;cursor:pointer;z-index:4;box-shadow:0 6px 20px rgba(59,130,246,.4);touch-action:manipulation;white-space:nowrap;}
.fab.hidden{display:none!important;}
</style>
</head>
<body>

<header>
  <div class="logo">Кривые второго порядка <span>— 2D</span></div>
  <div class="author-badge">Сайт <strong>Кенжегулова Нурсултана</strong></div>
  <button class="mob-btn" id="mob-btn">☰ Меню</button>
</header>

<div class="app">
  <div class="canvas-wrap" id="canvas-wrap">
    <canvas id="cv"></canvas>
    <div class="badge2d">
      <span class="b2-name" id="bname">Эллипс</span>
      <span class="b2-eq" id="beq">x²/a² + y²/b² = 1</span>
    </div>
    <div class="scale-indicator" id="scale-ind">масштаб: 1 ед. = 1</div>
    <div class="hint2d">🖱 Колёсико — масштаб<br>🖱 Перетащи — перемещение</div>
    <button class="fab" id="fab">📐 Параметры</button>
  </div>

  <div class="panel" id="panel">
    <div class="drag-bar" id="drag-bar"></div>

    <div class="surf-tabs">
      <button class="surf-tab active" data-s="ellipse">🥚 Эллипс</button>
      <button class="surf-tab" data-s="hyperbola">〰️ Гипербола</button>
      <button class="surf-tab" data-s="parabola">📈 Парабола</button>
    </div>

    <div class="eq-section">
      <div class="eq-box">
        <div class="eq-dot" id="eq-dot" style="background:#3b82f6"></div>
        <div class="eq-formula" id="eq-formula">x²/a² + y²/b² = 1</div>
      </div>
    </div>

    <div class="inputs-section">
      <div class="sec-title">Параметры</div>
      <div class="prop-grid" id="prop-grid"></div>
    </div>

    <button class="build-btn" id="bbtn">▶ Построить</button>

    <div class="info-sec">
      <div class="info-chips" id="info-chips"></div>
    </div>
  </div>
</div>

<script>
// ── PANEL ──────────────────────────────────────
const panel=document.getElementById('panel'),fab=document.getElementById('fab');
document.getElementById('drag-bar').onclick=()=>{panel.classList.remove('open');fab.classList.remove('hidden');};
fab.onclick=()=>{panel.classList.add('open');fab.classList.add('hidden');};
document.getElementById('mob-btn').onclick=()=>{panel.classList.contains('open')?(panel.classList.remove('open'),fab.classList.remove('hidden')):(panel.classList.add('open'),fab.classList.add('hidden'));};

// ── CANVAS ─────────────────────────────────────
const canvas=document.getElementById('cv');
const ctx=canvas.getContext('2d');
const wrap=document.getElementById('canvas-wrap');
let W=0,H=0;
// scale = pixels per unit
let scale=60, panX=0, panY=0;

function resize(){W=wrap.clientWidth;H=wrap.clientHeight;canvas.width=W;canvas.height=H;draw();}
window.addEventListener('resize',resize);

// Pan
let isPan=false,lastPan={x:0,y:0};
canvas.addEventListener('mousedown',e=>{isPan=true;lastPan={x:e.clientX,y:e.clientY};});
window.addEventListener('mouseup',()=>isPan=false);
window.addEventListener('mousemove',e=>{if(!isPan)return;panX+=e.clientX-lastPan.x;panY+=e.clientY-lastPan.y;lastPan={x:e.clientX,y:e.clientY};draw();});

// Zoom — wide range so we get 0.001 level
canvas.addEventListener('wheel',e=>{
  const factor=e.deltaY>0?0.85:1.18;
  scale=Math.max(2,Math.min(8000,scale*factor));
  draw();e.preventDefault();
},{passive:false});

// Touch
let tc2={},ld=0;
canvas.addEventListener('touchstart',e=>{[...e.touches].forEach(t=>tc2[t.identifier]={x:t.clientX,y:t.clientY});if(e.touches.length===2){const dx=e.touches[0].clientX-e.touches[1].clientX,dy=e.touches[0].clientY-e.touches[1].clientY;ld=Math.sqrt(dx*dx+dy*dy);}e.preventDefault();},{passive:false});
canvas.addEventListener('touchmove',e=>{if(e.touches.length===1){const t=e.touches[0],p=tc2[t.identifier]||{x:t.clientX,y:t.clientY};panX+=t.clientX-p.x;panY+=t.clientY-p.y;tc2[t.identifier]={x:t.clientX,y:t.clientY};}else if(e.touches.length===2){const dx=e.touches[0].clientX-e.touches[1].clientX,dy=e.touches[0].clientY-e.touches[1].clientY,d=Math.sqrt(dx*dx+dy*dy);scale=Math.max(2,Math.min(8000,scale*(d/ld)));ld=d;[...e.touches].forEach(t=>tc2[t.identifier]={x:t.clientX,y:t.clientY});}draw();e.preventDefault();},{passive:false});
canvas.addEventListener('touchend',e=>{[...e.changedTouches].forEach(t=>delete tc2[t.identifier]);},{passive:false});

function toS(x,y){return{sx:W/2+panX+x*scale,sy:H/2+panY-y*scale};}
function fromS(sx,sy){return{x:(sx-W/2-panX)/scale,y:-(sy-H/2-panY)/scale};}

// ── SMART GRID STEP ────────────────────────────
// Pick a "nice" grid step so labels show 1, 0.5, 0.1, 0.01, 0.001 etc.
function niceStep(targetPixels){
  // We want grid lines roughly every targetPixels pixels
  const unitSize = scale; // pixels per 1 unit
  const rawStep = targetPixels / unitSize;
  // Round to nearest nice number: 1, 2, 5 * 10^n
  const mag = Math.pow(10, Math.floor(Math.log10(rawStep)));
  const frac = rawStep / mag;
  let nice;
  if(frac < 1.5) nice = 1;
  else if(frac < 3.5) nice = 2;
  else if(frac < 7.5) nice = 5;
  else nice = 10;
  return nice * mag;
}

function formatNum(n, step){
  // Format number with appropriate decimal places for the step
  const decimals = Math.max(0, -Math.floor(Math.log10(step)));
  return parseFloat(n.toFixed(decimals+1));
}

// ── DRAW GRID ──────────────────────────────────
function drawGrid(){
  ctx.clearRect(0,0,W,H);
  ctx.fillStyle='#fff';ctx.fillRect(0,0,W,H);

  const left=fromS(0,0).x, right=fromS(W,0).x;
  const top=fromS(0,0).y, bottom=fromS(0,H).y;

  // Choose step: minor ~60px apart, major ~5x that
  const minorStep = niceStep(60);
  const majorStep = minorStep * 5;

  // Minor grid
  ctx.strokeStyle='#f0f4ff';ctx.lineWidth=1;
  for(let x=Math.ceil(left/minorStep)*minorStep; x<=right+minorStep; x+=minorStep){
    const{sx}=toS(x,0);
    ctx.beginPath();ctx.moveTo(sx,0);ctx.lineTo(sx,H);ctx.stroke();
  }
  for(let y=Math.ceil(bottom/minorStep)*minorStep; y<=top+minorStep; y+=minorStep){
    const{sy}=toS(0,y);
    ctx.beginPath();ctx.moveTo(0,sy);ctx.lineTo(W,sy);ctx.stroke();
  }

  // Major grid
  ctx.strokeStyle='#e2e8f0';ctx.lineWidth=1;
  for(let x=Math.ceil(left/majorStep)*majorStep; x<=right+majorStep; x+=majorStep){
    const{sx}=toS(x,0);
    ctx.beginPath();ctx.moveTo(sx,0);ctx.lineTo(sx,H);ctx.stroke();
  }
  for(let y=Math.ceil(bottom/majorStep)*majorStep; y<=top+majorStep; y+=majorStep){
    const{sy}=toS(0,y);
    ctx.beginPath();ctx.moveTo(0,sy);ctx.lineTo(W,sy);ctx.stroke();
  }

  // ── AXES ──
  const o=toS(0,0);
  ctx.strokeStyle='#ef4444';ctx.lineWidth=2;
  ctx.beginPath();ctx.moveTo(0,o.sy);ctx.lineTo(W,o.sy);ctx.stroke();
  ctx.strokeStyle='#22c55e';ctx.lineWidth=2;
  ctx.beginPath();ctx.moveTo(o.sx,0);ctx.lineTo(o.sx,H);ctx.stroke();

  // Arrows
  ctx.fillStyle='#ef4444';ctx.beginPath();ctx.moveTo(W,o.sy);ctx.lineTo(W-13,o.sy-5);ctx.lineTo(W-13,o.sy+5);ctx.closePath();ctx.fill();
  ctx.fillStyle='#22c55e';ctx.beginPath();ctx.moveTo(o.sx,0);ctx.lineTo(o.sx-5,13);ctx.lineTo(o.sx+5,13);ctx.closePath();ctx.fill();
  ctx.font='bold 13px Inter,sans-serif';
  ctx.fillStyle='#ef4444';ctx.textAlign='left';ctx.fillText('x',W-22,o.sy-9);
  ctx.fillStyle='#22c55e';ctx.textAlign='left';ctx.fillText('y',o.sx+9,14);

  // ── TICK LABELS on major step ──
  ctx.font='10px Inter,sans-serif';ctx.fillStyle='#94a3b8';
  const decimals=Math.max(0,-Math.floor(Math.log10(majorStep)));

  ctx.textAlign='center';
  for(let x=Math.ceil(left/majorStep)*majorStep; x<=right+majorStep; x+=majorStep){
    if(Math.abs(x)<majorStep*0.01) continue;
    const{sx}=toS(x,0);
    if(sx<5||sx>W-5) continue;
    ctx.strokeStyle='#94a3b8';ctx.lineWidth=1;
    ctx.beginPath();ctx.moveTo(sx,o.sy-5);ctx.lineTo(sx,o.sy+5);ctx.stroke();
    ctx.fillText(x.toFixed(decimals),sx,o.sy+16);
  }
  ctx.textAlign='right';
  for(let y=Math.ceil(bottom/majorStep)*majorStep; y<=top+majorStep; y+=majorStep){
    if(Math.abs(y)<majorStep*0.01) continue;
    const{sy}=toS(0,y);
    if(sy<5||sy>H-5) continue;
    ctx.strokeStyle='#94a3b8';ctx.lineWidth=1;
    ctx.beginPath();ctx.moveTo(o.sx-5,sy);ctx.lineTo(o.sx+5,sy);ctx.stroke();
    ctx.fillText(y.toFixed(decimals),o.sx-8,sy+4);
  }
  ctx.textAlign='center';ctx.fillStyle='#64748b';
  const oz=toS(0,0);ctx.fillText('0',oz.sx-10,oz.sy+16);

  // ── SCALE INDICATOR ──
  const segUnits = majorStep;
  const segPx = segUnits * scale;
  // Bar
  const bx=16,by=H-40,bw=Math.min(segPx,120);
  ctx.strokeStyle='#64748b';ctx.lineWidth=2;
  ctx.beginPath();ctx.moveTo(bx,by);ctx.lineTo(bx+bw,by);ctx.stroke();
  ctx.beginPath();ctx.moveTo(bx,by-4);ctx.lineTo(bx,by+4);ctx.stroke();
  ctx.beginPath();ctx.moveTo(bx+bw,by-4);ctx.lineTo(bx+bw,by+4);ctx.stroke();
  ctx.font='11px Inter,sans-serif';ctx.fillStyle='#64748b';ctx.textAlign='left';
  ctx.fillText(segUnits.toFixed(decimals)+' ед.',bx,by-8);

  // Update scale indicator overlay
  document.getElementById('scale-ind').textContent='шаг сетки: '+majorStep.toFixed(decimals);
}

// ── DRAW HELPERS ───────────────────────────────
function drawCurve(points,color,lw=2.5){
  const segs=[];let seg=[];
  points.forEach(p=>{if(p===null){if(seg.length>1)segs.push(seg);seg=[];}else seg.push(p);});
  if(seg.length>1)segs.push(seg);
  ctx.strokeStyle=color;ctx.lineWidth=lw;ctx.lineJoin='round';ctx.lineCap='round';
  segs.forEach(s=>{
    ctx.beginPath();const p0=toS(s[0].x,s[0].y);ctx.moveTo(p0.sx,p0.sy);
    for(let i=1;i<s.length;i++){const p=toS(s[i].x,s[i].y);ctx.lineTo(p.sx,p.sy);}
    ctx.stroke();
  });
}
function drawDot(x,y,color,label='',r=5){
  const{sx,sy}=toS(x,y);
  ctx.beginPath();ctx.arc(sx,sy,r,0,Math.PI*2);ctx.fillStyle=color;ctx.fill();
  ctx.strokeStyle='#fff';ctx.lineWidth=1.5;ctx.stroke();
  if(label){ctx.font='bold 11px Inter,sans-serif';ctx.fillStyle=color;ctx.textAlign='left';ctx.fillText(label,sx+8,sy-5);}
}
function drawDash(x1,y1,x2,y2,color='#94a3b8'){
  const p1=toS(x1,y1),p2=toS(x2,y2);
  ctx.strokeStyle=color;ctx.lineWidth=1.5;ctx.setLineDash([5,4]);
  ctx.beginPath();ctx.moveTo(p1.sx,p1.sy);ctx.lineTo(p2.sx,p2.sy);ctx.stroke();
  ctx.setLineDash([]);
}
function drawVLine(x,color,label=''){
  const{sx}=toS(x,0);
  ctx.strokeStyle=color;ctx.lineWidth=1.5;ctx.setLineDash([5,4]);
  ctx.beginPath();ctx.moveTo(sx,0);ctx.lineTo(sx,H);ctx.stroke();
  ctx.setLineDash([]);
  if(label){ctx.font='11px Inter,sans-serif';ctx.fillStyle=color;ctx.textAlign='left';ctx.fillText(label,sx+4,14);}
}

// ── SURFACES ───────────────────────────────────
const SURFS={
  ellipse:{
    name:'Эллипс', eq:'x²/a² + y²/b² = 1', color:'#3b82f6',
    props:[
      {id:'a',label:'a',desc:'большая полуось (a > b)',def:4},
      {id:'b',label:'b',desc:'малая полуось',def:2.5},
    ],
    draw(p){
      const a=p.a,b=p.b;if(!a||!b||a<=0||b<=0)return;
      const pts=[];for(let t=0;t<=Math.PI*2+.01;t+=.008)pts.push({x:a*Math.cos(t),y:b*Math.sin(t)});
      ctx.beginPath();const p0=toS(a,0);ctx.moveTo(p0.sx,p0.sy);
      for(let t=0;t<=Math.PI*2+.01;t+=.02){const s=toS(a*Math.cos(t),b*Math.sin(t));ctx.lineTo(s.sx,s.sy);}
      ctx.closePath();ctx.fillStyle='rgba(59,130,246,.07)';ctx.fill();
      drawCurve(pts,'#3b82f6',2.5);
      const c=Math.sqrt(Math.abs(a*a-b*b));
      if(c>0.001){
        drawDash(c,0,0,b,'#f59e0b');drawDash(-c,0,0,b,'#f59e0b');
        drawDot(c,0,'#f59e0b','F₁');drawDot(-c,0,'#f59e0b','F₂');
      }
      drawDash(0,0,a,0,'#ef4444');drawDash(0,0,0,b,'#22c55e');
      drawDot(0,0,'#94a3b8','O',3);
    },
    info(p){
      const a=p.a,b=p.b;if(!a||!b)return[];
      const c=Math.round(Math.sqrt(Math.abs(a*a-b*b))*10000)/10000;
      const e=Math.round(c/Math.max(a,b)*10000)/10000;
      const S=Math.round(Math.PI*a*b*100)/100;
      return[{k:'a (полуось X)',v:a},{k:'b (полуось Y)',v:b},{k:'c (фок. расст.)',v:c},{k:'e (эксцентриситет)',v:e},{k:'S = π·a·b',v:S},{k:'Тип',v:'Замкнутая кривая'}];
    }
  },
  hyperbola:{
    name:'Гипербола', eq:'x²/a² − y²/b² = 1', color:'#ef4444',
    props:[
      {id:'a',label:'a',desc:'вещественная полуось',def:2},
      {id:'b',label:'b',desc:'мнимая полуось',def:1.5},
    ],
    draw(p){
      const a=p.a,b=p.b;if(!a||!b||a<=0||b<=0)return;
      const r1=[],r2=[];
      const tmax=Math.min(3.5,Math.acosh(15/a+1));
      for(let t=-tmax;t<=tmax;t+=.015){r1.push({x:a*Math.cosh(t),y:b*Math.sinh(t)});r2.push({x:-a*Math.cosh(t),y:b*Math.sinh(t)});}
      drawCurve(r1,'#ef4444',2.5);drawCurve(r2,'#ef4444',2.5);
      const lim=20;
      drawDash(-lim,-lim*b/a,lim,lim*b/a,'#94a3b8');
      drawDash(-lim,lim*b/a,lim,-lim*b/a,'#94a3b8');
      const c=Math.sqrt(a*a+b*b);
      drawDot(c,0,'#f59e0b','F₁');drawDot(-c,0,'#f59e0b','F₂');
      drawDash(0,0,a,0,'#ef4444');drawDash(0,0,0,b,'#22c55e');
      drawDot(0,0,'#94a3b8','O',3);
    },
    info(p){
      const a=p.a,b=p.b;if(!a||!b)return[];
      const c=Math.round(Math.sqrt(a*a+b*b)*10000)/10000;
      const e=Math.round(c/a*10000)/10000;
      const k=Math.round(b/a*10000)/10000;
      return[{k:'a (вещ. полуось)',v:a},{k:'b (мним. полуось)',v:b},{k:'c (фок. расст.)',v:c},{k:'e (эксцентриситет)',v:e},{k:'k = b/a',v:k},{k:'Асимптоты',v:'y = ±'+k+'x'}];
    }
  },
  parabola:{
    name:'Парабола', eq:'y² = 2px', color:'#8b5cf6',
    props:[
      {id:'p',label:'p',desc:'параметр параболы',def:2},
    ],
    draw(p){
      const pv=p.p;if(!pv||pv<=0)return;
      const pts=[];
      const ymax=Math.sqrt(2*pv*15)+1;
      for(let y=-ymax;y<=ymax;y+=ymax/200){pts.push({x:y*y/(2*pv),y});}
      ctx.beginPath();const pf=toS(pts[0].x,pts[0].y);ctx.moveTo(pf.sx,pf.sy);
      pts.forEach(pt=>{const s=toS(pt.x,pt.y);ctx.lineTo(s.sx,s.sy);});
      ctx.fillStyle='rgba(139,92,246,.07)';ctx.fill();
      drawCurve(pts,'#8b5cf6',2.5);
      const f=pv/2;
      drawDot(f,0,'#f59e0b','F('+f+', 0)');
      drawVLine(-f,'#f59e0b','x=−'+f);
      drawDot(0,0,'#94a3b8','O',3);
    },
    info(p){
      const pv=p.p;if(!pv)return[];
      return[{k:'p (параметр)',v:pv},{k:'Фокус F',v:'('+Math.round(pv/2*1000)/1000+', 0)'},{k:'Директриса',v:'x = −'+Math.round(pv/2*1000)/1000},{k:'e (эксцентриситет)',v:'1'},{k:'Вершина',v:'(0, 0)'},{k:'Ось симметрии',v:'ось Ox'}];
    }
  },
};

let curSurf='ellipse';
let curParams={a:4,b:2.5,p:2};

function getParams(){
  const p={};
  SURFS[curSurf].props.forEach(pr=>{
    const v=parseFloat(document.getElementById('inp-'+pr.id).value);
    p[pr.id]=isNaN(v)?null:v;
  });
  return p;
}

function renderProps(surf){
  const s=SURFS[surf];
  const grid=document.getElementById('prop-grid');
  grid.innerHTML='';
  s.props.forEach(pr=>{
    const row=document.createElement('div');row.className='prop-row';
    const val=curParams[pr.id]!==undefined?curParams[pr.id]:pr.def;
    row.innerHTML=`
      <div class="prop-label">${pr.label}</div>
      <div class="prop-desc">${pr.desc}</div>
      <input class="prop-input" id="inp-${pr.id}" type="number" value="${val}" step="0.1" min="0.001" placeholder="...">`;
    grid.appendChild(row);
    row.querySelector('input').addEventListener('input',()=>{
      curParams[pr.id]=parseFloat(document.getElementById('inp-'+pr.id).value)||null;
      draw();
    });
  });
}

function draw(){
  if(!W)return;
  drawGrid();
  const s=SURFS[curSurf];
  const p=getParams();
  s.draw(p);
  // Info chips
  document.getElementById('info-chips').innerHTML=s.info(p).map(c=>`<div class="chip"><div class="chip-k">${c.k}</div><div class="chip-v">${c.v}</div></div>`).join('');
}

function selectSurf(surf){
  curSurf=surf;
  document.querySelectorAll('.surf-tab').forEach(b=>b.classList.toggle('active',b.dataset.s===surf));
  const s=SURFS[surf];
  s.props.forEach(pr=>{curParams[pr.id]=pr.def;});
  document.getElementById('eq-formula').textContent=s.eq;
  document.getElementById('eq-dot').style.background=s.color;
  document.getElementById('bname').textContent=s.name;
  document.getElementById('beq').textContent=s.eq;
  panX=0;panY=0;scale=60;
  renderProps(surf);draw();
}

document.querySelectorAll('.surf-tab').forEach(btn=>{
  btn.addEventListener('click',()=>{
    selectSurf(btn.dataset.s);
    if(window.innerWidth<700){panel.classList.add('open');fab.classList.add('hidden');}
  });
});
document.getElementById('bbtn').addEventListener('click',()=>{
  draw();
  if(window.innerWidth<700)setTimeout(()=>{panel.classList.remove('open');fab.classList.remove('hidden');},300);
});

resize();
selectSurf('ellipse');
</script>
</body>
</html>
