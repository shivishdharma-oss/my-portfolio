<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>AETHER_HEALTH // Shivish.ar</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&family=JetBrains+Mono:wght@300;500&display=swap');
        :root { --neon-cyan: #00f3ff; --alert-red: #ff4444; --safe-green: #00ff88; }
        
        body { background: #000; color: white; font-family: 'JetBrains Mono', monospace; margin: 0; overflow: hidden; height: 100vh; }

        /* --- BOOT SEQUENCE --- */
        #loading-screen { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: #000; z-index: 9999; display: flex; flex-direction: column; justify-content: center; align-items: center; transition: 0.8s; }
        .loader-text { font-family: 'Orbitron'; color: var(--neon-cyan); letter-spacing: 5px; font-size: 24px; animation: blink 0.5s infinite alternate; }

        /* --- DASHBOARD --- */
        .os-container { width: 95%; max-width: 1250px; height: 92vh; margin: 4vh auto; background: rgba(10, 10, 20, 0.98); border: 1px solid var(--neon-cyan); display: flex; opacity: 0; transition: 1s; }
        .sidebar { width: 260px; border-right: 1px solid rgba(0, 243, 255, 0.1); padding: 20px; }
        .main { flex: 1; padding: 25px; display: flex; flex-direction: column; position: relative; }

        /* --- SUGGESTION ENGINE CSS --- */
        .input-wrapper { position: relative; width: 70%; }
        input { background: #000; border: 1px solid var(--neon-cyan); color: white; padding: 15px; width: 100%; border-radius: 4px; outline: none; font-family: inherit; }
        
        #suggestion-box {
            position: absolute; bottom: 100%; left: 0; width: 100%;
            background: #111; border: 1px solid #333; border-bottom: none;
            max-height: 200px; overflow-y: auto; display: none; z-index: 100;
            box-shadow: 0 -5px 15px rgba(0,0,0,0.5);
        }
        .suggestion-item {
            padding: 10px 15px; cursor: pointer; border-bottom: 1px solid #222;
            display: flex; justify-content: space-between; font-size: 12px;
        }
        .suggestion-item:hover { background: var(--neon-cyan); color: #000; }
        .item-cal { opacity: 0.6; font-size: 10px; }

        .stats { display: grid; grid-template-columns: repeat(3, 1fr); gap: 15px; margin-bottom: 20px; }
        .box { background: #0a0a0a; padding: 15px; border-left: 2px solid var(--neon-cyan); }
        .box h2 { font-family: 'Orbitron'; color: var(--neon-cyan); margin: 5px 0; }
        
        .btn-main { background: var(--neon-cyan); border: none; padding: 15px 25px; font-weight: bold; cursor: pointer; font-family: 'Orbitron'; margin-left: 10px; }
        .member { padding: 10px; border: 1px solid #333; margin-bottom: 8px; cursor: pointer; border-radius: 4px; }
        .member.active { border-color: var(--neon-cyan); background: rgba(0, 243, 255, 0.05); }
    </style>
</head>
<body>

    <div id="loading-screen"><div class="loader-text">SCANNER_V13_ONLINE</div></div>

    <div class="os-container" id="app-content">
        <div class="sidebar">
            <h3 style="font-family:'Orbitron'; font-size: 12px; color: var(--neon-cyan);">USER_PROFILES</h3>
            <div id="btn-Shiv" class="member active" onclick="setUser('Shivish')">Shivish</div>
            <div id="btn-Dad" class="member" onclick="setUser('Father')">Father</div>
            <div id="btn-Mom" class="member" onclick="setUser('Mother')">Mother</div>
        </div>

        <div class="main">
            <h1 id="userLabel" style="font-family:'Orbitron'; font-size: 20px;">BIO_METRICS: SHIVISH</h1>
            
            <div class="stats">
                <div class="box"><span>CALORIES</span><h2 id="cals">0</h2></div>
                <div class="box"><span>SUGAR (g)</span><h2 id="sugs">0</h2></div>
                <div class="box"><span>PROTEIN (g)</span><h2 id="pros">0</h2></div>
            </div>

            <div id="history" style="flex-grow: 1; font-size: 11px; color: #444; overflow-y: auto;">Intake History Empty...</div>

            <div style="display: flex; align-items: flex-end; margin-top: 20px;">
                <div class="input-wrapper">
                    <div id="suggestion-box"></div>
                    <input type="text" id="foodIn" placeholder="Type dish name..." onkeyup="handleSearch(event)" autocomplete="off">
                </div>
                <button class="btn-main" onclick="logFood()">LOG</button>
            </div>
        </div>
    </div>

<script>
    // Database
    const foods = {
        "pizza": { c: 280, s: 5, p: 11 }, "paniyaram": { c: 50, s: 1, p: 1 }, "pasta": { c: 350, s: 6, p: 12 },
        "parotta": { c: 260, s: 2, p: 5 }, "pancakes": { c: 300, s: 12, p: 6 }, "sambar": { c: 120, s: 3, p: 5 },
        "sushi": { c: 200, s: 4, p: 12 }, "sandwich": { c: 250, s: 4, p: 8 }, "noodles": { c: 380, s: 4, p: 8 }
    };

    let user = "Shivish";
    const data = { "Shivish": { c: 0, s: 0, p: 0, h: [] }, "Father": { c: 0, s: 0, p: 0, h: [] }, "Mother": { c: 0, s: 0, p: 0, h: [] } };

    // Boot Sequence
    window.onload = () => {
        setTimeout(() => {
            document.getElementById('loading-screen').style.opacity = '0';
            document.getElementById('app-content').style.opacity = '1';
        }, 1200);
    };

    function handleSearch(e) {
        const val = e.target.value.toLowerCase();
        const box = document.getElementById('suggestion-box');
        box.innerHTML = "";
        
        if (val.length < 1) { box.style.display = "none"; return; }

        const matches = Object.keys(foods).filter(f => f.startsWith(val) || f.includes(val));

        if (matches.length > 0) {
            box.style.display = "block";
            matches.forEach(m => {
                const div = document.createElement('div');
                div.className = "suggestion-item";
                div.innerHTML = `<span>${m.toUpperCase()}</span><span class="item-cal">${foods[m].c} kcal</span>`;
                div.onclick = () => {
                    document.getElementById('foodIn').value = m;
                    box.style.display = "none";
                };
                box.appendChild(div);
            });
        } else {
            box.style.display = "none";
        }
    }

    function logFood() {
        const val = document.getElementById('foodIn').value.toLowerCase();
        if (foods[val]) {
            const f = foods[val];
            data[user].c += f.c; data[user].s += f.s; data[user].p += f.p;
            data[user].h.unshift(`[${new Date().toLocaleTimeString()}] ${val.toUpperCase()}`);
            updateUI();
            document.getElementById('foodIn').value = "";
            document.getElementById('suggestion-box').style.display = "none";
        }
    }

    function setUser(name) {
        user = name;
        document.getElementById('userLabel').innerText = "BIO_METRICS: " + name.toUpperCase();
        document.querySelectorAll('.member').forEach(m => m.classList.remove('active'));
        updateUI();
    }

    function updateUI() {
        const u = data[user];
        document.getElementById('cals').innerText = u.c;
        document.getElementById('sugs').innerText = u.s;
        document.getElementById('pros').innerText = u.p;
        document.getElementById('history').innerHTML = u.h.join("<br>");
    }
</script>
</body>
</html>
