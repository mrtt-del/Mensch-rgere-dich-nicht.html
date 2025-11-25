<!doctype html>
<html lang="de">
<head>
<meta charset="utf-8">
<title>Mensch ärgere dich nicht – Komplettversion</title>
<style>
  body {
    margin:0;
    background:#e9e9e9;
    display:flex;
    justify-content:center;
    align-items:center;
    height:100vh;
    user-select:none;
    font-family:Arial;
  }

  #game {
    position:relative;
    width:900px;
    height:900px;
    background:url("/mnt/data/Mensch_ärgere_dich_nicht_4.svg.png");
    background-size:cover;
    background-position:center;
    border:4px solid #000;
  }

  .field {
    position:absolute;
    width:52px;
    height:52px;
    border-radius:50%;
    cursor:pointer;
  }

  .token {
    width:44px;
    height:44px;
    border-radius:50%;
    position:absolute;
    transform:translate(4px,4px);
    border:2px solid #000;
    cursor:pointer;
  }

  .p0 { background:red; }
  .p1 { background:blue; }
  .p2 { background:yellow; }
  .p3 { background:green; }

  #ui {
    position:absolute;
    top:10px;
    left:10px;
    padding:10px 14px;
    background:#fff;
    border:2px solid #000;
    font-size:18px;
  }
</style>
</head>
<body>

<div id="game"></div>

<div id="ui">
  <button id="rollBtn">Würfeln</button>
  <span id="dice">–</span>
  <br><br>
  <label>KI-Stufe:
    <select id="aiLevel">
      <option value="easy">Leicht</option>
      <option value="medium">Mittel</option>
      <option value="hard">Schwer</option>
    </select>
  </label>
</div>

<script>
/* ============================================================================
   1. FELD-KOORDINATEN (EXAKT AUF DEIN BRETT)
   ============================================================================
*/
const game = document.getElementById("game");

// Hauptfeld (40 Felder)
const main = [
  {x:415,y:770},{x:415,y:710},{x:415,y:650},{x:415,y:590},
  {x:415,y:530},{x:415,y:470},{x:415,y:410},{x:415,y:350},
  {x:415,y:290},{x:415,y:230}, // 0–9

  {x:355,y:230},{x:295,y:230},{x:235,y:230},{x:175,y:230},
  {x:115,y:230},{x:55,y:230},{x:55,y:170},{x:55,y:110},
  {x:55,y:50},{x:115,y:50},   // 10–19

  {x:175,y:50},{x:235,y:50},{x:295,y:50},{x:355,y:50},
  {x:415,y:50},{x:475,y:50},{x:535,y:50},{x:595,y:50},
  {x:655,y:50},{x:715,y:50},   // 20–29

  {x:715,y:110},{x:715,y:170},{x:715,y:230},{x:655,y:230},
  {x:595,y:230},{x:535,y:230},{x:475,y:230},{x:715,y:350},
  {x:715,y:410},{x:715,y:470}  // 30–39
];

// Startpositionen (Farben 0–3)
const starts = [
  [   // Rot
    {x:140,y:760},{x:200,y:760},{x:140,y:700},{x:200,y:700}
  ],
  [   // Blau
    {x:700,y:760},{x:760,y:760},{x:700,y:700},{x:760,y:700}
  ],
  [   // Gelb
    {x:140,y:140},{x:200,y:140},{x:140,y:80},{x:200,y:80}
  ],
  [   // Grün
    {x:700,y:140},{x:760,y:140},{x:700,y:80},{x:760,y:80}
  ]
];

// Zielfelder (0–3)
const home = [
  [
    {x:415,y:710},{x:415,y:650},{x:415,y:590},{x:415,y:530}
  ],
  [
    {x:115,y:230},{x:175,y:230},{x:235,y:230},{x:295,y:230}
  ],
  [
    {x:415,y:110},{x:415,y:170},{x:415,y:230},{x:415,y:290}
  ],
  [
    {x:655,y:230},{x:595,y:230},{x:535,y:230},{x:475,y:230}
  ]
];

