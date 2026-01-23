<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Lively Host - Fan Community</title>
  <meta name="description" content="Real-time chat for Emmanuel Rising & football fans. Earn points, climb leaderboard, watch live!"/>
  
  <!-- PWA Manifest -->
  <link rel="manifest" href="data:application/manifest+json,{
    'name':'Lively Host',
    'short_name':'LivelyHost',
    'start_url':'./',
    'display':'standalone',
    'background_color':'#075e54',
    'theme_color':'#00a884',
    'icons':[
      {'src':'https://img.icons8.com/fluency/64/microphone.png','sizes':'64x64','type':'image/png'},
      {'src':'https://img.icons8.com/fluency/512/microphone.png','sizes':'512x512','type':'image/png'}
    ]
  }">
  
  <!-- OG for viral sharing -->
  <meta property="og:title" content="Lively Host - Join the Fan Vibe!"/>
  <meta property="og:description" content="Chat live with Emmanuel Rising & football fans, earn points, leaderboard battles 🔥"/>
  <meta property="og:image" content="https://img.icons8.com/fluency/512/microphone.png"/>
  
  <link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;500;700&display=swap" rel="stylesheet"/>
  
  <script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-auth-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-database-compat.js"></script>
  
  <style>
    * { margin:0; padding:0; box-sizing:border-box; font-family:'Roboto', sans-serif; }
    body { background:#0b141a; color:#e9edef; height:100vh; display:flex; flex-direction:column; }
    header { background:#075e54; padding:14px 18px; display:flex; align-items:center; justify-content:space-between; font-size:20px; font-weight:500; position:sticky; top:0; z-index:100; }
    .back { font-size:26px; cursor:pointer; display:none; }
    .tabs { display:flex; background:#111c21; overflow-x:auto; }
    .tabs button { padding:12px 20px; background:none; border:none; color:#8696a0; font-size:14px; white-space:nowrap; cursor:pointer; transition:0.3s; flex:1; text-align:center; }
    .tabs button.active { color:#00a884; border-bottom:3px solid #00a884; }
    .screen { flex:1; overflow-y:auto; display:none; }
    .screen.active { display:flex; flex-direction:column; }
    input, select { width:100%; padding:14px; margin:12px 0; background:#2a3942; border:none; border-radius:12px; color:white; font-size:16px; }
    button.primary { background:#00a884; padding:14px; border-radius:12px; font-size:18px; width:100%; cursor:pointer; border:none; color:white; }
    .messages { flex:1; padding:12px; overflow-y:auto; display:flex; flex-direction:column; background:#0b141a; }
    .bubble { max-width:80%; padding:10px 16px; border-radius:18px; margin:6px 0; word-wrap:break-word; }
    .bubble.left { background:#262d31; align-self:flex-start; border-bottom-left-radius:4px; }
    .bubble.right { background:#005c4b; align-self:flex-end; border-bottom-right-radius:4px; }
    .input-bar { background:#111c21; padding:8px; display:flex; align-items:center; }
    .input-bar input { flex:1; padding:12px 16px; background:#2a3942; border:none; border-radius:25px; color:white; margin:0 8px; }
    .input-bar button { background:#00a884; color:white; border:none; width:48px; height:48px; border-radius:50%; font-size:22px; cursor:pointer; }
    .live-section { padding:20px; text-align:center; flex:1; }
    iframe { width:100%; height:360px; border-radius:12px; border:none; margin:20px 0; }
    #loginScreen { padding:40px 20px; text-align:center; justify-content:center; }
    #errorMsg { color:#ff6b6b; margin:12px 0; }
    .viral-section { padding:20px; background:#111c21; margin:16px; border-radius:16px; text-align:center; }
    .viral-section h3 { color:#00a884; margin-bottom:8px; }
    #referralLink { background:#2a3942; padding:12px; border-radius:12px; word-break:break-all; margin:12px 0; font-size:14px; }
  </style>
</head>
<body>

  <!-- Login -->
  <div id="loginScreen" class="screen active">
    <h2>Welcome to Lively Host</h2>
    <p>Sign in with phone to join fans worldwide</p>
    <input type="tel" id="phone" placeholder="+2348012345678 or international" />
    <button class="primary" id="sendCodeBtn">Send Code</button>
    <input type="text" id="code" placeholder="Enter code" style="display:none;" />
    <button class="primary" id="verifyCodeBtn" style="display:none;">Verify</button>
    <div id="errorMsg"></div>
  </div>

  <!-- Header -->
  <header>
    <div>Lively Host</div>
    <div class="back" onclick="showScreen('home')">←</div>
  </header>

  <!-- Tabs -->
  <div class="tabs">
    <button class="active" onclick="showScreen('home')">Home</button>
    <button onclick="showScreen('chat')">Chat</button>
    <button onclick="showScreen('live')">Live</button>
    <button onclick="showScreen('leaderboard')">Leaderboard</button>
  </div>

  <!-- Home -->
  <div id="home" class="screen">
    <div style="padding:20px;">
      <h3>Join Communities</h3>
      <select id="musician">
        <option value="">Musician Fanbase</option>
        <option value="Emmanuel Rising">Emmanuel Rising</option>
        <option value="Burna Boy">Burna Boy</option>
      </select>
      <select id="football">
        <option value="">Football Team</option>
        <option value="Manchester United">Manchester United</option>
        <option value="Arsenal">Arsenal</option>
        <option value="Real Madrid">Real Madrid</option>
      </select>
      <button class="primary" onclick="updateCommunity()" style="margin:16px 0;">Join & Chat</button>
      
      <div class="viral-section">
        <h3>Invite Friends – Earn Points!</h3>
        <p>Share your link → +50 pts per friend who joins & sends 1 msg</p>
        <div id="referralLink"></div>
        <button class="primary" onclick="shareApp()">Share Now</button>
      </div>
      
      <div id="communityInfo" style="margin-top:20px; padding:16px; background:#111c21; border-radius:12px;">Select communities to start earning!</div>
    </div>
  </div>

  <!-- Chat -->
  <div id="chat" class="screen">
    <div class="chat-header">
      <img src="https://img.icons8.com/fluency/512/microphone.png" alt="Group"/>
      <div class="info">
        <div class="name">Global Fan Chat</div>
        <div class="status">Live • Fans online now</div>
      </div>
    </div>
    <div id="messages" class="messages"></div>
    <div class="input-bar">
      <input type="text" id="msgInput" placeholder="Type message..."/>
      <button onclick="sendMessage()">➤</button>
    </div>
  </div>

  <!-- Live -->
  <div id="live" class="screen live-section">
    <h2>Live Streaming</h2>
    <iframe src="https://www.youtube.com/embed/live_stream?autoplay=1" allowfullscreen></iframe>
    <p>Watch live sessions, matches & highlights!</p>
  </div>

  <!-- Leaderboard -->
  <div id="leaderboard" class="screen">
    <h2 style="text-align:center; padding:20px;">Top Fans Leaderboard</h2>
    <ul id="activityLeaderboard" style="list-style:none; padding:0 20px;"></ul>
    <div style="padding:20px; text-align:center;">
      <button class="primary" onclick="shareLeaderboard()">Share My Rank!</button>
    </div>
  </div>

  <script>
    // Firebase Config
    const firebaseConfig = {
      apiKey: "AIzaSyA_ReP7p9iy_uHv7zUpxiubIimO_sA8nPc",
      authDomain: "lively-host-official.firebaseapp.com",
      databaseURL: "https://lively-host-official-default-rtdb.firebaseio.com",
      projectId: "lively-host-official",
      storageBucket: "lively-host-official.firebasestorage.app",
      messagingSenderId: "418399137086",
      appId: "1:418399137086:web:69aa5e46dd798bff4b906f",
      measurementId: "G-R9GF89QK3J"
    };

    firebase.initializeApp(firebaseConfig);
    const db = firebase.database();
    const auth = firebase.auth();

    let user = { uid: null, name: null, points: 0 };

    // Phone Auth
    const sendCodeBtn = document.getElementById('sendCodeBtn');
    const verifyCodeBtn = document.getElementById('verifyCodeBtn');
    const errorMsg = document.getElementById('errorMsg');

    window.recaptchaVerifier = new firebase.auth.RecaptchaVerifier(sendCodeBtn, { 'size': 'invisible' });

    sendCodeBtn.onclick = () => {
      errorMsg.textContent = '';
      const phone = document.getElementById('phone').value.trim();
      if (!phone.startsWith('+') || phone.length < 10) {
        errorMsg.textContent = 'Use international format, e.g. +234...';
        return;
      }
      auth.signInWithPhoneNumber(phone, window.recaptchaVerifier)
        .then(confirmation => {
          window.confirmationResult = confirmation;
          document.getElementById('code').style.display = 'block';
          sendCodeBtn.style.display = 'none';
          verifyCodeBtn.style.display = 'block';
          errorMsg.textContent = 'Code sent!';
        })
        .catch(err => errorMsg.textContent = err.message);
    };

    verifyCodeBtn.onclick = () => {
      const code = document.getElementById('code').value.trim();
      if (!code) return;
      window.confirmationResult.confirm(code)
        .then(result => {
          user.uid = result.user.uid;
          user.name = 'Fan' + Math.random().toString(36).slice(2, 8); // Random temp name; user can change later if you add profile

          db.ref('users/' + user.uid).transaction(data => {
            if (!data) {
              return { 
                name: user.name, 
                points: 20, 
                createdAt: Date.now() 
              }; // Welcome bonus
            }
            user.name = data.name;
            user.points = data.points || 0;
            return data;
          });

          document.getElementById('loginScreen').style.display = 'none';
          showScreen('home');
          loadUserData();
          checkReferral();
        })
        .catch(() => errorMsg.textContent = 'Invalid code');
    };

    auth.onAuthStateChanged(u => {
      if (u) {
        user.uid = u.uid;
        document.getElementById('loginScreen').style.display = 'none';
        showScreen('home');
        loadUserData();
        checkReferral();
      }
    });

    function loadUserData() {
      if (!user.uid) return;
      db.ref('users/' + user.uid).on('value', snap => {
        const data = snap.val();
        if (data) {
          user.name = data.name;
          user.points = data.points || 0;
          updateReferralLink();
        }
      });
    }

    // Referral
    const urlParams = new URLSearchParams(window.location.search);
    const referrerUid = urlParams.get('ref'); // Now using UID as ref

    function checkReferral() {
      if (referrerUid && user.uid && referrerUid !== user.uid) {
        db.ref('users/' + referrerUid + '/referred').transaction(exists => {
          if (!exists) {
            db.ref('users/' + referrerUid + '/points').transaction(p => (p || 0) + 50);
            return true;
          }
          return exists;
        });
        addPoints(20, true);
      }
    }

    function updateReferralLink() {
      if (!user.uid) return;
      const link = `https://lively-host-official.web.app/?ref=${user.uid}`;
      document.getElementById('referralLink').textContent = link;
    }

    function shareApp() {
      const text = `Join Lively Host – live fan chat for Emmanuel Rising & football! Earn points & leaderboard 🔥\n\nMy invite: https://lively-host-official.web.app/?ref=${user.uid || 'fan'}`;
      if (navigator.share) {
        navigator.share({ title: 'Lively Host', text, url: 'https://lively-host-official.web.app' });
      } else {
        navigator.clipboard.writeText(text).then(() => alert('Copied! Share in WhatsApp/Twitter'));
      }
    }

    function shareLeaderboard() {
      const text = `I'm on the Lively Host leaderboard with \( {user.points} pts! Beat me? Join: https://lively-host-official.web.app/?ref= \){user.uid}`;
      navigator.clipboard.writeText(text).then(() => alert('Copied – share your rank!'));
    }

    // Community
    function updateCommunity() {
      const mus = document.getElementById('musician').value;
      const foot = document.getElementById('football').value;
      let text = 'Active in: ' + (mus || 'None') + ' + ' + (foot || 'None');
      document.getElementById('communityInfo').innerHTML = `<strong>${text}</strong><br>Chat & earn!`;
    }

    // Chat & points
    function sendMessage() {
      if (!user.uid || !user.name) return alert('Sign in first');
      const input = document.getElementById('msgInput');
      const text = input.value.trim();
      if (!text) return;

      const msgRef = db.ref('messages').push();
      msgRef.set({
        text,
        sender: user.name,
        timestamp: firebase.database.ServerValue.TIMESTAMP
      }).then(() => {
        // Update last message ts for rate limit
        db.ref('users/' + user.uid + '/lastMessageTs').set(Date.now());
        addPoints(1);
      });
      input.value = '';
    }

    db.ref('messages').orderByChild('timestamp').limitToLast(200).on('child_added', snap => {
      const msg = snap.val();
      const div = document.createElement('div');
      div.className = msg.sender === user.name ? 'bubble right' : 'bubble left';
      div.textContent = `${msg.sender}: ${msg.text}`;
      document.getElementById('messages').appendChild(div);
      document.getElementById('messages').scrollTop = document.getElementById('messages').scrollHeight;
    });

    function addPoints(pts = 1, silent = false) {
      if (!user.uid) return;
      db.ref('users/' + user.uid + '/points').transaction(curr => (curr || 0) + pts);
      if (!silent) alert(`+\( {pts} point \){pts > 1 ? 's' : ''}!`);
    }

    // Leaderboard
    function updateLeaderboard() {
      db.ref('users').orderByChild('points').limitToLast(15).once('value', snap => {
        let html = '';
        let rank = 1;
        snap.forEach(child => {
          const u = child.val();
          html += `<li>#${rank++} ${u.name || 'Fan'}: ${u.points || 0} pts</li>`;
        });
        document.getElementById('activityLeaderboard').innerHTML = html || '<li>Be the first fan!</li>';
      });
    }
    setInterval(updateLeaderboard, 30000);
    updateLeaderboard();

    // Navigation
    function showScreen(id) {
      document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
      document.getElementById(id).classList.add('active');
      document.querySelectorAll('.tabs button').forEach(b => b.classList.remove('active'));
      document.querySelector(`.tabs button[onclick="showScreen('${id}')"]`)?.classList.add('active');
      if (id === 'leaderboard') updateLeaderboard();
    }

    // Service Worker
    if ('serviceWorker' in navigator) {
      window.addEventListener('load', () => {
        navigator.serviceWorker.register('/sw.js').catch(err => console.log('SW error', err));
      });
    }
  </script>
</body>
</html>
