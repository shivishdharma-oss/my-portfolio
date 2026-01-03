<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>AETHER_HEALTH // Shivish.ar</title>

<style>
@import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&family=JetBrains+Mono:wght@300;500&display=swap');

:root {
    --neon-cyan: #00f3ff;
    --alert-red: #ff4444;
    --safe-green: #00ff88;
}

body {
    background: #000;
    color: white;
    font-family: 'JetBrains Mono', monospace;
    margin: 0;
    overflow: hidden;
    height: 100vh;
}

/* BOOT */
#loading-screen {
    position: fixed;
    inset: 0;
    background: #000;
    z-index: 9999;
    display: flex;
    justify-content: center;
    align-items: center;
}
.loader-text {
    font-family: 'Orbitron';
    color: var(--neon-cyan);
    letter-spacing: 5px;
    font-size: 22px;
    animation: blink 0.6s infinite alternate;
}
@keyframes blink { from {opacity: 0.3;} to {opacity: 1;} }

/* DASHBOARD */
.os-container {
    width: 95%;
    max-width: 1250px;
    height: 92vh;
    margin: 4vh auto;
    background: rgba(10,10,20,0.98);
    border: 1px solid var(--neon-cyan);
    display: flex;
    opacity: 0;
    transition: 1s;
}

/* SIDEBAR */
.sidebar {
    width: 260px;
    padding: 20px;
    border-right: 1px solid rgba(0,243,255,0.1);
}
.member {
    padding: 10px;
    border: 1px solid #333;
    margin-bottom: 8px;
    cursor: pointer;
    border-radius: 4px;
}
.member.active {
    border-color: var(--neon-cyan);
    background: rgba(0,243,255,0.08);
}

/* MAIN */
.main {
    flex: 1;
    padding: 25px;
    display: flex;
    flex-direction: column;
}

.stats {
    display: grid;
    grid-template-columns: repeat(3,1fr);
    gap: 15px;
    margin-bottom: 20px;
}
.box {
    background: #0a0a0a;
    padding: 15px;
    border-left: 2px solid var(--neon-cyan);
}
.box h2 {
    font-family: 'Orbitron';
    color: var(--safe-green);
    margin: 5px 0;
}

/* INPUT */
.input-wrapper { position: relative; width: 70%; }
input {
    background: #000;
    border: 1px solid var(--neon-cyan);
    color: white;
    padding: 15px;
    width: 100%;
    border-radius: 4px;
    outline: none;
}

#suggestion-box {
    position: absolute;
    bottom: 100%;
    width: 100%;
    background: #111;
    border: 1px solid #333;
    max-height: 200px;
    overflow-y: auto;
    display: none;
}
.suggestion-item {
    padding: 10px 15px;
    cursor: pointer;
    display: flex;
    justify-content: space-between;
    font-size: 12px;
}
.suggestion-item:hover {
    background: var(--neon-cyan);
    color: #000;
}

.btn-main {
    background: var(--neon-cyan);
    border: none;
    padding: 15px 25px;
    font-weight: bold;
    cursor: pointer;
    font-family: 'Orbitron';
    margin-left: 10px;
}

/* HISTORY */
#history {
    flex-grow: 1;
    font-size: 11px;
    color: #666;
    overflow-y: auto;
}
@keyframes fadeIn {
    from {opacity:0; transform: translateY(5px);}
    to {opacity:1; transform: translateY(0);}
}
</style>
</head>

<body>

<div id="loading-screen">
    <div class="loader-text">SCANNER_V13_ONLINE</div>
</div>

