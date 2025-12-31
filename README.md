# WhiskerBytesCatCafe
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>WHISKER BYTES</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, shrink-to-fit=no">
<style>
    :root { --wood: #5d4037; --floor: #d2b48c; --pink: #ffc0cb; }

    * { 
        -webkit-touch-callout: none; 
        -webkit-user-select: none; 
        user-select: none; 
        -webkit-tap-highlight-color: transparent;
        touch-action: manipulation; 
    }

    body { margin: 0; overflow: hidden; background: #222; font-family: sans-serif; }
    canvas { display: block; touch-action: none; background: var(--floor); width: 100vw; height: 100vh; }

    #overlay { 
        position: fixed; inset: 0; 
        background: #4a2c2a url('IMG_2475.jpeg') center/cover; 
        display: flex; flex-direction: column; align-items: center; justify-content: flex-end; 
        z-index: 5000; color: white; text-align: center; padding-bottom: 50px; 
    }

    #logo-header { position: absolute; top: 0; left: 0; width: 100%; height: 45%; background: url('whiskerbytes.png') center/contain no-repeat; z-index: 5001; pointer-events: none; }

    .char-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; position: relative; z-index: 6000; margin-top: 20px; }
    .btn-select { background: white; border: 3px solid var(--pink); border-radius: 15px; padding: 10px; color: #333; cursor: pointer; text-align: center; font-size: 13px; font-weight: bold; }
    .btn-select img { width: 50px; height: 50px; display: block; margin: 0 auto; }

    /* CREDITS STYLING */
    #credits { position: fixed; inset: 0; background: rgba(0,0,0,0.8); z-index: 9999; display: none; align-items: center; justify-content: center; }
    .credits-box { background: #fff8f2; border: 4px solid var(--wood); border-radius: 18px; padding: 25px 22px; width: 85%; max-width: 320px; text-align: center; font-size: 14px; color: #4a2c2a; position: relative; overflow: hidden; height: 300px; }
    .credits-box h2 { margin-top: 0; color: #5d4037; }
    .credits-box p { margin: 12px 0; line-height: 1.4; }
    .credits-box .soft { font-size: 12px; opacity: 0.7; font-style: italic; }
    .credits-content { position: absolute; bottom: -100%; width: 100%; animation: scrollCredits 15s linear forwards; }
    @keyframes scrollCredits { 0% { bottom: -100%; } 100% { bottom: 100%; } }
    .close-credits { position: absolute; top: 8px; right: 10px; background: none; border: none; font-size: 24px; cursor: pointer; color: var(--wood); }

    #stats { position: fixed; top: 10px; left: 10px; background: rgba(255,255,255,0.95); padding: 10px; border-radius: 12px; border: 3px solid var(--wood); z-index: 100; font-weight: bold; display: none; font-size: 13px; }
    #ui-right { position: fixed; top: 10px; right: 10px; z-index: 100; display: none; }
    .ui-btn { background: var(--pink); border: 2px solid white; padding: 8px 12px; font-weight: bold; border-radius: 8px; font-size: 12px; }

    #controls { position: fixed; bottom: 20px; left: 50%; transform: translateX(-50%); display: none; flex-direction: column; gap: 8px; z-index: 1000; }
    .d-pad { display: grid; grid-template-columns: repeat(3, 1fr); gap: 8px; }
    .ctrl-btn { width: 65px; height: 65px; border-radius: 50%; border: 4px solid var(--wood); background: white; font-size: 28px; display: flex; align-items: center; justify-content: center; }
</style>
</head>
<body>

<div id="overlay">
    <div id="logo-header"></div> 
    <div class="char-grid">
        <div class="btn-select" onpointerdown="startGame('mochi')"><img src="mochi_idle.png">MOCHI<br>Patience+</div>
        <div class="btn-select" onpointerdown="startGame('gingerella')"><img src="gingerella_idle.png">GINGER<br>Tips+</div>
        <div class="btn-select" onpointerdown="startGame('luna')"><img src="luna_idle.png">LUNA<br>Speed+</div>
        <div class="btn-select" onpointerdown="startGame('jinx')"><img src="jinx_idle.png">JINX<br>Luck+</div>
    </div>
    <div style="margin-top:20px; font-size:14px; color: white; text-decoration: underline; cursor: pointer;" onclick="openCredits()">🐾 Credits</div>
</div>

<div id="credits">
    <div class="credits-box">
        <button class="close-credits" onclick="closeCredits()">✕</button>
        <div class="credits-content">
            <h2>🐾 Whisker Bytes</h2>
            <p><strong>Created by</strong><br>Lori Kaye Sifuentes</p>
            <p><strong>Customer Sprites Inspired By</strong><br>Luna & Stella 💖</p>
            <p><strong>Dev Note</strong><br>Whisker Bytes was made from a few quiet ideas and HTML magic. Thank you for visiting—paws up for staying a while. 🐾</p>
            <p class="soft">Made with love, cats, and imagination.</p>
        </div>
    </div>
</div>

<div id="stats">
    ⭐ <span id="scoreVal">0</span> | 🏆 BEST: <span id="highVal">0</span><br>
    🕒 <span id="timeVal">9:00 AM</span> | <span id="holdingTxt" style="color: #8b4513;">🐾 Empty</span>
</div>

<div id="ui-right">
    <button class="ui-btn" onclick="location.reload()">▶ NEW DAY</button>
</div>

<canvas id="game"></canvas>

<div id="controls">
    <div class="d-pad">
        <div></div><button class="ctrl-btn" id="up">⬆️</button><div></div>
        <button class="ctrl-btn" id="left">⬅️</button>
        <button class="ctrl-btn" id="down">⬇️</button>
        <button class="ctrl-btn" id="right">➡️</button>
    </div>
    <button class="ctrl-btn" id="action-btn" style="width:160px; border-radius: 35px; background:var(--pink);" onpointerdown="interact()">📢 MEOW</button>
</div>

<script>
const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");
let gameActive = false, score = 0, gameMinutes = 540, unlockedCake = false;
let playerHolding = null, tvActive = false, criticActive = false;
const keys = { up: false, down: false, left: false, right: false };
const catImg = new Image(), bgImg = new Image(), f1 = new Image(), f2 = new Image(), criticImg = new Image();
f1.src = 'friend1.png'; f2.src = 'friend2.png'; criticImg.src = 'critic.png';

const player = { x: 200, y: 450, speed: 5, facing: 1, traits: { patienceBonus: 0, tipBonus: 0, luckBonus: 0 } };
let furniture = [], customers = [], kitchen = { active: false, timer: 0, item: null };

let highScore = localStorage.getItem("whiskerBytesHigh") || 0;
document.getElementById('highVal').innerText = highScore;

function openCredits() {
    const content = document.querySelector(".credits-content");
    content.style.animation = "none"; 
    void content.offsetWidth; 
    content.style.animation = "scrollCredits 15s linear forwards";
    document.getElementById('credits').style.display = 'flex';
}
function closeCredits() { document.getElementById('credits').style.display = 'none'; }

window.startGame = function(catId) {
    if (gameActive) return;
    catImg.src = catId + '_idle.png';
    bgImg.src = 'IMG_2480.jpeg'; 
    player.speed = (catId === 'luna') ? 8 : 5;
    player.traits.tipBonus = (catId === 'gingerella') ? 20 : 0;
    player.traits.patienceBonus = (catId === 'mochi') ? 30 : 0;
    player.traits.luckBonus = (catId === 'jinx') ? 0.15 : 0;
    player.cookTime = (catId === 'mochi') ? 40 : 80;

    furniture = [
        {x: 100, y: 320, type: "STATION", item: "☕", e: "☕"},
        {x: 180, y: 320, type: "STATION", item: "🍪", e: "🍪"},
        {x: 260, y: 320, type: "STATION", item: "🍼", e: "🍼"},
        {x: 340, y: 320, type: "TRASH", e: "🗑️"},
        {x: 50,  y: 320, type: "WINDOW", e: "🪟"}, 
        {x: canvas.width - 50, y: 320, type: "RADIO", e: "📻"},
        {x: canvas.width / 2, y: 140, type: "TV", e: "📺"},
        {x: 60, y: canvas.height - 80, type: "BED", e: "🛏️"},
        {x: canvas.width / 2, y: 550, type: "TABLE", e: "🟫"},
        {x: canvas.width / 2 - 65, y: 550, type: "CHAIR", e: "🪑", occupied: false},
        {x: canvas.width / 2 + 65, y: 550, type: "CHAIR", e: "🪑", occupied: false}
    ];

    document.getElementById('overlay').style.display = 'none';
    document.getElementById('stats').style.display = 'block';
    document.getElementById('ui-right').style.display = 'block';
    document.getElementById('controls').style.display = 'flex';
    gameActive = true;
    requestAnimationFrame(loop);
};

// (The rest of your game logic remains unchanged: interact(), update(), draw(), spawnCustomer(), loop(), etc.)

window.onload = () => {
    canvas.width = window.innerWidth; canvas.height = window.innerHeight;
    ["up","down","left","right"].forEach(id => {
        let b = document.getElementById(id);
        b.addEventListener('pointerdown', (e) => { e.preventDefault(); keys[id] = true; }, { passive: false });
        b.addEventListener('pointerup', (e) => { e.preventDefault(); keys[id] = false; }, { passive: false });
    });
};
</script>
</body>
</html>
