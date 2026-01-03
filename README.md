<!DOCTYPE html>
<html>
<head>
    <title>SHIVISH HEALTH CORE</title>
    <style>
        body { font-family: 'Segoe UI', Tahoma, sans-serif; background: #1a1a1a; color: white; padding: 20px; }
        .container { display: flex; gap: 20px; max-width: 900px; margin: auto; }
        
        /* Sidebar */
        .sidebar { background: #222; padding: 20px; border-radius: 10px; width: 200px; border: 2px solid #00f3ff; }
        button.user-btn { 
            display: block; width: 100%; padding: 10px; margin-bottom: 10px; 
            cursor: pointer; background: #333; color: white; border: 1px solid #555; border-radius: 5px;
        }
        button.active { background: #00f3ff; color: black; font-weight: bold; border-color: white; }

        /* Main Display */
        .display { flex: 1; background: #222; padding: 20px; border-radius: 10px; border: 2px solid #00f3ff; }
        .stats { display: flex; justify-content: space-around; margin: 20px 0; }
        .stat-box { text-align: center; background: #111; padding: 10px; border-radius: 5px; width: 30%; }
        .stat-box h2 { color: #00f3ff; margin: 5px 0; }

        /* Input */
        .input-area { margin-top: 20px; display: flex; gap: 10px; }
        input { flex: 1; padding: 10px; border-radius: 5px; border: none; font-size: 16px; }
        .log-btn { padding: 10px 20px; background: #00ff88; border: none; border-radius: 5px; cursor: pointer; font-weight: bold; }
        
        #history { margin-top: 20px; font-size: 12px; color: #888; border-top: 1px solid #444; padding-top: 10px; }
    </style>
</head>
<body>

<div class="container">
    <div class="sidebar">
        <h3 style="margin-top:0">MEMBERS</h3>
        <button id="btn-Shivish" class="user-btn active" onclick="setUser('Shivish')">SHIVISH</button>
        <button id="btn-Father" class="user-btn" onclick="setUser('Father')">FATHER</button>
        <button id="btn-Mother" class="user-btn" onclick="setUser('Mother')">MOTHER</button>
        <div style="font-size:12px; margin-top:20px; color:#aaa;">
            DAILY GOAL: <b id="goal-p" style="color:#00ff88">45g</b> Protein
        </div>
    </div>

    <div class="display">
        <h2 id="user-title" style="margin-top:0">PROFILE: SHIVISH</h2>
        
        <div class="stats">
            <div class="stat-box">CALORIES<h2 id="v-c">0</h2></div>
            <div class="stat-box">SUGAR<h2 id="v-s">0</h2></div>
            <div class="stat-box">PROTEIN<h2 id="v-p">0</h2></div>
        </div>

        <div class="input-area">
            <input type="text" id="dish-name" placeholder="Type: Pizza, Dosa, Idli...">
            <button class="log-btn" onclick="logData()">LOG DISH</button>
        </div>

        <div id="history">History ready...</div>
    </div>
</div>

<script>
    var foodData = {
        "pizza": {c: 280, s: 5, p: 12},
        "dosa": {c: 170, s: 1, p: 4},
        "idli": {c: 65, s: 1, p: 2},
        "biryani": {c: 550, s: 2, p: 18}
    };

    var current = "Shivish";
    var userProfiles = {
        "Shivish": {c:0, s:0, p:0, h:[], goal:45},
        "Father": {c:0, s:0, p:0, h:[], goal:60},
        "Mother": {c:0, s:0, p:0, h:[], goal:50}
    };

    function setUser(name) {
        current = name;
        
        // Update Button Colors
        document.querySelectorAll('.user-btn').forEach(function(b) { b.classList.remove('active'); });
        document.getElementById('btn-' + name).classList.add('active');

        // Update Text
        document.getElementById('user-title').innerText = "PROFILE: " + name.toUpperCase();
        document.getElementById('goal-p').innerText = userProfiles[name].goal + "g";
        
        updateUI();
    }

    function logData() {
        var input = document.getElementById('dish-name');
        var name = input.value.toLowerCase().trim();
        if (name === "") return;

        var nutrients = foodData[name] || {c: 200, s: 5, p: 5};

        userProfiles[current].c += nutrients.c;
        userProfiles[current].s += nutrients.s;
        userProfiles[current].p += nutrients.p;
        userProfiles[current].h.unshift(new Date().toLocaleTimeString() + " - " + name.toUpperCase());

        input.value = "";
        updateUI();
    }

    function updateUI() {
        var u = userProfiles[current];
        document.getElementById('v-c').innerText = u.c;
        document.getElementById('v-s').innerText = u.s;
        document.getElementById('v-p').innerText = u.p;
        document.getElementById('history').innerHTML = u.h.join("<br>");
    }
</script>

</body>
</html>