<div class="os-container" id="app">
    <div class="sidebar">
        <h3 style="font-family:Orbitron;color:var(--neon-cyan);font-size:12px;">USER_PROFILES</h3>
        <div id="btn-Shiv" class="member active" onclick="setUser('Shivish')">Shivish</div>
        <div id="btn-Dad" class="member" onclick="setUser('Father')">Father</div>
        <div id="btn-Mom" class="member" onclick="setUser('Mother')">Mother</div>
    </div>

    <div class="main">
        <h1 id="userLabel" style="font-family:Orbitron;font-size:20px;">BIO_METRICS: SHIVISH</h1>

        <div class="stats">
            <div class="box"><span>CALORIES</span><h2 id="cals">0</h2></div>
            <div class="box"><span>SUGAR (g)</span><h2 id="sugs">0</h2></div>
            <div class="box"><span>PROTEIN (g)</span><h2 id="pros">0</h2></div>
        </div>

        <div id="history">Intake History Empty...</div>

        <div style="display:flex;align-items:flex-end;margin-top:20px;">
            <div class="input-wrapper">
                <div id="suggestion-box"></div>
                <input id="foodIn" placeholder="Type dish name..." autocomplete="off">
            </div>
            <button class="btn-main" onclick="logFood()">LOG</button>
        </div>
    </div>
</div>

<script>
const foods = {
    pizza:{c:280,s:5,p:11}, dosa:{c:160,s:2,p:6},
    pasta:{c:350,s:6,p:12}, noodles:{c:380,s:4,p:8},
    sandwich:{c:250,s:4,p:8}, sambar:{c:120,s:3,p:5}
};

const limits = {
    Shivish:{c:2200,s:30},
    Father:{c:2000,s:25},
    Mother:{c:1800,s:25}
};

let data = {
    Shivish:{c:0,s:0,p:0,h:[]},
    Father:{c:0,s:0,p:0,h:[]},
    Mother:{c:0,s:0,p:0,h:[]}
};

const saved = localStorage.getItem("AETHER_DATA");
if (saved) data = JSON.parse(saved);

let user = "Shivish";

window.onload = () => {
    setTimeout(()=>{
        document.getElementById("loading-screen").style.opacity=0;
        document.getElementById("app").style.opacity=1;
    },1200);
    updateUI();
};

foodIn.addEventListener("input", e=>{
    const val=e.target.value.toLowerCase();
    const box=suggestion-box;
    box.innerHTML="";
    if(!val){box.style.display="none";return;}
    Object.keys(foods).filter(f=>f.includes(val)).forEach(f=>{
        const d=document.createElement("div");
        d.className="suggestion-item";
        d.innerHTML=`<span>${f.toUpperCase()}</span><span>${foods[f].c} kcal</span>`;
        d.onclick=()=>{foodIn.value=f;box.style.display="none";}
        box.appendChild(d);
    });
    box.style.display="block";
});

foodIn.addEventListener("keydown",e=>{
    if(e.key==="Enter") logFood();
});

function logFood(){
    const v=foodIn.value.toLowerCase();
    if(!foods[v]) return;
    const f=foods[v];
    const u=data[user];
    u.c+=f.c; u.s+=f.s; u.p+=f.p;
    u.h.unshift(`[${new Date().toLocaleTimeString()}] ${v.toUpperCase()}`);
    foodIn.value="";
    updateUI();
}

function setUser(n){
    user=n;
    userLabel.innerText="BIO_METRICS: "+n.toUpperCase();
    document.querySelectorAll(".member").forEach(m=>m.classList.remove("active"));
    document.getElementById(n==="Shivish"?"btn-Shiv":n==="Father"?"btn-Dad":"btn-Mom").classList.add("active");
    updateUI();
}

function updateUI(){
    const u=data[user];
    cals.innerText=u.c;
    sugs.innerText=u.s;
    pros.innerText=u.p;

    cals.style.color = u.c>limits[user].c ? "var(--alert-red)" : "var(--safe-green)";
    sugs.style.color = u.s>limits[user].s ? "var(--alert-red)" : "var(--safe-green)";

    history.innerHTML = u.h.map(x=>`<div style="animation:fadeIn .3s">${x}</div>`).join("");
    localStorage.setItem("AETHER_DATA",JSON.stringify(data));
}
</script>

</body>
</html>
