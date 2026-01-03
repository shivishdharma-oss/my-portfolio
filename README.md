<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>AETHER_HEALTH // Shivish.ar</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&family=JetBrains+Mono:wght@300;500&display=swap');
        :root { --neon-cyan: #00f3ff; --alert-red: #ff4444; --safe-green: #00ff88; }
        
        * { box-sizing: border-box; }
        body { background: #000; color: white; font-family: 'JetBrains Mono', monospace; margin: 0; overflow: hidden; height: 100vh; }

        /* --- LOADING SCREEN --- */
        #loading-screen {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: #000; z-index: 9999; display: flex; flex-direction: column;
            justify-content: center; align-items: center; transition: opacity 1s ease;
        }
        .loader-text { font-family: 'Orbitron'; color: var(--neon-cyan); margin-bottom: 20px; letter-spacing: 5px; font-size: 24px; animation: blink 0.5s infinite alternate; }
        .progress-container { width: 300px; height: 2px; background: #222; position: relative; }
        .progress-bar { width: 0%; height: 100%; background: var(--neon-cyan); box-shadow: 0 0 15px var(--neon-cyan); animation: load 2.5s forwards; }
        .status-msg { margin-top: 15px; font-size: 10px; color: #555; text-transform: uppercase; }

        @keyframes load { 0% { width: 0%; } 100% { width: 100%; } }
        @keyframes blink { from { opacity: 1; } to { opacity: 0.5; } }

        /* --- DASHBOARD --- */
        .os-container { 
            width: 95%; max-width: 1200px; height: 92vh; margin: 4vh auto;
            background: rgba(15, 15, 25, 0.98); border: 2px solid var(--neon-cyan); 
            border-radius: 12px; display: flex; box-shadow: 0 0 30px rgba(0, 243, 255, 0.15);
            opacity: 0; transition: opacity 1s; 
        }

        .sidebar { width: 260px; border-right: 1px solid rgba(0, 243, 255, 0.2); padding: 20px; display: flex; flex-direction: column; }
        .member { padding: 12px; border: 1px solid #333; margin-bottom: 10px; border-radius: 5px; cursor: pointer; transition: 0.3s; }
        .member.active { border-color: var(--neon-cyan); background: rgba(0, 243, 255, 0.1); }
        .member p { font-size: 10px; color: #888; margin-top: 4px; }
        
        .main { flex: 1; padding: 25px; overflow-y: auto; display: flex; flex-direction: column; }
        .stats { display: grid; grid-template-columns: repeat(3, 1fr); gap: 15px; margin: 15px 0; }
        .box { background: #111; padding: 15px; border-radius: 8px; border-left: 3px solid var(--neon-cyan); }
        .box h2 { margin: 5px 0; font-family: 'Orbitron'; color: var(--neon-cyan); font-size: 24px; }
        
        .input-group { background: #1a1a1a; padding: 20px; border-radius: 8px; border: 1px solid #333; margin-top: auto; }
        input { background: #000; border: 1px solid var(--neon-cyan); color: white; padding: 12px; width: 70%; border-radius: 4px; outline: none; }
        .btn-main { background: var(--neon-cyan); border: none; padding: 12px 20px; font-weight: bold; cursor: pointer; border-radius: 4px; font-family: 'Orbitron'; }
        
        .alert { display: none; background: rgba(255, 68, 68, 0.2); border: 1px solid var(--alert-red); color: var(--alert-red); padding: 15px; text-align: center; margin-bottom: 10px; font-weight: bold; border-radius: 5px; font-family: 'Orbitron'; }
    </style>
</head>
<body>

    <div id="loading-screen">
        <div class="loader-text">AETHER_CORE</div>
        <div class="progress-container"><div class="progress-bar"></div></div>
        <div class="status-msg" id="status-text">Initializing Bio-Sensors...</div>
    </div>

    <div class="os-container" id="app-content">
        <div class="sidebar">
            <h3 style="font-family:'Orbitron'; color:var(--neon-cyan); font-size: 14px; margin-bottom: 25px;">WORLD_HEALTH_V9</h3>
            <div id="btn-Shiv" class="member active" onclick="setUser('Shivish')">Shivish <p>DOB: 14/02/2013</p></div>
            <div id="btn-Dad" class="member" onclick="setUser('Father')">Father <p>DOB: 09/03/1977</p></div>
            <div id="btn-Mom" class="member" onclick="setUser('Mother')">Mother <p>DOB: 26/06/1983</p></div>
            
            <div style="margin-top: 20px; padding: 10px; background: rgba(0,0,0,0.3); border-radius: 5px;">
                <p style="font-size: 10px; color: var(--neon-cyan);">LIVE STATUS (2025):</p>
                <p id="ageDisplay" style="font-size: 12px; margin-top: 5px; color: white;"></p>
            </div>
            <p style="margin-top: auto; font-size: 9px; color: #444;">SHIVISH.AR // DEV_BUILD</p>
        </div>
        
        <div class="main">
            <div id="alert" class="alert">BIO-LIMIT: NUTRITION ALERT ACTIVATED</div>
            <h1 id="userLabel" style="font-family:'Orbitron'; margin:0; font-size: 22px;">Profile: Shivish</h1>
            
            <div class="stats">
                <div class="box"><span>CALORIES</span><h2 id="cals">0</h2></div>
                <div class="box"><span>SUGAR (g)</span><h2 id="sugs">0</h2></div>
                <div class="box"><span>PROTEIN (g)</span><h2 id="pros">0</h2></div>
            </div>

            <div id="history" style="font-size: 11px; color: #555; height: 100px; overflow-y: auto; margin-top: 10px; border-top: 1px solid #222; padding-top: 10px;">Intake Log: Ready.</div>

            <div class="input-group">
                <input type="text" id="foodIn" placeholder="Enter dish (e.g. Pasta, Sambar, Chicken 65...)">
                <button class="btn-main" onclick="logFood()">LOG</button>
            </div>
        </div>
    </div>

<script>
    // --- BOOT SEQUENCE LOGIC ---
    window.addEventListener('load', () => {
        const msgs = ["Loading Global Database...", "Syncing Bio-Data...", "Calculating Ages...", "Aether Health Online."];
        let i = 0;
        const interval = setInterval(() => {
            document.getElementById('status-text').innerText = msgs[i];
            i++;
            if(i >= msgs.length) clearInterval(interval);
        }, 600);

        setTimeout(() => {
            document.getElementById('loading-screen').style.opacity = '0';
            setTimeout(() => {
                document.getElementById('loading-screen').style.display = 'none';
                document.getElementById('app-content').style.opacity = '1';
            }, 1000);
        }, 3000);
    });

    // --- DASHBOARD LOGIC ---
    let user = "Shivish";
    const today = new Date("2025-01-01"); 
    const data = {
        "Shivish": { dob: "2013-02-14", c: 0, s: 0, p: 0, h: [] },
        "Father": { dob: "1977-03-09", c: 0, s: 0, p: 0, h: [] },
        "Mother": { dob: "1983-06-26", c: 0, s: 0, p: 0, h: [] }
    };

    const foods = {
        "idli": { c: 65, s: 1, p: 2 }, "dosa": { c: 170, s: 1, p: 4 }, "paniyaram": { c: 50, s: 1, p: 1 },
        "sambar": { c: 120, s: 3, p: 5 }, "coconut chutney": { c: 90, s: 1, p: 2 }, "kuruma": { c: 180, s: 3, p: 4 },
        "chicken 65": { c: 320, s: 1, p: 22 }, "biryani": { c: 480, s: 2, p: 18 }, "pasta": { c: 320, s: 6, p: 12 },
        "noodles": { c: 380, s: 4, p: 8 }, "pizza": { c: 280, s: 5, p: 11 }, "soda": { c: 150, s: 39, p: 0 }
    };

    function calculateAge(dobString) {
        const birthDate = new Date(dobString);
        let age = today.getFullYear() - birthDate.getFullYear();
        const m = today.getMonth() - birthDate.getMonth();
        if (m < 0 || (m === 0 && today.getDate() < birthDate.getDate())) age--;
        return age;
    }

    function setUser(name) {
        user = name;
        document.getElementById('userLabel').innerText = "Profile: " + name;
        document.querySelectorAll('.member').forEach(m => m.classList.remove('active'));
        if(name === "Shivish") document.getElementById('btn-Shiv').classList.add('active');
        if(name === "Father") document.getElementById('btn-Dad').classList.add('active');
        if(name === "Mother") document.getElementById('btn-Mom').classList.add('active');
        updateUI();
    }

    function logFood() {
        const val = document.getElementById('foodIn').value.toLowerCase().trim();
        const match = Object.keys(foods).find(f => f.includes(val) && val.length > 1);
        if (match) {
            const f = foods[match];
            data[user].c += f.c; data[user].s += f.s; data[user].p += f.p;
            data[user].h.unshift(`[Logged] ${match.toUpperCase()}`);
            updateUI();
            document.getElementById('foodIn').value = "";
        }
    }

    function updateUI() {
        const u = data[user];
        document.getElementById('cals').innerText = u.c;
        document.getElementById('sugs').innerText = u.s;
        document.getElementById('pros').innerText = u.p;
        document.getElementById('ageDisplay').innerText = "Age: " + calculateAge(u.dob) + " Yrs";
        document.getElementById('history').innerHTML = u.h.join("<br>");
        document.getElementById('alert').style.display = (u.s > 45 || u.c > 2000) ? 'block' : 'none';
    }
    updateUI();
</script>
</body>
</html>
