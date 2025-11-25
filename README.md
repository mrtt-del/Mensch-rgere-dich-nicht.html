<!doctype html>
<html lang="de">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Mensch ärger dich nicht — Vollversion (Browser)</title>
<style>
  :root{
    --board-size: 820px;
    --space: 18px;
    --bg: linear-gradient(180deg,#f6f9ff,#ffffff);
    --font: "Inter", "Helvetica Neue", Arial, sans-serif;
  }
  * { box-sizing: border-box; }
  body{
    margin:0;
    min-height:100vh;
    display:flex;
    align-items:center;
    justify-content:center;
    background:var(--bg);
    font-family:var(--font);
    color:#222;
    padding:20px;
  }

  .app {
    width: min(var(--board-size), 96vw);
    background: white;
    border-radius:14px;
    box-shadow: 0 10px 40px rgba(20,30,60,0.08);
    padding:16px;
    display:grid;
    grid-template-columns: 1fr 320px;
    gap:14px;
    align-items:start;
  }

  header{ display:flex; gap:12px; align-items:center; }
  header h1{ font-size:18px; margin:0; }
  header p{ margin:0; color:#666; font-size:13px; }

  /* board area */
  .board-wrap{
    width:100%;
    display:flex;
    justify-content:center;
    align-items:center;
    padding:10px;
  }
  .board {
    width: 680px;
    height: 680px;
    border-radius:50%;
    position:relative;
    background:
      radial-gradient(circle at 50% 50%, #fff 0%, #f5f8ff 45%, transparent 100%);
    box-shadow: inset 0 6px 18px rgba(0,0,0,0.03);
    overflow:visible;
  }

  /* board squares arranged in circle */
  .square {
    position:absolute;
    width:58px;
    height:58px;
    border-radius:12px;
    display:flex;
    align-items:center;
    justify-content:center;
    font-size:10px;
    color:#444;
    box-shadow: 0 6px 10px rgba(20,30,60,0.04);
    background: linear-gradient(180deg, #fff, #fbfdff);
    border:1px solid rgba(0,0,0,0.04);
  }

  /* home stretch container (inwards) */
  .homes {
    position:absolute;
    left:50%;
    top:50%;
    transform:translate(-50%,-50%);
    width:360px;
    height:360px;
    pointer-events:none;
  }
  .home-column{
    position:absolute;
    display:flex;
    gap:8px;
    flex-direction:column;
    align-items:center;
    pointer-events:none;
  }
  .home-cell {
    width:44px;
    height:44px;
    border-radius:8px;
    background:#fff;
    border:1px solid rgba(0,0,0,0.04);
    display:flex;
    align-items:center;
    justify-content:center;
    font-size:11px;
  }

  /* tokens */
  .token {
    position:absolute;
    width:28px;
    height:28px;
    border-radius:50%;
    border:3px solid #fff;
    box-shadow: 0 6px 12px rgba(0,0,0,0.12);
    display:flex;
    align-items:center;
    justify-content:center;
    font-size:12px;
    font-weight:700;
    cursor:pointer;
    transition: transform 200ms cubic-bezier(.2,.9,.3,1);
  }
  .token.selectable { box-shadow: 0 0 0 6px rgba(255,165,0,0.18); transform:translateY(-6px); }
  .token.small { width:22px; height:22px; font-size:10px; border-width:2px; }

  /* player colors */
  .c0 { background: #ff5c5c; color:white; }
  .c1 { background: #4aa3ff; color:white; }
  .c2 { background: #ffd24a; color:#3a2b00; }
  .c3 { background: #5bd16a; color:white; }

  /* right panel */
  aside {
    width:320px;
    display:flex;
    flex-direction:column;
    gap:12px;
  }
  .panel {
    background:linear-gradient(180deg,#fff,#fbfcff);
    border-radius:10px;
    padding:12px;
    border:1px solid #eef2ff;
    box-shadow: 0 8px 18px rgba(20,30,60,0.03);
  }
  label{ font-size:13px; color:#333; display:block; margin-bottom:6px; }
  select, button, input[type="number"]{
    width:100%;
    padding:8px 10px;
    border-radius:8px;
    border:1px solid #dfe7ff;
    background:white;
    font-size:14px;
  }
  .row{ display:flex; gap:8px; }
  .row > * { flex:1; }

  .players-list { display:flex; flex-direction:column; gap:8px; margin-top:6px; }
  .player-entry { display:flex; gap:8px; align-items:center; justify-content:space-between; }
  .player-entry .left { display:flex; gap:8px; align-items:center; }
  .dot { width:18px; height:18px; border-radius:50%; border:2px solid #fff; box-shadow:0 3px 8px rgba(0,0,0,0.08); }

  .controls { display:flex; gap:8px; margin-top:6px; }
  .dice { width:70px; height:70px; border-radius:10px; display:flex; align-items:center; justify-content:center; font-size:28px; font-weight:700; border:1px solid #e6ecff; background:white; }
  .log { max-height:160px; overflow:auto; font-size:13px; background:#fff; padding:8px; border-radius:8px; border:1px solid #f0f4ff; }

  footer{ font-size:12px; color:#666; text-align:right; margin-top:6px; }

  @media (max-width:980px){
    .app { grid-template-columns: 1fr; }
    aside { order:2; width:100%; }
    .board { width:90vw; height:90vw; }
  }
</style>
</head>
<body>
  <div class="app" role="application" aria-label="Mensch ärger dich nicht, Browser">
    <div>
      <header>
        <div>
          <h1>Mensch ärger dich nicht — Vollversion</h1>
          <p>Wähle Spieler, KI-Stärke und starte. Klicke nach dem Würfeln auf den Stein, den du bewegen möchtest.</p>
        </div>
      </header>

      <div class="board-wrap">
        <div id="board" class="board" role="region" aria-label="Spielbrett"></div>
        <div class="homes" id="homes"></div>
      </div>

      <footer>Made for fun — lokal im Browser.</footer>
    </div>

    <aside>
      <div class="panel">
        <label>Spieler gesamt (2-4)</label>
        <div class="row">
          <select id="totalPlayers">
            <option value="2">2 Spieler</option>
            <option value="3">3 Spieler</option>
            <option value="4" selected>4 Spieler</option>
          </select>
          <button id="setupBtn">Einrichten</button>
        </div>

        <div class="players-list" id="playersList"></div>

        <div style="margin-top:8px;">
          <label>Würfeln</label>
          <div class="row">
            <div class="dice" id="dice">–</div>
            <div style="display:flex;flex-direction:column;gap:8px;">
              <button id="rollBtn">Würfeln</button>
              <button id="endTurnBtn">Zug beenden</button>
            </div>
          </div>
        </div>
      </div>

      <div class="panel">
        <label>Spiel-Log</label>
        <div class="log" id="log"></div>
        <div style="display:flex;gap:8px;margin-top:8px;">
          <button id="newGameBtn">Neues Spiel</button>
          <button id="resetBtn">Zurücksetzen</button>
        </div>
      </div>

      <div class="panel small">
        <div><strong>Hinweise</strong></div>
        <ul style="margin:8px 0 0 16px;padding:0;color:#555">
          <li>6 = rausziehen + Extrazug</li>
          <li>Klicke den Stein an, nachdem du geworfen hast</li>
          <li>KI-Modi: Leicht = Zufall, Mittel = pragmatisch, Schwer = bewertend</li>
        </ul>
      </div>
    </aside>
  </div>

<script>
/* ---------------------------
  Vollversion: Spiel + KI (3 Stufen)
  - Rundes Board (40 Felder)
  - Für 2–4 Spieler
  - Jeder Spieler: 4 Tokens
  - Click-to-move für Mensch
  - KI: easy / medium / hard
  - Animation + einfache Töne
--------------------------- */

/* ---------- Konfiguration ---------- */
const BOARD_SIZE = 40;
const HOME_LEN = 5; // H1..H4 + FIN
const PLAYER_COLORS = ['c0','c1','c2','c3'];
const PLAYER_NAMES_DEFAULT = ['Spieler 1','Spieler 2','Spieler 3','Spieler 4'];

/* ---------- DOM ---------- */
const boardEl = document.getElementById('board');
const homesEl = document.getElementById('homes');
const totalPlayersSelect = document.getElementById('totalPlayers');
const playersListEl = document.getElementById('playersList');
const setupBtn = document.getElementById('setupBtn');
const rollBtn = document.getElementById('rollBtn');
const endTurnBtn = document.getElementById('endTurnBtn');
const diceEl = document.getElementById('dice');
const logEl = document.getElementById('log');
const newGameBtn = document.getElementById('newGameBtn');
const resetBtn = document.getElementById('resetBtn');

let state = null;

/* ---------- Utilities ---------- */
function log(text){
  const time = new Date().toLocaleTimeString();
  const row = document.createElement('div');
  row.textContent = `${time} — ${text}`;
  logEl.prepend(row);
}
function tone(freq=440, duration=120, type='sine'){
  try{
    const ctx = window.__audioCtx || (window.__audioCtx = new (window.AudioContext || window.webkitAudioContext)());
    const o = ctx.createOscillator();
    const g = ctx.createGain();
    o.type = type;
    o.frequency.value = freq;
    o.connect(g);
    g.connect(ctx.destination);
    g.gain.value = 0.0001;
    o.start();
    const now = ctx.currentTime;
    g.gain.linearRampToValueAtTime(0.12, now + 0.01);
    g.gain.linearRampToValueAtTime(0.0001, now + (duration/1000));
    o.stop(now + (duration/1000) + 0.02);
  }catch(e){ /* audio not available */ }
}

function rand(n){ return Math.floor(Math.random()*n); }

/* ---------- Game model ---------- */
function makeInitialState(playerCount=4, playerTypes=null, aiLevels=null){
  // playerTypes: array 'human' or 'ai'
  // aiLevels: array 'easy'|'medium'|'hard' (unused for humans)
  const players = [];
  for(let i=0;i<playerCount;i++){
    players.push({
      id:i,
      name: PLAYER_NAMES_DEFAULT[i],
      type: (playerTypes && playerTypes[i]) ? playerTypes[i] : 'ai',
      aiLevel: (aiLevels && aiLevels[i]) ? aiLevels[i] : 'easy',
      tokens: [null,null,null,null], // null == at home; else index into path
      finishedCount:0
    });
  }
  const paths = buildPaths(playerCount);
  return {
    players,
    paths,
    current: 0,
    dice: null,
    rollingAllowed: true,
    animating: false
  };
}

/* buildPaths(playerCount) => array paths[playerId] = [boardIndices..., 'P#H1', ... 'P#FIN'] */
function buildPaths(playerCount){
  const entries = [0,10,20,30];
  const paths = [];
  for(let p=0;p<playerCount;p++){
    const entry = entries[p];
    const path = [];
    for(let i=0;i<BOARD_SIZE;i++){
      path.push((entry + i) % BOARD_SIZE);
    }
    // home stretch
    for(let h=1; h<=HOME_LEN; h++){
      path.push(`P${p}H${h}`);
    }
    paths.push(path);
  }
  return paths;
}

/* ---------- Rendering ---------- */

function drawBoard(){
  boardEl.innerHTML = '';
  homesEl.innerHTML = '';
  const rect = boardEl.getBoundingClientRect();
  const w = rect.width;
  const h = rect.height;
  const cx = w/2, cy = h/2;
  const outerR = Math.min(w,h)/2 - 40;
  const innerR = outerR - 92;

  // place 40 squares around circle
  for(let i=0;i<BOARD_SIZE;i++){
    const angle = (i / BOARD_SIZE) * Math.PI * 2 - Math.PI/2; // start top
    const x = cx + Math.cos(angle) * outerR - 29; // -half width
    const y = cy + Math.sin(angle) * outerR - 29;
    const el = document.createElement('div');
    el.className = 'square';
    el.dataset.index = i;
    el.style.left = `${x}px`;
    el.style.top = `${y}px`;
    el.innerHTML = `<div style="font-size:10px;color:#8b97a6">${i}</div>`;
    boardEl.appendChild(el);
  }

  // create home columns oriented to 4 sides: top, right, bottom, left
  // each column has HOME_LEN cells pointing inward
  const sidePos = [
    {x: cx - 22, y: cy - innerR - 10, dir: 'down'}, // top (player 0)
    {x: cx + innerR + 10, y: cy - 22, dir: 'left'}, // right (player 1)
    {x: cx - 22, y: cy + innerR + 10, dir: 'up'}, // bottom (player 2)
    {x: cx - innerR - 10, y: cy - 22, dir: 'right'} // left (player 3)
  ];

  for(let p=0;p<state.paths.length;p++){
    if(p>=4) break; // layout for up to 4
    const sp = sidePos[p];
    const container = document.createElement('div');
    container.className = 'home-column';
    container.style.left = `${sp.x}px`;
    container.style.top = `${sp.y}px`;
    container.style.pointerEvents = 'none';
    container.dataset.player = p;
    homesEl.appendChild(container);
    // orientation control via transform
    if(sp.dir==='left') container.style.transform = 'translate(-50%,-50%) rotate(-90deg)';
    if(sp.dir==='right') container.style.transform = 'translate(-50%,-50%) rotate(90deg)';
    if(sp.dir==='up') container.style.transform = 'translate(-50%,-50%) rotate(180deg)';
    // add home cells
    for(let h=1; h<=HOME_LEN; h++){
      const cell = document.createElement('div');
      cell.className = 'home-cell';
      cell.dataset.home = `P${p}H${h}`;
      cell.textContent = h===HOME_LEN ? 'Z' : '';
      container.appendChild(cell);
    }
  }

  // initial token render
  updateTokensUI(true);
}

/* place tokens visually */
function updateTokensUI(force=false){
  // remove old tokens
  document.querySelectorAll('.token').forEach(t=> t.remove());
  // for each player and token: compute position
  state.players.forEach(player=>{
    player.tokens.forEach((tokIndex, tid)=>{
      const tokenEl = document.createElement('div');
      tokenEl.className = `token ${PLAYER_COLORS[player.id]}`;
      tokenEl.dataset.player = player.id;
      tokenEl.dataset.tid = tid;
      tokenEl.title = `${player.name} ${tid+1}`;
      tokenEl.textContent = tid+1;
      // find destination element to attach
      if(tokIndex === null){
        // place near player's home corner (stacked)
        placeTokenAtHome(tokenEl, player.id, tid);
      } else {
        const path = state.paths[player.id];
        const cellId = path[tokIndex];
        if(typeof cellId === 'number'){
          // find square by data-index
          const squareEl = boardEl.querySelector(`.square[data-index="${cellId}"]`);
          if(squareEl){
            // place token inside square, slight offset for stacking
            placeTokenOver(squareEl, tokenEl, tid);
          }
        } else {
          // home cell (P#H#)
          const homeEl = homesEl.querySelector(`.home-cell[data-home="${cellId}"]`);
          if(homeEl){
            placeTokenOver(homeEl, tokenEl, 0);
          }
        }
      }
      // click handler for human players
      tokenEl.addEventListener('click', onTokenClick);
      boardEl.appendChild(tokenEl); // keep absolute; placeTokenOver sets transform
    });
  });
  highlightSelectable();
}

/* helper: place token over an element (center), with small offsets for multiple tokens */
function placeTokenOver(targetEl, tokenEl, indexInStack){
  const rectBoard = boardEl.getBoundingClientRect();
  const rect = targetEl.getBoundingClientRect();
  const tx = rect.left - rectBoard.left + rect.width/2;
  const ty = rect.top - rectBoard.top + rect.height/2;
  // offset for stacking
  const ox = (indexInStack % 2 === 0) ? -10 : 10;
  const oy = Math.floor(indexInStack/2) * -8;
  tokenEl.style.left = `${tx - 14 + ox}px`;
  tokenEl.style.top = `${ty - 14 + oy}px`;
}

/* helper: place token in home stack area */
function placeTokenAtHome(tokenEl, playerId, tid){
  // find the outer corner square for player as base
  // We'll pick board square nearest to entry position
  const entry = state.paths[playerId][0]; // board index
  const squareEl = boardEl.querySelector(`.square[data-index="${entry}"]`);
  if(!squareEl){
    // fallback to center
    tokenEl.style.left = `50%`;
    tokenEl.style.top = `50%`;
    return;
  }
  // position token slightly off the square so it looks like in starting area
  const rectBoard = boardEl.getBoundingClientRect();
  const rect = squareEl.getBoundingClientRect();
  const tx = rect.left - rectBoard.left + rect.width/2;
  const ty = rect.top - rectBoard.top + rect.height/2;
  const baseAngle = (entry / BOARD_SIZE) * Math.PI*2 - Math.PI/2;
  // move radially outward
  const r = Math.min(rectBoard.width, rectBoard.height)/2 + 20;
  const ox = Math.cos(baseAngle) * 80;
  const oy = Math.sin(baseAngle) * 80;
  // stack tokens around that point
  const sx = tx + ox + (tid-1.5)*18;
  const sy = ty + oy + (tid%2? -8 : 8);
  tokenEl.style.left = `${sx - 14}px`;
  tokenEl.style.top = `${sy - 14}px`;
}

/* mark which tokens are selectable (human current) */
function highlightSelectable(){
  document.querySelectorAll('.token').forEach(t=> t.classList.remove('selectable'));
  const cur = state.current;
  const curPlayer = state.players[cur];
  if(!curPlayer || curPlayer.type !== 'human') return;
  if(state.dice === null) return;
  // tokens that can move
  curPlayer.tokens.forEach((pos, tid)=>{
    if(canMoveToken(cur, tid, state.dice)){
      const selector = `.token[data-player="${cur}"][data-tid="${tid}"]`;
      const el = document.querySelector(selector);
      if(el) el.classList.add('selectable');
    }
  });
}

/* ---------- Game rules & movement ---------- */

function canMoveToken(playerId, tid, dice){
  const player = state.players[playerId];
  const pos = player.tokens[tid];
  const path = state.paths[playerId];
  if(pos === null){
    return dice === 6; // can only leave home on 6
  } else {
    return (pos + dice) < path.length; // cannot overrun final
  }
}

/* move with animation: token idents to destination index in path */
function moveTokenAnimated(playerId, tid, steps, callback){
  if(state.animating) return;
  const player = state.players[playerId];
  const pos = player.tokens[tid];
  const path = state.paths[playerId];
  // determine new index
  let newIndex;
  if(pos === null){
    if(steps !== 6) { callback && callback(false); return; }
    newIndex = 0;
  } else {
    newIndex = pos + steps;
    if(newIndex >= path.length){ callback && callback(false); return; }
  }
  state.animating = true;
  // compute start coords and end coords
  // find element for token currently (existing token DOM)
  const tokenEl = document.querySelector(`.token[data-player="${playerId}"][data-tid="${tid}"]`);
  if(!tokenEl){
    // fallback: set state and update UI
    player.tokens[tid] = newIndex;
    finalizeMove(playerId, tid, newIndex);
    state.animating = false;
    updateTokensUI();
    callback && callback(true);
    return;
  }
  // animate along path by moving the token element step-by-step
  const pathIndices = [];
  const startIdx = pos === null ? -1 : pos;
  for(let s = 1; s <= steps; s++){
    pathIndices.push(startIdx + s);
  }
  // function to compute center coords for a path index
  function coordsForPathIdx(pid, idx){
    const pth = state.paths[pid];
    const cell = pth[idx];
    if(typeof cell === 'number'){
      const squareEl = boardEl.querySelector(`.square[data-index="${cell}"]`);
      const rectBoard = boardEl.getBoundingClientRect();
      const r = squareEl.getBoundingClientRect();
      return {
        x: r.left - rectBoard.left + r.width/2 - 14,
        y: r.top - rectBoard.top + r.height/2 - 14
      };
    } else {
      const homeEl = homesEl.querySelector(`.home-cell[data-home="${cell}"]`);
      if(homeEl){
        const rectBoard = boardEl.getBoundingClientRect();
        const r = homeEl.getBoundingClientRect();
        return {
          x: r.left - rectBoard.left + r.width/2 - 14,
          y: r.top - rectBoard.top + r.height/2 - 14
        };
      } else {
        return {x: boardEl.clientWidth/2 - 14, y: boardEl.clientHeight/2 - 14};
      }
    }
  }

  // perform step animation sequentially
  let stepI = 0;
  function stepAnimate(){
    if(stepI >= pathIndices.length){
      // finalize
      player.tokens[tid] = newIndex;
      finalizeMove(playerId, tid, newIndex);
      state.animating = false;
      updateTokensUI();
      callback && callback(true);
      return;
    }
    const idx = pathIndices[stepI];
    const posCoords = coordsForPathIdx(playerId, idx);
    // animate tokenEl to posCoords
    tokenEl.style.transition = 'transform 360ms cubic-bezier(.2,.9,.3,1)';
    tokenEl.style.transform = `translate(${posCoords.x - parseFloat(tokenEl.style.left || 0)}px, ${posCoords.y - parseFloat(tokenEl.style.top || 0)}px)`;
    // after transition, set absolute coords and clear transform to keep stacking right
    setTimeout(() => {
      tokenEl.style.transition = '';
      tokenEl.style.left = `${posCoords.x}px`;
      tokenEl.style.top = `${posCoords.y}px`;
      tokenEl.style.transform = '';
      stepI++;
      // small sound
      tone(520, 60, 'triangle');
      setTimeout(stepAnimate, 120);
    }, 420);
  }
  stepAnimate();
}

/* finalize move: apply captures and finish logic */
function finalizeMove(playerId, tid, newIndex){
  const player = state.players[playerId];
  const landed = state.paths[playerId][newIndex];
  // if landed on board number -> capture opponents on this board cell
  if(typeof landed === 'number'){
    // for each other player, any token on that board index gets sent home
    state.players.forEach(op=>{
      if(op.id === playerId) return;
      op.tokens.forEach((pos, tIdx)=>{
        if(pos === null) return;
        const otherCell = state.paths[op.id][pos];
        if(otherCell === landed){
          op.tokens[tIdx] = null;
          log(`${op.name} wurde von ${player.name} rausgeworfen (Token ${tIdx+1}).`);
          tone(220, 220, 'sawtooth'); // "ouch" tone
        }
      });
    });
  }
  // if reached FIN
  if(landed === `P${playerId}FIN`){
    player.finishedCount++;
    log(`${player.name} hat einen Stein ins Ziel gebracht! (${player.finishedCount}/4)`);
    tone(880, 200, 'sine');
    // check winner
    if(player.finishedCount === 4){
      log(`🎉 ${player.name} hat gewonnen! Spiel beendet.`);
      alert(`${player.name} hat gewonnen!`);
      state.rollingAllowed = false;
    }
  }
  // small settle tone
  tone(420, 90, 'sine');
}

/* ---------- Turn flow ---------- */

function rollDice(){
  if(!state.rollingAllowed || state.animating) return;
  const v = rand(6) + 1;
  state.dice = v;
  diceEl.textContent = v;
  tone(220 + v*40, 140, 'sine');
  log(`${state.players[state.current].name} würfelt ${v}.`);
  highlightSelectable();
  // auto: if current is ai -> perform ai move after slight delay
  const curP = state.players[state.current];
  if(curP.type === 'ai'){
    setTimeout(() => doAIMove(curP), 600);
  }
}

function endTurn(){
  if(state.animating) return;
  state.dice = null;
  diceEl.textContent = '–';
  state.current = (state.current + 1) % state.players.length;
  log(`Zug an ${state.players[state.current].name}.`);
  highlightSelectable();
  // if next is ai and automove allowed, roll automatically after delay
  const curP = state.players[state.current];
  if(curP.type === 'ai'){
    setTimeout(() => { if(state.rollingAllowed) rollDice(); }, 650);
  }
}

/* on token click (human chooses which token to move) */
function onTokenClick(evt){
  if(state.animating) return;
  const el = evt.currentTarget;
  const pid = parseInt(el.dataset.player,10);
  const tid = parseInt(el.dataset.tid,10);
  if(pid !== state.current) return; // not their turn
  const curP = state.players[pid];
  if(curP.type !== 'human') return;
  if(state.dice === null) { log('Bitte zuerst würfeln.'); return; }
  if(!canMoveToken(pid, tid, state.dice)){ log('Dieser Stein kann nicht bewegt werden.'); return; }
  // move
  moveTokenAnimated(pid, tid, state.dice, (ok)=>{
    if(ok){
      // extra turn on 6
      if(state.dice === 6){
        log(`${curP.name} hat eine 6 und darf nochmal würfeln.`);
        state.dice = null;
        diceEl.textContent = '–';
        // allow immediate roll
      } else {
        endTurn();
      }
    }
  });
}

/* ---------- AI logic (3 levels) ---------- */

function doAIMove(player){
  if(player.type !== 'ai') return;
  const level = player.aiLevel || 'easy';
  // find movable tokens
  const movable = [];
  for(let t=0;t<4;t++){
    if(canMoveToken(player.id, t, state.dice)) movable.push(t);
  }
  if(movable.length === 0){
    log(`${player.name} hat keine möglichen Züge.`);
    // no move: if dice was 6, still get extra? rules: if cannot move, you pass
    if(state.dice === 6){
      // still stays with same player? we'll grant extra turn rule: 6 grants extra only if moved; simpler: end turn.
      endTurn();
    } else {
      endTurn();
    }
    return;
  }

  let choice = null;
  if(level === 'easy'){
    // random
    choice = movable[rand(movable.length)];
  } else if(level === 'medium'){
    // prefer finishing > capturing > farthest forward > from home on 6
    choice = aiMediumChoice(player, movable);
  } else {
    // hard: score each move
    choice = aiHardChoice(player, movable);
  }
  log(`${player.name} (KI ${level}) wählt Token ${choice+1}.`);
  // animate chosen move
  moveTokenAnimated(player.id, choice, state.dice, (ok)=>{
    if(ok){
      if(state.dice === 6){
        // allow immediate roll if still AI
        setTimeout(()=> {
          if(player.type==='ai') rollDice();
        }, 350);
      } else {
        endTurn();
      }
    }
  });
}

function aiMediumChoice(player, movable){
  // finishing
  const path = state.paths[player.id];
  for(const t of movable){
    const pos = player.tokens[t];
    if(pos !== null && pos + state.dice === path.length -1) return t;
  }
  // capture
  for(const t of movable){
    const pos = player.tokens[t];
    const targetIndex = (pos===null? 0 : pos + state.dice);
    const cell = path[targetIndex];
    if(typeof cell === 'number' && enemyOnCell(cell)) return t;
  }
  // if can move out from home and others are near, maybe take out
  for(const t of movable){
    if(player.tokens[t] === null) return t;
  }
  // farthest forward
  let best = movable[0];
  let bestPos = (player.tokens[best] === null) ? -1 : player.tokens[best];
  for(const t of movable){
    const ppos = (player.tokens[t] === null) ? -1 : player.tokens[t];
    if(ppos > bestPos){ best = t; bestPos = ppos; }
  }
  return best;
}

function aiHardChoice(player, movable){
  // scoring: finish +200, capture +120, safety +40, advance scaled by distance to finish
  let best = movable[0];
  let bestScore = -1e9;
  const path = state.paths[player.id];
  for(const t of movable){
    const pos = player.tokens[t];
    const targetIndex = (pos === null) ? 0 : pos + state.dice;
    const cell = path[targetIndex];
    let score = 0;
    // finish
    if(targetIndex === path.length -1) score += 200;
    // capture
    if(typeof cell === 'number' && enemyOnCell(cell)) score += 120;
    // safety: check if landing in spot where an enemy could immediately capture next turn
    if(!landingIsDanger(player.id, targetIndex)) score += 40;
    // prefer moving tokens closer to finish
    const rem = (path.length -1) - targetIndex;
    score += Math.max(0, 50 - rem); // less remaining = higher score
    // avoid moving token out from home into immediate danger (slight penalty)
    if(pos === null && landingIsDanger(player.id, targetIndex)) score -= 30;
    // small random jitter to break ties
    score += rand(6);
    if(score > bestScore){ bestScore = score; best = t; }
  }
  return best;
}

/* helper: enemyOnCell(boardIndex) */
function enemyOnCell(cellIndex){
  for(const p of state.players){
    for(const t of p.tokens){
      if(t === null) continue;
      const cell = state.paths[p.id][t];
      if(typeof cell === 'number' && cell === cellIndex) return true;
    }
  }
  // more precise would check only enemies, but for our AI it's ok
  return false;
}

/* landingIsDanger(playerId, pathIndex) -> whether an opponent can capture you next move (simple lookahead) */
function landingIsDanger(playerId, pathIndex){
  const target = state.paths[playerId][pathIndex];
  if(typeof target !== 'number') return false; // home zone safe
  // check if any enemy can reach that board index in one move (roll 1..6)
  for(const p of state.players){
    if(p.id === playerId) continue;
    for(const t=0; t<4; t++){
      const pos = p.tokens[t];
      if(pos === null) continue;
      const pth = state.paths[p.id];
      const cellNow = pth[pos];
      // if enemy is on board, see possible landing cells for dice 1..6
      for(let d=1; d<=6; d++){
        const futureIdx = pos + d;
        if(futureIdx >= pth.length) continue;
        const futureCell = pth[futureIdx];
        if(futureCell === target) return true;
      }
    }
    // also enemies could come out from home with a 6 onto their entry; if entry equals target and they have a token at home, they could come out
    const entry = state.paths[p.id][0];
    if(entry === target){
      // if they have at least one token at home and a dice 6 could let them come out
      if(p.tokens.some(x => x === null)) return true;
    }
  }
  return false;
}

/* ---------- helper: checking victory ---------- */
function checkVictory(){
  const winner = state.players.find(p => p.finishedCount === 4);
  if(winner){
    state.rollingAllowed = false;
    log(`${winner.name} hat gewonnen!`);
    tone(1000, 500, 'sine');
    return winner;
  }
  return null;
}

/* ---------- Setup / UI binding ---------- */

function buildPlayersPanel(cnt){
  playersListEl.innerHTML = '';
  for(let i=0;i<cnt;i++){
    const row = document.createElement('div');
    row.className = 'player-entry';
    const left = document.createElement('div');
    left.className = 'left';
    const dot = document.createElement('div');
    dot.className = 'dot ' + PLAYER_COLORS[i];
    left.appendChild(dot);
    const nameInput = document.createElement('input');
    nameInput.type = 'text';
    nameInput.value = PLAYER_NAMES_DEFAULT[i];
    nameInput.style.flex = '1';
    left.appendChild(nameInput);
    row.appendChild(left);

    const right = document.createElement('div');
    right.style.display='flex';
    right.style.gap='8px';
    right.style.alignItems='center';
    const typeSelect = document.createElement('select');
    ['human','ai'].forEach(v=>{
      const opt = document.createElement('option'); opt.value = v; opt.textContent = v=== 'human' ? 'Mensch' : 'KI';
      typeSelect.appendChild(opt);
    });
    const aiSelect = document.createElement('select');
    ['easy','medium','hard'].forEach(v=>{
      const opt = document.createElement('option'); opt.value = v; opt.textContent = v[0].toUpperCase() + v.slice(1);
      aiSelect.appendChild(opt);
    });
    right.appendChild(typeSelect);
    right.appendChild(aiSelect);
    row.appendChild(right);

    // defaults: first player human
    if(i===0) typeSelect.value = 'human';
    if(i>0) typeSelect.value = 'ai';
    if(i===1) aiSelect.value = 'medium';

    playersListEl.appendChild(row);
  }
}

setupBtn.addEventListener('click', ()=>{
  const cnt = parseInt(totalPlayersSelect.value,10);
  buildPlayersPanel(cnt);
});

rollBtn.addEventListener('click', ()=>{
  // if dice already rolled and it's human, clicking roll again does nothing
  rollDice();
});

endTurnBtn.addEventListener('click', endTurn);

newGameBtn.addEventListener('click', ()=>{
  startGameFromUI();
});

resetBtn.addEventListener('click', ()=>{
  location.reload();
});

/* create state from UI and start */
function startGameFromUI(){
  const rows = Array.from(playersListEl.querySelectorAll('.player-entry'));
  const playerCount = rows.length;
  const playerTypes = [];
  const aiLevels = [];
  const names = [];
  rows.forEach((r,i)=>{
    const name = r.querySelector('input[type="text"]').value || PLAYER_NAMES_DEFAULT[i];
    const type = r.querySelector('select').value;
    const aiSel = r.querySelectorAll('select')[1].value;
    playerTypes.push(type);
    aiLevels.push(aiSel);
    names.push(name);
  });
  // build state
  state = makeInitialState(playerCount, playerTypes, aiLevels);
  // set names
  state.players.forEach((p,i)=> p.name = names[i] || PLAYER_NAMES_DEFAULT[i]);
  state.rollingAllowed = true;
  state.current = 0;
  state.dice = null;
  diceEl.textContent = '–';
  drawBoard();
  log('Spiel gestartet mit ' + playerCount + ' Spielern.');
  // first auto roll for AI if first is AI
  if(state.players[state.current].type === 'ai'){
    setTimeout(()=> rollDice(), 800);
  }
}

/* ---------- Initialization ---------- */
(function init(){
  buildPlayersPanel(parseInt(totalPlayersSelect.value,10));
  // initial dummy state to render board
  state = makeInitialState(4, ['human','ai','ai','ai'], ['easy','medium','hard']);
  drawBoard();
  log('Lade Spiel — richte Spieler ein und drücke Einrichten.');
})();

/* ---------- ensure responsive redraw on resize ---------- */
let resizeTO = null;
window.addEventListener('resize', ()=>{
  clearTimeout(resizeTO);
  resizeTO = setTimeout(()=> {
    if(state) drawBoard();
  }, 200);
});
</script>
</body>
</html>
