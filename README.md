<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AETHER_HEALTH // Dharmendiran</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&family=JetBrains+Mono:wght@300;500&display=swap');
        
        :root { 
            --safe-green: #00ff88; 
            --alert-red: #ff4444; 
            --neon-cyan: #00f3ff; 
            --bg-dark: #050505; 
        }
        
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body { background: var(--bg-dark); color: white; font-family: 'JetBrains Mono', monospace; height: 100vh; display: flex; justify-content: center; align-items: center; overflow: hidden; }

        .os-container { width: 95%; max-width: 1100px; height: 85vh; background: rgba(10, 10, 20, 0.95); border: 2px solid var(--neon-cyan); border-radius: 15px; display: grid; grid-template-columns: 250px 1fr; box-shadow: 0 0 30px rgba(0, 243, 255, 0.2); }

        /* LEFT PANEL: FAMILY MEMBERS */
        .family-side { border-right: 1px solid rgba(0, 243, 255, 0.3); padding: 20px; display: flex; flex-direction: column; gap: 15px; }
        .member-card { padding: 15px; border: 1px solid #333; border-radius: 8px; cursor: pointer; transition: 0.3s; }
        .member-card:hover, .member-card.active { border-color: var(--neon-cyan); background: rgba(0, 243, 255, 0.1); }
        .status-dot { width: 10px; height: 10px; border-radius: 50%; display: inline-block; margin-right: 10px; }

        /* MAIN CONTENT */
        .main-content { padding: 30px; overflow-y: auto; display: flex; flex-direction: column; gap: 20px; }
        .stats-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 20px; }
        .stat-box { background: rgba(255,255,255,0.05); padding: 20px; border-radius: 10px; border-left: 4px solid var(--neon-cyan); }
        
        /* ALERT BOX */
        .alert-banner { padding: 15px; border-radius: 8px; font-weight: bold; text-align: center; display: none; }
        .alert-danger { display: block; background: rgba(255, 68, 68, 0.2); border: 1px solid var(--alert-red); color: var(--alert-red); animation: blink 1s infinite; }
        @keyframes blink { 50% { opacity: 0.6; } }

        /* INPUT AREA */
        .input-group { background: #111; padding: 20px; border-radius: 10px; display: flex; gap: 10px; border: 1px solid #333; }
        input, select { background: #000; border: 1px solid #444; color: white; padding: 10px; border-radius: 5px; outline: none; }
        button { background: var(--neon-cyan); border: none; padding: 10px 20px; color: black; font-weight: bold; cursor: pointer; border-radius: 5px; font-family: 'Orbitron'; }
    </style>
</head>
<body>

    <div class="os-container">
        <div class="family-side">
            <h3 style="color:var(--neon-cyan); font-family: 'Orbitron'; margin-bottom: 20px;">FAMILY UNIT</h3>
            <div class="member-card active" onclick="selectMember('Dad')">
                <span class="status-dot" style="background:var(--safe-green)"></span> Dad
            </div>
            <div class="member-card" onclick="selectMember('Mom')">
                <span class="status-dot" style="background:var(--safe-green)"></span> Mom
            </div>
            <div class="member-card" onclick="selectMember('Kid 1')">
                <span class="status-dot" id="kid-status" style="background:var(--safe-green)"></span> Kid 1
            </div>
        </div>

        <div class="main-content">
            <div id="healthAlert" class="alert-banner alert-danger">ALERT: SUGAR LIMIT EXCEEDED</div>
            
            <h1 id="memberName" style="font-family: 'Orbitron';">Profile: Dad</h1>
            <p style="font-size: 12px; color: #888;">Lead Developer: Dharmendiran.ar</p>

            <div class="stats-grid">
                <div class="stat-box">
                    <p style="font-size: 12px; color: #888;">Calories</p>
                    <h2 id="calVal">0</h2>
                </div>
                <div class="stat-box">
                    <p style="font-size: 12px; color: #888;">Sugar (g)</p>
                    <h2 id="sugarVal">0</h2>
                </div>
                <div class="stat-box">
                    <p style="font-size: 12px; color: #888;">Protein (g)</p>
                    <h2 id="proVal">0</h2>
                </div>
            </div>

            <div class="input-group">
                <select id="foodItem">
                    <option value="apple">Apple (10g sugar)</option>
                    <option value="soda">Soda (40g sugar)</option>
                    <option value="chicken">Chicken (0g sugar)</option>
                    <option value="donut">Donut (25g sugar)</option>
                </select>
                <button onclick="addIntake()">LOG INTAKE</button>
                <button style="background:#444; color:white;" onclick="resetStats()">RESET</button>
            </div>

            <div id="history" style="font-size: 13px; color: #aaa; margin-top: 10px;">
                Intake History: Waiting for data...
            </div>
        </div>
    </div>

    <script>
        // DATA OBJECTS
        let currentMember = "Dad";
        let familyData = {
            "Dad": { cal: 0, sugar: 0, pro: 0 },
            "Mom": { cal: 0, sugar: 0, pro: 0 },
            "Kid 1": { cal: 0, sugar: 0, pro: 0 }
        };

        const foodDb = {
            "apple": { cal: 52, sugar: 10, pro: 1 },
            "soda": { cal: 150, sugar: 40, pro: 0 },
            "chicken": { cal: 200, sugar: 0, pro: 30 },
            "donut": { cal: 250, sugar: 25, pro: 2 }
        };

        function selectMember(name) {
            currentMember = name;
            document.getElementById('memberName').innerText = "Profile: " + name;
            // Update active state in UI
            document.querySelectorAll('.member-card').forEach(c => c.classList.remove('active'));
            event.currentTarget.classList.add('active');
            updateDisplay();
        }

        function addIntake() {
            const food = document.getElementById('foodItem').value;
            const data = foodDb[food];

            familyData[currentMember].cal += data.cal;
            familyData[currentMember].sugar += data.sugar;
            familyData[currentMember].pro += data.pro;

            updateDisplay();
            checkAlerts();
        }

        function updateDisplay() {
            const m = familyData[currentMember];
            document.getElementById('calVal').innerText = m.cal;
            document.getElementById('sugarVal').innerText = m.sugar;
            document.getElementById('proVal').innerText = m.pro;
        }

        function checkAlerts() {
            const sugar = familyData[currentMember].sugar;
            const alertBox = document.getElementById('healthAlert');
            
            if (sugar > 35) {
                alertBox.style.display = 'block';
            } else {
                alertBox.style.display = 'none';
            }
        }

        function resetStats() {
            familyData[currentMember] = { cal: 0, sugar: 0, pro: 0 };
            updateDisplay();
            checkAlerts();
        }
    </script>
</body>
</html>
