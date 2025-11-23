<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>What sport do you like best?</title>
  <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
  <style>
    body{font-family:Arial,sans-serif;background:#f0f9ff;padding:20px 10px;text-align:center;margin:0;}
    h1{color:#2c3e50;margin:30px 0 50px;font-size:34px;}
    .grid{display:grid;grid-template-columns:repeat(3,1fr);gap:30px;max-width:1100px;margin:0 auto 70px;}
    .card{background:white;padding:60px 10px;font-size:34px;font-weight:bold;border-radius:28px;
          box-shadow:0 12px 35px rgba(0,0,0,0.15);cursor:pointer;position:relative;transition:all 0.3s;}
    .card:hover{transform:translateY(-12px);}
    .temp{position:absolute;bottom:18px;right:18px;background:#27ae60;color:white;padding:10px 18px;
           border-radius:30px;font-size:26px;font-weight:bold;}
    #submit{padding:24px 120px;font-size:38px;background:#27ae60;color:white;border:none;border-radius:50px;
            cursor:pointer;margin:20px;box-shadow:0 12px 30px rgba(0,0,0,0.2);}
    #submit:disabled{background:#95a5a6;cursor:not-allowed;}
    .thanks{font-size:52px;color:#27ae60;margin:120px 20px;display:none;}
    .counter{font-size:34px;margin:20px;color:#2c3e50;}
    @media(max-width:800px){
      .grid{grid-template-columns:repeat(2,1fr);}
      .card{font-size:28px;padding:50px 8px;}
    }
  </style>
</head>
<body>

<h1>What sport do you like best?</h1>

<div class="counter">
  This group has selected: <strong id="cnt">0</strong> votes
</div>

<div class="grid">
  <div class="card" data-sport="pingpong">Ping-pong<span class="temp">0</span></div>
  <div class="card" data-sport="ropejumping">Rope jumping<span class="temp">0</span></div>
  <div class="card" data-sport="basketball">Basketball<span class="temp">0</span></div>
  <div class="card" data-sport="football">Football<span class="temp">0</span></div>
  <div class="card" data-sport="running">Running<span class="temp">0</span></div>
  <div class="card" data-sport="swimming">Swimming<span class="temp">0</span></div>
  <div class="card" data-sport="biking">Biking<span class="temp">0</span></div>
  <div class="card" data-sport="skating">Skating<span class="temp">0</span></div>
  <div class="card" data-sport="badminton">Badminton<span class="temp">0</span></div>
</div>

<button id="submit" disabled>Submit this group</button>
<div class="thanks">Thanks!<br>Next group →</div>

<script>
const firebaseConfig = {
  apiKey: "AIzaSyDCdnWxSetr8pHGMOFVipe2pajXEJdM2fQ",
  authDomain: "class-sport-vote.firebaseapp.com",
  projectId: "class-sport-vote",
  storageBucket: "class-sport-vote.firebasestorage.app",
  messagingSenderId: "702881210294",
  appId: "1:702881210294:web:b2566337b114fc12d26b17",
  measurementId: "G-CVX6ZBCPKW"
};

firebase.initializeApp(firebaseConfig);
const db = firebase.firestore();
const votesCol = db.collection("free_votes_2025");   // 你的最终数据在这里

let selections = [];

// 点击 → 绿色数字立刻+1，按钮随时可点
document.querySelectorAll('.card').forEach(card => {
  card.addEventListener('click', () => {
    const sport = card.getAttribute('data-sport');
    selections.push(sport);
    updateTemp();
    document.getElementById('cnt').textContent = selections.length;
    document.getElementById('submit').disabled = false;   // 只要点一次就能提交
  });
});

function updateTemp() {
  const temp = {};
  selections.forEach(s => temp[s] = (temp[s]||0) + 1);
  document.querySelectorAll('.card').forEach(card => {
    const s = card.getAttribute('data-sport');
    card.querySelector('.temp').textContent = temp[s] || 0;
  });
}

// 提交当前这组所有票
document.getElementById('submit').addEventListener('click', () => {
  if (selections.length === 0) return;

  const batch = db.batch();
  selections.forEach(sport => {
    const doc = votesCol.doc();
    batch.set(doc, {sport: sport, time: new Date()});
  });

  batch.commit().then(() => {
    document.querySelector('.thanks').style.display = 'block';
    document.querySelector('.grid').style.display = 'none';
    document.getElementById('submit').style.display = 'none';
    document.querySelector('.counter').style.display = 'none';
  });
});
</script>
</body>
</html>
