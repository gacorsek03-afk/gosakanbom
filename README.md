<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="utf-8"/>
<meta name="viewport" content="width=device-width,initial-scale=1"/>
<title>💣 Bom Gosok — 15 Kotak 2 Bom</title>
<style>
  body{
    margin:0;
    background:radial-gradient(circle at top,#111,#000 70%);
    font-family:'Poppins',sans-serif;
    color:#fff;
    display:flex;
    flex-direction:column;
    align-items:center;
    padding:25px;
  }
  h1{color:#ffeb3b;text-shadow:0 0 15px #ff0;margin:10px 0;}
  p{color:#bbb;margin-top:0}
  .grid{
    display:grid;
    grid-template-columns:repeat(5,90px);
    gap:10px;
    justify-content:center;
    margin-top:20px;
  }
  .cell{
    width:90px;height:90px;border-radius:10px;border:2px solid #ffeb3b;
    background:#777;position:relative;overflow:hidden;
    cursor:crosshair;box-shadow:0 5px 15px rgba(0,0,0,0.5);
    touch-action:none;
  }
  .emoji{
    position:absolute;
    top:50%;left:50%;
    transform:translate(-50%,-50%);
    font-size:42px;
    pointer-events:none;
    opacity:0;
    transition:opacity .2s ease;
  }
  canvas{position:absolute;top:0;left:0;}
  #mainBtn{
    margin-top:25px;
    background:linear-gradient(90deg,#ff3d00,#ffcc00);
    color:#000;font-weight:bold;
    border:none;
    padding:10px 22px;
    border-radius:10px;
    cursor:pointer;
    box-shadow:0 0 10px #ff9800;
    transition:transform .2s;
  }
  #mainBtn:hover{transform:scale(1.1);}
  #info{margin-top:10px;color:#ccc;}
  @keyframes shake{
    0%,100%{transform:translate(0,0);}
    25%{transform:translate(-5px,5px);}
    50%{transform:translate(5px,-5px);}
    75%{transform:translate(-5px,5px);}
  }
  /* asap */
  .smoke{
    position:absolute;
    width:120px;height:120px;
    left:-15px;top:-15px;
    background:radial-gradient(circle,rgba(100,100,100,0.8),rgba(10,10,10,0) 70%);
    border-radius:50%;
    filter:blur(4px);
    opacity:0;
    animation:smokeRise 1.2s ease-out forwards;
  }
  @keyframes smokeRise{
    0%{opacity:1;transform:scale(0.5) translateY(20px);}
    100%{opacity:0;transform:scale(2.5) translateY(-30px);}
  }
  /* percikan api */
  .spark{
    position:absolute;width:6px;height:6px;border-radius:50%;
    background:radial-gradient(circle,#fff,#ff6600 60%,#330000 100%);
    opacity:0.9;
    animation:sparkFly 0.6s ease-out forwards;
  }
  @keyframes sparkFly{
    to{transform:translate(var(--dx),var(--dy)) scale(0);opacity:0;}
  }
  @media(max-width:520px){
    .grid{grid-template-columns:repeat(3,90px);}
  }
</style>
</head>
<body>
  <h1>BOM GOSOK MR.D</h1>
  <p>AWAS GILAK KLEN MAIN INI 🙏🤣</p>

  <div class="grid" id="grid"></div>
  <button id="mainBtn">Mulai Main</button>
  <p id="info">Kesempatan: 3</p>
  <p id="info">MODAL HABIS BISA CASBON HUBUNGI : MR.D</p>

  <audio id="boomSound" src="https://assets.mixkit.co/sfx/download/mixkit-explosion-in-battle-2809.wav"></audio>
  <audio id="revealSound" src="https://assets.mixkit.co/sfx/download/mixkit-retro-arcade-bling-211.wav"></audio>
  <audio id="winSound" src="https://assets.mixkit.co/sfx/download/mixkit-video-game-win-2016.wav"></audio>

<script>
const grid=document.getElementById('grid');
const btn=document.getElementById('mainBtn');
const info=document.getElementById('info');
const boom=document.getElementById('boomSound');
const reveal=document.getElementById('revealSound');
const win=document.getElementById('winSound');

let active=false;
let locked=false;
let attempts=3;
let bombSet=new Set();
const TOTAL=15, BOMBS=2;
const fruits=['🍉','🍊','🍌','🍇','🍓','🍏','🍍','🥝','🍒','🍑','🍐','🥭','🍋','🥥','🍈'];

btn.addEventListener('click',start);

function start(){
  if(attempts<=0){alert('Kesempatan habis 😢');return;}
  grid.innerHTML='';
  active=true;
  locked=false;
  bombSet=randomSet(TOTAL,BOMBS);
  info.textContent=`Kesempatan: ${attempts}`;
  btn.textContent='Main Lagi';
  build();
}

function randomSet(total,n){
  const a=[...Array(total).keys()];
  for(let i=a.length-1;i>0;i--){
    const j=Math.floor(Math.random()*(i+1));
    [a[i],a[j]]=[a[j],a[i]];
  }
  return new Set(a.slice(0,n));
}

function build(){
  for(let i=0;i<TOTAL;i++){
    const cell=document.createElement('div');
    cell.className='cell';
    cell.dataset.i=i;
    const emoji=document.createElement('div');
    emoji.className='emoji';
    emoji.textContent=bombSet.has(i)?'💣':fruits[i%fruits.length];
    cell.appendChild(emoji);

    const canvas=document.createElement('canvas');
    canvas.width=90;canvas.height=90;
    const ctx=canvas.getContext('2d');
    const grad=ctx.createLinearGradient(0,0,90,90);
    grad.addColorStop(0,'#555');
    grad.addColorStop(1,'#888');
    ctx.fillStyle=grad;
    ctx.fillRect(0,0,90,90);
    ctx.fillStyle='rgba(0,0,0,0.2)';
    for(let k=0;k<8;k++){
      ctx.beginPath();
      ctx.arc(Math.random()*90,Math.random()*90,8+Math.random()*10,0,Math.PI*2);
      ctx.fill();
    }
    cell.appendChild(canvas);
    grid.appendChild(cell);
    attachScratch(cell,canvas,emoji);
  }
}

function attachScratch(cell,canvas,emoji){
  const ctx=canvas.getContext('2d');
  let pressed=false;
  canvas.addEventListener('pointerdown',e=>{
    if(!active||locked)return;
    pressed=true;
    canvas.setPointerCapture(e.pointerId);
    scratch(e,cell,canvas,emoji);
  });
  canvas.addEventListener('pointermove',e=>{
    if(!active||!pressed||locked)return;
    scratch(e,cell,canvas,emoji);
  });
  canvas.addEventListener('pointerup',()=>{pressed=false;});
}

function scratch(e,cell,canvas,emoji){
  const rect=canvas.getBoundingClientRect();
  const x=e.clientX-rect.left,y=e.clientY-rect.top;
  const ctx=canvas.getContext('2d');
  ctx.globalCompositeOperation='destination-out';
  for(let i=0;i<3;i++){
    const rx=x+(Math.random()-0.5)*15;
    const ry=y+(Math.random()-0.5)*15;
    ctx.beginPath();
    ctx.arc(rx,ry,8+Math.random()*5,0,Math.PI*2);
    ctx.fill();
  }

  const idx=+cell.dataset.i;
  const img=ctx.getImageData(0,0,90,90).data;
  let clear=0;
  for(let i=3;i<img.length;i+=4)if(img[i]<100)clear++;
  const percent=clear/(90*90)*100;

  if(bombSet.has(idx)){
    if(percent>25 && !locked){
      locked=true;
      revealBomb(cell,canvas,emoji);
    }
  }else if(percent>40 && !cell._revealed){
    cell._revealed=true;
    emoji.style.opacity=1;
    ctx.clearRect(0,0,90,90);
    reveal.play().catch(()=>{});
    checkWin();
  }
}

function revealBomb(cell,canvas,emoji){
  emoji.style.opacity=1;
  setTimeout(()=>triggerExplosion(cell),700);
}

function triggerExplosion(cell){
  if(!active)return;
  active=false;
  attempts--;
  info.textContent=`Kesempatan: ${attempts}`;
  boom.currentTime=0;
  boom.play().catch(()=>{});
  document.body.style.animation='shake 0.5s';
  setTimeout(()=>document.body.style.animation='',600);

  // asap
  for(let i=0;i<3;i++){
    const smoke=document.createElement('div');
    smoke.className='smoke';
    smoke.style.left=Math.random()*30-10+'px';
    smoke.style.top=Math.random()*30-10+'px';
    cell.appendChild(smoke);
    setTimeout(()=>smoke.remove(),1200);
  }
  // percikan api
  for(let i=0;i<10;i++){
    const spark=document.createElement('div');
    spark.className='spark';
    const dx=(Math.random()-0.5)*120+'px';
    const dy=(Math.random()-0.8)*120+'px';
    spark.style.setProperty('--dx',dx);
    spark.style.setProperty('--dy',dy);
    cell.appendChild(spark);
    setTimeout(()=>spark.remove(),600);
  }

  setTimeout(()=>{
    alert('💣 DOORRR MEMEK!! WWKWKWK!');
    locked=false;
    if(attempts>0){active=true;}
    else{alert('TENTUKAN ☠️');}
  },600);
}

function checkWin(){
  const cells=[...document.querySelectorAll('.cell')];
  const safe=cells.filter(c=>!bombSet.has(+c.dataset.i));
  if(safe.every(c=>c._revealed)&&attempts>0){
    active=false;
    win.currentTime=0;
    win.play().catch(()=>{});
    setTimeout(()=>alert('🎉 Kamu berhasil membuka semua buah tanpa meledak!'),300);
  }
}

// tampil placeholder awal
for(let i=0;i<TOTAL;i++){
  const c=document.createElement('div');
  c.className='cell';
  const e=document.createElement('div');
  e.className='emoji';
  e.textContent='❔';
  e.style.opacity=1;
  c.appendChild(e);
  grid.appendChild(c);
}
</script>
</body>
</html>
