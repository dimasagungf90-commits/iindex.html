# Index.htmll
Happybirthdayy
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Kejutan Spesial!</title>
    <style>
        /* --- RESET & GAYA DASAR --- */
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body {
            background-color: #ffe4e1; /* Pink lembut untuk nanti */
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            overflow: hidden;
            height: 100vh;
            width: 100vw;
        }

        /* --- CONTAINER KEMBANG API (AWAL) --- */
        #fireworks-container {
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background: #000; /* Latar hitam biar kembang api terang */
            z-index: 999; /* Paling depan */
            display: flex;
            justify-content: center;
            align-items: center;
            transition: opacity 1.5s ease-out; /* Transisi halus saat menghilang */
        }
        #canvas { display: block; }
        .loading-text {
            position: absolute;
            color: white;
            bottom: 20%;
            font-size: 1.2rem;
            animation: kedip 1s infinite;
        }
        @keyframes kedip { 50% { opacity: 0.5; } }

        /* --- CONTAINER KARTU UTAMA (NANTI MUNCUL) --- */
        #main-card-container {
            position: absolute;
            top: 0; left: 0; width: 100%; height: 100%;
            display: flex;
            justify-content: center;
            align-items: center;
            flex-direction: column;
            opacity: 0; /* Awalnya tersembunyi */
            transition: opacity 1.5s ease-in;
            pointer-events: none; /* Biar gak bisa diklik sebelum muncul */
        }
        /* Kelas agar container kartu muncul */
        #main-card-container.show-now {
            opacity: 1;
            pointer-events: auto;
        }

        /* --- GAYA KARTU SEBELUMNYA --- */
        .heart-bg {
            position: fixed; top: -10vh; color: #ff69b4; font-size: 20px;
            z-index: -1; animation: fall linear forwards;
        }
        @keyframes fall { to { transform: translateY(110vh); } }

        .card {
            background: rgba(255, 255, 255, 0.95);
            padding: 30px; border-radius: 20px;
            box-shadow: 0 10px 25px rgba(255, 105, 180, 0.3);
            text-align: center; max-width: 90%; width: 350px;
            border: 2px solid #ffb6c1;
        }
        .cake-emoji { font-size: 80px; display: block; margin-bottom: 20px; animation: bounce 2s infinite; }
        @keyframes bounce { 0%, 100% { transform: translateY(0); } 50% { transform: translateY(-20px); } }
        h1 { color: #d63384; margin-bottom: 10px; font-size: 1.5rem; }
        p { color: #555; margin-bottom: 20px; line-height: 1.5; font-size: 1rem; }
        .romantis { font-style: italic; color: #d63384; margin-top: 15px; font-weight: bold; }
        button {
            background: #ff69b4; color: white; border: none; padding: 12px 24px;
            border-radius: 50px; font-size: 1rem; font-weight: bold; cursor: pointer;
            box-shadow: 0 4px 10px rgba(255, 105, 180, 0.4); transition: transform 0.2s;
        }
        button:active { transform: scale(0.95); }
        .hidden { display: none; }
        .fade-in { animation: fadeIn 0.8s; }
        @keyframes fadeIn { from { opacity: 0; transform: scale(0.8); } to { opacity: 1; transform: scale(1); } }
    </style>
</head>
<body>

    <div id="fireworks-container">
        <canvas id="canvas"></canvas>
        <p class="loading-text">Tunggu sebentar ya... ✨</p>
    </div>


    <div id="main-card-container">
        <div id="page1" class="card fade-in">
            <div class="cake-emoji">🎂</div>
            <h1>SURPRISE!</h1>
            <p>Ada sesuatu buat kamu nih...</p>
            <button onclick="goToPage2()">Klik Di Sini 🎁</button>
        </div>

        <div id="page2" class="card hidden">
            <div class="cake-emoji" style="font-size: 60px;">🫣</div>
            <h1>Cieee...</h1>
            <p>Ada yang lagi ulang tahun nih yeee...<br>Siapa hayooo?</p>
            <button onclick="goToPage3()">Klik Di Sini Lagi 👉</button>
        </div>

        <div id="page3" class="card hidden">
            <div class="cake-emoji" style="font-size: 60px;">❤️</div>
            <h1>Happy Birthday Cantik! ✨</h1>
            <p>Selamat ulang tahun ya! <br> Sehat selalu dan bahagia terus.</p>
            <p class="romantis">"Semoga di usiamu yang baru ini, kamu makin bersinar. I Love You! 🌹"</p>
        </div>
    </div>


    <script>
        // =========================================
        // SCRIPT 1: LOGIKA KEMBANG API (Canvas)
        // =========================================
        let canvas, ctx, w, h, particles = [], probability = 0.04, xPoint, yPoint;
        
        function initFireworks() {
            canvas = document.getElementById("canvas");
            ctx = canvas.getContext("2d");
            resizeCanvas();
            window.addEventListener("resize", resizeCanvas);
            window.requestAnimationFrame(updateWorld);
        }

        function resizeCanvas() {
            if (!!canvas) { w = canvas.width = window.innerWidth; h = canvas.height = window.innerHeight; }
        }

        function updateWorld() {
            update(); paint();
            window.requestAnimationFrame(updateWorld);
        }

        function update() {
            if (particles.length < 500 && Math.random() < probability) { createFirework(); }
            let alive = [];
            for (let i = 0; i < particles.length; i++) {
                if (particles[i].move()) { alive.push(particles[i]); }
            }
            particles = alive;
        }

        function paint() {
            ctx.globalCompositeOperation = 'source-over';
            ctx.fillStyle = "rgba(0,0,0,0.2)";
            ctx.fillRect(0, 0, w, h);
            ctx.globalCompositeOperation = 'lighter';
            for (let i = 0; i < particles.length; i++) { particles[i].draw(ctx); }
        }

        function createFirework() {
            xPoint = Math.random() * (w - 200) + 100;
            yPoint = Math.random() * (h - 200) + 100;
            let nParticles = Math.random() * 50 + 100;
            let color = "rgb(" + (~~(Math.random() * 200 + 55)) + "," + (~~(Math.random() * 200 + 55)) + "," + (~~(Math.random() * 200 + 55)) + ")";
            for (let i = 0; i < nParticles; i++) {
                particles.push(new Particle(color));
            }
        }

        function Particle(color) {
            this.w = this.h = Math.random() * 4 + 1;
            this.x = xPoint - this.w / 2; this.y = yPoint - this.h / 2;
            this.vx = (Math.random() - 0.5) * 10; this.vy = (Math.random() - 0.5) * 10;
            this.alpha = Math.random() * 0.5 + 0.5; this.color = color;
        }

        Particle.prototype = {
            gravity: 0.05, move: function () {
                this.x += this.vx; this.vy += this.gravity; this.y += this.vy;
                this.alpha -= 0.01; return (this.alpha > 0 && this.y < h && this.x > 0 && this.x < w);
            }, draw: function (c) {
                c.save(); c.fillStyle = this.color; c.globalAlpha = this.alpha;
                c.fillRect(this.x, this.y, this.w, this.h); c.restore();
            }
        };

        // Jalankan Kembang Api saat halaman dimuat
        initFireworks();


        // =========================================
        // SCRIPT 2: TRANSISI DARI KEMBANG API KE KARTU
        // =========================================
        window.onload = () => {
            // Set waktu kembang api berjalan (contoh: 5000ms = 5 detik)
            setTimeout(() => {
                // 1. Hilangkan container kembang api
                const fwContainer = document.getElementById('fireworks-container');
                fwContainer.style.opacity = '0';
                
                // Hapus dari layar setelah transisi selesai agar tidak berat
                setTimeout(() => { fwContainer.style.display = 'none'; }, 1500);

                // 2. Munculkan container kartu utama
                const mainContainer = document.getElementById('main-card-container');
                mainContainer.classList.add('show-now');

                // 3. Mulai hujan love kecil-kecilan
                setInterval(createHeart, 500);

            }, 5000); // <--- GANTI DURASI DI SINI (5000 = 5 detik)
        };


        // =========================================
        // SCRIPT 3: LOGIKA PINDAH KARTU (Sama seperti sebelumnya)
        // =========================================
        function goToPage2() {
            document.getElementById('page1').classList.add('hidden');
            const page2 = document.getElementById('page2');
            page2.classList.remove('hidden'); page2.classList.add('fade-in');
        }

        function goToPage3() {
            document.getElementById('page2').classList.add('hidden');
            const page3 = document.getElementById('page3');
            page3.classList.remove('hidden'); page3.classList.add('fade-in');
            // Hujan love makin deras di akhir
            setInterval(createHeart, 200);
        }

        function createHeart() {
            const heart = document.createElement('div');
            heart.classList.add('heart-bg'); heart.innerHTML = '❤️';
            heart.style.left = Math.random() * 100 + 'vw';
            heart.style.fontSize = Math.random() * 20 + 10 + 'px';
            heart.style.animationDuration = Math.random() * 3 + 2 + 's';
            document.body.appendChild(heart);
            setTimeout(() => { heart.remove(); }, 5000);
        }
    </script>
</body>
</html>
