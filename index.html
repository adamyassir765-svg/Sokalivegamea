<!DOCTYPE html>
<html lang="sw">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Soka Live Pro - App</title>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database-compat.js"></script>
    <script src="https://download.agora.io/sdk/release/AgoraRTC_N-4.11.0.js"></script>
    
    <style>
        :root { --primary: #00d2ff; --bg: #0a0a0a; --card: #1a1a1a; }
        body { margin: 0; background: var(--bg); color: white; font-family: sans-serif; }
        .app-header { background: var(--card); padding: 15px; text-align: center; border-bottom: 2px solid var(--primary); }
        .container { padding: 20px; max-width: 500px; margin: auto; }
        .card { background: var(--card); border-radius: 15px; padding: 20px; margin-bottom: 20px; border: 1px solid #333; }
        .btn { width: 100%; padding: 15px; border-radius: 10px; border: none; background: var(--primary); font-weight: bold; cursor: pointer; color: black; }
        input { width: 100%; padding: 12px; margin: 10px 0; border-radius: 10px; border: 1px solid #444; background: #000; color: white; box-sizing: border-box; }
        #video-container { width: 100%; height: 230px; background: #000; border-radius: 15px; display: none; border: 2px solid var(--primary); overflow: hidden; }
        .bottom-nav { position: fixed; bottom: 0; width: 100%; background: var(--card); display: flex; justify-content: space-around; padding: 15px 0; border-top: 1px solid #333; }
        
        /* Admin Styling */
        #admin-panel { display: none; background: #f4f4f4; color: #000; min-height: 100vh; padding: 20px; }
        .user-row { background: white; padding: 10px; margin-bottom: 5px; border-radius: 8px; display: flex; justify-content: space-between; align-items: center; }
        .live-control { background: #000; color: white; padding: 20px; border-radius: 15px; margin-bottom: 20px; text-align: center; }
    </style>
</head>
<body>

    <div id="user-view">
        <div class="app-header"><strong>SOKA LIVE PRO ⚽</strong></div>
        <div class="container">
            <div id="auth-section">
                <div class="card">
                    <h3>Pata Access ya Mechi</h3>
                    <p style="font-size: 13px; color: #888;">Ingiza namba yako uliyolipia kuanza kutazama.</p>
                    <input type="number" id="userPhone" placeholder="Namba ya Simu">
                    <button class="btn" onclick="submitToFirebase()">INGIA KWENYE LIVE</button>
                    <p style="font-size: 11px; margin-top: 10px;">LIPA: 2000 (Wiki) / 5000 (Mwezi) kwenda 567890</p>
                </div>
            </div>

            <div id="live-section" style="display:none;">
                <div class="card">
                    <h4>Hali: <span id="status-tag" style="color:orange;">Inasubiri...</span></h4>
                    <div id="video-container"></div>
                    <p id="status-msg" style="font-size: 13px;">Admin akishakubali, mechi itatokea hapa.</p>
                </div>
            </div>
        </div>

        <div class="bottom-nav">
            <div style="cursor:pointer" onclick="location.reload()">🏠<br><small>Home</small></div>
            <div style="cursor:pointer" onclick="toggleAdmin()">⚙️<br><small>Admin</small></div>
        </div>
    </div>

    <div id="admin-panel">
        <button onclick="location.reload()" style="background:red; color:white; border:none; padding:10px; border-radius:5px;">X FUNGA ADMIN</button>
        
        <div class="live-control">
            <h3 style="color:var(--primary);">CONTROL CENTER</h3>
            <button id="start-live-btn" class="btn" onclick="startBroadcasting()" style="background:#ff4444; color:white;">🔴 ANZA KURUSHA KIOO (LIVE)</button>
            <p id="host-status" style="font-size: 12px; margin-top:10px;">Hali: Hujarusha bado</p>
        </div>

        <h3>Wateja Wanaosubiri</h3>
        <div id="user-list">Inapakia...</div>
    </div>

    <script>
        // --- CONFIG ---
        const firebaseConfig = {
            apiKey: "AIzaSyAeitKH2BRrWgh5oKiUMDpjegJZRncDEOE",
            authDomain: "soka-live-3383a.firebaseapp.com",
            projectId: "soka-live-3383a",
            databaseURL: "https://soka-live-3383a-default-rtdb.firebaseio.com",
            appId: "1:47057232215:web:5f8d88af6628c2964d5e12"
        };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.database();

        const AGORA_APP_ID = "86d52bb9bb6c467d9c1611fce6e63f9f"; 
        const CHANNEL = "mechi_live";
        const agoraClient = AgoraRTC.createClient({ mode: "live", codec: "vp8" });

        // --- USER LOGIC ---
        function submitToFirebase() {
            const phone = document.getElementById('userPhone').value;
            if(phone.length < 5) return alert("Namba haijatimia!");
            localStorage.setItem('soka_user', phone);
            db.ref('wateja/' + phone).set({ phone: phone, status: 'pending' });
            checkUserStatus();
        }

        function checkUserStatus() {
            const savedPhone = localStorage.getItem('soka_user');
            if(savedPhone) {
                document.getElementById('auth-section').style.display = 'none';
                document.getElementById('live-section').style.display = 'block';
                db.ref('wateja/' + savedPhone).on('value', async (snap) => {
                    const data = snap.val();
                    if(data && data.status === 'approved') {
                        document.getElementById('status-tag').innerText = "IMEKUBALIWA ✅";
                        document.getElementById('status-tag').style.color = "#00ff00";
                        document.getElementById('video-container').style.display = "block";
                        await startWatching();
                    }
                });
            }
        }

        async function startWatching() {
            await agoraClient.setClientRole("audience");
            agoraClient.on("user-published", async (user, mediaType) => {
                await agoraClient.subscribe(user, mediaType);
                if (mediaType === "video") {
                    const container = document.getElementById("video-container");
                    container.innerHTML = "";
                    user.videoTrack.play(container);
                }
                if (mediaType === "audio") user.audioTrack.play();
            });
            await agoraClient.join(AGORA_APP_ID, CHANNEL, null, null);
        }

        // --- ADMIN & HOST LOGIC ---
        function toggleAdmin() {
            if(prompt("Siri ya Admin:") === "1234") {
                document.getElementById('user-view').style.display = 'none';
                document.getElementById('admin-panel').style.display = 'block';
                loadUsers();
            }
        }

        function loadUsers() {
            db.ref('wateja').on('value', (snap) => {
                const list = document.getElementById('user-list');
                list.innerHTML = "";
                snap.forEach((child) => {
                    const u = child.val();
                    list.innerHTML += `<div class="user-row">
                        <span>${u.phone}</span>
                        <button onclick="approve('${u.phone}')" style="background:green;color:white;border:none;padding:5px;">Ruhusu</button>
                    </div>`;
                });
            });
        }

        function approve(id) { db.ref('wateja/' + id).update({ status: 'approved' }); }

        async function startBroadcasting() {
            try {
                await agoraClient.setClientRole("host");
                const screenTrack = await AgoraRTC.createScreenVideoTrack({ optimizationMode: "detail" });
                await agoraClient.publish(screenTrack);
                document.getElementById('host-status').innerText = "Hali: UPO LIVE SASA! ✅";
                alert("Umeanza kurusha kioo! Sasa fungua eFootball na ucheze.");
            } catch (e) {
                alert("Hitilafu: Hakikisha unatumia Chrome na umeruhusu Screen Cast.");
            }
        }

        window.onload = checkUserStatus;
    </script>
</body>
</html>