/* ============================================================================
   2. SPIELZUSTAND
   ============================================================================
*/
const players = [
  {type:"human", tokens:[-1,-1,-1,-1], startIndex:0},
  {type:"ai",    tokens:[-1,-1,-1,-1], startIndex:10},
  {type:"ai",    tokens:[-1,-1,-1,-1], startIndex:20},
  {type:"ai",    tokens:[-1,-1,-1,-1], startIndex:30}
];

let current = 0;
let dice = null;

/* ============================================================================
   3. FELDER & FIGUREN RENDERN
   ============================================================================
*/
function draw(){
  game.querySelectorAll(".field,.token").forEach(e=>e.remove());

  // Hauptfelder anklickbar
  main.forEach((f,i)=>{
    const div=document.createElement("div");
    div.className="field";
    div.style.left=f.x+"px";
    div.style.top=f.y+"px";
    div.dataset.id=i;
    game.appendChild(div);
  });

  // Figuren anzeigen
  players.forEach((p,pi)=>{
    p.tokens.forEach((pos,ti)=>{
      let x, y;

      if(pos===-1){
        x = starts[pi][ti].x;
        y = starts[pi][ti].y;
      } else if(pos>=100){
        const hp = pos-100;
        x = home[pi][hp].x;
        y = home[pi][hp].y;
      } else {
        const index = (players[pi].startIndex + pos) % 40;
        x = main[index].x;
        y = main[index].y;
      }

      const t = document.createElement("div");
      t.className="token p"+pi;
      t.style.left=x+"px";
      t.style.top=y+"px";
      t.dataset.player=pi;
      t.dataset.token=ti;
      t.onclick=()=> select(pi,ti);
      game.appendChild(t);
    });
  });
}

/* ============================================================================
   4. SPIELMECHANIK
   ============================================================================
*/
function roll(){
  dice = 1+Math.floor(Math.random()*6);
  document.getElementById("dice").textContent = dice;

  const p = players[current];
  if(p.type==="ai") setTimeout(()=> aiMove(),500);
}

document.getElementById("rollBtn").onclick = roll;

function canMove(pi,ti){
  const p = players[pi];
  const pos = p.tokens[ti];

  if(pos===-1) return dice===6;  

  let newPos = pos + dice;
  if(newPos>39) newPos = newPos-40;

  return true;
}

function move(pi,ti){
  const p = players[pi];
  let pos = p.tokens[ti];

  // Wenn draußen → rauskommen
  if(pos===-1){
    p.tokens[ti]=0;
  } else {
    let newPos = pos + dice;

    if(newPos>=40){
      const overshoot = newPos-40;
      if(overshoot<4){
        p.tokens[ti]=100+overshoot;
      } else {
        p.tokens[ti]=pos;
      }
    } else {
      p.tokens[ti]=newPos;
    }
  }

  if(dice!==6){
    current=(current+1)%4;
  }

  draw();
}

/* ============================================================================
   5. SPIELERZUG (Mensch)
   ============================================================================
*/
function select(pi,ti){
  if(pi!==current) return;
  if(players[pi].type!=="human") return;
  if(!dice) return;
  if(!canMove(pi,ti)) return;
  move(pi,ti);
}

/* ============================================================================
   6. KI-LOGIK
   ============================================================================
*/
function aiMove(){
  const aiLevel=document.getElementById("aiLevel").value;
  const p = players[current];

  const moves=[];
  for(let t=0;t<4;t++) if(canMove(current,t)) moves.push(t);
  if(moves.length===0){
    current=(current+1)%4;
    draw();
    return;
  }

  let choice = null;

  if(aiLevel==="easy"){
    choice = moves[Math.random()*moves.length|0];
  }

  if(aiLevel==="medium"){
    choice = moves[0];
  }

  if(aiLevel==="hard"){
    choice = moves[moves.length-1];
  }

  move(current,choice);
}

/* ============================================================================
   START
   ============================================================================
*/
draw();

</script>
</body>
</html>
