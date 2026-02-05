<script src="https://download.agora.io/sdk/release/AgoraRTC_N-4.11.0.js"></script>
<script>
    // --- 1. FIREBASE CONFIG ---
    const firebaseConfig = {
        apiKey: "AIzaSyAeitKH2BRrWgh5oKiUMDpjegJZRncDEOE",
        authDomain: "soka-live-3383a.firebaseapp.com",
        projectId: "soka-live-3383a",
        databaseURL: "https://soka-live-3383a-default-rtdb.firebaseio.com",
        appId: "1:47057232215:web:5f8d88af6628c2964d5e12"
    };
    firebase.initializeApp(firebaseConfig);
    const db = firebase.database();

    // --- 2. AGORA CONFIG ---
    const AGORA_APP_ID = "86d52bb9bb6c467d9c1611fce6e63f9f"; 
    const CHANNEL = "mechi_live";
    const agoraClient = AgoraRTC.createClient({ mode: "live", codec: "vp8" });

    // --- 3. KAZI ZA MTUMIAJI (USER) ---
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

    async function startWatching() {
        await agoraClient.setClientRole("audience");
        agoraClient.on("user-published", async (user, mediaType) => {
            await agoraClient.subscribe(user, mediaType);
            if (mediaType === "video") {
                const remotePlayerContainer = document.getElementById("video-container");
                remotePlayerContainer.innerHTML = ""; 
                user.videoTrack.play(remotePlayerContainer);
            }
            if (mediaType === "audio") { user.audioTrack.play(); }
        });
        try {
            await agoraClient.join(AGORA_APP_ID, CHANNEL, null, null);
        } catch (e) { console.log("Agora error: ", e); }
    }

    function checkUserStatus() {
        const savedPhone = localStorage.getItem('soka_user');
        if(savedPhone) {
            document.getElementById('step-payment').style.display = 'none';
            document.getElementById('step-register').style.display = 'none';
            document.getElementById('step-live').style.display = 'block';

            db.ref('wateja/' + savedPhone).on('value', async (snapshot) => {
                const data = snapshot.val();
                if(data && data.status === 'approved') {
                    document.getElementById('status-tag').innerText = "IMEKUBALIWA ✅";
                    document.getElementById('status-tag').style.color = "#00ff00";
                    document.getElementById('video-container').style.display = "block";
                    document.getElementById('status-msg').innerText = "Furahia Burudani Sasa!";
                    await startWatching();
                }
            });
        }
    }

    // --- 4. KAZI ZA ADMIN ---
    function toggleAdmin() {
        const pass = prompt("Ingiza Namba ya Siri ya Admin:");
        if(pass === "1234") {
            document.getElementById('user-view').style.display = 'none';
            document.getElementById('admin-panel').style.display = 'block';
            loadUsersForAdmin();
        } else { alert("Siri imekosewa!"); }
    }

    function loadUsersForAdmin() {
        db.ref('wateja').on('value', (snapshot) => {
            const list = document.getElementById('user-list');
            list.innerHTML = "";
            snapshot.forEach((child) => {
                const user = child.val();
                list.innerHTML += `
                    <div class="user-row">
                        <span>${user.phone} (${user.status})</span>
                        <div>
                            <button onclick="approveUser('${user.phone}')" style="background:green; color:white; border:none; padding:5px; border-radius:5px;">Ruhusu</button>
                            <button onclick="deleteUser('${user.phone}')" style="background:red; color:white; border:none; padding:5px; border-radius:5px;">Futa</button>
                        </div>
                    </div>`;
            });
        });
    }

    function approveUser(id) { db.ref('wateja/' + id).update({ status: 'approved' }); }
    function deleteUser(id) { if(confirm("Futa?")) db.ref('wateja/' + id).remove(); }

    window.onload = checkUserStatus;
</script>
