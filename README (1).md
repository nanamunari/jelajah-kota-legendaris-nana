# jelajah-kota-legendaris-nana
kota dengan sejuta harta Karun 
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Pemalang Pixel Adventure</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap');

        :root {
            --pixel-size: 4px;
            --bg-color: #4da6ff;
        }

        body {
            margin: 0;
            background: #111;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            font-family: 'Press Start 2P', cursive;
            overflow: hidden;
            color: white;
        }

        #game-window {
            width: 800px;
            height: 600px;
            background: var(--bg-color);
            position: relative;
            border: 10px solid #f1c40f;
            box-shadow: 0 0 20px rgba(0,0,0,0.5);
            image-rendering: pixelated;
        }

        /* --- LOADING & START --- */
        .overlay {
            position: absolute;
            inset: 0;
            background: #2ecc71;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 100;
            text-align: center;
            padding: 20px;
        }

        .btn-play {
            margin-top: 30px;
            padding: 20px 40px;
            background: #e67e22;
            color: white;
            border: 4px solid #fff;
            font-family: 'Press Start 2P';
            cursor: pointer;
            box-shadow: 4px 4px 0px #d35400;
        }

        .btn-play:active { transform: translate(2px, 2px); box-shadow: 2px 2px 0px #d35400; }

        /* --- MAP & ICONS --- */
        #map-area {
            width: 100%;
            height: 100%;
            background: url('https://img.freepik.com/free-vector/pixel-art-rural-landscape-background_52683-108253.jpg') center/cover;
            display: none;
        }

        .icon {
            position: absolute;
            cursor: pointer;
            display: flex;
            flex-direction: column;
            align-items: center;
            transition: transform 0.2s;
        }

        .icon:hover { transform: scale(1.2); }

        .sprite {
            font-size: 50px;
            animation: smoothFloat 2s infinite ease-in-out;
            filter: drop-shadow(0 5px 5px rgba(0,0,0,0.3));
        }

        .label {
            font-size: 8px;
            background: rgba(0,0,0,0.7);
            padding: 5px;
            margin-top: 5px;
            border: 1px solid white;
        }

        @keyframes smoothFloat {
            0%, 100% { transform: translateY(0px); }
            50% { transform: translateY(-15px); }
        }

        /* --- UI ELEMENTS --- */
        .hud {
            position: absolute;
            top: 20px;
            left: 20px;
            background: #f1c40f;
            color: #000;
            padding: 10px;
            border: 3px solid white;
            z-index: 50;
        }

        #dialog-box {
            position: absolute;
            bottom: 20px;
            left: 50%;
            transform: translateX(-50%);
            width: 90%;
            height: 150px;
            background: #000;
            border: 4px solid #fff;
            padding: 20px;
            display: none;
            z-index: 200;
        }

        #dialog-box h2 { font-size: 12px; color: #f1c40f; margin-top: 0; }
        #dialog-box p { font-size: 10px; line-height: 1.8; }

        .hidden { display: none; }

        /* Trio Imutzz Sprite Placeholder */
        .trio-container {
            display: flex;
            gap: 10px;
            margin-bottom: 20px;
        }
        .character { width: 50px; height: 70px; background: #eee; border: 2px solid #000; }
    </style>
</head>
<body>

<div id="game-window">
    <div class="hud">🍍 POIN: <span id="score">0</span></div>

    <div id="start-screen" class="overlay">
        <h1 style="font-size: 24px; text-shadow: 4px 4px #000;">PETUALANGAN<br>KOTA NANAS</h1>
        <div class="trio-container">
            <div style="font-size: 40px;">👧<br><small style="font-size: 8px;">Lala</small></div>
            <div style="font-size: 40px;">👧<br><small style="font-size: 8px;">Sita</small></div>
            <div style="font-size: 40px;">👧<br><small style="font-size: 8px;">Asri</small></div>
        </div>
        <p style="font-size: 8px; line-height: 1.5;">"Ayo jelajahi indahnya kota ini<br>bersama Trio Imutzz!"</p>
        <button class="btn-play" onclick="startGame()">GAS MULAI!</button>
    </div>

    <div id="map-area">
        <div class="icon" style="top: 100px; left: 350px;" onclick="klikWisata('widuri')">
            <div class="sprite">🌊</div>
            <div class="label">Pantai Widuri</div>
        </div>
        <div class="icon" style="top: 250px; left: 450px;" onclick="klikWisata('grombyang')">
            <div class="sprite">🍲</div>
            <div class="label">Nasi Grombyang</div>
        </div>
        <div class="icon" style="top: 400px; left: 200px;" onclick="klikWisata('tangkeban')">
            <div class="sprite">⛰️</div>
            <div class="label">Bukit Tangkeban</div>
        </div>
        <div class="icon" style="top: 450px; left: 550px;" onclick="klikWisata('nanas')">
            <div class="sprite">🍍</div>
            <div class="label">Kebun Nanas</div>
        </div>
    </div>

    <div id="dialog-box" onclick="tutupDialog()">
        <h2 id="speaker-name">Nama Karakter</h2>
        <p id="dialog-text">Isi cerita harta karun...</p>
        <p style="text-align: right; font-size: 7px; color: #888;">(Klik di mana saja untuk tutup)</p>
    </div>
</div>

<script>
    let score = 0;
    const dataWisata = {
        widuri: { 
            nama: "Lala", 
            text: "Wah! Aku menemukan Mutiara Pesisir di Pantai Widuri! Airnya biru sekali, ayo kita jaga pantai kita!", 
            poin: 100 
        },
        grombyang: { 
            nama: "Sita", 
            text: "Nyam! Mangkuk Emas Nasi Grombyang ditemukan! Bau kuahnya harum sekali, bikin laper ya!", 
            poin: 50 
        },
        tangkeban: { 
            nama: "Asri", 
            text: "GONG! Teropong Ajaib ditemukan di Bukit Tangkeban! Gunung Slamet kelihatan gagah sekali dari sini!", 
            poin: 150 
        },
        nanas: { 
            nama: "Trio Imutzz", 
            text: "HORE! Kita menemukan Mahkota Nanas Madu! Pemalang memang Kota Nanas terbaik di dunia!", 
            poin: 200 
        }
    };

    function startGame() {
        document.getElementById('start-screen').style.display = 'none';
        document.getElementById('map-area').style.display = 'block';
        console.log("Game Dimulai!");
    }

    function klikWisata(id) {
        const wisata = dataWisata[id];
        document.getElementById('speaker-name').innerText = wisata.nama;
        document.getElementById('dialog-text').innerText = wisata.text;
        document.getElementById('dialog-box').style.display = 'block';
        
        // Update Skor
        score += wisata.poin;
        document.getElementById('score').innerText = score;
    }

    function tutupDialog() {
        document.getElementById('dialog-box').style.display = 'none';
        if(score >= 500) {
            alert("SELAMAT! Semua Harta Karun Pemalang Ditemukan! Kamu adalah Sahabat Kota Nanas!");
        }
    }
</script>

</body>
</html>
