<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BrainBoost - Game Pengasah Otak</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;600;800&display=swap" rel="stylesheet">
    <style>
        * {
            font-family: 'Poppins', sans-serif;
            user-select: none;
        }
        
        body {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            overflow-x: hidden;
        }
        
        .glass-panel {
            background: rgba(255, 255, 255, 0.95);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.2);
            box-shadow: 0 8px 32px 0 rgba(31, 38, 135, 0.37);
        }
        
        .btn-press {
            transition: all 0.1s;
        }
        .btn-press:active {
            transform: scale(0.95);
        }
        
        .shake {
            animation: shake 0.5s;
        }
        
        @keyframes shake {
            0%, 100% { transform: translateX(0); }
            25% { transform: translateX(-10px); }
            75% { transform: translateX(10px); }
        }
        
        .pulse-ring {
            animation: pulse-ring 2s cubic-bezier(0.215, 0.61, 0.355, 1) infinite;
        }
        
        @keyframes pulse-ring {
            0% { transform: scale(0.95); box-shadow: 0 0 0 0 rgba(255, 255, 255, 0.7); }
            70% { transform: scale(1); box-shadow: 0 0 0 20px rgba(255, 255, 255, 0); }
            100% { transform: scale(0.95); box-shadow: 0 0 0 0 rgba(255, 255, 255, 0); }
        }
        
        .memory-btn {
            transition: all 0.3s;
        }
        .memory-btn.lit {
            filter: brightness(1.5);
            transform: scale(1.1);
        }
        
        .floating {
            animation: floating 3s ease-in-out infinite;
        }
        
        @keyframes floating {
            0% { transform: translateY(0px); }
            50% { transform: translateY(-10px); }
            100% { transform: translateY(0px); }
        }
        
        .progress-ring {
            transform: rotate(-90deg);
            transform-origin: 50% 50%;
        }
        
        .gradient-text {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }
    </style>
