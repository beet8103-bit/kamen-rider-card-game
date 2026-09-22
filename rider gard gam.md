<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Kamen Rider Card Battle</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700;900&family=Prompt:wght@300;400;600;700&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <style>
        body {
            font-family: 'Prompt', 'Orbitron', sans-serif;
            background-color: #0b0f19;
            color: #e2e8f0;
            user-select: none;
            overflow-x: hidden;
        }

        .font-orbitron {
            font-family: 'Orbitron', sans-serif;
        }

        .cyber-border {
            border: 1px solid rgba(0, 240, 255, 0.3);
            box-shadow: 0 0 15px rgba(0, 240, 255, 0.15), inset 0 0 15px rgba(0, 240, 255, 0.1);
        }

        .cyber-card {
            background: linear-gradient(135deg, rgba(20, 27, 45, 0.9) 0%, rgba(10, 14, 26, 0.95) 100%);
            border: 1px solid rgba(255, 255, 255, 0.1);
            transition: all 0.25s cubic-bezier(0.4, 0, 0.2, 1);
        }

        .cyber-card:hover {
            transform: translateY(-8px) scale(1.02);
            box-shadow: 0 10px 25px rgba(0, 240, 255, 0.3);
            border-color: #00f0ff;
        }

        .cyber-button {
            background: linear-gradient(90deg, #1e293b 0%, #0f172a 100%);
            border: 1px solid #38bdf8;
            transition: all 0.2s ease;
            position: relative;
            overflow: hidden;
        }

        .cyber-button:hover:not(:disabled) {
            background: #38bdf8;
            color: #0f172a;
            box-shadow: 0 0 15px #38bdf8;
        }

        .cyber-button:disabled {
            opacity: 0.4;
            cursor: not-allowed;
            border-color: #475569;
        }

        /* Custom Scrollbar */
        ::-webkit-scrollbar {
            width: 6px;
            height: 6px;
        }
        ::-webkit-scrollbar-track {
            background: #0f172a;
        }
        ::-webkit-scrollbar-thumb {
            background: #334155;
            border-radius: 3px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #00f0ff;
        }

        .card-attack { border-top: 4px solid #ef4444; }
        .card-defense { border-top: 4px solid #3b82f6; }
        .card-form { border-top: 4px solid #eab308; }
        .card-special { border-top: 4px solid #a855f7; }

        /* Card Art Container Styling */
        .card-art-frame {
            background: radial-gradient(circle at center, rgba(30, 41, 59, 0.8) 0%, rgba(15, 23, 42, 0.95) 100%);
            border: 1px solid rgba(255, 255, 255, 0.08);
            border-radius: 8px;
            overflow: hidden;
            display: flex;
            align-items: center;
            justify-content: center;
            height: 64px;
            width: 100%;
            position: relative;
        }

        .card-art-frame svg {
            filter: drop-shadow(0 0 6px rgba(0, 240, 255, 0.4));
            transition: transform 0.3s ease;
        }

        .cyber-card:hover .card-art-frame svg {
            transform: scale(1.1);
        }

        @keyframes pulse-glow {
            0%, 100% { box-shadow: 0 0 10px rgba(0, 240, 255, 0.4); }
            50% { box-shadow: 0 0 25px rgba(0, 240, 255, 0.8); }
        }

        .active-turn-glow {
            animation: pulse-glow 2s infinite;
        }
    </style>
</head>
<body class="min-h-screen bg-slate-950 text-slate-100 flex flex-col justify-between">

    <!-- TOP NAVIGATION / HUD HEADER -->
    <header class="bg-slate-900/80 backdrop-blur border-b border-slate-800 p-3 px-6 flex justify-between items-center sticky top-0 z-30">
        <div class="flex items-center gap-3">
            <i class="fa-solid fa-bolt text-cyan-400 text-2xl animate-pulse"></i>
            <div>
                <h1 class="font-orbitron font-black text-xl tracking-wider text-transparent bg-clip-text bg-gradient-to-r from-cyan-400 via-blue-500 to-purple-500">
                    KAMEN RIDER CARD BATTLE
                </h1>
                <p class="text-xs text-slate-400">Ver 3.0 • Full Graphic Edition</p>
            </div>
        </div>

        <div id="game-phase-badge" class="font-orbitron text-xs px-4 py-1.5 rounded-full bg-slate-800 border border-slate-700 text-cyan-400 flex items-center gap-2">
            <span class="w-2 h-2 rounded-full bg-cyan-400 animate-ping"></span>
            <span id="phase-text">INITIALIZING</span>
        </div>

        <button onclick="resetGameModal()" class="px-3 py-1.5 text-xs rounded border border-red-500/50 hover:bg-red-500/20 text-red-400 transition">
            <i class="fa-solid fa-rotate-left mr-1"></i> เริ่มเกมใหม่
        </button>
    </header>

    <!-- MAIN GAME ARENA -->
    <main class="flex-1 max-w-7xl w-full mx-auto p-4 flex flex-col justify-between gap-4">

        <!-- ENEMY AREA (TOP) -->
        <div id="enemy-area" class="cyber-card rounded-2xl p-4 flex flex-col md:flex-row justify-between items-center gap-4 border-l-4 border-red-500 relative">
            <div class="flex items-center gap-4 w-full md:w-auto">
                <div class="relative">
                    <div id="enemy-avatar" class="w-16 h-16 rounded-xl bg-slate-900 flex items-center justify-center border-2 border-red-500 p-1">
                        <!-- Dynamic SVG Avatar -->
                    </div>
                    <div id="enemy-hearts" class="absolute -bottom-2 -right-2 flex gap-1 bg-slate-950 px-2 py-0.5 rounded-full border border-slate-700 text-xs text-red-500">
                        <i class="fa-solid fa-heart"></i><i class="fa-solid fa-heart"></i><i class="fa-solid fa-heart"></i>
                    </div>
                </div>
                <div>
                    <div class="flex items-center gap-2">
                        <span id="enemy-name" class="font-orbitron font-bold text-lg text-slate-100">Kamen Rider</span>
                        <span id="enemy-form-badge" class="text-xs px-2 py-0.5 rounded bg-red-950 text-red-300 border border-red-800 font-medium">Base Form</span>
                    </div>
                    <!-- HP BAR -->
                    <div class="w-48 md:w-64 bg-slate-800 h-4 rounded-full overflow-hidden mt-1.5 border border-slate-700 relative">
                        <div id="enemy-hp-bar" class="bg-gradient-to-r from-red-600 to-rose-400 h-full w-full transition-all duration-300"></div>
                        <span id="enemy-hp-text" class="absolute inset-0 flex items-center justify-center text-[10px] font-bold text-white drop-shadow">30 / 30 HP</span>
                    </div>
                </div>
            </div>

            <!-- ENEMY STATS & GAUGE -->
            <div class="flex items-center gap-6 text-sm w-full md:w-auto justify-around">
                <div class="text-center">
                    <div class="text-xs text-slate-400">ATK</div>
                    <div id="enemy-atk-val" class="font-orbitron font-bold text-lg text-red-400">4</div>
                </div>
                <div class="text-center">
                    <div class="text-xs text-slate-400">Energy</div>
                    <div id="enemy-energy-val" class="font-orbitron font-bold text-lg text-amber-400">3 / 10</div>
                </div>
                <div class="text-center">
                    <div id="enemy-gauge-label" class="text-xs text-slate-400">Points</div>
                    <div id="enemy-gauge-val" class="font-orbitron font-bold text-lg text-cyan-400">0 / 5</div>
                </div>
                <div class="text-center">
                    <div class="text-xs text-slate-400">การ์ดในมือ</div>
                    <div id="enemy-hand-count" class="font-orbitron font-bold text-lg text-slate-300">5 ใบ</div>
                </div>
            </div>
        </div>

        <!-- ... existing code battlefield & log ... -->

        <!-- PLAYER AREA (BOTTOM) -->
        <div id="player-area" class="cyber-card rounded-2xl p-4 flex flex-col gap-3 border-l-4 border-cyan-500 relative">
            <div class="flex flex-col md:flex-row justify-between items-center gap-4">
                <div class="flex items-center gap-4 w-full md:w-auto">
                    <div class="relative">
                        <div id="player-avatar" class="w-16 h-16 rounded-xl bg-slate-900 flex items-center justify-center border-2 border-cyan-400 p-1">
                            <!-- Dynamic SVG Avatar -->
                        </div>
                        <div id="player-hearts" class="absolute -bottom-2 -right-2 flex gap-1 bg-slate-950 px-2 py-0.5 rounded-full border border-slate-700 text-xs text-red-500">
                            <i class="fa-solid fa-heart"></i><i class="fa-solid fa-heart"></i><i class="fa-solid fa-heart"></i>
                        </div>
                    </div>
                    <div>
                        <div class="flex items-center gap-2">
                            <span id="player-name" class="font-orbitron font-bold text-lg text-slate-100">Kamen Rider Kuuga</span>
                            <span id="player-form-badge" class="text-xs px-2 py-0.5 rounded bg-cyan-950 text-cyan-300 border border-cyan-800 font-medium">Mighty Form</span>
                        </div>
                        <!-- HP BAR -->
                        <div class="w-48 md:w-64 bg-slate-800 h-4 rounded-full overflow-hidden mt-1.5 border border-slate-700 relative">
                            <div id="player-hp-bar" class="bg-gradient-to-r from-cyan-500 to-emerald-400 h-full w-full transition-all duration-300"></div>
                            <span id="player-hp-text" class="absolute inset-0 flex items-center justify-center text-[10px] font-bold text-white drop-shadow">30 / 30 HP</span>
                        </div>
                    </div>
                </div>

                <!-- PLAYER STATS & GAUGES -->
                <div class="flex items-center gap-6 text-sm w-full md:w-auto justify-around">
                    <div class="text-center">
                        <div class="text-xs text-slate-400">ATK</div>
                        <div id="player-atk-val" class="font-orbitron font-bold text-lg text-red-400">4</div>
                    </div>
                    <div class="text-center">
                        <div class="text-xs text-slate-400">Energy</div>
                        <div id="player-energy-val" class="font-orbitron font-bold text-lg text-amber-400">3 / 10</div>
                    </div>
                    <div class="text-center">
                        <div id="player-gauge-label" class="text-xs text-slate-400">Power Points</div>
                        <div id="player-gauge-val" class="font-orbitron font-bold text-lg text-cyan-400">0 / 5</div>
                    </div>
                    <div class="text-center">
                        <div class="text-xs text-slate-400">การ์ดในกอง</div>
                        <div id="player-deck-count" class="font-orbitron font-bold text-lg text-slate-300">15 ใบ</div>
                    </div>
                </div>
            </div>

            <!-- PLAYER HAND CARDS DISPLAY -->
            <div class="mt-2">
                <div class="flex justify-between items-center mb-2">
                    <span class="text-xs text-slate-400 font-semibold flex items-center gap-1.5">
                        <i class="fa-solid fa-layer-group text-cyan-400"></i> การ์ดในมือ (<span id="hand-count-num">5</span>/7)
                        <span class="text-[10px] text-slate-500 ml-2">(กดปุ่ม 🔥 บนการ์ดเพื่อ Sacrifice +1 Energy)</span>
                    </span>
                </div>
                
                <div id="player-hand-container" class="flex gap-3 overflow-x-auto pb-2 pt-1 min-h-[220px] items-center">
                    <!-- Cards will be dynamically injected here -->
                </div>
            </div>
        </div>

    </main>

    <!-- RIDER SELECTION MODAL -->
    <div id="select-rider-modal" class="fixed inset-0 bg-slate-950/90 backdrop-blur-md z-50 flex items-center justify-center p-4">
        <div class="max-w-4xl w-full cyber-card rounded-2xl p-6 border-2 border-cyan-500 shadow-2xl">
            <h2 class="text-2xl font-black font-orbitron text-center text-cyan-400 mb-2">เลือก KAMEN RIDER ของคุณ</h2>
            <p class="text-center text-xs text-slate-400 mb-6">เลือกไรเดอร์เพื่อเข้าสู่การต่อสู้ด้วยการ์ดเด็คเฉพาะตัว 20 ใบ</p>

            <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                <!-- KUUGA -->
                <div onclick="selectRider('kuuga')" class="cyber-card rounded-xl p-4 cursor-pointer hover:border-red-500 text-center flex flex-col justify-between group">
                    <div>
                        <div class="w-28 h-32 rounded-2xl bg-slate-900 border-2 border-red-500 mx-auto flex items-center justify-center mb-3 group-hover:scale-105 transition overflow-hidden relative p-2">
                            <svg viewBox="0 0 100 100" class="w-full h-full drop-shadow-[0_0_12px_rgba(239,68,68,0.7)]">
                                <circle cx="50" cy="50" r="45" fill="#180e18" stroke="#ef4444" stroke-width="2"/>
                                <!-- Kuuga Horns Crest -->
                                <path d="M50,22 L58,38 L78,28 L66,48 L80,58 L58,58 L50,82 L42,58 L20,58 L34,48 L22,28 L42,38 Z" fill="#eab308" stroke="#fef08a" stroke-width="1.5"/>
                                <!-- Amadam Gem -->
                                <circle cx="50" cy="52" r="8" fill="#dc2626" stroke="#fca5a5" stroke-width="1.5"/>
                                <circle cx="50" cy="52" r="4" fill="#ef4444"/>
                            </svg>
                        </div>
                        <h3 class="font-bold text-lg text-red-400 font-orbitron">Kamen Rider Kuuga</h3>
                        <p class="text-xs text-slate-400 mt-1">สมดุลสูง • เปลี่ยนฟอร์มหลากหลาย (Dragon, Pegasus, Titan, Ultimate)</p>
                    </div>
                    <div class="mt-4 pt-3 border-t border-slate-800 text-[11px] text-slate-400">
                        เกจสะสม: <span class="text-red-400 font-bold">Power Points</span>
                    </div>
                </div>

                <!-- AGITO -->
                <div onclick="selectRider('agito')" class="cyber-card rounded-xl p-4 cursor-pointer hover:border-amber-500 text-center flex flex-col justify-between group">
                    <div>
                        <div class="w-28 h-32 rounded-2xl bg-slate-900 border-2 border-amber-500 mx-auto flex items-center justify-center mb-3 group-hover:scale-105 transition overflow-hidden relative p-2">
                            <svg viewBox="0 0 100 100" class="w-full h-full drop-shadow-[0_0_12px_rgba(245,158,11,0.7)]">
                                <circle cx="50" cy="50" r="45" fill="#1a1405" stroke="#f59e0b" stroke-width="2"/>
                                <!-- Agito Cross Horns Emblem -->
                                <path d="M50,15 L56,35 L76,20 L65,42 L85,50 L65,58 L76,80 L56,65 L50,85 L44,65 L24,80 L35,58 L15,50 L35,42 L24,20 L44,35 Z" fill="#f59e0b" stroke="#fef08a" stroke-width="1.5"/>
                                <!-- Wiseman Monolith Core -->
                                <polygon points="50,38 58,50 50,62 42,50" fill="#facc15" stroke="#ffffff" stroke-width="1"/>
                            </svg>
                        </div>
                        <h3 class="font-bold text-lg text-amber-400 font-orbitron">Kamen Rider Agito</h3>
                        <p class="text-xs text-slate-400 mt-1">พลังโจมตีรุนแรง • วิวัฒนาการต่อเนื่อง (Flame, Storm, Trinity, Shining)</p>
                    </div>
                    <div class="mt-4 pt-3 border-t border-slate-800 text-[11px] text-slate-400">
                        เกจสะสม: <span class="text-amber-400 font-bold">Evolution Points</span>
                    </div>
                </div>

                <!-- RYUKI -->
                <div onclick="selectRider('ryuki')" class="cyber-card rounded-xl p-4 cursor-pointer hover:border-rose-600 text-center flex flex-col justify-between group">
                    <div>
                        <div class="w-28 h-32 rounded-2xl bg-slate-900 border-2 border-rose-600 mx-auto flex items-center justify-center mb-3 group-hover:scale-105 transition overflow-hidden relative p-2">
                            <svg viewBox="0 0 100 100" class="w-full h-full drop-shadow-[0_0_12px_rgba(225,29,72,0.7)]">
                                <circle cx="50" cy="50" r="45" fill="#1f0910" stroke="#e11d48" stroke-width="2"/>
                                <!-- Dragredder Dragon Head Visor Emblem -->
                                <path d="M25,35 L50,20 L75,35 L80,55 L50,82 L20,55 Z" fill="#9f1239" stroke="#fda4af" stroke-width="1.5"/>
                                <path d="M30,42 H70 M32,48 H68 M35,54 H65 M38,60 H62" stroke="#f43f5e" stroke-width="2" stroke-linecap="round"/>
                                <polygon points="50,25 56,35 44,35" fill="#fbbf24"/>
                            </svg>
                        </div>
                        <h3 class="font-bold text-lg text-rose-400 font-orbitron">Kamen Rider Ryuki</h3>
                        <p class="text-xs text-slate-400 mt-1">กลยุทธ์การ์ด Advent & Vent • มี Guard Vent / Trick Vent / Final Vent</p>
                    </div>
                    <div class="mt-4 pt-3 border-t border-slate-800 text-[11px] text-slate-400">
                        เกจสะสม: <span class="text-rose-400 font-bold">Advent Points</span>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- ... existing defense modal and game over modal ... -->

    <script>
        const RIDER_AVATAR_IMAGES = {
            kuuga: 'https://static.wikia.nocookie.net/kamenrider/images/2/23/Kamen_Rider_Kuuga_Mighty_Form.png',
            agito: 'https://static.wikia.nocookie.net/kamenrider/images/f/f6/KR-Agito_Ground.png',
            ryuki: 'https://static.wikia.nocookie.net/kamenrider/images/4/4e/KR-Ryuki.png'
        };

        const CARD_PHOTO_URLS = {
            kick: 'https://static.wikia.nocookie.net/kamenrider/images/3/36/Rider_Kick_Kuuga.png',
            sword: 'https://static.wikia.nocookie.net/kamenrider/images/0/05/Titan_Sword.png',
            bow: 'https://static.wikia.nocookie.net/kamenrider/images/0/07/Pegasus_Bowgun.png',
            shield: 'https://static.wikia.nocookie.net/kamenrider/images/6/67/Drag_Shield.png',
            form: 'https://static.wikia.nocookie.net/kamenrider/images/b/b3/Kuuga_Ultimate.png',
            default: 'https://static.wikia.nocookie.net/kamenrider/images/d/d4/Advent_Card_Ryuki.png'
        };

        function getRiderAvatarHTML(riderKey) {
            const imgUrl = RIDER_AVATAR_IMAGES[riderKey];
            return `<img src="${imgUrl}" alt="${riderKey}" class="w-full h-full object-contain p-0.5 filter drop-shadow" onerror="this.onerror=null; this.parentElement.innerHTML=getRiderAvatarSVG('${riderKey}');">`;
        }

        function getCardImageURL(card) {
            const name = card.name.toLowerCase();
            if (name.includes('kick') || name.includes('vent')) return CARD_PHOTO_URLS.kick;
            if (name.includes('sword') || name.includes('saber') || name.includes('rod')) return CARD_PHOTO_URLS.sword;
            if (name.includes('bow')) return CARD_PHOTO_URLS.bow;
            if (card.type === 'defense' || name.includes('guard')) return CARD_PHOTO_URLS.shield;
            if (card.type === 'form') return CARD_PHOTO_URLS.form;
            return CARD_PHOTO_URLS.default;
        }

        function updateUI() {
            // ... existing code in updateUI ...
            // Render Rider Avatars with Real Photos
            document.getElementById('player-avatar').innerHTML = getRiderAvatarHTML(gameState.player.rider);
            document.getElementById('enemy-avatar').innerHTML = getRiderAvatarHTML(gameState.enemy.rider);
            // ... existing code in updateUI ...
        }

        function getCardIllustrationSVG(card) {
            const name = card.name.toLowerCase();
            const type = card.type;

            // KICK / RIDER KICK / FINAL VENT
            if (name.includes('kick') || name.includes('vent')) {
                return `<svg viewBox="0 0 100 60" class="w-full h-12">
                    <path d="M10,50 L40,30 L70,10" stroke="#f59e0b" stroke-width="6" stroke-linecap="round"/>
                    <circle cx="70" cy="10" r="12" fill="#ef4444" opacity="0.8"/>
                    <path d="M60,5 L85,10 L75,25 Z" fill="#facc15"/>
                    <line x1="5" y1="55" x2="35" y2="35" stroke="#f97316" stroke-width="3" stroke-dasharray="3,3"/>
                </svg>`;
            }
            // PUNCH / STRIKE / ATTACK
            if (name.includes('punch') || name.includes('strike') || name.includes('slash')) {
                return `<svg viewBox="0 0 100 60" class="w-full h-12">
                    <circle cx="50" cy="30" r="18" fill="#dc2626"/>
                    <path d="M40,20 L60,20 L65,35 L35,35 Z" fill="#facc15"/>
                    <path d="M10,30 L32,30" stroke="#f87171" stroke-width="4"/>
                    <path d="M68,15 L88,5 M68,30 L90,30 M68,45 L88,55" stroke="#fef08a" stroke-width="3"/>
                </svg>`;
            }
            // SWORD / SABER / HALBERD / ROD
            if (name.includes('sword') || name.includes('saber') || name.includes('halberd') || name.includes('rod')) {
                return `<svg viewBox="0 0 100 60" class="w-full h-12">
                    <path d="M15,50 L75,10" stroke="#38bdf8" stroke-width="5" stroke-linecap="round"/>
                    <path d="M70,5 L90,10 L85,30 Z" fill="#f0f9ff"/>
                    <circle cx="20" cy="46" r="6" fill="#eab308"/>
                </svg>`;
            }
            // BOW / BOWGUN
            if (name.includes('bow')) {
                return `<svg viewBox="0 0 100 60" class="w-full h-12">
                    <path d="M30,10 Q60,30 30,50" fill="none" stroke="#10b981" stroke-width="4"/>
                    <line x1="30" y1="10" x2="30" y2="50" stroke="#ecfdf5" stroke-width="2"/>
                    <line x1="20" y1="30" x2="80" y2="30" stroke="#facc15" stroke-width="4"/>
                    <polygon points="80,25 92,30 80,35" fill="#facc15"/>
                </svg>`;
            }
            // DEFENSE / GUARD / SHIELD
            if (type === 'defense' || name.includes('guard') || name.includes('shield')) {
                return `<svg viewBox="0 0 100 60" class="w-full h-12">
                    <path d="M50,8 L80,20 V38 Q50,55 50,55 Q50,55 20,38 V20 Z" fill="#1e3a8a" stroke="#60a5fa" stroke-width="3"/>
                    <circle cx="50" cy="30" r="10" fill="#93c5fd" opacity="0.6"/>
                </svg>`;
            }
            // FORM CHANGE / SURVIVE / ULTIMATE
            if (type === 'form' || name.includes('form') || name.includes('survive')) {
                return `<svg viewBox="0 0 100 60" class="w-full h-12">
                    <circle cx="50" cy="30" r="22" fill="none" stroke="#f59e0b" stroke-width="3" stroke-dasharray="6,4"/>
                    <polygon points="50,12 60,25 75,30 60,35 50,48 40,35 25,30 40,25" fill="#fbbf24"/>
                </svg>`;
            }
            // SPECIAL / ADVENT / CHARGE / HEAL
            return `<svg viewBox="0 0 100 60" class="w-full h-12">
                <circle cx="50" cy="30" r="16" fill="#7e22ce" opacity="0.8"/>
                <path d="M50,10 L50,50 M30,30 L70,30" stroke="#c084fc" stroke-width="4"/>
                <circle cx="50" cy="30" r="6" fill="#f0abfc"/>
            </svg>`;
        }

        function updateUI() {
            /* ... existing UI updates ... */

            // Render Rider Avatars
            document.getElementById('player-avatar').innerHTML = getRiderAvatarSVG(gameState.player.rider);
            document.getElementById('enemy-avatar').innerHTML = getRiderAvatarSVG(gameState.enemy.rider);

            0%, 100% { box-shadow: 0 0 10px rgba(0, 240, 255, 0.4); }
            50% { box-shadow: 0 0 25px rgba(0, 240, 255, 0.8); }
        }

        .active-turn-glow {
            animation: pulse-glow 2s infinite;
        }
    </style>
</head>
<body class="min-h-screen bg-slate-950 text-slate-100 flex flex-col justify-between">

    <!-- TOP NAVIGATION / HUD HEADER -->
    <header class="bg-slate-900/80 backdrop-blur border-b border-slate-800 p-3 px-6 flex justify-between items-center sticky top-0 z-30">
        <div class="flex items-center gap-3">
            <i class="fa-solid fa-bolt text-cyan-400 text-2xl animate-pulse"></i>
            <div>
                <h1 class="font-orbitron font-black text-xl tracking-wider text-transparent bg-clip-text bg-gradient-to-r from-cyan-400 via-blue-500 to-purple-500">
                    KAMEN RIDER CARD BATTLE
                </h1>
                <p class="text-xs text-slate-400">Ver 2.5 • Official Rule Engine</p>
            </div>
        </div>

        <div id="game-phase-badge" class="font-orbitron text-xs px-4 py-1.5 rounded-full bg-slate-800 border border-slate-700 text-cyan-400 flex items-center gap-2">
            <span class="w-2 h-2 rounded-full bg-cyan-400 animate-ping"></span>
            <span id="phase-text">INITIALIZING</span>
        </div>

        <button onclick="resetGameModal()" class="px-3 py-1.5 text-xs rounded border border-red-500/50 hover:bg-red-500/20 text-red-400 transition">
            <i class="fa-solid fa-rotate-left mr-1"></i> เริ่มเกมใหม่
        </button>
    </header>

    <!-- MAIN GAME ARENA -->
    <main class="flex-1 max-w-7xl w-full mx-auto p-4 flex flex-col justify-between gap-4">

        <!-- ENEMY AREA (TOP) -->
        <div id="enemy-area" class="cyber-card rounded-2xl p-4 flex flex-col md:flex-row justify-between items-center gap-4 border-l-4 border-red-500 relative">
            <div class="flex items-center gap-4 w-full md:w-auto">
                <div class="relative">
                    <div id="enemy-avatar" class="w-16 h-16 rounded-xl bg-slate-800 flex items-center justify-center border-2 border-red-500 text-3xl text-red-400 font-bold font-orbitron">
                        AI
                    </div>
                    <div id="enemy-hearts" class="absolute -bottom-2 -right-2 flex gap-1 bg-slate-950 px-2 py-0.5 rounded-full border border-slate-700 text-xs text-red-500">
                        <i class="fa-solid fa-heart"></i><i class="fa-solid fa-heart"></i><i class="fa-solid fa-heart"></i>
                    </div>
                </div>
                <div>
                    <div class="flex items-center gap-2">
                        <span id="enemy-name" class="font-orbitron font-bold text-lg text-slate-100">Kamen Rider</span>
                        <span id="enemy-form-badge" class="text-xs px-2 py-0.5 rounded bg-red-950 text-red-300 border border-red-800 font-medium">Base Form</span>
                    </div>
                    <!-- HP BAR -->
                    <div class="w-48 md:w-64 bg-slate-800 h-4 rounded-full overflow-hidden mt-1.5 border border-slate-700 relative">
                        <div id="enemy-hp-bar" class="bg-gradient-to-r from-red-600 to-rose-400 h-full w-full transition-all duration-300"></div>
                        <span id="enemy-hp-text" class="absolute inset-0 flex items-center justify-center text-[10px] font-bold text-white drop-shadow">30 / 30 HP</span>
                    </div>
                </div>
            </div>

            <!-- ENEMY STATS & GAUGE -->
            <div class="flex items-center gap-6 text-sm w-full md:w-auto justify-around">
                <div class="text-center">
                    <div class="text-xs text-slate-400">ATK</div>
                    <div id="enemy-atk-val" class="font-orbitron font-bold text-lg text-red-400">4</div>
                </div>
                <div class="text-center">
                    <div class="text-xs text-slate-400">Energy</div>
                    <div id="enemy-energy-val" class="font-orbitron font-bold text-lg text-amber-400">3 / 10</div>
                </div>
                <div class="text-center">
                    <div id="enemy-gauge-label" class="text-xs text-slate-400">Points</div>
                    <div id="enemy-gauge-val" class="font-orbitron font-bold text-lg text-cyan-400">0 / 5</div>
                </div>
                <div class="text-center">
                    <div class="text-xs text-slate-400">การ์ดในมือ</div>
                    <div id="enemy-hand-count" class="font-orbitron font-bold text-lg text-slate-300">5 ใบ</div>
                </div>
            </div>
        </div>

        <!-- CENTER COMBAT BATTLEFIELD & LOG -->
        <div class="grid grid-cols-1 lg:grid-cols-3 gap-4 my-2">
            <!-- COMBAT LOG -->
            <div class="lg:col-span-2 cyber-card rounded-2xl p-3 flex flex-col h-44 border border-slate-800">
                <div class="flex justify-between items-center mb-2 pb-1 border-b border-slate-800/80 text-xs text-slate-400">
                    <span><i class="fa-solid fa-terminal text-cyan-400 mr-1"></i> COMBAT BATTLE LOG</span>
                    <span id="turn-counter-badge" class="font-orbitron text-cyan-300">Turn 1</span>
                </div>
                <div id="battle-log" class="flex-1 overflow-y-auto space-y-1 text-xs text-slate-300 pr-2">
                    <div class="text-slate-500 italic">ยินดีต้อนรับสู่ Kamen Rider Card Battle! เลือกไรเดอร์ของคุณเพื่อเริ่ม...</div>
                </div>
            </div>

            <!-- ACTION CONTROLS / BASIC ATTACK CONTAINER -->
            <div class="cyber-card rounded-2xl p-4 flex flex-col justify-between border border-cyan-500/30 bg-gradient-to-b from-slate-900 to-slate-950">
                <div>
                    <div class="text-xs font-bold text-slate-400 uppercase tracking-wider mb-2 flex items-center justify-between">
                        <span><i class="fa-solid fa-gamepad text-amber-400 mr-1"></i> แอ็กชันการต่อสู้</span>
                        <span id="attack-status-indicator" class="text-[10px] text-emerald-400 bg-emerald-950 px-2 py-0.5 rounded border border-emerald-800">พร้อมโจมตี</span>
                    </div>
                    
                    <!-- DEDICATED BASIC ATTACK BUTTON -->
                    <button id="btn-basic-attack" onclick="handlePlayerBasicAttack()" class="w-full cyber-button py-3 px-4 rounded-xl font-bold text-sm text-cyan-300 flex items-center justify-center gap-2 border-2 border-cyan-400 shadow-lg mb-2">
                        <i class="fa-solid fa-hand-fist text-lg text-amber-400"></i>
                        <span>👊 โจมตีปกติ (Basic Attack)</span>
                    </button>
                    <p class="text-[11px] text-slate-400 text-center leading-tight mb-3">
                        ใช้ค่า ATK ของไรเดอร์โดยตรง (<span id="basic-atk-preview" class="text-amber-400 font-bold">4 DMG</span>) • ไม่เสีย Energy
                    </p>
                </div>

                <div class="flex gap-2">
                    <button id="btn-end-turn" onclick="endTurn()" class="flex-1 py-2.5 px-3 rounded-xl bg-slate-800 hover:bg-slate-700 border border-slate-600 font-semibold text-xs text-slate-200 transition flex items-center justify-center gap-1.5">
                        <i class="fa-solid fa-hourglass-end text-cyan-400"></i>
                        <span>จบเทิร์น (End Turn)</span>
                    </button>
                </div>
            </div>
        </div>

        <!-- PLAYER AREA (BOTTOM) -->
        <div id="player-area" class="cyber-card rounded-2xl p-4 flex flex-col gap-3 border-l-4 border-cyan-500 relative">
            <div class="flex flex-col md:flex-row justify-between items-center gap-4">
                <div class="flex items-center gap-4 w-full md:w-auto">
                    <div class="relative">
                        <div id="player-avatar" class="w-16 h-16 rounded-xl bg-slate-800 flex items-center justify-center border-2 border-cyan-400 text-3xl text-cyan-400 font-bold font-orbitron">
                            P1
                        </div>
                        <div id="player-hearts" class="absolute -bottom-2 -right-2 flex gap-1 bg-slate-950 px-2 py-0.5 rounded-full border border-slate-700 text-xs text-red-500">
                            <i class="fa-solid fa-heart"></i><i class="fa-solid fa-heart"></i><i class="fa-solid fa-heart"></i>
                        </div>
                    </div>
                    <div>
                        <div class="flex items-center gap-2">
                            <span id="player-name" class="font-orbitron font-bold text-lg text-slate-100">Kamen Rider Kuuga</span>
                            <span id="player-form-badge" class="text-xs px-2 py-0.5 rounded bg-cyan-950 text-cyan-300 border border-cyan-800 font-medium">Mighty Form</span>
                        </div>
                        <!-- HP BAR -->
                        <div class="w-48 md:w-64 bg-slate-800 h-4 rounded-full overflow-hidden mt-1.5 border border-slate-700 relative">
                            <div id="player-hp-bar" class="bg-gradient-to-r from-cyan-500 to-emerald-400 h-full w-full transition-all duration-300"></div>
                            <span id="player-hp-text" class="absolute inset-0 flex items-center justify-center text-[10px] font-bold text-white drop-shadow">30 / 30 HP</span>
                        </div>
                    </div>
                </div>

                <!-- PLAYER STATS & GAUGES -->
                <div class="flex items-center gap-6 text-sm w-full md:w-auto justify-around">
                    <div class="text-center">
                        <div class="text-xs text-slate-400">ATK</div>
                        <div id="player-atk-val" class="font-orbitron font-bold text-lg text-red-400">4</div>
                    </div>
                    <div class="text-center">
                        <div class="text-xs text-slate-400">Energy</div>
                        <div id="player-energy-val" class="font-orbitron font-bold text-lg text-amber-400">3 / 10</div>
                    </div>
                    <div class="text-center">
                        <div id="player-gauge-label" class="text-xs text-slate-400">Power Points</div>
                        <div id="player-gauge-val" class="font-orbitron font-bold text-lg text-cyan-400">0 / 5</div>
                    </div>
                    <div class="text-center">
                        <div class="text-xs text-slate-400">การ์ดในกอง</div>
                        <div id="player-deck-count" class="font-orbitron font-bold text-lg text-slate-300">15 ใบ</div>
                    </div>
                </div>
            </div>

            <!-- PLAYER HAND CARDS DISPLAY -->
            <div class="mt-2">
                <div class="flex justify-between items-center mb-2">
                    <span class="text-xs text-slate-400 font-semibold flex items-center gap-1.5">
                        <i class="fa-solid fa-layer-group text-cyan-400"></i> การ์ดในมือ (<span id="hand-count-num">5</span>/7)
                        <span class="text-[10px] text-slate-500 ml-2">(กดปุ่ม 🔥 บนการ์ดเพื่อ Sacrifice +1 Energy)</span>
                    </span>
                </div>
                
                <div id="player-hand-container" class="flex gap-3 overflow-x-auto pb-2 pt-1 min-h-[160px] items-center">
                    <!-- Cards will be dynamically injected here -->
                </div>
            </div>
        </div>

    </main>

    <!-- RIDER SELECTION MODAL -->
    <div id="select-rider-modal" class="fixed inset-0 bg-slate-950/90 backdrop-blur-md z-50 flex items-center justify-center p-4">
        <div class="max-w-3xl w-full cyber-card rounded-2xl p-6 border-2 border-cyan-500 shadow-2xl">
            <h2 class="text-2xl font-black font-orbitron text-center text-cyan-400 mb-2">เลือก KAMEN RIDER ของคุณ</h2>
            <p class="text-center text-xs text-slate-400 mb-6">เลือกไรเดอร์เพื่อเข้าสู่การต่อสู้ด้วยการ์ดเด็คเฉพาะตัว 20 ใบ</p>

            <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                <!-- KUUGA -->
                <div onclick="selectRider('kuuga')" class="cyber-card rounded-xl p-4 cursor-pointer hover:border-red-500 text-center flex flex-col justify-between group">
                    <div>
                        <div class="w-16 h-16 rounded-full bg-red-950 border-2 border-red-500 mx-auto flex items-center justify-center text-red-400 font-black font-orbitron text-xl mb-3 group-hover:scale-110 transition">
                            KUG
                        </div>
                        <h3 class="font-bold text-lg text-red-400 font-orbitron">Kamen Rider Kuuga</h3>
                        <p class="text-xs text-slate-400 mt-1">สมดุลสูง • เปลี่ยนฟอร์มหลากหลาย (Dragon, Pegasus, Titan, Ultimate)</p>
                    </div>
                    <div class="mt-4 pt-3 border-t border-slate-800 text-[11px] text-slate-400">
                        เกจสะสม: <span class="text-red-400 font-bold">Power Points</span>
                    </div>
                </div>

                <!-- AGITO -->
                <div onclick="selectRider('agito')" class="cyber-card rounded-xl p-4 cursor-pointer hover:border-amber-500 text-center flex flex-col justify-between group">
                    <div>
                        <div class="w-16 h-16 rounded-full bg-amber-950 border-2 border-amber-500 mx-auto flex items-center justify-center text-amber-400 font-black font-orbitron text-xl mb-3 group-hover:scale-110 transition">
                            AGT
                        </div>
                        <h3 class="font-bold text-lg text-amber-400 font-orbitron">Kamen Rider Agito</h3>
                        <p class="text-xs text-slate-400 mt-1">พลังโจมตีรุนแรง • วิวัฒนาการต่อเนื่อง (Flame, Storm, Trinity, Shining)</p>
                    </div>
                    <div class="mt-4 pt-3 border-t border-slate-800 text-[11px] text-slate-400">
                        เกจสะสม: <span class="text-amber-400 font-bold">Evolution Points</span>
                    </div>
                </div>

                <!-- RYUKI -->
                <div onclick="selectRider('ryuki')" class="cyber-card rounded-xl p-4 cursor-pointer hover:border-red-600 text-center flex flex-col justify-between group">
                    <div>
                        <div class="w-16 h-16 rounded-full bg-rose-950 border-2 border-rose-600 mx-auto flex items-center justify-center text-rose-400 font-black font-orbitron text-xl mb-3 group-hover:scale-110 transition">
                            RYU
                        </div>
                        <h3 class="font-bold text-lg text-rose-400 font-orbitron">Kamen Rider Ryuki</h3>
                        <p class="text-xs text-slate-400 mt-1">กลยุทธ์การ์ด Advent & Vent • มี Guard Vent / Trick Vent / Final Vent</p>
                    </div>
                    <div class="mt-4 pt-3 border-t border-slate-800 text-[11px] text-slate-400">
                        เกจสะสม: <span class="text-rose-400 font-bold">Advent Points</span>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- DEFENSE REACTION MODAL -->
    <div id="defense-modal" class="hidden fixed inset-0 bg-slate-950/85 backdrop-blur-md z-50 flex items-center justify-center p-4">
        <div class="max-w-md w-full cyber-card rounded-2xl p-5 border-2 border-blue-500 shadow-2xl">
            <div class="text-center mb-4">
                <span class="text-xs font-bold text-blue-400 uppercase tracking-widest">⚠️ DEFENSE REACTION PHASE</span>
                <h3 class="text-xl font-bold text-slate-100 mt-1" id="defense-title">ศัตรูกำลังโจมตีคุณ!</h3>
                <p class="text-xs text-slate-400 mt-1" id="defense-desc">ความเสียหายที่จะได้รับ: <span id="defense-incoming-dmg" class="text-red-400 font-bold text-base">0</span> DMG</p>
            </div>

            <div class="space-y-3 mb-4">
                <div class="text-xs text-slate-400 font-semibold">เลือกการ์ดป้องกันในมือ:</div>
                <div id="defense-card-options" class="flex flex-col gap-2 max-h-48 overflow-y-auto pr-1">
                    <!-- Dynamic defense choices -->
                </div>
            </div>

            <div class="flex gap-2">
                <button onclick="resolveDefense(null)" class="w-full py-2.5 rounded-xl bg-slate-800 hover:bg-slate-700 text-xs font-semibold text-slate-300 border border-slate-600">
                    ไม่ใช้การ์ดป้องกัน (รับความเสียหาย)
                </button>
            </div>
        </div>
    </div>

    <!-- GAME OVER / VICTORY MODAL -->
    <div id="game-over-modal" class="hidden fixed inset-0 bg-slate-950/90 backdrop-blur-md z-50 flex items-center justify-center p-4">
        <div class="max-w-md w-full cyber-card rounded-2xl p-6 text-center border-2 border-cyan-400">
            <div id="game-over-icon" class="text-5xl text-amber-400 mb-3">🏆</div>
            <h2 id="game-over-title" class="text-2xl font-black font-orbitron text-cyan-400 mb-2">VICTORY!</h2>
            <p id="game-over-msg" class="text-xs text-slate-300 mb-6">คุณสามารถเอาชนะคู่ต่อสู้ได้สำเร็จ!</p>
            <button onclick="location.reload()" class="w-full cyber-button py-3 rounded-xl font-bold text-cyan-300 border border-cyan-400">
                เล่นใหม่อีกครั้ง
            </button>
        </div>
    </div>

    <script>
        function getRiderAvatarSVG(riderKey) {
            if (riderKey === 'kuuga') {
                return `<svg viewBox="0 0 100 100" class="w-full h-full p-1 drop-shadow-[0_0_8px_rgba(239,68,68,0.8)]">
                    <circle cx="50" cy="50" r="44" fill="#180e18" stroke="#ef4444" stroke-width="2"/>
                    <path d="M50,22 L58,38 L78,28 L66,48 L80,58 L58,58 L50,82 L42,58 L20,58 L34,48 L22,28 L42,38 Z" fill="#eab308" stroke="#fef08a" stroke-width="1.5"/>
                    <circle cx="50" cy="52" r="8" fill="#dc2626" stroke="#fca5a5" stroke-width="1.5"/>
                    <circle cx="50" cy="52" r="4" fill="#ef4444"/>
                </svg>`;
            }
            if (riderKey === 'agito') {
                return `<svg viewBox="0 0 100 100" class="w-full h-full p-1 drop-shadow-[0_0_8px_rgba(245,158,11,0.8)]">
                    <circle cx="50" cy="50" r="44" fill="#1a1405" stroke="#f59e0b" stroke-width="2"/>
                    <path d="M50,15 L56,35 L76,20 L65,42 L85,50 L65,58 L76,80 L56,65 L50,85 L44,65 L24,80 L35,58 L15,50 L35,42 L24,20 L44,35 Z" fill="#f59e0b" stroke="#fef08a" stroke-width="1.5"/>
                    <polygon points="50,38 58,50 50,62 42,50" fill="#facc15" stroke="#ffffff" stroke-width="1"/>
                </svg>`;
            }
            // Ryuki
            return `<svg viewBox="0 0 100 100" class="w-full h-full p-1 drop-shadow-[0_0_8px_rgba(225,29,72,0.8)]">
                <circle cx="50" cy="50" r="44" fill="#1f0910" stroke="#e11d48" stroke-width="2"/>
                <path d="M25,35 L50,20 L75,35 L80,55 L50,82 L20,55 Z" fill="#9f1239" stroke="#fda4af" stroke-width="1.5"/>
                <path d="M30,42 H70 M32,48 H68 M35,54 H65 M38,60 H62" stroke="#f43f5e" stroke-width="2" stroke-linecap="round"/>
                <polygon points="50,25 56,35 44,35" fill="#fbbf24"/>
            </svg>`;
        }

        function getCardIllustrationSVG(card) {
            const name = card.name.toLowerCase();
            const type = card.type;

            // RIDER KICK / FINAL VENT / RISING KICK
            if (name.includes('kick') || name.includes('vent')) {
                return `<svg viewBox="0 0 100 60" class="w-full h-12">
                    <defs>
                        <radialGradient id="kickGlow" cx="70%" cy="30%" r="70%">
                            <stop offset="0%" stop-color="#fef08a"/>
                            <stop offset="60%" stop-color="#ef4444"/>
                            <stop offset="100%" stop-color="#7f1d1d"/>
                        </radialGradient>
                    </defs>
                    <path d="M10,52 L45,32 L75,12" stroke="#f59e0b" stroke-width="6" stroke-linecap="round"/>
                    <circle cx="75" cy="12" r="16" fill="url(#kickGlow)" opacity="0.95"/>
                    <polygon points="65,4 92,12 78,28" fill="#facc15"/>
                    <line x1="5" y1="58" x2="38" y2="38" stroke="#f97316" stroke-width="3" stroke-dasharray="4,3"/>
                </svg>`;
            }
            // PUNCH / STRIKE / BOMB
            if (name.includes('punch') || name.includes('strike') || name.includes('slash') || name.includes('attack')) {
                return `<svg viewBox="0 0 100 60" class="w-full h-12">
                    <circle cx="50" cy="30" r="18" fill="#dc2626"/>
                    <path d="M38,18 L62,18 L68,36 L32,36 Z" fill="#facc15" stroke="#fef08a" stroke-width="1"/>
                    <path d="M10,30 L30,30" stroke="#f87171" stroke-width="4" stroke-linecap="round"/>
                    <path d="M68,12 L90,2 M72,30 L95,30 M68,48 L90,58" stroke="#fef08a" stroke-width="3" stroke-linecap="round"/>
                </svg>`;
            }
            // SWORD / SABER / HALBERD / ROD
            if (name.includes('sword') || name.includes('saber') || name.includes('halberd') || name.includes('rod') || name.includes('blade')) {
                return `<svg viewBox="0 0 100 60" class="w-full h-12">
                    <path d="M15,50 L75,10" stroke="#38bdf8" stroke-width="5" stroke-linecap="round"/>
                    <path d="M70,5 L92,8 L84,30 Z" fill="#f0f9ff" stroke="#0284c7" stroke-width="1"/>
                    <circle cx="20" cy="46" r="6" fill="#eab308"/>
                    <path d="M25,42 L80,8" stroke="#ffffff" stroke-width="1.5" stroke-linecap="round"/>
                </svg>`;
            }
            // BOW / BOWGUN / SHOOT
            if (name.includes('bow') || name.includes('shoot')) {
                return `<svg viewBox="0 0 100 60" class="w-full h-12">
                    <path d="M25,8 Q65,30 25,52" fill="none" stroke="#10b981" stroke-width="4" stroke-linecap="round"/>
                    <line x1="25" y1="8" x2="25" y2="52" stroke="#ecfdf5" stroke-width="1.5"/>
                    <line x1="15" y1="30" x2="80" y2="30" stroke="#facc15" stroke-width="4" stroke-linecap="round"/>
                    <polygon points="80,24 95,30 80,36" fill="#facc15"/>
                </svg>`;
            }
            // DEFENSE / GUARD / SHIELD / BARRIER / ESCAPE
            if (type === 'defense' || name.includes('guard') || name.includes('shield') || name.includes('barrier') || name.includes('escape')) {
                return `<svg viewBox="0 0 100 60" class="w-full h-12">
                    <path d="M50,6 L82,18 V38 Q50,56 50,56 Q50,56 18,38 V18 Z" fill="#1e3a8a" stroke="#60a5fa" stroke-width="2.5"/>
                    <circle cx="50" cy="28" r="11" fill="#3b82f6" opacity="0.7"/>
                    <path d="M50,18 L50,38 M40,28 L60,28" stroke="#93c5fd" stroke-width="3" stroke-linecap="round"/>
                </svg>`;
            }
            // FORM CHANGE / SURVIVE / ULTIMATE
            if (type === 'form' || name.includes('form') || name.includes('survive')) {
                return `<svg viewBox="0 0 100 60" class="w-full h-12">
                    <circle cx="50" cy="30" r="22" fill="none" stroke="#f59e0b" stroke-width="2.5" stroke-dasharray="6,3"/>
                    <polygon points="50,10 60,24 76,28 62,36 50,50 38,36 24,28 40,24" fill="#fbbf24" stroke="#fef08a" stroke-width="1"/>
                    <circle cx="50" cy="29" r="6" fill="#ef4444"/>
                </svg>`;
            }
            // SPECIAL / CHARGE / POWER / ADVENT / HEAL
            return `<svg viewBox="0 0 100 60" class="w-full h-12">
                <circle cx="50" cy="30" r="18" fill="#7e22ce" opacity="0.8"/>
                <path d="M50,8 L50,52 M28,30 L72,30" stroke="#c084fc" stroke-width="4" stroke-linecap="round"/>
                <circle cx="50" cy="30" r="8" fill="#f0abfc"/>
            </svg>`;
        }

        function updateUI() {
            // Player stats
            document.getElementById('player-name').innerText = gameState.player.name;
            document.getElementById('player-form-badge').innerText = gameState.player.form;
            document.getElementById('player-hp-text').innerText = `${Math.max(0, gameState.player.hp)} / ${gameState.player.maxHp} HP`;
            document.getElementById('player-hp-bar').style.width = `${Math.max(0, (gameState.player.hp / gameState.player.maxHp) * 100)}%`;
            document.getElementById('player-atk-val').innerText = gameState.player.atk;
            document.getElementById('player-energy-val').innerText = `${gameState.player.energy} / 10`;
            document.getElementById('player-gauge-label').innerText = gameState.player.gaugeName;
            document.getElementById('player-gauge-val').innerText = `${gameState.player.points} / ${gameState.player.maxPoints}`;
            document.getElementById('player-deck-count').innerText = `${gameState.player.deck.length} ใบ`;
            document.getElementById('basic-atk-preview').innerText = `${gameState.player.atk} DMG`;

            // Enemy stats
            document.getElementById('enemy-name').innerText = gameState.enemy.name;
            document.getElementById('enemy-form-badge').innerText = gameState.enemy.form;
            document.getElementById('enemy-hp-text').innerText = `${Math.max(0, gameState.enemy.hp)} / ${gameState.enemy.maxHp} HP`;
            document.getElementById('enemy-hp-bar').style.width = `${Math.max(0, (gameState.enemy.hp / gameState.enemy.maxHp) * 100)}%`;
            document.getElementById('enemy-atk-val').innerText = gameState.enemy.atk;
            document.getElementById('enemy-energy-val').innerText = `${gameState.enemy.energy} / 10`;
            document.getElementById('enemy-gauge-label').innerText = gameState.enemy.gaugeName;
            document.getElementById('enemy-gauge-val').innerText = `${gameState.enemy.points} / ${gameState.enemy.maxPoints}`;
            document.getElementById('enemy-hand-count').innerText = `${gameState.enemy.hand.length} ใบ`;

            // Render Rider Avatars with clean Vector SVGs
            document.getElementById('player-avatar').innerHTML = getRiderAvatarSVG(gameState.player.rider);
            document.getElementById('enemy-avatar').innerHTML = getRiderAvatarSVG(gameState.enemy.rider);

            // Render Hearts
            renderHearts('player-hearts', gameState.player.hearts);
            renderHearts('enemy-hearts', gameState.enemy.hearts);

            // Phase Badge & Turn Counter
            document.getElementById('turn-counter-badge').innerText = `Turn ${gameState.turnNumber}`;
            const phaseBadge = document.getElementById('phase-text');
            if (gameState.currentTurnOwner === 'player') {
                phaseBadge.innerText = "YOUR TURN";
                phaseBadge.className = "text-cyan-400 font-bold";
            } else {
                phaseBadge.innerText = "ENEMY TURN";
                phaseBadge.className = "text-red-400 font-bold";
            }

            // Dedicated Basic Attack Button state
            const basicAtkBtn = document.getElementById('btn-basic-attack');
            const atkStatus = document.getElementById('attack-status-indicator');

            if (gameState.currentTurnOwner !== 'player') {
                basicAtkBtn.disabled = true;
                atkStatus.innerText = "เทิร์นศัตรู";
                atkStatus.className = "text-[10px] text-slate-400 bg-slate-800 px-2 py-0.5 rounded border border-slate-700";
            } else if (gameState.turnNumber === 1) {
                basicAtkBtn.disabled = true;
                atkStatus.innerText = "เทิร์นแรกห้ามโจมตี";
                atkStatus.className = "text-[10px] text-amber-400 bg-amber-950 px-2 py-0.5 rounded border border-amber-800";
            } else if (gameState.hasAttackedThisTurn) {
                basicAtkBtn.disabled = true;
                atkStatus.innerText = "โจมตีไปแล้ว";
                atkStatus.className = "text-[10px] text-red-400 bg-red-950 px-2 py-0.5 rounded border border-red-800";
            } else {
                basicAtkBtn.disabled = false;
                atkStatus.innerText = "พร้อมโจมตี";
                atkStatus.className = "text-[10px] text-emerald-400 bg-emerald-950 px-2 py-0.5 rounded border border-emerald-800";
            }

            // Render Hand Cards
            renderPlayerHand();
        }

        function renderPlayerHand() {
            const container = document.getElementById('player-hand-container');
            document.getElementById('hand-count-num').innerText = gameState.player.hand.length;
            container.innerHTML = '';

            if (gameState.player.hand.length === 0) {
                container.innerHTML = `<div class="text-xs text-slate-500 italic p-4">ไม่มีการ์ดในมือ</div>`;
                return;
            }

            gameState.player.hand.forEach((card, index) => {
                const cardEl = document.createElement('div');
                
                let typeBorder = 'card-attack';
                let typeBadge = 'bg-red-950 text-red-300 border-red-800';
                if (card.type === 'defense') { typeBorder = 'card-defense'; typeBadge = 'bg-blue-950 text-blue-300 border-blue-800'; }
                if (card.type === 'form') { typeBorder = 'card-form'; typeBadge = 'bg-amber-950 text-amber-300 border-amber-800'; }
                if (card.type === 'special') { typeBorder = 'card-special'; typeBadge = 'bg-purple-950 text-purple-300 border-purple-800'; }

                const canAfford = gameState.player.energy >= card.cost;
                const turn1Restricted = (card.type === 'attack' && gameState.turnNumber === 1);
                const isAttackedRestricted = (card.type === 'attack' && gameState.hasAttackedThisTurn);
                const isDisabled = !canAfford || turn1Restricted || isAttackedRestricted || gameState.currentTurnOwner !== 'player';

                const cardSVG = getCardIllustrationSVG(card);

                cardEl.className = `cyber-card ${typeBorder} rounded-xl p-2.5 min-w-[165px] w-[170px] flex flex-col justify-between h-[230px] relative group flex-shrink-0`;
                
                cardEl.innerHTML = `
                    <div>
                        <div class="flex justify-between items-start mb-1">
                            <span class="text-[9px] px-1.5 py-0.5 rounded border uppercase font-bold ${typeBadge}">${card.type}</span>
                            <span class="text-xs font-bold text-amber-300 bg-amber-950/80 px-1.5 py-0.5 rounded border border-amber-800/80">⚡ ${card.cost}</span>
                        </div>
                        <h4 class="font-bold text-xs text-slate-100 truncate mb-1">${card.name}</h4>
                        
                        <!-- CARD ARTWORK VECTOR ILLUSTRATION -->
                        <div class="card-art-frame mb-1.5 overflow-hidden rounded bg-slate-900 border border-slate-700/60 flex items-center justify-center p-1">
                            ${cardSVG}
                        </div>

                        <p class="text-[10px] text-slate-400 line-clamp-2 leading-tight">${card.desc}</p>
                    </div>

                    <div class="flex gap-1 mt-1 pt-1.5 border-t border-slate-800/80">
                        <button onclick="playCard(${index})" ${isDisabled ? 'disabled' : ''} class="flex-1 py-1 rounded text-[11px] font-bold cyber-button text-cyan-300 border-cyan-500/50">
                            ใช้การ์ด
                        </button>
                        <button onclick="sacrificeCard(${index})" ${gameState.currentTurnOwner !== 'player' ? 'disabled' : ''} title="Sacrifice (+1 Energy)" class="px-2 py-1 rounded bg-amber-950 hover:bg-amber-900 border border-amber-800 text-amber-400 text-[10px] font-bold">
                            🔥
                        </button>
                    </div>
                `;

                container.appendChild(cardEl);
            });
        }

        /* ... existing code script logic ... */
    </script>
</body>
</html>
