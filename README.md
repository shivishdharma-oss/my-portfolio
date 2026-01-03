<!DOCTYPE html>
<html>
<head>
    <title>AETHER HEALTH FIXED</title>
    <style>
        body { background: #000; color: #00f3ff; font-family: 'Courier New', monospace; padding: 20px; }
        
        /* Sidebar */
        #side { float: left; width: 200px; border: 2px solid #00f3ff; padding: 10px; min-height: 400px; }
        .u-btn { 
            display: block; width: 100%; padding: 15px; margin-bottom: 10px; 
            background: #111; color: #00f3ff; border: 1px solid #00f3ff; 
            cursor: pointer; font-weight: bold; text-align: left;
        }
        .u-btn:active { background: #00f3ff; color: #000; }
        .active-user { background: #00f3ff; color: #000; }

        /* Main */
        #main { margin-left: 230px; border: 2px solid #00f3ff; padding: 20px; min-height: 400px; }
        .box { display: inline-block; width: 30%; border: 1px solid #00f3ff; padding: 10px; text-align: center; }
        h2 { font-size: 40px; margin: 10px 0; }

        /* Input */
        #controls { margin-top: 30px; border-top: 2px solid #333; padding-top: 20px; }
        input { padding: 15px; width: 60%; background: #000; border: 1px solid #00f3ff; color: #fff; font-size: 18px; }
        button#log-btn { padding: 15px 30px; background: #00f3ff; color: #000; font-weight: bold; cursor: pointer; border: none; }
        
        #log { margin-top: 20px; height: 100px; overflow-y: auto; color: #555; font-size: 12px; }
    </style>
</head>
<body>

    <h1 style="text-align: center;">AETHER_HEALTH_SYSTEM_V18</h1>

    <div id="side">
        <p>SELECT USER:</p>
        <button id="b-Shivish" class="u-btn active-user" onclick="selUser('Shivish')">SHIVISH</button>
        <button id="b-Father" class="u-btn" onclick="selUser('Father')">FATHER</button>
        <button id="b-Mother" class="u-btn" onclick="selUser('Mother')">MOTHER</button>
        <hr>
        <p>PROTEIN GOAL:</p>
        <b id="goal-p">45g</b>
    </div>

    <div id="main">
        <h2 id="current-title">USER: SHIVISH</h2>
        
        <div class="box">CALORIES<h2 id="v-c">0</h2></div>
        <div class="box">SUGAR<h2 id="v-s">0</h2></div>
        <div class="box">PROTEIN<h2 id="v-p">0</h2></div>

        <div id="controls">
            <input type="text" id="food" placeholder="Type: Pizza, Dosa, etc.">
            <button id="log-btn" onclick="addFood()">LOG DISH</button>
        </div>

        <div id="log">History log ready...</div>
    </div>

<script>
    // Database
    var dishes = {
        "pizza": {c:280, s:5, p:12},
        "dosa": {c:170, s:1, p:4},
        "idli": {c:65, s:1, p:2},
        "biryani": {c:500, s:2, p:18}
    };

    var current = "Shivish";
    var users = {
        "Shivish": {c:0, s:0, p:0, h:[], g:45},
        "Father": {c:0, s:0, p:0, h:[], g:60},
        "Mother": {c:0, s:0, p:0, h:[], g:50}
    };

    function selUser(name) {
        current = name;
        
        // Update Buttons
        document.getElementById("b-Shivish").className = "u-btn";
        document.getElementById("b-Father").className = "u-btn";
        document.getElementById("b-Mother").className = "u-btn";
        document.getElementById("b-" + name).className = "u-btn active-user";

        document.getElementById("current-title").innerText = "USER: " + name.toUpperCase();
        document.getElementById("goal-p").innerText = users[name].g + "g";
        
        refresh();
    }

    function addFood() {
        var input = document.getElementById("food");
        var val = input.value.toLowerCase().trim();
        
        if (val == "") return;

        var stats = dishes[val] || {c:200, s:5, p:5}; // Default if not in list

        users[current].c += stats.c;
        users[current].s += stats.s;
        users[current].p += stats.p;
        users[current].h.unshift(new Date().toLocaleTimeString() + " - " + val.toUpperCase());

        input.value = "";
        refresh();
    }

    function refresh() {
        var u = users[current];
        document.getElementById("v-c").innerText = u.c;
        document.getElementById("v-s").innerText = u.s;
        document.getElementById("v-p").innerText = u.p;
        document.getElementById("log").innerHTML = u.h.join("<br>");
    }
</script>

</body>
</html>