</head>
<body class="flex items-center justify-center p-4">

    <!-- Audio Context Setup -->
    <div id="audio-overlay" class="fixed inset-0 bg-black bg-opacity-50 z-50 flex items-center justify-center" style="display: none;">
        <div class="glass-panel rounded-2xl p-8 text-center max-w-md mx-4">
            <h2 class="text-2xl font-bold mb-4 text-gray-800">Aktifkan Suara?</h2>
            <p class="text-gray-600 mb-6">Game ini menggunakan efek suara untuk pengalaman lebih baik.</p>
            <button onclick="initAudio()" class="bg-purple-600 text-white px-8 py-3 rounded-full font-semibold hover:bg-purple-700 transition">
                Mulai Bermain
            </button>
        </div>
    </div>

    <!-- Main Container -->
    <div class="w-full max-w-2xl">
        
        <!-- Header -->
        <div class="text-center mb-6 floating">
            <h1 class="text-4xl md:text-6xl font-extrabold text-white mb-2 drop-shadow-lg">🧠 BrainBoost</h1>
            <p class="text-white text-opacity-90 text-lg">Latih otakmu setiap hari!</p>
        </div>

        <!-- START SCREEN -->
        <div id="start-screen" class="glass-panel rounded-3xl p-8 md:p-12">
            <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mb-8">
                <button onclick="selectMode('memory')" class="mode-btn btn-press bg-gradient-to-br from-pink-500 to-rose-600 text-white p-6 rounded-2xl hover:shadow-xl transition">
                    <div class="text-4xl mb-2">🎵</div>
                    <div class="font-bold text-lg">Memory</div>
                    <div class="text-xs opacity-90">Ingat urutan</div>
                </button>
                
                <button onclick="selectMode('math')" class="mode-btn btn-press bg-gradient-to-br from-blue-500 to-cyan-600 text-white p-6 rounded-2xl hover:shadow-xl transition">
                    <div class="text-4xl mb-2">🔢</div>
                    <div class="font-bold text-lg">Math</div>
                    <div class="text-xs opacity-90">Hitung cepat</div>
                </button>
                
                <button onclick="selectMode('reflex')" class="mode-btn btn-press bg-gradient-to-br from-green-500 to-emerald-600 text-white p-6 rounded-2xl hover:shadow-xl transition">
                    <div class="text-4xl mb-2">⚡</div>
                    <div class="font-bold text-lg">Reflex</div>
                    <div class="text-xs opacity-90">Warna vs Kata</div>
                </button>
            </div>
            
            <div class="text-center">
                <p class="text-gray-600 mb-4">Pilih mode permainan di atas</p>
                <div class="flex justify-center space-x-4 text-sm text-gray-500">
                    <span class="flex items-center"><span class="w-2 h-2 bg-green-500 rounded-full mr-2"></span>Responsive</span>
                    <span class="flex items-center"><span class="w-2 h-2 bg-blue-500 rounded-full mr-2"></span>Audio</span>
                    <span class="flex items-center"><span class="w-2 h-2 bg-purple-500 rounded-full mr-2"></span>Progressive</span>
                </div>
            </div>
        </div>

        <!-- GAME SCREEN -->
        <div id="game-screen" class="hidden glass-panel rounded-3xl p-6 md:p-8 relative overflow-hidden">
            <!-- HUD -->
            <div class="flex justify-between items-center mb-6">
                <div class="flex items-center space-x-2">
                    <div class="relative w-12 h-12">
                        <svg class="progress-ring w-12 h-12">
                            <circle cx="24" cy="24" r="20" stroke="#e5e7eb" stroke-width="4" fill="none"/>
                            <circle id="timer-ring" cx="24" cy="24" r="20" stroke="#8b5cf6" stroke-width="4" fill="none" 
                                    stroke-dasharray="125.6" stroke-dashoffset="0" stroke-linecap="round"/>
                        </svg>
                        <span id="timer-text" class="absolute inset-0 flex items-center justify-center text-sm font-bold text-gray-700">60</span>
                    </div>
                    <div>
                        <div class="text-xs text-gray-500">Waktu</div>
                        <div class="text-sm font-bold text-gray-700">detik</div>
                    </div>
                </div>
                
                <div class="text-center">
                    <div class="text-xs text-gray-500 mb-1">Level <span id="level-display">1</span></div>
                    <div class="h-2 w-24 bg-gray-200 rounded-full overflow-hidden">
                        <div id="progress-bar" class="h-full bg-gradient-to-r from-purple-500 to-pink-500 transition-all duration-300" style="width: 0%"></div>
                    </div>
                </div>
                
                <div class="text-right">
                    <div class="text-xs text-gray-500">Skor</div>
                    <div id="score-display" class="text-2xl font-bold gradient-text">0</div>
                </div>
            </div>

            <!-- MEMORY GAME -->
            <div id="memory-game" class="hidden">
                <div class="text-center mb-6">
                    <p id="memory-instruction" class="text-gray-600 font-medium">Perhatikan urutan warna!</p>
                </div>
                <div class="grid grid-cols-2 gap-4 max-w-xs mx-auto mb-6">
                    <button id="mem-0" class="memory-btn h-24 md:h-32 rounded-2xl bg-red-500 shadow-lg" onclick="handleMemoryInput(0)"></button>
                    <button id="mem-1" class="memory-btn h-24 md:h-32 rounded-2xl bg-blue-500 shadow-lg" onclick="handleMemoryInput(1)"></button>
                    <button id="mem-2" class="memory-btn h-24 md:h-32 rounded-2xl bg-yellow-500 shadow-lg" onclick="handleMemoryInput(2)"></button>
                    <button id="mem-3" class="memory-btn h-24 md:h-32 rounded-2xl bg-green-500 shadow-lg" onclick="handleMemoryInput(3)"></button>
                </div>
                <div class="text-center">
                    <button id="start-memory-btn" onclick="startMemoryRound()" class="bg-purple-600 text-white px-8 py-3 rounded-full font-semibold hover:bg-purple-700 transition pulse-ring">
                        Mulai
                    </button>
                </div>
            </div>

            <!-- MATH GAME -->
            <div id="math-game" class="hidden">
                <div class="text-center mb-8">
                    <div id="math-question" class="text-5xl md:text-6xl font-bold text-gray-800 mb-2">12 + 8 = ?</div>
                    <p class="text-gray-500">Pilih jawaban yang benar</p>
                </div>
                <div class="grid grid-cols-2 gap-4" id="math-options">
                    <!-- Generated by JS -->
                </div>
            </div>

            <!-- REFLEX GAME (STROOP) -->
            <div id="reflex-game" class="hidden">
                <div class="text-center mb-6">
                    <div class="mb-4">
                        <span class="text-gray-500 text-sm uppercase tracking-wide font-semibold">Tekan warna dari TEKS ini:</span>
                    </div>
                    <div id="stroop-word" class="text-6xl md:text-7xl font-black mb-6 transition-all duration-200">MERAH</div>
                    <div class="text-sm text-gray-500 bg-gray-100 inline-block px-4 py-2 rounded-full">
                        Bukan arti kata, tapi warna hurufnya!
                    </div>
                </div>
                <div class="grid grid-cols-3 gap-3 max-w-md mx-auto">
                    <button onclick="checkStroop('red')" class="h-16 rounded-xl bg-red-500 hover:bg-red-600 text-white font-bold shadow-lg btn-press border-2 border-white"></button>
                    <button onclick="checkStroop('blue')" class="h-16 rounded-xl bg-blue-500 hover:bg-blue-600 text-white font-bold shadow-lg btn-press border-2 border-white"></button>
                    <button onclick="checkStroop('green')" class="h-16 rounded-xl bg-green-500 hover:bg-green-600 text-white font-bold shadow-lg btn-press border-2 border-white"></button>
                    <button onclick="checkStroop('yellow')" class="h-16 rounded-xl bg-yellow-500 hover:bg-yellow-600 text-white font-bold shadow-lg btn-press border-2 border-white"></button>
                    <button onclick="checkStroop('purple')" class="h-16 rounded-xl bg-purple-500 hover:bg-purple-600 text-white font-bold shadow-lg btn-press border-2 border-white"></button>
                    <button onclick="checkStroop('orange')" class="h-16 rounded-xl bg-orange-500 hover:bg-orange-600 text-white font-bold shadow-lg btn-press border-2 border-white"></button>
                </div>
            </div>

            <!-- Back Button -->
            <button onclick="backToMenu()" class="absolute top-4 right-4 text-gray-400 hover:text-gray-600">
                <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path></svg>
            </button>
        </div>

        <!-- GAME OVER SCREEN -->
        <div id="game-over-screen" class="hidden glass-panel rounded-3xl p-8 text-center">
            <div class="text-6xl mb-4">🏆</div>
            <h2 class="text-3xl font-bold text-gray-800 mb-2">Permainan Selesai!</h2>
            <p class="text-gray-600 mb-6">Kerja bagus! Berikut hasil latihan otakmu:</p>
            
            <div class="grid grid-cols-2 gap-4 mb-8">
                <div class="bg-purple-50 rounded-2xl p-4">
                    <div class="text-sm text-gray-500 mb-1">Skor Akhir</div>
                    <div id="final-score" class="text-3xl font-bold text-purple-600">0</div>
                </div>
                <div class="bg-pink-50 rounded-2xl p-4">
                    <div class="text-sm text-gray-500 mb-1">Level Tercapai</div>
                    <div id="final-level" class="text-3xl font-bold text-pink-600">1</div>
                </div>
                <div class="bg-blue-50 rounded-2xl p-4">
                    <div class="text-sm text-gray-500 mb-1">Ketepatan</div>
                    <div id="final-accuracy" class="text-3xl font-bold text-blue-600">0%</div>
                </div>
                <div class="bg-green-50 rounded-2xl p-4">
                    <div class="text-sm text-gray-500 mb-1">Rating</div>
                    <div id="final-rating" class="text-3xl font-bold text-green-600">⭐</div>
                </div>
            </div>
            
            <div class="space-y-3">
                <button onclick="restartGame()" class="w-full bg-purple-600 text-white py-4 rounded-2xl font-bold text-lg hover:bg-purple-700 transition shadow-lg btn-press">
                    Main Lagi
                </button>
                <button onclick="backToMenu()" class="w-full bg-gray-200 text-gray-700 py-3 rounded-2xl font-semibold hover:bg-gray-300 transition btn-press">
                    Ganti Mode
                </button>
            </div>
        </div>
    </div>

    <script>
        // Audio Context
        let audioCtx;
        let oscillator;
        
        function initAudio() {
            audioCtx = new (window.AudioContext || window.webkitAudioContext)();
            document.getElementById('audio-overlay').style.display = 'none';
        }
        
        function playTone(freq, type = 'sine', duration = 0.2) {
            if (!audioCtx) return;
            const osc = audioCtx.createOscillator();
            const gain = audioCtx.createGain();
            osc.connect(gain);
            gain.connect(audioCtx.destination);
            osc.frequency.value = freq;
            osc.type = type;
            gain.gain.setValueAtTime(0.3, audioCtx.currentTime);
            gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + duration);
            osc.start(audioCtx.currentTime);
            osc.stop(audioCtx.currentTime + duration);
        }
        
        function playSuccess() {
            playTone(523.25, 'sine', 0.1);
            setTimeout(() => playTone(659.25, 'sine', 0.1), 100);
            setTimeout(() => playTone(783.99, 'sine', 0.2), 200);
        }
        
        function playError() {
            playTone(200, 'sawtooth', 0.3);
        }
        
        function playClick() {
            playTone(800, 'sine', 0.05);
        }

        // Game State
        let currentMode = null;
        let score = 0;
        let level = 1;
        let timeLeft = 60;
        let timerInterval;
        let gameActive = false;
        
        // Memory Game State
        let memorySequence = [];
        let playerSequence = [];
        let memoryRound = 0;
        let showingSequence = false;
        const memoryColors = ['bg-red-500', 'bg-blue-500', 'bg-yellow-500', 'bg-green-500'];
        const memoryFreqs = [261.63, 329.63, 392.00, 523.25];
        
        // Math Game State
        let mathCorrect = 0;
        let mathTotal = 0;
        
        // Reflex Game State
        let reflexCorrect = 0;
        let reflexTotal = 0;
        const stroopColors = [
            {name: 'MERAH', color: 'red', text: 'text-red-500'},
            {name: 'BIRU', color: 'blue', text: 'text-blue-500'},
            {name: 'HIJAU', color: 'green', text: 'text-green-500'},
            {name: 'KUNING', color: 'yellow', text: 'text-yellow-500'},
            {name: 'UNGU', color: 'purple', text: 'text-purple-500'},
            {name: 'ORANYE', color: 'orange', text: 'text-orange-500'}
        ];

        // Check audio on first interaction
        document.addEventListener('click', function initAudioCheck() {
            if (!audioCtx) {
                document.getElementById('audio-overlay').style.display = 'flex';
            }
            document.removeEventListener('click', initAudioCheck);
        }, {once: true});

        function selectMode(mode) {
            playClick();
            currentMode = mode;
            document.getElementById('start-screen').classList.add('hidden');
            document.getElementById('game-screen').classList.remove('hidden');
            document.getElementById('game-over-screen').classList.add('hidden');
            
            // Reset game state
            score = 0;
            level = 1;
            timeLeft = 60;
            gameActive = true;
            updateHUD();
            
            // Show relevant game section
            document.getElementById('memory-game').classList.add('hidden');
            document.getElementById('math-game').classList.add('hidden');
            document.getElementById('reflex-game').classList.add('hidden');
            
            if (mode === 'memory') {
                document.getElementById('memory-game').classList.remove('hidden');
                document.getElementById('start-memory-btn').classList.remove('hidden');
                memoryRound = 0;
                memorySequence = [];
            } else if (mode === 'math') {
                document.getElementById('math-game').classList.remove('hidden');
                mathCorrect = 0;
                mathTotal = 0;
                generateMath();
                startTimer();
            } else if (mode === 'reflex') {
                document.getElementById('reflex-game').classList.remove('hidden');
                reflexCorrect = 0;
                reflexTotal = 0;
                generateStroop();
                startTimer();
            }
        }

        function startTimer() {
            clearInterval(timerInterval);
            timerInterval = setInterval(() => {
                if (!gameActive) return;
                timeLeft--;
                updateTimer();
                if (timeLeft <= 0) {
                    endGame();
                }
            }, 1000);
        }

        function updateTimer() {
            document.getElementById('timer-text').textContent = timeLeft;
            const circle = document.getElementById('timer-ring');
            const circumference = 125.6;
            const offset = circumference - (timeLeft / 60) * circumference;
            circle.style.strokeDashoffset = offset;
            
            if (timeLeft <= 10) {
                circle.style.stroke = '#ef4444';
            } else {
                circle.style.stroke = '#8b5cf6';
            }
        }

        function updateHUD() {
            document.getElementById('score-display').textContent = score;
            document.getElementById('level-display').textContent = level;
            const progress = ((score % 100) / 100) * 100;
            document.getElementById('progress-bar').style.width = progress + '%';
        }

        // Memory Game Logic
        function startMemoryRound() {
            playClick();
            document.getElementById('start-memory-btn').classList.add('hidden');
            document.getElementById('memory-instruction').textContent = 'Perhatikan urutan...';
            playerSequence = [];
            showingSequence = true;
            
            // Add new step
            memorySequence.push(Math.floor(Math.random() * 4));
            
            // Play sequence
            let i = 0;
            const interval = setInterval(() => {
                if (i >= memorySequence.length) {
                    clearInterval(interval);
                    showingSequence = false;
                    document.getElementById('memory-instruction').textContent = 'Ulangi urutannya!';
                    return;
                }
                flashButton(memorySequence[i]);
                i++;
            }, 800 - (level * 50)); // Gets faster with levels
        }

        function flashButton(index) {
            const btn = document.getElementById(`mem-${index}`);
            btn.classList.add('lit');
            playTone(memoryFreqs[index], 'sine', 0.3);
            setTimeout(() => {
                btn.classList.remove('lit');
            }, 300);
        }

        function handleMemoryInput(index) {
            if (!gameActive || showingSequence || currentMode !== 'memory') return;
            
            playClick();
            flashButton(index);
            playerSequence.push(index);
            
            // Check correctness
            if (playerSequence[playerSequence.length - 1] !== memorySequence[playerSequence.length - 1]) {
                playError();
                document.getElementById('game-screen').classList.add('shake');
                setTimeout(() => document.getElementById('game-screen').classList.remove('shake'), 500);
                score = Math.max(0, score - 10);
                updateHUD();
                
                if (score <= 0 && memorySequence.length > 1) {
                    endGame();
                } else {
                    // Retry same level
                    playerSequence = [];
                    document.getElementById('memory-instruction').textContent = 'Salah! Coba lagi.';
                    setTimeout(() => {
                        showingSequence = true;
                        let i = 0;
                        const interval = setInterval(() => {
                            if (i >= memorySequence.length) {
                                clearInterval(interval);
                                showingSequence = false;
                                document.getElementById('memory-instruction').textContent = 'Ulangi urutannya!';
                                return;
                            }
                            flashButton(memorySequence[i]);
                            i++;
                        }, 800);
                    }, 1000);
                }
                return;
            }
            
            // Check if round complete
            if (playerSequence.length === memorySequence.length) {
                playSuccess();
                score += 20 * level;
                level++;
                updateHUD();
                document.getElementById('memory-instruction').textContent = 'Benar! Level naik!';
                setTimeout(startMemoryRound, 1000);
            }
        }

        // Math Game Logic
        function generateMath() {
            if (!gameActive) return;
            
            const operators = ['+', '-', '×'];
            const operator = operators[Math.floor(Math.random() * 3)];
            let a, b, answer;
            
            switch(operator) {
                case '+':
                    a = Math.floor(Math.random() * (10 + level * 5)) + 1;
                    b = Math.floor(Math.random() * (10 + level * 5)) + 1;
                    answer = a + b;
                    break;
                case '-':
                    a = Math.floor(Math.random() * (10 + level * 5)) + 5;
                    b = Math.floor(Math.random() * a) + 1;
                    answer = a - b;
                    break;
                case '×':
                    a = Math.floor(Math.random() * (5 + level * 2)) + 1;
                    b = Math.floor(Math.random() * 8) + 1;
                    answer = a * b;
                    break;
            }
            
            document.getElementById('math-question').textContent = `${a} ${operator} ${b} = ?`;
            
            // Generate options
            const options = [answer];
            while (options.length < 4) {
                const wrong = answer + Math.floor(Math.random() * 20) - 10;
                if (wrong !== answer && wrong > 0 && !options.includes(wrong)) {
                    options.push(wrong);
                }
            }
            
            // Shuffle
            options.sort(() => Math.random() - 0.5);
            
            const container = document.getElementById('math-options');
            container.innerHTML = '';
            options.forEach(opt => {
                const btn = document.createElement('button');
                btn.className = 'bg-white border-2 border-purple-200 hover:border-purple-500 text-gray-800 text-2xl font-bold py-6 rounded-2xl transition shadow-sm btn-press';
                btn.textContent = opt;
                btn.onclick = () => checkMath(opt, answer);
                container.appendChild(btn);
            });
        }

        function checkMath(selected, correct) {
            if (!gameActive) return;
            mathTotal++;
            
            if (selected === correct) {
                playSuccess();
                mathCorrect++;
                score += 15 * level;
                if (mathTotal % 5 === 0) level++;
                updateHUD();
                generateMath();
            } else {
                playError();
                document.getElementById('game-screen').classList.add('shake');
                setTimeout(() => document.getElementById('game-screen').classList.remove('shake'), 500);
                score = Math.max(0, score - 5);
                timeLeft = Math.max(5, timeLeft - 2);
                updateHUD();
                updateTimer();
            }
        }

        // Reflex/Stroop Game Logic
        let currentStroop = null;

        function generateStroop() {
            if (!gameActive) return;
            
            // Pick random text and color (can be same or different)
            const textObj = stroopColors[Math.floor(Math.random() * stroopColors.length)];
            const colorObj = stroopColors[Math.floor(Math.random() * stroopColors.length)];
            
            currentStroop = colorObj.color;
            
            const wordEl = document.getElementById('stroop-word');
            wordEl.textContent = textObj.name;
            wordEl.className = `text-6xl md:text-7xl font-black mb-6 transition-all duration-200 ${colorObj.text}`;
        }

        function checkStroop(color) {
            if (!gameActive) return;
            reflexTotal++;
            
            if (color === currentStroop) {
                playSuccess();
                reflexCorrect++;
                score += 25;
                if (reflexTotal % 4 === 0) level++;
                updateHUD();
                generateStroop();
            } else {
                playError();
                document.getElementById('game-screen').classList.add('shake');
                setTimeout(() => document.getElementById('game-screen').classList.remove('shake'), 500);
                score = Math.max(0, score - 10);
                timeLeft = Math.max(5, timeLeft - 3);
                updateHUD();
                updateTimer();
            }
        }

        function endGame() {
            gameActive = false;
            clearInterval(timerInterval);
            
            let accuracy = 0;
            let rating = '⭐';
            
            if (currentMode === 'memory') {
                accuracy = Math.round((score / (level * 20)) * 100) || 0;
            } else if (currentMode === 'math') {
                accuracy = Math.round((mathCorrect / mathTotal) * 100) || 0;
            } else if (currentMode === 'reflex') {
                accuracy = Math.round((reflexCorrect / reflexTotal) * 100) || 0;
            }
            
            if (accuracy >= 90) rating = '⭐⭐⭐';
            else if (accuracy >= 70) rating = '⭐⭐';
            else if (accuracy >= 50) rating = '⭐';
            else rating = '💪';
            
            document.getElementById('final-score').textContent = score;
            document.getElementById('final-level').textContent = level;
            document.getElementById('final-accuracy').textContent = accuracy + '%';
            document.getElementById('final-rating').textContent = rating;
            
            document.getElementById('game-screen').classList.add('hidden');
            document.getElementById('game-over-screen').classList.remove('hidden');
            
            playTone(523.25, 'sine', 0.4);
            setTimeout(() => playTone(659.25, 'sine', 0.4), 200);
            setTimeout(() => playTone(783.99, 'sine', 0.4), 400);
            setTimeout(() => playTone(1046.50, 'sine', 0.8), 600);
        }

        function restartGame() {
            playClick();
            selectMode(currentMode);
        }

        function backToMenu() {
            playClick();
            gameActive = false;
            clearInterval(timerInterval);
            document.getElementById('game-screen').classList.add('hidden');
            document.getElementById('game-over-screen').classList.add('hidden');
            document.getElementById('start-screen').classList.remove('hidden');
        }

        // Keyboard support
        document.addEventListener('keydown', (e) => {
            if (!gameActive) return;
            
            if (currentMode === 'memory') {
                if (e.key >= '1' && e.key <= '4') {
                    handleMemoryInput(parseInt(e.key) - 1);
                }
            }
        });
    </script>
</body>
</html>
