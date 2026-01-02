<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>AETHER_HEALTH_FIX</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&display=swap');
        
        body { 
            background: #050505; color: white; font-family: sans-serif; 
            margin: 0; padding: 20px; display: flex; gap: 20px; height: 95vh;
        }

        /* SIDEBAR */
        #sidebar { 
            width: 250px; background: #111; border: 2px solid #00f3ff; 
            padding: 20px; display: flex; flex-direction: column; gap: 10px;
        }
        .user-btn { 
            padding: 15px; background: #222; border: 1px solid #444; 
            color: white; cursor: pointer; font-family: 'Orbitron'; text-align: left;
        }
        .user-btn:hover { background: #333; }
        .user-btn.active { background: #00f3ff; color: black; font-weight: bold; }

        /* MAIN CONTENT */
        #main { 
            flex: 1; background: #111; border: 2px solid #00f3ff; 
            padding: 30px; display: flex; flex-direction: column;
        }
        .stats-row { display: flex; gap: 20px; margin-bottom: 30px; }
        .stat-card { 
            flex: 1; background: #000; border: 1px solid #00f3ff; 
            padding: 20px; text-align: center;
        }
        .stat-card h2 { font-family: 'Orbitron'; font-size: 35px; margin: 10px 0; color: #00f3ff; }

        /* LOGGING */
        .log-area { margin-top: auto; }
        input { 
            width: 70%; padding: 15px; font-size: 18px; 
            background: #000; border: 1px solid #00f3ff; color: white; 
        }
        button#log-btn { 
            width: 28%; padding: 15px; font-size: 18px; 
            background: #00f3ff; color: black; font-family: 'Orbitron'; 
            font-weight: bold; cursor: pointer; border: none;
        }

        #history { 
            margin-top: 20px; height: 150px; overflow-y: auto; 
            color: #888; border-top: 1px solid #222; padding-top: 10px;
        }
    </style>
</head>
<body>

    <div id="sidebar">
        <h3 style="font-family:'Orbitron'; color:#00f3ff; margin-top:0;">SELECT USER</h3>
        <button class="user-btn active" onclick="switchUser('Shivish')">SHIVISH</button>
        <button class="user-btn" onclick="switchUser('Father')">FATHER</button>
        <button class="user-btn" onclick="switchUser('Mother')">MOTHER</button>
        
        <div style="margin-top:auto; font-size:12px; color:#555;">
            PROTEIN GOAL: <span id="goal-text" style="color:#00ff88">45g</span>
        </div>
    </div>

    <div id="main">
        <h1 id="title" style="font-family:'Orbitron'; margin:0 0 20px 0;">USER: SHIVISH</h1>
        
        <div class="stats-row">
            <div class="stat-card"><span>CALORIES</span><h2 id="c-val">0</h2></div>
            <div class="stat-card"><span>SUGAR (g)</span><h2 id="s-val">0</h2></div>
            <div class="stat-card"><span>PROTEIN (g)</span><h2 id="p-val">0</h2></div>
        </div>

        <div id="history">Log history is empty...</div>

        <div class="log-area">
            <input type="text" id="food-input" placeholder="Type: Pizza, Dosa, Idli...">
            <button id="log-btn" onclick="addItem()">LOG DISH</button>
        </div>
    </div>

<script>
    // Simple Database
    const foodList = {
        "pizza": {c:280, s:5, p:12},
        "dosa": {c:170, s:1, p:4},
        "idli": {c:65, s:1, p:2},
        "biryani": {c:550, s:2, p:18}
    };

    let currentUser = "Shivish";
    const users = {
        "Shivish": {c:0, s:0, p:0, h:[], goal:45},
        "Father": {c:0, s:0, p:0, h:[], goal:60},
        "Mother": {c:0, s:0, p:0, h:[], goal:50}
    };

    function switchUser(name) {
        currentUser = name;
        
        // Update Buttons
        document.querySelectorAll('.user-btn').forEach(btn => {
            btn.classList.remove('active');
            if(btn.innerText === name.toUpperCase()) btn.classList.add('active');
        });

        // Update Text
        document.getElementById('title').innerText = "USER: " + name.toUpperCase();
        document.getElementById('goal-text').innerText = users[name].goal + "g";
        
        render();
    }

    function addItem() {
        const input = document.getElementById('food-input');
        const dish = input.value.toLowerCase().trim();
        
        if(dish === "") return;

        // Get nutrients (or default if not in list)
        const stats = foodList[dish] || {c:200, s:5, p:5};

        users[currentUser].c += stats.c;
        users[currentUser].s += stats.s;
        users[currentUser].p += stats.p;
        users[currentUser].h.unshift(new Date().toLocaleTimeString() + " - " + dish.toUpperCase());

        input.value = "";
        render();
    }

    function render() {
        const u = users[currentUser];
        document.getElementById('c-val').innerText = u.c;
        document.getElementById('s-val').innerText = u.s;
        document.getElementById('p-val').innerText = u.p;
        document.getElementById('history').innerHTML = u.h.join("<br>");
    }
</script>

</body>
</html>
