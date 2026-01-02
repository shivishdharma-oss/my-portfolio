<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>AETHER_HEALTH // Shivish.ar</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&family=JetBrains+Mono:wght@300;500&display=swap');
        :root { --neon-cyan: #00f3ff; --alert-red: #ff4444; --safe-green: #00ff88; }
        
        * { box-sizing: border-box; }
        body { background: #000; color: white; font-family: 'JetBrains Mono', monospace; margin: 0; height: 100vh; display: flex; align-items: center; justify-content: center; overflow: hidden; }

        /* --- FIXED DASHBOARD --- */
        .os-container { 
            width: 95vw; height: 90vh; 
            background: #0a0a0f; border: 2px solid var(--neon-cyan); 
            display: flex; position: relative; z-index: 10;
        }

        /* SIDEBAR - High Z-Index for clicking */
        .sidebar { 
            width: 250px; border-right: 1px solid #222; 
            padding: 20px; display: flex; flex-direction: column; 
            z-index: 20; background: #0d0d14;
        }
        .member { 
            padding: 15px; border: 1px solid #333; margin-bottom: 12px; 
            cursor: pointer; border-radius: 4px; text-align: center;
            font-family: 'Orbitron'; font-size: 13px; transition: 0.2s;
        }
        .member:hover { border-color: var(--neon-cyan); background: rgba(0, 243, 255, 0.1); }
        .member.active { border-color: var(--neon-cyan); background: var(--neon-cyan); color: #000; box-shadow: 0 0 15px var(--neon-cyan); }

        /* MAIN CONTENT */
        .main { flex: 1; padding: 30px; display: flex; flex-direction: column; overflow-y: auto; }
        .stats { display: grid; grid-template-columns: repeat(3, 1fr); gap: 20px; margin: 20px 0; }
        .box { background: #000; border: 1px solid #222; padding: 20px; border-top: 3px solid var(--neon-cyan); }
        .box h2 { font-family: 'Orbitron'; color: var(--neon-cyan); margin: 10px 0 0 0; font-size: 28px; }

        /* INPUT FIX */
        .input-section { margin-top: auto; padding-top: 20px; position: relative; z-index: 30; }
        input { 
            width: 100%; background: #111; border: 2px solid #333; 
            color: #fff; padding: 18px; font-size: 18px; 
            font-family: 'JetBrains Mono'; border-radius: 5px; outline: none;
        }
        input:focus { border-color: var(--neon-cyan); background: #000; }
        
        .btn-log { 
            width: 100%; margin-top: 10px; padding: 15px; 
            background: var(--neon-cyan); color: #000; border: none;
            font-family: 'Orbitron'; font-weight: bold; font-size: 16px;
            cursor: pointer; transition: 0.2s;
        }
        .btn-log:hover { background: #fff; box-shadow: 0 0 20px #fff; }

        /* BOOT SCREEN FIX (Transparent background after load) */
        #boot-screen {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: #000; z-index: 100; display: flex; 
            justify-content: center; align-items: center; transition: 0.5s;
        }
    </style>
</head>
<body>

    <div id="boot-screen">
        <div style="text-align:center;">
            <div style="font-family:'Orbitron'; color:var(--neon-cyan); font-size:24px; letter-spacing:8px; margin-bottom:10px;">AETHER_OS</div>
            <div style="font-size:10px; color:#555;">REPAIRING_CLICK_INTERFACE... 100%</div>
        </div>
    </div>

    <div class="os-container">
        <div class="sidebar">
            <div style="font-family:'Orbitron'; color:var(--neon-cyan); font-size:10px; margin-bottom:30px;">[ SELECT_USER ]</div>
            <div id="btn-Shivish" class="member active" onclick="changeUser('Shivish')">SHIVISH</div>
            <div id="btn-Father" class="member" onclick="changeUser('Father')">FATHER</div>
            <div id="btn-Mother" class="member" onclick="changeUser('Mother')">MOTHER</div>
            
            <div style="margin-top:auto; padding:10px; background:#000; font-size:11px; color:#555; border:1px solid #222;">
                GOAL: <span id="goal-pro" style="color:var(--safe-green)">45g</span> Protein
            </div>
        </div>

        <div class="main">
            <h1 id="user-title" style="font-family:'Orbitron'; margin:0;">PROFILE: SHIVISH</h1>
            
            <div class="stats">
                <div class="box"><span>CALORIES</span><h2 id="stat-c">0</h2></div>
                <div class="box"><span>SUGAR (g)</span><h2 id="stat-s">0</h2></div>
                <div class="box"><span>PROTEIN (g)</span><h2 id="stat-p">0</h2></div>
            </div>

            <div id="log-history" style="flex:1; border-top:1px solid #111; padding:15px; font-size:12px; color:#444; overflow-y:auto;">
                System Ready for Input...
            </div>

            <div class="input-section">
                <input type="text" id="food-input" placeholder="TYPE DISH NAME HERE...">
                <button class="btn-log" onclick="executeLog()">LOG DATA</button>
            </div>
        </div>
    </div>

<script>
    // Database
    const foodDB = {
        "pizza": {c:280, s:5, p:12}, "pasta": {c:350, s:6, p:10}, "idli": {c:65, s:1, p:2},
        "dosa": {c:170, s:1, p:4}, "chicken 65": {c:320, s:1, p:22}, "biryani": {c:500, s:2, p:18}
    };

    let currentUser = "Shivish";
    const userStore = {
        "Shivish": {c:0, s:0, p:0, h:[], goal:45},
        "Father": {c:0, s:0, p:0, h:[], goal:60},
        "Mother": {c:0, s:0, p:0, h:[], goal:50}
    };

    // Kill Boot Screen after 1 second
    window.onload = () => {
        setTimeout(() => {
            document.getElementById('boot-screen').style.opacity = '0';
            setTimeout(() => {
                document.getElementById('boot-screen').style.display = 'none';
            }, 500);
        }, 1000);
    };

    function changeUser(name) {
        currentUser = name;
        // Reset buttons
        document.querySelectorAll('.member').forEach(m => m.classList.remove('active'));
        document.getElementById('btn-' + name).classList.add('active');
        // Update Title
        document.getElementById('user-title').innerText = "PROFILE: " + name.toUpperCase();
        // Update Side Goal
        document.getElementById('goal-pro').innerText = userStore[name].goal + "g";
        updateStats();
    }

    function executeLog() {
        const input = document.getElementById('food-input');
        const dish = input.value.toLowerCase().trim();
        if(!dish) return;

        const nutrition = foodDB[dish] || {c:200, s:5, p:5}; // Default if not in list

        userStore[currentUser].c += nutrition.c;
        userStore[currentUser].s += nutrition.s;
        userStore[currentUser].p += nutrition.p;
        userStore[currentUser].h.unshift(`[${new Date().toLocaleTimeString()}] ${dish.toUpperCase()}`);

        input.value = "";
        updateStats();
    }

    function updateStats() {
        const u = userStore[currentUser];
        document.getElementById('stat-c').innerText = u.c;
        document.getElementById('stat-s').innerText = u.s;
        document.getElementById('stat-p').innerText = u.p;
        document.getElementById('log-history').innerHTML = u.h.join("<br>");
    }
</script>
</body>
</html>
