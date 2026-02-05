sokalive games
<html lang="sw">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Soka Live Pro - GitHub Edition</title>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database-compat.js"></script>
    
    <style>
        :root { --primary: #00d2ff; --bg: #0a0a0a; --card: #1a1a1a; --text: #ffffff; }
        body { margin: 0; background: var(--bg); color: var(--text); font-family: 'Segoe UI', sans-serif; }
        
        /* UI Utangulizi */
        .app-header { background: var(--card); padding: 15px; text-align: center; border-bottom: 2px solid var(--primary); position: sticky; top: 0; z-index: 1000; }
        .container { padding: 20px; padding-bottom: 100px; max-width: 500px; margin: auto; }
        
        /* Kadi za Vifurushi */
        .plan-card { background: var(--card); border-radius: 15px; padding: 20px; margin-bottom: 20px; border: 1px solid #333; text-align: center; }
        .price { font-size: 26px; font-weight: bold; color: var(--primary); margin: 10px 0; }
        
        /* Input na Button */
        input { width: 100%; padding: 12px; margin: 10px 0; border-radius: 10px; border: 1px solid #444; background: #000; color: white; box-sizing: border-box; }
        .btn { width: 100%; padding: 15px; border-radius: 10px; border: none; background: var(--primary); font-weight: bold; font-size: 16px; cursor: pointer; transition: 0.3s; }
        .btn:active { transform: scale(0.95); }

        /* Admin Section (Hidden by Default) */
        #admin-panel { display: none; background: #fff; color: #000; padding: 20px; min-height: 100vh; }
        .user-row { border-bottom: 1px solid #ccc; padding: 10px 0; display: flex; justify-content: space-between; align-items: center; }
        
        /* Bottom Nav */
        .bottom-nav { position: fixed; bottom: 0; width: 100%; background: var(--card); display: flex; justify-content: space-around; padding: 15px 0; border-top: 1px solid #333; }
        .nav-item { font-size: 12px; color: #888; text-align: center; cursor: pointer; }
        .nav-item.active { color: var(--primary); }

        /* Video Area */
        #video-container { width: 100%; height: 220px; background: #000; border-radius: 15px; display: none; margin-top: 10px; border: 2px solid var(--primary); }
    </style>
</head>
<body>

    <div id="user-view">
        <div class="app-header">
            <strong>SOKA LIVE PRO ⚽</strong>
        </div>

        <div class="container" id="page-content">
            <div id="step-payment">
                <div class="plan-card">
                    <div style="background:var(--primary); color:#000; font-size:10px; width:fit-content; padding:2px 8px; border-radius:5px; margin:auto;">WIKI</div>
                    <div class="price">Tsh 2,000</div>
                    <button class="btn" onclick="showRegistration('Wiki', 2000)">CHAGUA HII</button>
                </div>

                <div class="plan-card" style="border-color: gold;">
                    <div style="background:gold; color:#000; font-size:10px; width:fit-content; padding:2px 8px; border-radius:5px; margin:auto;">MWEZI</div>
                    <div class="price">Tsh 5,000</div>
                    <button class="btn" style="background:gold;" onclick="showRegistration('Mwezi', 5000)">CHAGUA HII</button>
                </div>
                <p style="text-align:center; font-size:12px; color:#666;">Lipia kwa namba: 567890 (Vodacom)</p>
            </div>

            <div id="step-register" style="display:none;">
                <h3>Kamilisha Usajili</h3>
                <p id="plan-info"></p>
                <input type="number" id="userPhone" placeholder="Ingiza namba ya muamala/simu">
                <button class="btn" onclick="submitToFirebase()">TUMA MAOMBI</button>
                <button onclick="location.reload()" style="background:none; border:none; color:#888; margin-top:15px; width:100%;">Rudi nyuma</button>
            </div>

            <div id="step-live" style="display:none;">
                <div class="plan-card">
                    <h4>Hali ya Akaunti: <span id="status-tag" style="color:orange;">Inasubiri...</span></h4>
                    <div id="video-container">
                        <div style="display:flex; align-items:center; justify-content:center; height:100%;">🔴 MECHI IPO LIVE</div>
                    </div>
                    <p id="status-msg">Subiri kidogo, Admin anakagua malipo yako.</p>
                </div>
            </div>
        </div>

        <div class="bottom-nav">
            <div class="nav-item active">🏠<br>Nyumbani</div>
            <div class="nav-item" onclick="toggleAdmin()">⚙️<br>Settings</div>
        </div>
    </div>

    <div id="admin-panel">
        <button onclick="toggleAdmin()" style="background:red; color:white; padding:10px; border:none; border-radius:5px;">X FUNGA ADMIN</button>
        <h2>👑 Dashboard</h2>
        <div id="admin-controls">
            <p>Badili Namba ya Malipo: <input type="text" id="payNum" placeholder="07..."></p>
            <button class="btn" onclick="saveAdminSettings()">Hifadhi Settings</button>
        </div>
        <hr>
        <h3>Wateja Wanaosubiri</h3>
        <div id="user-list">Inapakia...</div>
    </div>

    <script>
        // --- FIREBASE CONFIG (YAKO ULIONIPA) ---
        const firebaseConfig = {
            apiKey: "AIzaSyAeitKH2BRrWgh5oKiUMDpjegJZRncDEOE",
            authDomain: "soka-live-3383a.firebaseapp.com",
            projectId: "soka-live-3383a",
            databaseURL: "https://soka-live-3383a-default-rtdb.firebaseio.com",
            appId: "1:47057232215:web:5f8d88af6628c2964d5e12"
        };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.database();

        // --- USER FUNCTIONS ---
        function showRegistration(plan, price) {
            document.getElementById('step-payment').style.display = 'none';
            document.getElementById('step-register').style.display = 'block';
            document.getElementById('plan-info').innerText = "Umechagua Kifurushi cha " + plan + " (Tsh " + price + ")";
        }

        function submitToFirebase() {
            const phone = document.getElementById('userPhone').value;
            if(phone.length < 5) return alert("Ingiza taarifa sahihi!");
            
            localStorage.setItem('soka_user', phone);
            db.ref('wateja/' + phone).set({
                phone: phone,
                status: 'pending',
                timestamp: Date.now()
            });
            checkUserStatus();
        }

        function checkUserStatus() {
            const savedPhone = localStorage.getItem('soka_user');
            if(savedPhone) {
                document.getElementById('step-payment').style.display = 'none';
                document.getElementById('step-register').style.display = 'none';
                document.getElementById('step-live').style.display = 'block';

                db.ref('wateja/' + savedPhone).on('value', (snapshot) => {
                    const data = snapshot.val();
                    if(data && data.status === 'approved') {
                        document.getElementById('status-tag').innerText = "IMEKUBALIWA ✅";
                        document.getElementById('status-tag').style.color = "#00ff00";
                        document.getElementById('video-container').style.display = "block";
                        document.getElementById('status-msg').innerText = "Furahia Burudani Sasa!";
                    }
                });
            }
        }

        // --- ADMIN FUNCTIONS ---
        function toggleAdmin() {
            const pass = prompt("Ingiza Namba ya Siri ya Admin:");
            if(pass === "1234") { // Namba ya siri ya kuingia Admin
                document.getElementById('user-view').style.display = 'none';
                document.getElementById('admin-panel').
