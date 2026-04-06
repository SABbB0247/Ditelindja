<!DOCTYPE html>
<html lang="sq">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HAPPY BIRTHDAY OLEANDRA</title>
    <style>
        body { background-color: #E0F7FA; text-align: center; font-family: 'Arial', sans-serif; margin: 0; padding: 10px; color: #01579B; }
        
        #game-container { max-width: 500px; margin: 20px auto; background: white; padding: 20px; border-radius: 30px; box-shadow: 0 20px 40px rgba(0,0,0,0.1); border: 5px solid #B3E5FC; }

        /* Torta Reale */
        .cake-area { position: relative; width: 320px; height: 320px; margin: 20px auto; background: rgba(255,255,255,0.5); border-radius: 20px; }

        .layer { position: absolute; left: 50%; transform: translateX(-50%); border-radius: 15px 15px 10px 10px; border: 1px solid rgba(255,255,255,0.4); }

        .layer1 { width: 250px; height: 80px; bottom: 10px; background: linear-gradient(to right, #01579B, #0288D1, #01579B); z-index: 1; }
        .gold-ribbon { position: absolute; width: 100%; height: 8px; background: #FFD700; bottom: 15px; }
        
        .layer2 { width: 180px; height: 75px; bottom: 90px; background: linear-gradient(to right, #0288D1, #29B6F6, #0288D1); z-index: 2; }
        
        .layer3 { width: 120px; height: 70px; bottom: 165px; background: linear-gradient(to right, #29B6F6, #81D4FA, #29B6F6); z-index: 3; }
        
        .candle { position: absolute; width: 8px; height: 30px; background: linear-gradient(#fff, #FFEB3B); border-radius: 4px; z-index: 20; }
        .candle::after { content: '🔥'; position: absolute; top: -18px; left: -5px; font-size: 14px; animation: flicker 0.5s infinite alternate; }

        @keyframes flicker { from { transform: scale(1); } to { transform: scale(1.2); } }

        .riddle-text { font-size: 1.1rem; min-height: 80px; margin: 15px 0; font-weight: bold; color: #004d40; display: flex; align-items: center; justify-content: center; }
        input { padding: 12px; border-radius: 20px; border: 3px solid #81D4FA; width: 80%; text-align: center; font-size: 16px; outline: none; }
        button { margin-top: 15px; padding: 12px 40px; background: #01579B; color: white; border: none; border-radius: 25px; cursor: pointer; font-weight: bold; font-size: 18px; }
        #status { height: 25px; margin-top: 10px; font-weight: bold; }
        
        .pearl { width: 6px; height: 6px; background: white; border-radius: 50%; display: inline-block; margin: 2px; }
    </style>
</head>
<body>

<div id="game-container">
    <h1>HBD OLEANDRA 💙</h1>
    <div style="font-weight: bold; color: #0288D1;">Happy 17th Birthday!</div>

    <div class="cake-area" id="cake">
        <div class="layer layer1"><div class="gold-ribbon"></div></div>
        <div class="layer layer2"></div>
        <div class="layer layer3"></div>
    </div>

    <div id="game-ui">
        <p><strong>Sfida <span id="count">1</span> / 17</strong></p>
        <div class="riddle-text" id="riddle-box"></div>
        <input type="text" id="answer" placeholder="Përgjigja këtu..." onkeypress="if(event.key==='Enter') checkAnswer()">
        <br>
        <button onclick="checkAnswer()">DËRGO ✨</button>
        <p id="status"></p>
    </div>
</div>

<script>
    const riddles = [
        { q: "Kush është kunata e Lacianit?", a: "Oleandra" },
        { q: "Kujt ia shkuli floket Kaltra ne ekskursion?", a: "Oleandra" },
        { q: "Kush po mbush 17 vjeç sot?", a: "Oleandra" },
        { q: "Kush thotë gjithmonë 'nuk kam çfarë të vesh'?", a: "Oleandra" },
        { q: "Kush thotë gjithmonë: 'Hajd lem oret'?", a: "Oleandra" },
        { q: "Kush e ka besti Jonelën pas Kaltrës?", a: "Oleandra" },
        { q: "Kush e ka kacurrelat me te bukur?", a: "Oleandra" },
        { q: "Kush është çdo javë nga nëna?", a: "Oleandra" },
        { q: "Kush ka një zog me emrin \"Muti\"?", a: "Oleandra" },
        { q: "Kush është personi i perbashket qe Kaltra,Drilena dhe Meku duan shumë?", a: "Oleandra" },
        { q: "Kush ka numrin 7 brënda kasës së telefonit?", a: "Oleandra" },
        { q: "Kush meriton dhuratën më të bukur sot?", a: "Oleandra" },
        { q: "Kush është njeriu më i dashur që Drilena njeh pas Kaltres?", a: "Oleandra" },
        { q: "Kush e meriton tortën më të madhe në botë?", a: "Oleandra" },
        { q: "Kush thoshte që sa të hap instagram do hedh shum story ose foto dhe tani spo hedh asgje?", a: "Oleandra" },
        { q: "Kush i thot \"vruk\" Marielit?", a: "Oleandra" },
        { q: "Dhe pyetja finale: Kush është besti i Ermesit?", a: "Kaltra" }
    ];

    const candlePos = [
        {l:50, b:40}, {l:90, b:35}, {l:130, b:30}, {l:170, b:30}, {l:210, b:35}, {l:250, b:40},
        {l:90, b:120}, {l:125, b:115}, {l:160, b:115}, {l:195, b:120}, {l:230, b:125},
        {l:120, b:195}, {l:145, b:190}, {l:175, b:190}, {l:200, b:195},
        {l:145, b:250}, {l:175, b:250}
    ];

    let current = 0;

    function loadRiddle() {
        if (current < riddles.length) {
            document.getElementById('riddle-box').innerText = riddles[current].q;
            document.getElementById('count').innerText = current + 1;
        } else {
            document.getElementById('game-ui').innerHTML = "<h2 style='color:#01579B'>🎊 HAPPY BIRTHDAY 🎊</h2><p style='font-size:1.2rem'>QIRINJT U GJETËN! 🥳💙</p>";
        }
    }

    function checkAnswer() {
        let val = document.getElementById('answer').value.trim();
        if (val.toLowerCase() === riddles[current].a.toLowerCase()) {
            const c = document.createElement('div');
            c.className = 'candle';
            c.style.left = candlePos[current].l + 'px';
            c.style.bottom = candlePos[current].b + 'px';
            document.getElementById('cake').appendChild(c);

            current++;
            document.getElementById('status').innerText = "Saktë! ✨";
            document.getElementById('status').style.color = "#00C853";
            document.getElementById('answer').value = "";
            loadRiddle();
        } else {
            document.getElementById('status').innerText = "Gabim! Provo përsëri... 👀";
            document.getElementById('status').style.color = "#D50000";
        }
    }
    loadRiddle();
</script>
</body>
</html>
