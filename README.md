
<!DOCTYPE html>
<html lang="fa">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>RP City Mini</title>
<style>
*{box-sizing:border-box}
body{margin:0;background:#151b24;color:white;
font-family:Arial;text-align:center}
h2{margin:10px}
#info{padding:8px;background:#252f3d}
canvas{display:block;width:100%;max-width:700px;
margin:auto;background:#669b55;image-rendering:pixelated}
.controls{display:grid;grid-template-columns:repeat(3,65px);
gap:7px;justify-content:center;margin:12px}
button{height:48px;border:0;border-radius:10px;
background:#35475e;color:white;font-size:23px}
#message{min-height:28px;color:#ffd65b}
</style>
</head>
<body>
<h2>🏙️ RP CITY MINI</h2>
<div id="info">
💰 پول: <span id="money">0</span>
&nbsp; 💎 جم: <span id="gems">0</span>
</div>
<canvas id="game" width="420" height="560"></canvas>
<div class="controls">
<div></div><button data-dir="up">⬆️</button><div></div>
<button data-dir="left">⬅️</button>
<button data-dir="down">⬇️</button>
<button data-dir="right">➡️</button>
</div>
<div id="message">حرکت کن و سکه‌ها را جمع کن!</div>
<script>
const c=document.getElementById("game");
const ctx=c.getContext("2d");
const keys={};
let money=0,gems=0;
const player={x:200,y:280};
const coins=[
{x:90,y:125},{x:330,y:160},
{x:100,y:390},{x:320,y:430}
];
const chest={x:185,y:90,opened:false};

function rect(x,y,w,h,color){
 ctx.fillStyle=color;ctx.fillRect(x,y,w,h);
}
function draw(){
 // چمن شهر
 rect(0,0,420,560,"#619653");

 // خیابان‌ها
 rect(0,210,420,125,"#454b53");
 rect(145,0,125,560,"#454b53");

 // خط‌کشی خیابان
 for(let x=10;x<420;x+=45)rect(x,267,25,4,"#f5d76e");
 for(let y=10;y<560;y+=45)rect(205,y,4,25,"#f5d76e");

 // ساختمان‌ها
 const buildings=[
 [15,20,105,100],[290,20,110,100],
 [15,365,105,100],[290,365,110,100],
 [15,475,105,65],[290,475,110,65]
 ];
 buildings.forEach(([x,y,w,h],i)=>{
   rect(x,y,w,h,"#b5a99a");
   rect(x+8,y+8,w-16,h-16,"#777f91");
   for(let xx=x+15;xx<x+w-10;xx+=28)
     for(let yy=y+18;yy<y+h-8;yy+=28)
       rect(xx,yy,12,12,"#8ee0f0");
 });

 // سکه‌ها
 coins.forEach(a=>{
   if(a.gone)return;
   ctx.fillStyle="#ffd12e";
   ctx.beginPath();ctx.arc(a.x,a.y,9,0,Math.PI*2);
   ctx.fill();
   ctx.fillStyle="#a66b00";
   ctx.font="bold 12px Arial";
   ctx.fillText("$",a.x-4,a.y+4);
 });

 // صندوق سبز آزمایشی
 if(!chest.opened){
   rect(chest.x,chest.y,45,32,"#16883f");
   rect(chest.x,chest.y+12,45,7,"#54e879");
   rect(chest.x+18,chest.y+11,9,9,"#ffd34f");
   ctx.fillStyle="white";
   ctx.font="12px Arial";
   ctx.fillText("CHEST",chest.x-3,chest.y-5);
 }

 // شخصیت
 rect(player.x-9,player.y-10,18,20,"#2478ff");
 rect(player.x-7,player.y-19,14,12,"#f0bc8a");
 rect(player.x-6,player.y-16,3,3,"#222");
 rect(player.x+3,player.y-16,3,3,"#222");
 rect(player.x-11,player.y+8,8,8,"#20242c");
 rect(player.x+3,player.y+8,8,8,"#20242c");
}

function update(){
 let dx=0,dy=0;
 if(keys.up)dy=-2.5;
 if(keys.down)dy=2.5;
 if(keys.left)dx=-2.5;
 if(keys.right)dx=2.5;
 player.x=Math.max(10,Math.min(410,player.x+dx));
 player.y=Math.max(25,Math.min(550,player.y+dy));

 coins.forEach(a=>{
   if(!a.gone&&Math.hypot(player.x-a.x,player.y-a.y)<20){
     a.gone=true;money+=10;
     document.getElementById("money").textContent=money;
     document.getElementById("message").textContent=
       "آفرین! ۱۰ سکه گرفتی 🤑";
   }
 });

 if(!chest.opened&&
 Math.hypot(player.x-(chest.x+22),player.y-(chest.y+16))<30){
   chest.opened=true;
   gems+=5;
   document.getElementById("gems").textContent=gems;
   document.getElementById("message").textContent=
     "صندوق آزمایشی باز شد! ۵ جم گرفتی 💎";
 }
 draw();
 requestAnimationFrame(update);
}

document.querySelectorAll("button[data-dir]").forEach(b=>{
 const d=b.dataset.dir;
 b.addEventListener("pointerdown",e=>{
   e.preventDefault();keys[d]=true;
 });
 for(const ev of ["pointerup","pointerleave","pointercancel"])
   b.addEventListener(ev,()=>keys[d]=false);
});
window.addEventListener("keydown",e=>{
 const map={ArrowUp:"up",ArrowDown:"down",
 ArrowLeft:"left",ArrowRight:"right",
 w:"up",s:"down",a:"left",d:"right"};
 if(map[e.key])keys[map[e.key]]=true;
});
window.addEventListener("keyup",e=>{
 const map={ArrowUp:"up",ArrowDown:"down",
 ArrowLeft:"left",ArrowRight:"right",
 w:"up",s:"down",a:"left",d:"right"};
 if(map[e.key])keys[map[e.key]]=false;
});
update();
</script>
</body>
</html>

