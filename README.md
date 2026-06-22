<!DOCTYPE html>
<html lang="ru" class="scroll-smooth">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AnyQwer WebRadar — Приватный Тактический Радар и ESP для CS2</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        darkBg: 'var(--bg)',
                        darkCard: 'var(--card)',
                        accentColor: 'var(--accent)',
                        accentHover: 'var(--accent-hover)',
                        borderColor: 'var(--border)',
                        textColor: 'var(--text)'
                    },
                    fontFamily: {
                        sans: ['Inter', 'sans-serif'],
                        mono: ['JetBrains Mono', 'monospace'],
                    },
                    boxShadow: {
                        neonGlow: '0 0 25px var(--glow)',
                        neonBorder: '0 0 10px rgba(255, 255, 255, 0.05)',
                    }
                }
            }
        }
    </script>
    <!-- Google Fonts: Inter & JetBrains Mono -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800;900&family=JetBrains+Mono:wght@400;500;700;800&display=swap" rel="stylesheet">
    <!-- FontAwesome for Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <style>
        /* CSS-переменные синхронизированные с темами */
        :root {
            --bg: #06070a;
            --card: #0c0e15;
            --accent: #4f46e5;
            --accent-hover: #6366f1;
            --glow: rgba(79, 70, 229, 0.45);
            --border: rgba(255, 255, 255, 0.08);
            --text: #ffffff;
            --map-filter: hue-rotate(220deg) brightness(0.6) contrast(1.2) saturate(1.4);
        }

        [data-theme="frost"] {
            --accent: #06b6d4;
            --accent-hover: #22d3ee;
            --glow: rgba(6, 182, 212, 0.45);
            --bg: #0b0f19;
            --card: #111827;
            --border: rgba(6, 182, 212, 0.2);
            --text: #f0f9ff;
            --map-filter: hue-rotate(180deg) brightness(0.5) contrast(1.3) saturate(1.5);
        }

        [data-theme="toxic"] {
            --accent: #22c55e;
            --accent-hover: #4ade80;
            --glow: rgba(34, 197, 94, 0.5);
            --bg: #040805;
            --card: #09110c;
            --border: rgba(34, 197, 94, 0.2);
            --text: #f0fdf4;
            --map-filter: hue-rotate(90deg) brightness(0.5) contrast(1.2) saturate(1.6);
        }

        [data-theme="gold"] {
            --accent: #eab308;
            --accent-hover: #fde047;
            --glow: rgba(234, 179, 8, 0.5);
            --bg: #090702;
            --card: #120e05;
            --border: rgba(234, 179, 8, 0.2);
            --text: #fefdf0;
            --map-filter: hue-rotate(40deg) brightness(0.6) contrast(1.1) saturate(1.3);
        }

        [data-theme="blood"] {
            --accent: #ef4444;
            --accent-hover: #f87171;
            --glow: rgba(239, 68, 68, 0.5);
            --bg: #0a0303;
            --card: #140707;
            --border: rgba(239, 68, 68, 0.25);
            --text: #fef2f2;
            --map-filter: hue-rotate(340deg) brightness(0.5) contrast(1.3) saturate(1.4);
        }

        [data-theme="matrix"] {
            --accent: #10b981;
            --accent-hover: #34d399;
            --glow: rgba(16, 185, 129, 0.5);
            --bg: #000000;
            --card: #020a05;
            --border: rgba(16, 185, 129, 0.3);
            --text: #34d399;
            --map-filter: hue-rotate(120deg) brightness(0.4) contrast(1.5) saturate(1.8) invert(0.05);
        }

        body {
            font-family: 'Inter', sans-serif;
            background-color: var(--bg);
            color: var(--text);
            overflow-x: hidden;
            transition: background-color 0.4s ease, color 0.4s ease;
        }

        /* Grid Pattern Overlay */
        .cyber-grid {
            background-size: 40px 40px;
            background-image: 
                linear-gradient(to right, rgba(255, 255, 255, 0.015) 1px, transparent 1px),
                linear-gradient(to bottom, rgba(255, 255, 255, 0.015) 1px, transparent 1px);
        }

        /* Pulsing & Scanner effects */
        @keyframes pulse-radar {
            0% { transform: scale(0.2); opacity: 0.9; }
            100% { transform: scale(1.4); opacity: 0; }
        }
        .pulse-ring {
            animation: pulse-radar 2.5s cubic-bezier(0.25, 0, 0, 1) infinite;
        }

        @keyframes sweep {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
        .scan-line-animation {
            animation: sweep 12s linear infinite;
        }

        .glass-panel {
            background: rgba(12, 14, 21, 0.8);
            backdrop-filter: blur(24px);
            -webkit-backdrop-filter: blur(24px);
            border: 1px solid rgba(255, 255, 255, 0.04);
        }

        /* Theme styled radar map */
        .radar-image-bg {
            filter: var(--map-filter);
            transition: filter 0.4s ease;
        }

        /* Scrollbar styling */
        ::-webkit-scrollbar {
            width: 8px;
        }
        ::-webkit-scrollbar-track {
            background: var(--bg);
        }
        ::-webkit-scrollbar-thumb {
            background: var(--border);
            border-radius: 4px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: var(--accent);
        }

        /* Drag handle helper */
        .draggable-dot {
            cursor: grab;
        }
        .draggable-dot:active {
            cursor: grabbing;
        }
    </style>
</head>
<body class="antialiased selection:bg-indigo-600 selection:text-white">

    <!-- Navigation Bar -->
    <nav class="fixed top-0 left-0 w-full z-50 glass-panel border-b border-white/5 transition-all duration-300">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
            <div class="flex items-center justify-between h-20">
                <!-- Logo -->
                <div class="flex items-center gap-3">
                    <div class="relative flex items-center justify-center w-11 h-11 rounded-xl shadow-neonGlow transition-colors duration-300" style="background: var(--accent);">
                        <i class="fa-solid fa-satellite-dish text-white text-lg animate-pulse"></i>
                    </div>
                    <div>
                        <span class="font-black text-xl tracking-wider text-transparent bg-clip-text bg-gradient-to-r from-gray-100 to-gray-300">ANYQWER</span>
                        <span class="text-[9px] block text-gray-400 font-mono tracking-widest uppercase">TACTICAL HUD 2.0</span>
                    </div>
                </div>

                <!-- Desktop Links -->
                <div class="hidden lg:flex items-center gap-6">
                    <a href="#features" class="text-gray-300 hover:text-white transition-colors duration-200 text-sm font-semibold">Особенности</a>
                    <a href="#demo" class="text-gray-300 hover:text-white transition-colors duration-200 text-sm font-semibold flex items-center gap-1.5">
                        <span class="relative flex h-2 w-2">
                            <span class="animate-ping absolute inline-flex h-full w-full rounded-full bg-emerald-400 opacity-75"></span>
                            <span class="relative inline-flex rounded-full h-2 w-2 bg-emerald-500"></span>
                        </span>
                        Интерактивный Радар
                    </a>
                    <a href="#wh-sandbox" class="text-gray-300 hover:text-white transition-colors duration-200 text-sm font-semibold">Настройка ESP (WH)</a>
                    <a href="#pricing" class="text-gray-300 hover:text-white transition-colors duration-200 text-sm font-semibold">Тарифы</a>
                    <a href="#status" class="text-gray-300 hover:text-white transition-colors duration-200 text-sm font-semibold">Bypass Статус</a>
                </div>

                <!-- Themes Selector & Action -->
                <div class="flex items-center gap-3">
                    <!-- Quick Theme Selector -->
                    <div class="relative">
                        <select id="globalThemeSelector" class="bg-black/60 border border-white/10 text-xs font-mono rounded-lg px-2.5 py-1.5 text-gray-300 focus:outline-none focus:border-indigo-500 cursor-pointer" onchange="switchTheme(this.value)">
                            <option value="indigo">🌌 Indigo</option>
                            <option value="frost">❄️ Frost</option>
                            <option value="toxic">☣️ Toxic</option>
                            <option value="gold">👑 Gold</option>
                            <option value="blood">🩸 Blood</option>
                            <option value="matrix">📟 Matrix</option>
                        </select>
                    </div>

                    <a href="#pricing" class="px-4 py-2 bg-white/5 hover:bg-white/10 border border-white/10 text-xs font-bold rounded-lg transition-all hidden sm:block">
                        Купить Ключ
                    </a>
                </div>
            </div>
        </div>
    </nav>

    <!-- Hero Section -->
    <header class="relative pt-32 pb-24 min-h-screen flex items-center cyber-grid overflow-hidden">
        <!-- Radial Gradients for Background depth -->
        <div class="absolute top-1/4 left-1/4 -translate-x-1/2 -translate-y-1/2 w-[500px] h-[500px] rounded-full blur-[120px] pointer-events-none transition-colors duration-500" style="background: var(--glow);"></div>
        <div class="absolute bottom-1/4 right-1/4 translate-x-1/2 translate-y-1/2 w-[600px] h-[600px] bg-purple-600/5 rounded-full blur-[150px] pointer-events-none"></div>

        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 w-full relative z-10">
            <div class="grid grid-cols-1 lg:grid-cols-12 gap-12 items-center">
                
                <!-- Hero Text -->
                <div class="lg:col-span-7 space-y-6 text-center lg:text-left">
                    <div class="inline-flex items-center gap-2 bg-white/5 border border-white/10 rounded-full px-4 py-1.5 text-xs font-semibold text-gray-300 tracking-wider uppercase mx-auto lg:mx-0">
                        <i class="fa-solid fa-microchip text-indigo-400"></i> Продвинутый C++ Драйвер • Обход OBS & Discord
                    </div>
                    
                    <h1 class="text-4xl sm:text-5xl lg:text-6xl font-black tracking-tight leading-tight">
                        Доминируй в CS2.<br>
                        <span class="text-transparent bg-clip-text bg-gradient-to-r from-indigo-400 via-purple-400 to-pink-500 transition-colors duration-300" style="text-shadow: 0 0 40px var(--glow); color: var(--accent-hover);">
                            Внешний WebRadar & ESP
                        </span><br>
                        в одном клиенте.
                    </h1>

                    <p class="text-gray-400 text-base sm:text-lg max-w-xl mx-auto lg:mx-0 leading-relaxed">
                        Асинхронный драйвер считывает координаты игроков напрямую без инжектов в код. Выводите полноценную интерактивную карту-радар на телефон, планшет или второй монитор, или включите DirectX 11 Overlay для идеального ESP на основном экране.
                    </p>

                    <!-- Core highlights -->
                    <div class="grid grid-cols-3 gap-3 pt-2 max-w-lg mx-auto lg:mx-0">
                        <div class="bg-white/[0.02] border border-white/5 p-3 rounded-xl text-center">
                            <span class="text-xs text-gray-500 font-bold block">СКОРОСТЬ API</span>
                            <span class="text-sm font-black text-gray-200">0.12ms Realtime</span>
                        </div>
                        <div class="bg-white/[0.02] border border-white/5 p-3 rounded-xl text-center">
                            <span class="text-xs text-gray-500 font-bold block">FPS НАГРУЗКА</span>
                            <span class="text-sm font-black text-emerald-400">0% (Идеально)</span>
                        </div>
                        <div class="bg-white/[0.02] border border-white/5 p-3 rounded-xl text-center">
                            <span class="text-xs text-gray-500 font-bold block">ЗАЩИТА</span>
                            <span class="text-sm font-black text-indigo-400">Vanguard/VAC</span>
                        </div>
                    </div>

                    <!-- Action buttons -->
                    <div class="flex flex-col sm:flex-row items-center gap-4 justify-center lg:justify-start">
                        <a href="#pricing" class="w-full sm:w-auto text-center px-8 py-4 rounded-xl font-bold text-white transition-all shadow-neonGlow flex items-center justify-center gap-2 transition-all duration-300" style="background: var(--accent); hover:background: var(--accent-hover);">
                            <i class="fa-solid fa-bolt animate-bounce"></i> Выбрать тариф от $7.49
                        </a>
                        <a href="#demo" class="w-full sm:w-auto text-center px-8 py-4 rounded-xl bg-white/5 border border-white/10 hover:bg-white/10 transition-all text-gray-300 font-semibold flex items-center justify-center gap-2">
                            Тестировать Радар <i class="fa-solid fa-circle-play text-xs"></i>
                        </a>
                    </div>
                </div>

                <!-- Hero Graphic (Live preview of Radar on smartphone frame) -->
                <div class="lg:col-span-5 relative flex justify-center">
                    <div class="relative w-full max-w-[360px]">
                        <div class="absolute inset-0 rounded-[40px] blur-3xl opacity-20 animate-pulse transition-colors duration-500" style="background: var(--accent);"></div>
                        
                        <!-- Phone Simulation -->
                        <div class="relative mx-auto border-[10px] border-slate-900 rounded-[42px] bg-[#0c0e17] shadow-2xl w-[280px] h-[560px] overflow-hidden">
                            <div class="absolute top-2 left-1/2 -translate-x-1/2 w-28 h-4 bg-slate-900 rounded-b-xl z-40"></div>
                            
                            <!-- Screen -->
                            <div class="h-full w-full p-3 pt-8 flex flex-col justify-between bg-[#08090e]">
                                <div class="flex items-center justify-between border-b border-white/5 pb-2">
                                    <span class="text-[8px] font-mono tracking-widest text-indigo-400 uppercase">WEBLINK STREAM</span>
                                    <span class="text-[8px] text-emerald-400 font-mono animate-pulse flex items-center gap-1">
                                        <span class="w-1.5 h-1.5 bg-emerald-500 rounded-full"></span> LIVE
                                    </span>
                                </div>

                                <!-- Minimap mockup -->
                                <div class="relative flex-1 my-3 bg-black/50 rounded-xl border border-white/5 overflow-hidden flex items-center justify-center">
                                    <div class="absolute inset-0 bg-cover bg-center opacity-30 radar-image-bg" style="background-image: url('public/data/de_mirage/radar.png');"></div>
                                    <div class="absolute inset-0 cyber-grid opacity-20"></div>
                                    
                                    <!-- Scan Sweeper -->
                                    <div class="absolute inset-0 w-full h-full pointer-events-none overflow-hidden rounded-xl">
                                        <div class="absolute top-1/2 left-1/2 w-[150%] h-[150%] -translate-x-1/2 -translate-y-1/2 border-t border-indigo-500/20 scan-line-animation origin-center"></div>
                                    </div>
                                    
                                    <!-- Core Marker -->
                                    <div class="absolute w-2.5 h-2.5 rounded-full shadow-lg z-10" style="left: 48%; top: 48%; background: var(--accent);"></div>
                                    <div class="absolute w-10 h-10 border rounded-full pulse-ring" style="left: 41%; top: 41%; border-color: var(--accent);"></div>

                                    <!-- Enemy with Health representation -->
                                    <div class="absolute flex flex-col items-center" style="left: 28%; top: 25%;">
                                        <div class="w-4 h-4 rounded-full bg-red-500 border border-white flex items-center justify-center shadow-lg">
                                            <span class="text-[8px] font-black text-white">E1</span>
                                        </div>
                                        <span class="text-[6px] text-red-400 font-mono mt-0.5">HP: 100</span>
                                    </div>
                                </div>

                                <!-- Mini Win Predictor component -->
                                <div class="bg-black/40 p-2 rounded-xl border border-white/5 mb-1 text-[8px] font-mono">
                                    <div class="flex justify-between text-gray-400 mb-1">
                                        <span class="text-red-400 font-bold">T: 58%</span>
                                        <span>Шансы На Раунд</span>
                                        <span class="text-indigo-400 font-bold">CT: 42%</span>
                                    </div>
                                    <div class="w-full h-1 bg-indigo-950 rounded-full flex overflow-hidden">
                                        <div class="h-full bg-red-500" style="width: 58%"></div>
                                        <div class="h-full bg-indigo-500" style="width: 42%"></div>
                                    </div>
                                </div>
                            </div>
                        </div>

                        <!-- Floating Tech Tags -->
                        <div class="absolute -left-10 top-16 bg-darkCard/90 border border-white/10 px-3 py-2 rounded-xl shadow-xl z-20 backdrop-blur-md text-left">
                            <p class="text-[8px] text-gray-500 font-bold uppercase tracking-wider">C++ DRIVER</p>
                            <p class="text-xs font-bold text-gray-200">Zero Kernel Traces</p>
                        </div>
                        <div class="absolute -right-8 bottom-16 bg-darkCard/90 border border-white/10 px-3 py-2 rounded-xl shadow-xl z-20 backdrop-blur-md text-left">
                            <p class="text-[8px] text-gray-500 font-bold uppercase tracking-wider">WEBRADAR HUB</p>
                            <p class="text-xs font-bold text-gray-200">Syncs instantly</p>
                        </div>
                    </div>
                </div>

            </div>
        </div>
    </header>

    <!-- Features Grid -->
    <section id="features" class="py-20 relative bg-black/40 border-t border-white/5">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
            <div class="text-center max-w-3xl mx-auto mb-16">
                <span class="text-xs font-mono font-bold tracking-widest text-indigo-400 uppercase px-3 py-1.5 rounded-full bg-indigo-500/10">ПОЧЕМУ МЫ ЛУЧШИЕ</span>
                <h2 class="text-3xl sm:text-4xl font-black mt-3">Создано профессионалами для незаметной игры</h2>
                <p class="text-gray-400 mt-4 text-base">Используйте современные обходы чтения памяти. Наш софт работает асинхронно с движком CS2, не оставляя цифровых следов.</p>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-3 gap-8">
                <!-- Feature 1 -->
                <div class="bg-darkCard/50 border border-white/5 p-6 rounded-2xl hover:border-indigo-500/20 transition-all">
                    <div class="w-12 h-12 bg-indigo-500/10 rounded-xl flex items-center justify-center text-indigo-400 text-lg mb-4">
                        <i class="fa-solid fa-shield-halved"></i>
                    </div>
                    <h3 class="text-lg font-bold text-gray-100">Zero-Footprint Kernel Bypass</h3>
                    <p class="text-gray-400 text-sm mt-2 leading-relaxed">Драйвер работает на уровне ядра Windows, считывая хэндл игры без внедрения DLL. Это полностью исключает детекты от VAC и других античитов.</p>
                </div>
                <!-- Feature 2 -->
                <div class="bg-darkCard/50 border border-white/5 p-6 rounded-2xl hover:border-indigo-500/20 transition-all">
                    <div class="w-12 h-12 bg-indigo-500/10 rounded-xl flex items-center justify-center text-indigo-400 text-lg mb-4">
                        <i class="fa-solid fa-mobile-screen"></i>
                    </div>
                    <h3 class="text-lg font-bold text-gray-100">Мультиплатформенность</h3>
                    <p class="text-gray-400 text-sm mt-2 leading-relaxed">Стриминг радара по уникальному WebSocket-линку позволяет открыть тактическую карту на любом браузере смартфона, планшета или MacBook.</p>
                </div>
                <!-- Feature 3 -->
                <div class="bg-darkCard/50 border border-white/5 p-6 rounded-2xl hover:border-indigo-500/20 transition-all">
                    <div class="w-12 h-12 bg-indigo-500/10 rounded-xl flex items-center justify-center text-indigo-400 text-lg mb-4">
                        <i class="fa-solid fa-eye-slash"></i>
                    </div>
                    <h3 class="text-lg font-bold text-gray-100">Anti-OBS Обход Записи</h3>
                    <p class="text-gray-400 text-sm mt-2 leading-relaxed">При активации DX11 Overlay оверлей рендерится поверх экрана, но скрыт при записи через OBS, Discord, Shadowplay или при стриминге.</p>
                </div>
            </div>
        </div>
    </section>

    <!-- Interactive Live Radar Simulator Block -->
    <section id="demo" class="py-24 relative overflow-hidden bg-black/60 border-y border-white/5">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 relative z-10">
            <div class="text-center max-w-3xl mx-auto mb-12">
                <span class="text-xs font-mono font-bold tracking-widest text-indigo-400 uppercase px-3 py-1.5 rounded-full bg-indigo-500/10">ИНТЕРАКТИВНЫЙ СТРИМИНГ</span>
                <h2 class="text-3xl sm:text-5xl font-black tracking-tight mt-3">
                    Асинхронный Веб-Радар (MIRAGE)
                </h2>
                <p class="text-gray-400 mt-4 text-base">
                    Попробуйте живой веб-интерфейс радара прямо сейчас. <span class="text-white font-bold">Перетаскивайте значки игроков по карте</span>, чтобы изменять позиции, взрывать смоки или активировать таймер C4!
                </p>
            </div>

            <!-- Simulator Container -->
            <div class="grid grid-cols-1 lg:grid-cols-12 gap-8 items-stretch">
                
                <!-- Controls Panel Left Column -->
                <div class="lg:col-span-4 bg-[#0b0d14]/95 border border-white/5 p-6 rounded-3xl flex flex-col justify-between text-left">
                    <div class="space-y-6">
                        <div class="flex items-center justify-between border-b border-white/5 pb-4">
                            <span class="font-bold text-gray-200 flex items-center gap-2">
                                <i class="fa-solid fa-sliders text-indigo-400"></i> Панель управления
                            </span>
                            <span class="text-[9px] bg-emerald-500/10 text-emerald-400 px-2.5 py-1 rounded font-mono animate-pulse">SYSTEM OK</span>
                        </div>

                        <!-- Controls Actions toggles -->
                        <div class="space-y-3">
                            <span class="text-xs font-bold text-gray-400 uppercase tracking-widest">Действия симуляции:</span>
                            <div class="grid grid-cols-2 gap-2">
                                <button id="btnSpawnEnemy" class="px-3 py-2.5 bg-red-500/10 hover:bg-red-500/20 text-red-400 border border-red-500/20 rounded-xl text-xs font-semibold transition-all flex items-center justify-center gap-1.5">
                                    <i class="fa-solid fa-user-plus text-[10px]"></i> +1 Враг
                                </button>
                                <button id="btnThrowSmoke" class="px-3 py-2.5 bg-gray-500/10 hover:bg-gray-500/20 text-gray-300 border border-gray-500/20 rounded-xl text-xs font-semibold transition-all flex items-center justify-center gap-1.5">
                                    <i class="fa-solid fa-smog text-[10px]"></i> Бросить Смок
                                </button>
                                <button id="btnPlantBomb" class="px-3 py-2.5 bg-amber-500/10 hover:bg-amber-500/20 text-amber-500 border border-amber-500/20 rounded-xl text-xs font-semibold transition-all flex items-center justify-center gap-1.5">
                                    <i class="fa-solid fa-bomb text-[10px]"></i> Заложить C4
                                </button>
                                <button id="btnTriggerDefuse" class="px-3 py-2.5 bg-indigo-500/10 hover:bg-indigo-500/20 text-indigo-400 border border-indigo-500/20 rounded-xl text-xs font-semibold transition-all flex items-center justify-center gap-1.5" disabled>
                                    <i class="fa-solid fa-kit-medical text-[10px]"></i> Разминировать
                                </button>
                            </div>
                        </div>

                        <div class="space-y-4 pt-4 border-t border-white/5">
                            <!-- Toggle View Cones -->
                            <div class="flex items-center justify-between p-3 bg-white/[0.01] border border-white/5 rounded-xl hover:bg-white/[0.03]">
                                <div class="flex items-center gap-2">
                                    <i class="fa-solid fa-compass-drafting text-indigo-400"></i>
                                    <div>
                                        <p class="text-xs font-bold text-gray-200">Конусы обзора</p>
                                        <p class="text-[9px] text-gray-500">Направление взгляда целей</p>
                                    </div>
                                </div>
                                <label class="relative inline-flex items-center cursor-pointer">
                                    <input type="checkbox" id="toggleViewCones" class="sr-only peer" checked>
                                    <div class="w-9 h-5 bg-slate-800 rounded-full peer peer-checked:after:translate-x-full after:content-[''] after:absolute after:top-[2px] after:left-[2px] after:bg-gray-300 after:rounded-full after:h-4 after:w-4 after:transition-all peer-checked:bg-indigo-500"></div>
                                </label>
                            </div>

                            <!-- Toggle Names -->
                            <div class="flex items-center justify-between p-3 bg-white/[0.01] border border-white/5 rounded-xl hover:bg-white/[0.03]">
                                <div class="flex items-center gap-2">
                                    <i class="fa-solid fa-font text-violet-400"></i>
                                    <div>
                                        <p class="text-xs font-bold text-gray-200">Показывать Ники</p>
                                        <p class="text-[9px] text-gray-500">Отображать имена целей</p>
                                    </div>
                                </div>
                                <label class="relative inline-flex items-center cursor-pointer">
                                    <input type="checkbox" id="toggleNames" class="sr-only peer" checked>
                                    <div class="w-9 h-5 bg-slate-800 rounded-full peer peer-checked:after:translate-x-full after:content-[''] after:absolute after:top-[2px] after:left-[2px] after:bg-gray-300 after:rounded-full after:h-4 after:w-4 after:transition-all peer-checked:bg-indigo-500"></div>
                                </label>
                            </div>
                        </div>
                    </div>

                    <!-- Tech specs mimicking diagnostics -->
                    <div class="mt-6 bg-[#05060a] p-4 rounded-2xl border border-white/5 space-y-2 font-mono text-xs">
                        <div class="flex justify-between items-center">
                            <span class="text-gray-500">Разрешение игры:</span>
                            <span class="font-bold text-emerald-400">4:3 Stretched / 16:9 OK</span>
                        </div>
                        <div class="flex justify-between items-center">
                            <span class="text-gray-500">Активные цели:</span>
                            <span class="font-bold text-red-400" id="enemyCount">3 врага</span>
                        </div>
                    </div>
                </div>

                <!-- Right Radar viewport -->
                <div class="lg:col-span-8 bg-[#04050a] border border-white/5 rounded-3xl overflow-hidden flex flex-col h-[580px] relative shadow-2xl">
                    <!-- Top bar of radar window -->
                    <div class="bg-[#0b0d14] border-b border-white/5 px-6 py-4 flex items-center justify-between">
                        <div class="flex items-center gap-2">
                            <span class="flex h-2.5 w-2.5 rounded-full bg-emerald-500 animate-pulse"></span>
                            <span class="text-xs font-mono font-bold tracking-wider text-gray-300 uppercase">AnyQwer Live WebRadar (de_mirage)</span>
                        </div>
                        <div class="flex items-center gap-2 text-xs text-gray-400 bg-white/5 px-3 py-1 rounded-lg">
                            <i class="fa-solid fa-link text-indigo-400"></i>
                            <span class="font-mono text-[10px]">https://radar.anyqwer.net/mirage-live</span>
                        </div>
                    </div>

                    <!-- Win Predictor Bar -->
                    <div class="bg-[#08090e] border-b border-white/5 px-6 py-2.5 flex items-center gap-4">
                        <span class="text-[10px] font-bold text-gray-400 uppercase tracking-widest font-mono shrink-0">Шансы на Победу:</span>
                        <div class="w-full h-5 bg-indigo-950/40 rounded-md flex overflow-hidden relative border border-white/5 shadow-inner">
                            <div id="predictT" class="bg-red-600/80 h-full flex items-center justify-center font-bold text-white text-[10px] tracking-wider transition-all duration-700" style="width: 50%;">
                                T: 50%
                            </div>
                            <div id="predictCT" class="bg-indigo-600/80 h-full flex items-center justify-center font-bold text-white text-[10px] tracking-wider transition-all duration-700" style="width: 50%;">
                                CT: 50%
                            </div>
                        </div>
                    </div>

                    <!-- Inner Site mode simulation layout -->
                    <div class="flex-1 flex min-h-0 relative">
                        
                        <!-- Left Panel: CT Team Cards -->
                        <div class="w-44 bg-[#07090f]/95 border-r border-white/5 overflow-y-auto p-2 space-y-2 hidden md:block">
                            <div class="text-[9px] font-black text-indigo-400 uppercase tracking-widest pb-1 border-b border-white/5 flex items-center gap-1">
                                <span class="w-1.5 h-1.5 rounded-full bg-indigo-400 animate-pulse"></span> Counter-Terrorists
                            </div>
                            <!-- Local Player Card -->
                            <div class="bg-white/[0.01] border border-white/5 p-2 rounded-lg">
                                <div class="flex justify-between items-center text-[10px] font-bold text-gray-200">
                                    <span class="truncate">Вы (Local)</span>
                                    <span class="text-emerald-400 font-mono">$4,800</span>
                                </div>
                                <div class="flex items-center gap-1 mt-1">
                                    <div class="text-[8px] bg-indigo-500/10 text-indigo-400 px-1 py-0.2 rounded font-mono">M4A1-S</div>
                                    <div class="text-[8px] bg-slate-800 text-gray-400 px-1 py-0.2 rounded font-mono">USP-S</div>
                                </div>
                                <div class="w-full h-1 bg-indigo-950 rounded-full mt-1.5 overflow-hidden">
                                    <div class="h-full bg-indigo-400 animate-pulse" style="width: 100%"></div>
                                </div>
                            </div>
                        </div>

                        <!-- Center Radar Map Container with exact public/data/de_mirage/radar.png background -->
                        <div class="flex-1 relative bg-slate-950 overflow-hidden" id="radarMapContainer">
                            <!-- MAIN RADAR IMAGE FROM REQUEST WITH DETAILED COLOR GRADING FILTER -->
                            <div class="absolute inset-0 w-full h-full bg-cover bg-center opacity-70 pointer-events-none radar-image-bg" 
                                 style="background-image: url('public/data/de_mirage/radar.png'); background-color: #0b0d14;" 
                                 onerror="this.style.backgroundImage='none'; this.innerHTML='<div class=\'absolute inset-0 flex items-center justify-center text-xs font-mono text-gray-600\'>[Карта Mirage de_mirage/radar.png]</div>';">
                            </div>

                            <!-- Radar Grid Overlay -->
                            <div class="absolute inset-0 cyber-grid opacity-10 pointer-events-none"></div>

                            <!-- Animated Sweep line -->
                            <div class="absolute inset-0 w-full h-full pointer-events-none overflow-hidden">
                                <div class="absolute top-1/2 left-1/2 w-[160%] h-[160%] -translate-x-1/2 -translate-y-1/2 border-t border-indigo-500/10 scan-line-animation origin-center"></div>
                            </div>

                            <!-- DRAGGABLE Local Player CT Indicator -->
                            <div class="radar-dot draggable-dot absolute flex flex-col items-center justify-center transform -translate-x-1/2 -translate-y-1/2 z-30" 
                                 style="left: 60%; top: 55%;" id="localPlayerDot">
                                <div class="relative w-7 h-7 rounded-full bg-indigo-500 border-2 border-white flex items-center justify-center shadow-lg shadow-indigo-500/50">
                                    <i class="fa-solid fa-crosshairs text-[10px] text-white"></i>
                                    <div class="view-cone absolute -top-8 -left-8 w-20 h-20 border-t-2 border-l-2 border-indigo-400/30 rounded-tl-full rotate-[110deg] pointer-events-none"></div>
                                </div>
                                <span class="text-[8px] font-bold text-indigo-300 font-mono mt-1 bg-slate-950/95 px-1 py-0.2 rounded border border-indigo-500/20">ВЫ (CT)</span>
                            </div>

                            <!-- Enemy 1 on Mirage A Site -->
                            <div class="radar-dot enemy-dot draggable-dot absolute flex flex-col items-center justify-center transform -translate-x-1/2 -translate-y-1/2 z-20" 
                                 style="left: 35%; top: 35%;" data-hp="100" data-id="1">
                                <div class="relative w-6 h-6 rounded-full bg-red-500 border-2 border-white flex items-center justify-center shadow-lg shadow-red-500/50">
                                    <span class="text-[8px] font-black text-white">E1</span>
                                    <div class="view-cone absolute -top-6 -left-6 w-14 h-14 border-t-2 border-l-2 border-red-500/30 rounded-tl-full -rotate-45 pointer-events-none"></div>
                                </div>
                                <div class="player-info mt-1 flex flex-col items-center pointer-events-none">
                                    <span class="player-name text-[8px] font-bold text-red-400 font-mono bg-slate-950/90 px-1 py-0.2 rounded border border-red-500/10">s1mple</span>
                                </div>
                            </div>

                            <!-- Enemy 2 -->
                            <div class="radar-dot enemy-dot draggable-dot absolute flex flex-col items-center justify-center transform -translate-x-1/2 -translate-y-1/2 z-20" 
                                 style="left: 20%; top: 25%;" data-hp="72" data-id="2">
                                <div class="relative w-6 h-6 rounded-full bg-red-500 border-2 border-white flex items-center justify-center shadow-lg shadow-red-500/50">
                                    <span class="text-[8px] font-black text-white">E2</span>
                                    <div class="view-cone absolute -bottom-6 -right-6 w-14 h-14 border-b-2 border-r-2 border-red-500/30 rounded-br-full rotate-45 pointer-events-none"></div>
                                </div>
                                <div class="player-info mt-1 flex flex-col items-center pointer-events-none">
                                    <span class="player-name text-[8px] font-bold text-red-400 font-mono bg-slate-950/90 px-1 py-0.2 rounded border border-red-500/10">donk</span>
                                </div>
                            </div>

                            <!-- Enemy 3 -->
                            <div class="radar-dot enemy-dot draggable-dot absolute flex flex-col items-center justify-center transform -translate-x-1/2 -translate-y-1/2 z-20" 
                                 style="left: 42%; top: 65%;" data-hp="45" data-id="3">
                                <div class="relative w-6 h-6 rounded-full bg-red-500 border-2 border-white flex items-center justify-center shadow-lg shadow-red-500/50">
                                    <span class="text-[8px] font-black text-white">E3</span>
                                    <div class="view-cone absolute -bottom-6 -left-6 w-14 h-14 border-b-2 border-l-2 border-red-500/30 rounded-bl-full rotate-45 pointer-events-none"></div>
                                </div>
                                <div class="player-info mt-1 flex flex-col items-center pointer-events-none">
                                    <span class="player-name text-[8px] font-bold text-red-400 font-mono bg-slate-950/90 px-1 py-0.2 rounded border border-red-500/10">m0nesy</span>
                                </div>
                            </div>

                            <!-- Bomb Icon (Planted) -->
                            <div class="absolute z-30 pointer-events-none flex flex-col items-center justify-center transform -translate-x-1/2 -translate-y-1/2 opacity-0 scale-50 transition-all duration-500" style="left: 36%; top: 48%;" id="bombOnMap">
                                <div class="w-4 h-4 bg-red-500 rounded-full shadow-[0_0_12px_#ef4444] animate-ping absolute"></div>
                                <div class="w-3.5 h-3.5 bg-red-600 rounded-full shadow-[0_0_8px_#ef4444] relative z-10"></div>
                                <span class="text-[8px] font-black text-yellow-400 font-mono bg-slate-950/90 px-1 py-0.2 rounded mt-1">C4 BOMB</span>
                            </div>

                            <!-- Smoke Grenade Zone -->
                            <div class="absolute z-20 pointer-events-none flex items-center justify-center transform -translate-x-1/2 -translate-y-1/2 opacity-0 transition-all duration-500" style="left: 50%; top: 35%;" id="smokeOnMap">
                                <div class="relative w-28 h-28 rounded-full bg-gray-500/20 border border-dashed border-gray-400/80 flex items-center justify-center">
                                    <div class="absolute inset-0 bg-gray-500/10 rounded-full animate-pulse"></div>
                                    <span class="text-[8px] font-mono font-bold text-gray-300 bg-slate-950/90 px-1.5 py-0.5 rounded" id="smokeTimerVal">18.0s</span>
                                </div>
                            </div>

                            <!-- Simulated Bomb Timer Component -->
                            <div class="absolute top-4 left-1/2 -translate-x-1/2 z-40 hidden" id="bombTimerWidget">
                                <div class="flex items-center gap-2 px-3 py-1.5 rounded-full bg-slate-950/95 border border-red-500/40 shadow-lg text-[10px]">
                                    <i class="fa-solid fa-bomb text-red-500 animate-pulse"></i>
                                    <span class="font-black font-mono text-red-400 tabular-nums" id="bombTimerSeconds">40.0s</span>
                                </div>
                            </div>

                        </div>

                        <!-- Right Panel: T Team Cards -->
                        <div class="w-44 bg-[#07090f]/95 border-l border-white/5 overflow-y-auto p-2 space-y-2 text-left hidden md:block" id="enemiesListContainer">
                            <div class="text-[9px] font-black text-red-400 uppercase tracking-widest pb-1 border-b border-white/5 flex items-center gap-1">
                                <span class="w-1.5 h-1.5 rounded-full bg-red-400"></span> Terrorists (Враги)
                            </div>
                            
                            <!-- Cards will render here -->
                        </div>

                    </div>
                </div>

            </div>
        </div>
    </section>

    <!-- Interactive ESP WH Customizer Section -->
    <section id="wh-sandbox" class="py-24 relative bg-darkCard/40 border-b border-white/5">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
            <div class="text-center max-w-3xl mx-auto mb-16">
                <span class="text-xs font-mono font-bold tracking-widest text-indigo-400 uppercase px-3 py-1.5 rounded-full bg-indigo-500/10">PREMIUM НАСТРОЙКИ</span>
                <h2 class="text-3xl sm:text-5xl font-black tracking-tight mt-3">
                    Интерактивный симулятор ESP меню
                </h2>
                <p class="text-gray-400 mt-4 text-base">
                    Симуляция тонкого DX11 оверлея из нашего софта. Настраивайте скелеты, обводки, цвета линий прямо в этой панели — и результат мгновенно обновится на 3D модели справа!
                </p>
            </div>

            <!-- ESP Customizer Panel -->
            <div class="bg-black/60 rounded-3xl border border-white/5 p-6 md:p-8 shadow-2xl relative">
                <!-- Status header in simulation -->
                <div class="flex flex-wrap items-center justify-between gap-4 border-b border-white/5 pb-6 mb-6">
                    <div class="flex items-center gap-2">
                        <button class="px-4 py-2 text-xs font-bold uppercase tracking-wider text-white rounded-lg bg-white/5 flex items-center gap-1.5 border border-white/10 shadow-neonBorder">
                            <i class="fa-solid fa-eye text-indigo-400"></i> Visuals (Оверлей)
                        </button>
                        <button class="px-4 py-2 text-xs font-bold uppercase tracking-wider text-gray-500 hover:text-gray-300 rounded-lg transition-all flex items-center gap-1.5">
                            <i class="fa-solid fa-crosshairs"></i> Triggerbot
                        </button>
                        <button class="px-4 py-2 text-xs font-bold uppercase tracking-wider text-gray-500 hover:text-gray-300 rounded-lg transition-all flex items-center gap-1.5">
                            <i class="fa-solid fa-robot"></i> Автоматизация
                        </button>
                    </div>

                    <div class="flex items-center gap-3">
                        <span class="text-xs text-emerald-400 font-bold flex items-center gap-1">
                            <span class="w-1.5 h-1.5 bg-emerald-500 rounded-full animate-ping"></span> • СЕРВЕР CS2 СИНХРОНИЗИРОВАН
                        </span>
                        <div class="flex items-center gap-2 border-l border-white/5 pl-3">
                            <span class="text-xs text-gray-400 font-bold uppercase">Включить ESP</span>
                            <label class="relative inline-flex items-center cursor-pointer">
                                <input type="checkbox" id="espMasterToggle" class="sr-only peer" checked>
                                <div class="w-10 h-5 bg-slate-800 peer-focus:outline-none rounded-full peer peer-checked:after:translate-x-full peer-checked:after:border-white after:content-[''] after:absolute after:top-[2px] after:left-[2px] after:bg-gray-300 after:border-gray-300 after:border after:rounded-full after:h-4 after:w-4 after:transition-all peer-checked:bg-indigo-500"></div>
                            </label>
                        </div>
                    </div>
                </div>

                <div class="grid grid-cols-1 lg:grid-cols-12 gap-8 items-stretch">
                    
                    <!-- Left column: ESP Configuration options -->
                    <div class="lg:col-span-5 space-y-6">
                        <!-- Global Rendering Block -->
                        <div class="bg-[#0b0d14]/80 rounded-2xl border border-white/5 p-5 space-y-4 text-left">
                            <h4 class="text-xs font-bold uppercase text-gray-400 tracking-wider mb-2">Глобальный Рендеринг</h4>
                            
                            <!-- Team ESP Toggle -->
                            <div class="flex items-center justify-between">
                                <span class="text-xs text-gray-300 font-bold">ESP на Своих (Teammates)</span>
                                <label class="relative inline-flex items-center cursor-pointer">
                                    <input type="checkbox" id="espTeamToggle" class="sr-only peer">
                                    <div class="w-9 h-5 bg-slate-800 rounded-full peer peer-checked:after:translate-x-full after:content-[''] after:absolute after:top-[2px] after:left-[2px] after:bg-gray-300 after:rounded-full after:h-4 after:w-4 after:transition-all peer-checked:bg-indigo-500"></div>
                                </label>
                            </div>

                            <!-- Show Box Toggle -->
                            <div class="flex items-center justify-between">
                                <span class="text-xs text-gray-300 font-bold">Отображать Box (Рамку)</span>
                                <label class="relative inline-flex items-center cursor-pointer">
                                    <input type="checkbox" id="espBoxToggle" class="sr-only peer" checked>
                                    <div class="w-9 h-5 bg-slate-800 rounded-full peer peer-checked:after:translate-x-full after:content-[''] after:absolute after:top-[2px] after:left-[2px] after:bg-gray-300 after:rounded-full after:h-4 after:w-4 after:transition-all peer-checked:bg-indigo-500"></div>
                                </label>
                            </div>

                            <!-- Fill Box Toggle -->
                            <div class="flex items-center justify-between">
                                <span class="text-xs text-gray-300 font-bold">Заливка фона рамки</span>
                                <label class="relative inline-flex items-center cursor-pointer">
                                    <input type="checkbox" id="espFillToggle" class="sr-only peer">
                                    <div class="w-9 h-5 bg-slate-800 rounded-full peer peer-checked:after:translate-x-full after:content-[''] after:absolute after:top-[2px] after:left-[2px] after:bg-gray-300 after:rounded-full after:h-4 after:w-4 after:transition-all peer-checked:bg-indigo-500"></div>
                                </label>
                            </div>

                            <!-- Draw Skeleton Toggle -->
                            <div class="flex items-center justify-between">
                                <span class="text-xs text-gray-300 font-bold">Рисовать Скелет (Skeleton)</span>
                                <label class="relative inline-flex items-center cursor-pointer">
                                    <input type="checkbox" id="espSkeletonToggle" class="sr-only peer" checked>
                                    <div class="w-9 h-5 bg-slate-800 rounded-full peer peer-checked:after:translate-x-full after:content-[''] after:absolute after:top-[2px] after:left-[2px] after:bg-gray-300 after:rounded-full after:h-4 after:w-4 after:transition-all peer-checked:bg-indigo-500"></div>
                                </label>
                            </div>
                        </div>

                        <!-- Theme Colors Block -->
                        <div class="bg-[#0b0d14]/80 rounded-2xl border border-white/5 p-5 space-y-4 text-left">
                            <h4 class="text-xs font-bold uppercase text-gray-400 tracking-wider mb-2">Настройки Цвета</h4>
                            
                            <!-- Box Color Picker -->
                            <div class="flex items-center justify-between">
                                <span class="text-xs text-gray-300 font-bold">Цвет Рамки (Box)</span>
                                <div class="flex items-center gap-2">
                                    <input type="color" id="boxColorPicker" value="#ef4444" class="w-7 h-7 rounded bg-transparent border-none cursor-pointer">
                                    <span class="text-[10px] font-mono text-gray-400 uppercase" id="boxColorHex">#ef4444</span>
                                </div>
                            </div>

                            <!-- Skeleton Color Picker -->
                            <div class="flex items-center justify-between">
                                <span class="text-xs text-gray-300 font-bold">Цвет Скелета</span>
                                <div class="flex items-center gap-2">
                                    <input type="color" id="skeletonColorPicker" value="#ffffff" class="w-7 h-7 rounded bg-transparent border-none cursor-pointer">
                                    <span class="text-[10px] font-mono text-gray-400 uppercase" id="skeletonColorHex">#ffffff</span>
                                </div>
                            </div>

                            <!-- Label Color Picker -->
                            <div class="flex items-center justify-between">
                                <span class="text-xs text-gray-300 font-bold">Цвет Текста & Информации</span>
                                <div class="flex items-center gap-2">
                                    <input type="color" id="labelColorPicker" value="#d8b4fe" class="w-7 h-7 rounded bg-transparent border-none cursor-pointer">
                                    <span class="text-[10px] font-mono text-gray-400 uppercase" id="labelColorHex">#d8b4fe</span>
                                </div>
                            </div>
                        </div>
                    </div>

                    <!-- Right column: Simulated DX11 Viewport Rendering with ESP tags -->
                    <div class="lg:col-span-7 bg-[#05060b] rounded-2xl border border-white/5 p-6 flex flex-col justify-between relative overflow-hidden h-[480px]">
                        
                        <!-- Top status inside DX11 panel -->
                        <div class="flex justify-between items-center border-b border-white/5 pb-3">
                            <span class="text-[10px] font-mono tracking-wider text-gray-500 uppercase">DirectX 11 Engine Overlay Stream</span>
                            <div class="px-2 py-0.5 bg-indigo-500/10 border border-indigo-500/20 text-indigo-400 rounded text-[9px] font-bold uppercase tracking-widest animate-pulse">OVERLAY ACTIVE</div>
                        </div>

                        <!-- Main model simulator -->
                        <div class="flex-1 relative flex items-center justify-center py-4">
                            
                            <!-- Static Grid lines overlay mimicking engine view -->
                            <div class="absolute inset-0 cyber-grid opacity-10"></div>

                            <!-- 2D Skeleton Container Box (Fine adjustable) -->
                            <div id="espSimulatedTarget" class="absolute w-44 h-80 transition-all duration-300 flex items-center justify-center" style="border: 2px solid #ef4444;">
                                
                                <!-- Inside Filled background -->
                                <div id="espSimulatedFill" class="absolute inset-0 bg-red-500/5 transition-opacity opacity-0"></div>

                                <!-- Stickman Skeleton SVG mimicking real draw-skeleton calls -->
                                <svg id="espSimulatedSkeleton" class="absolute w-full h-full p-2 overflow-visible" viewBox="0 0 100 200">
                                    <!-- Head -->
                                    <circle cx="50" cy="40" r="10" stroke="#ffffff" stroke-width="2.5" fill="none" />
                                    <!-- Neck to spine -->
                                    <line x1="50" y1="50" x2="50" y2="110" stroke="#ffffff" stroke-width="2.5" />
                                    <!-- Left arm -->
                                    <line x1="50" y1="60" x2="25" y2="85" stroke="#ffffff" stroke-width="2.5" />
                                    <!-- Right arm -->
                                    <line x1="50" y1="60" x2="75" y2="85" stroke="#ffffff" stroke-width="2.5" />
                                    <!-- Left leg -->
                                    <line x1="50" y1="110" x2="30" y2="165" stroke="#ffffff" stroke-width="2.5" />
                                    <!-- Right leg -->
                                    <line x1="50" y1="110" x2="70" y2="165" stroke="#ffffff" stroke-width="2.5" />
                                </svg>

                                <!-- Active HUD labels (Positioned exactly around the bounding box) -->
                                <!-- Player name label (top center) -->
                                <div id="labelPlayerName" class="absolute -top-6 left-1/2 -translate-x-1/2 bg-slate-900/90 text-[10px] font-mono font-bold border border-white/5 px-2 py-0.5 rounded text-white flex items-center gap-1 transition-all duration-200">
                                    XANTARES
                                </div>

                                <!-- Utility Status elements (top-right side stacked) -->
                                <div class="absolute -right-20 top-4 flex flex-col gap-1.5 items-start">
                                    <div id="labelFlashed" class="bg-amber-500/10 border border-amber-500/20 text-amber-500 text-[8px] font-black uppercase tracking-widest px-1.5 py-0.5 rounded flex items-center gap-1 transition-all duration-200">
                                        ⚡ FLASHED
                                    </div>
                                    <div id="labelDefusing" class="bg-emerald-500/10 border border-emerald-500/20 text-emerald-400 text-[8px] font-black uppercase tracking-widest px-1.5 py-0.5 rounded flex items-center gap-1 transition-all duration-200">
                                        ⚙️ DEFUSING
                                    </div>
                                    <div id="labelScoped" class="bg-blue-500/10 border border-blue-500/20 text-blue-400 text-[8px] font-black uppercase tracking-widest px-1.5 py-0.5 rounded flex items-center gap-1 transition-all duration-200">
                                        🔭 SCOPED
                                    </div>
                                </div>

                                <!-- Left side Health bar indicator -->
                                <div id="labelHealthBar" class="absolute -left-6 top-0 bottom-0 w-1.5 bg-slate-900/80 rounded border border-white/5 p-[1px] flex flex-col justify-end transition-all duration-200">
                                    <div class="w-full bg-emerald-500 rounded-sm" style="height: 100%;"></div>
                                </div>

                                <!-- Right side Ammo bar indicator -->
                                <div id="labelAmmoBar" class="absolute -right-6 top-0 bottom-0 w-1.5 bg-slate-900/80 rounded border border-white/5 p-[1px] flex flex-col justify-end transition-all duration-200">
                                    <div class="w-full bg-blue-500 rounded-sm" style="height: 75%;"></div>
                                </div>

                                <!-- Weapon Label (bottom center) -->
                                <div id="labelWeapon" class="absolute -bottom-8 left-1/2 -translate-x-1/2 bg-slate-900/90 text-[10px] font-mono font-bold border border-white/5 px-2 py-0.5 rounded text-purple-300 uppercase transition-all duration-200">
                                    AK-47 [30/90]
                                </div>
                            </div>

                        </div>

                        <!-- Quick control description footer -->
                        <div class="border-t border-white/5 pt-3">
                            <span class="text-[10px] font-bold text-gray-500 uppercase tracking-widest">Переключение элементов оверлея на экране:</span>
                            <div class="flex flex-wrap gap-1.5 mt-2">
                                <button onclick="toggleElementWidget('labelPlayerName', this)" class="px-2 py-1 bg-white/5 hover:bg-white/10 text-[9px] font-mono font-bold rounded text-gray-300 border border-white/10 transition-all shadow-sm">Имя</button>
                                <button onclick="toggleElementWidget('labelFlashed', this)" class="px-2 py-1 bg-white/5 hover:bg-white/10 text-[9px] font-mono font-bold rounded text-gray-300 border border-white/10 transition-all shadow-sm">Flashed</button>
                                <button onclick="toggleElementWidget('labelDefusing', this)" class="px-2 py-1 bg-white/5 hover:bg-white/10 text-[9px] font-mono font-bold rounded text-gray-300 border border-white/10 transition-all shadow-sm">Defusing</button>
                                <button onclick="toggleElementWidget('labelScoped', this)" class="px-2 py-1 bg-white/5 hover:bg-white/10 text-[9px] font-mono font-bold rounded text-gray-300 border border-white/10 transition-all shadow-sm">Scoped</button>
                                <button onclick="toggleElementWidget('labelHealthBar', this)" class="px-2 py-1 bg-white/5 hover:bg-white/10 text-[9px] font-mono font-bold rounded text-gray-300 border border-white/10 transition-all shadow-sm">Здоровье</button>
                                <button onclick="toggleElementWidget('labelAmmoBar', this)" class="px-2 py-1 bg-white/5 hover:bg-white/10 text-[9px] font-mono font-bold rounded text-gray-300 border border-white/10 transition-all shadow-sm">Патроны</button>
                                <button onclick="toggleElementWidget('labelWeapon', this)" class="px-2 py-1 bg-white/5 hover:bg-white/10 text-[9px] font-mono font-bold rounded text-gray-300 border border-white/10 transition-all shadow-sm">Оружие</button>
                            </div>
                        </div>

                    </div>

                </div>
            </div>
        </div>
    </section>

    <!-- Live Kernel Engine Stream Log -->
    <section class="py-12 bg-black/80 border-b border-white/5">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
            <div class="bg-black/90 rounded-2xl border border-white/10 p-5 font-mono text-left relative overflow-hidden">
                <div class="flex items-center justify-between border-b border-white/10 pb-3 mb-4">
                    <div class="flex items-center gap-2">
                        <span class="w-3 h-3 rounded-full bg-red-500"></span>
                        <span class="w-3 h-3 rounded-full bg-yellow-500"></span>
                        <span class="w-3 h-3 rounded-full bg-green-500"></span>
                        <span class="text-xs text-gray-400 ml-2">ANYQWER DRIVER SERVICE CONSOLE</span>
                    </div>
                    <span class="text-xs text-indigo-400">STATUS: KERNEL_ACTIVE</span>
                </div>
                <div id="driverLogOutput" class="space-y-1.5 text-[11px] text-gray-400 h-32 overflow-y-auto">
                    <!-- Logs dynamically populate here -->
                </div>
            </div>
        </div>
    </section>

    <!-- Pricing / Rates Grid Section -->
    <section id="pricing" class="py-24 relative overflow-hidden bg-gradient-to-b from-darkBg to-darkCard border-t border-white/5">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 text-center relative z-10">
            <div class="max-w-3xl mx-auto mb-16">
                <span class="text-xs font-mono font-bold tracking-widest text-indigo-400 uppercase px-3 py-1.5 rounded-full bg-indigo-500/10">ВЫБЕРИ СВОЙ УРОВЕНЬ ДОМИНИРОВАНИЯ</span>
                <h2 class="text-3xl sm:text-5xl font-black mt-3">Два сбалансированных тарифа</h2>
                <p class="text-gray-400 mt-4 text-base">Используйте базовый тактический веб-радар на смартфонах или откройте полный Premium-доступ с асинхронным DX11 Overlay и защищенным Triggerbot.</p>
            </div>

            <!-- Pricing cards grid -->
            <div class="grid grid-cols-1 md:grid-cols-2 gap-8 max-w-4xl mx-auto items-stretch">
                
                <!-- Plan 1: WebRadar (Standard) -->
                <div class="bg-darkCard/80 border border-white/5 p-8 rounded-3xl flex flex-col justify-between text-left relative overflow-hidden hover:border-indigo-500/30 transition-all duration-300">
                    <div class="space-y-6">
                        <div>
                            <span class="text-[10px] font-mono text-gray-500 uppercase tracking-wider font-bold">Standard Лицензия</span>
                            <h3 class="text-2xl font-bold text-gray-100 mt-1">AnyQwer WebRadar</h3>
                        </div>

                        <div class="flex items-baseline gap-2">
                            <span class="text-4xl font-extrabold text-white" id="priceRadarVal">$7.49</span>
                            <span class="text-sm text-gray-500 line-through">$12.99</span>
                            <span class="text-xs text-indigo-400 font-bold bg-indigo-500/10 px-2.5 py-0.5 rounded">30 дней</span>
                        </div>

                        <p class="text-gray-400 text-xs leading-relaxed">
                            Идеальный тактический пакет для игры на втором устройстве (смартфон, планшет или ноутбук). Нулевое влияние на производительность.
                        </p>

                        <!-- Features checklist -->
                        <ul class="space-y-3.5 text-xs text-gray-300 border-t border-white/5 pt-6">
                            <li class="flex items-center gap-2.5">
                                <i class="fa-solid fa-circle-check text-emerald-400"></i> Асинхронный C++ Драйвер (0.12ms)
                            </li>
                            <li class="flex items-center gap-2.5">
                                <i class="fa-solid fa-circle-check text-emerald-400"></i> Стриминг по шифрованной ссылке
                            </li>
                            <li class="flex items-center gap-2.5">
                                <i class="fa-solid fa-circle-check text-emerald-400"></i> Интерактивный Радар на любом браузере
                            </li>
                            <li class="flex items-center gap-2.5">
                                <i class="fa-solid fa-circle-check text-emerald-400"></i> Отображение ХП, ников, оружия врага
                            </li>
                            <li class="flex items-center gap-2.5">
                                <i class="fa-solid fa-circle-check text-emerald-400"></i> Анализатор шансов победы (Win Predictor)
                            </li>
                            <li class="flex items-center gap-2.5">
                                <i class="fa-solid fa-circle-check text-emerald-400"></i> Таймер бомбы и детекция спектраторов
                            </li>
                        </ul>
                    </div>

                    <div class="pt-8">
                        <button onclick="openPaymentModal(7.49, 'WebRadar')" class="w-full py-4 rounded-xl bg-white/5 hover:bg-white/10 text-white border border-white/10 font-bold text-xs uppercase tracking-widest transition-all">
                            Выбрать WebRadar
                        </button>
                    </div>
                </div>

                <!-- Plan 2: Full Premium Overlay -->
                <div class="bg-[#0b0d14] border-2 border-indigo-500/60 p-8 rounded-3xl flex flex-col justify-between text-left relative overflow-hidden shadow-neonGlow">
                    <!-- Ribbon -->
                    <div class="absolute top-0 right-6 transform -translate-y-1/2">
                        <span class="bg-gradient-to-r from-indigo-500 to-violet-600 text-white font-mono font-bold text-[9px] tracking-widest uppercase px-3 py-1 rounded-full shadow-lg animate-pulse">
                            МАКСИМАЛЬНЫЙ КОНТРОЛЬ
                        </span>
                    </div>

                    <div class="space-y-6">
                        <div>
                            <span class="text-[10px] font-mono text-indigo-400 uppercase tracking-wider font-bold">Premium Лицензия</span>
                            <h3 class="text-2xl font-bold text-gray-100 mt-1">AnyQwer Full Premium</h3>
                        </div>

                        <div class="flex items-baseline gap-2">
                            <span class="text-4xl font-extrabold text-white" id="pricePremiumVal">$12.99</span>
                            <span class="text-sm text-gray-500 line-through">$19.99</span>
                            <span class="text-xs text-emerald-400 font-bold bg-emerald-500/10 px-2.5 py-0.5 rounded">30 дней</span>
                        </div>

                        <p class="text-gray-400 text-xs leading-relaxed">
                            Максимальная тактическая сборка. Включает DirectX 11 Overlay поверх игры, тонкую калибровку ESP скелетов, триггер и автоматизацию.
                        </p>

                        <!-- Features checklist -->
                        <ul class="space-y-3.5 text-xs text-gray-200 border-t border-white/5 pt-6">
                            <li class="flex items-center gap-2.5 text-emerald-400 font-semibold">
                                <i class="fa-solid fa-star"></i> Всё из тарифа WebRadar
                            </li>
                            <li class="flex items-center gap-2.5">
                                <i class="fa-solid fa-circle-check text-indigo-400"></i> Аппаратный DirectX 11 Overlay в CS2
                            </li>
                            <li class="flex items-center gap-2.5">
                                <i class="fa-solid fa-circle-check text-indigo-400"></i> Тонкое WH (ESP) скелетов и боксов
                            </li>
                            <li class="flex items-center gap-2.5">
                                <i class="fa-solid fa-circle-check text-indigo-400"></i> Stealth Triggerbot с настраиваемой задержкой
                            </li>
                            <li class="flex items-center gap-2.5">
                                <i class="fa-solid fa-circle-check text-indigo-400"></i> Auto-Pistol и Smart Zoom Auto-Swap
                            </li>
                            <li class="flex items-center gap-2.5">
                                <i class="fa-solid fa-circle-check text-indigo-400"></i> Приятные хитмаркеры и киллсаунды
                            </li>
                            <li class="flex items-center gap-2.5">
                                <i class="fa-solid fa-circle-check text-indigo-400"></i> ESP оружия на земле и 3D траекторий гранат
                            </li>
                        </ul>
                    </div>

                    <div class="pt-8">
                        <button onclick="openPaymentModal(12.99, 'Full Premium')" class="w-full py-4 rounded-xl font-bold text-xs uppercase tracking-widest text-white transition-all shadow-lg" style="background: var(--accent); hover:background: var(--accent-hover);">
                            Активировать Premium
                        </button>
                    </div>
                </div>

            </div>

            <!-- Promo coupon block -->
            <div class="max-w-4xl mx-auto mt-12 bg-darkCard/60 border border-white/5 p-6 rounded-2xl flex flex-col md:flex-row items-center justify-between gap-6 text-left">
                <div>
                    <h4 class="text-sm font-bold text-gray-200">✨ Есть купон на скидку?</h4>
                    <p class="text-xs text-gray-400">Введите ваш промокод для мгновенной скидки 20% на любую лицензию.</p>
                </div>
                <div class="flex items-center gap-2 w-full md:w-auto">
                    <input type="text" id="couponCodeInput" class="bg-black border border-white/10 rounded-lg px-4 py-2 font-mono text-xs text-indigo-300 focus:outline-none focus:border-indigo-500 w-full md:w-48 text-center" placeholder="ANYQWER20">
                    <button onclick="applyCoupon()" class="px-5 py-2 bg-indigo-500 hover:bg-indigo-600 text-white rounded-lg text-xs font-bold transition-all shrink-0">
                        Применить
                    </button>
                </div>
            </div>
        </div>
    </section>

    <!-- Security status badges -->
    <section id="status" class="py-20 relative bg-[#04050a] border-y border-white/5 text-center">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
            <h2 class="text-2xl sm:text-3xl font-black tracking-tight">Текущий статус античит-защиты</h2>
            <p class="text-gray-400 text-sm mt-2 max-w-xl mx-auto">Наше ядро обновляется автоматически при патчах Valve. Мы ведем мониторинг систем безопасности 24/7.</p>
            
            <div class="grid grid-cols-2 md:grid-cols-4 gap-4 max-w-4xl mx-auto mt-12">
                <div class="bg-[#0b0d14]/40 border border-emerald-500/20 p-5 rounded-2xl shadow-sm">
                    <span class="text-[10px] text-gray-500 font-mono font-bold uppercase tracking-widest">Valve Anti-Cheat</span>
                    <p class="text-emerald-400 font-bold text-sm mt-1">🟢 UNDETECTED</p>
                </div>
                <div class="bg-[#0b0d14]/40 border border-emerald-500/20 p-5 rounded-2xl shadow-sm">
                    <span class="text-[10px] text-gray-500 font-mono font-bold uppercase tracking-widest">VAC-Net / Premier</span>
                    <p class="text-emerald-400 font-bold text-sm mt-1">🟢 UNDETECTED</p>
                </div>
                <div class="bg-[#0b0d14]/40 border border-red-500/20 p-5 rounded-2xl shadow-sm">
                    <span class="text-[10px] text-gray-500 font-mono font-bold uppercase tracking-widest">Faceit AC</span>
                    <p class="text-red-500 font-bold text-sm mt-1">🔴 DETECTED</p>
                </div>
                <div class="bg-[#0b0d14]/40 border border-emerald-500/20 p-5 rounded-2xl shadow-sm">
                    <span class="text-[10px] text-gray-500 font-mono font-bold uppercase tracking-widest">OBS capturing</span>
                    <p class="text-emerald-400 font-bold text-sm mt-1">🟢 BYPASSED</p>
                </div>
            </div>
        </div>
    </section>

    <!-- Footer Section -->
    <footer class="bg-[#04050a] border-t border-white/5 py-12 relative z-10 text-left">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
            <div class="flex flex-col md:flex-row items-center justify-between gap-6 border-b border-white/5 pb-8">
                <!-- Logo -->
                <div class="flex items-center gap-3">
                    <div class="w-8 h-8 rounded-xl flex items-center justify-center shadow-neonGlow transition-colors duration-300" style="background: var(--accent);">
                        <i class="fa-solid fa-satellite-dish text-white text-sm"></i>
                    </div>
                    <div>
                        <span class="font-extrabold text-lg tracking-wider text-transparent bg-clip-text bg-gradient-to-r from-gray-100 to-gray-300">ANYQWER</span>
                        <span class="text-[9px] block text-gray-500 font-mono tracking-widest uppercase">WebRadar 2026</span>
                    </div>
                </div>

                <div class="flex flex-wrap gap-6 text-sm text-gray-400">
                    <a href="#features" class="hover:text-indigo-400 transition-colors">Особенности</a>
                    <a href="#demo" class="hover:text-indigo-400 transition-colors">Радар</a>
                    <a href="#wh-sandbox" class="hover:text-indigo-400 transition-colors">ESP Меню</a>
                    <a href="#pricing" class="hover:text-indigo-400 transition-colors">Лицензии</a>
                </div>
            </div>

            <div class="mt-8 grid grid-cols-1 md:grid-cols-12 gap-6 items-center">
                <div class="md:col-span-8 text-[11px] text-gray-500 space-y-2 leading-relaxed">
                    <p>© 2026 AnyQwer. Все права защищены. Мы не связаны с Valve Corporation, Steam или Counter-Strike.</p>
                    <p>Дисклеймер: Любая модификация игрового процесса сторонним ПО несет риски блокировки. Мы делаем всё для поддержки стабильных внешних драйверов, но ответственность за игровые аккаунты лежит исключительно на вас.</p>
                </div>
                <div class="md:col-span-4 flex justify-center md:justify-end gap-4 text-gray-400 text-lg">
                    <a href="#" class="hover:text-indigo-400 transition-colors"><i class="fa-brands fa-telegram"></i></a>
                    <a href="#" class="hover:text-indigo-400 transition-colors"><i class="fa-brands fa-discord"></i></a>
                    <a href="#" class="hover:text-indigo-400 transition-colors"><i class="fa-brands fa-youtube"></i></a>
                </div>
            </div>
        </div>
    </footer>

    <!-- Interactive Payment Modal -->
    <div id="paymentModal" class="fixed inset-0 z-50 flex items-center justify-center p-4 bg-black/80 backdrop-blur-md opacity-0 pointer-events-none transition-all duration-300">
        <div class="w-full max-w-md bg-darkCard border border-white/10 rounded-3xl p-6 shadow-2xl relative">
            <button onclick="closePaymentModal()" class="absolute top-4 right-4 text-gray-500 hover:text-white text-lg"><i class="fa-solid fa-xmark"></i></button>
            <h3 class="text-xl font-bold text-white flex items-center gap-2">
                <i class="fa-solid fa-wallet text-indigo-400"></i> Оформление Заказа
            </h3>
            <p class="text-xs text-gray-400 mt-1">Вы приобретаете подписку на тариф: <span id="paymentPlanName" class="text-white font-bold">Standard</span></p>

            <div class="mt-6 space-y-4">
                <!-- Summary of costs -->
                <div class="flex justify-between items-center text-sm border-b border-white/5 pb-3">
                    <span class="text-gray-400">Срок лицензии:</span>
                    <span class="text-white font-mono font-bold">30 дней</span>
                </div>
                <div class="flex justify-between items-center text-sm border-b border-white/5 pb-3">
                    <span class="text-gray-400">Итого к оплате:</span>
                    <span class="text-emerald-400 font-black font-mono text-lg" id="paymentFinalPrice">$7.49</span>
                </div>

                <!-- Payment Methods Grid -->
                <p class="text-xs font-bold text-gray-400 uppercase tracking-wider">Выберите способ оплаты:</p>
                <div class="grid grid-cols-2 gap-2">
                    <button onclick="confirmSimulatedPayment('Банковская Карта (Мир/Visa/MC)')" class="p-3 bg-white/5 hover:bg-white/10 border border-white/5 hover:border-indigo-500/50 rounded-xl text-left text-xs font-semibold flex items-center gap-2 transition-all">
                        <i class="fa-solid fa-credit-card text-blue-400"></i> Карта РФ/СНГ
                    </button>
                    <button onclick="confirmSimulatedPayment('Crypto USDT / BTC')" class="p-3 bg-white/5 hover:bg-white/10 border border-white/5 hover:border-indigo-500/50 rounded-xl text-left text-xs font-semibold flex items-center gap-2 transition-all">
                        <i class="fa-brands fa-bitcoin text-yellow-500"></i> Криптовалюта
                    </button>
                    <button onclick="confirmSimulatedPayment('CS2 Skins (Steam Pay)')" class="p-3 bg-white/5 hover:bg-white/10 border border-white/5 hover:border-indigo-500/50 rounded-xl text-left text-xs font-semibold flex items-center gap-2 transition-all col-span-2 justify-center">
                        <i class="fa-solid fa-gun text-emerald-400"></i> Оплата Скинами CS2 (+20%)
                    </button>
                </div>
                <p class="text-[10px] text-gray-500 text-center">Все транзакции шифруются и проходят через защищенные гейты.</p>
            </div>
        </div>
    </div>

    <!-- Custom Toast Notification Box -->
    <div id="statusMessageBox" class="fixed bottom-6 right-6 z-50 transform translate-y-20 opacity-0 pointer-events-none transition-all duration-300 max-w-sm w-full bg-[#0c0e15]/95 border border-emerald-500/30 rounded-2xl p-4 shadow-2xl backdrop-blur-md">
        <div class="flex gap-3 items-start text-left">
            <div class="w-8 h-8 rounded-full bg-emerald-500/10 flex items-center justify-center text-emerald-400 shrink-0">
                <i class="fa-solid fa-circle-check" id="notifIcon"></i>
            </div>
            <div class="flex-1">
                <h4 class="text-sm font-bold text-gray-100" id="msgTitle">Успешно!</h4>
                <p class="text-xs text-gray-400 mt-1" id="msgBody">Действие выполнено.</p>
            </div>
            <button onclick="closeMsgBox()" class="text-gray-500 hover:text-gray-300 transition-colors text-sm"><i class="fa-solid fa-xmark"></i></button>
        </div>
    </div>

    <script>
        // --- Core States & Pricing Variables ---
        let discountApplied = false;
        let originalPrices = { radar: 7.49, premium: 12.99 };
        let activePlanPrice = 7.49;

        // Theme Changer Hub Logic
        function switchTheme(theme) {
            document.documentElement.setAttribute('data-theme', theme);
            showNotification("Палитра обновлена", `Стиль сайта переключен на ${theme.toUpperCase()}.`);
        }

        // Custom Notification box logic
        function showNotification(title, message, isError = false) {
            const box = document.getElementById('statusMessageBox');
            const msgTitle = document.getElementById('msgTitle');
            const msgBody = document.getElementById('msgBody');
            const notifIcon = document.getElementById('notifIcon');
            
            msgTitle.innerText = title;
            msgBody.innerText = message;
            
            if (isError) {
                box.classList.remove('border-emerald-500/30');
                box.classList.add('border-red-500/30');
                notifIcon.className = "fa-solid fa-triangle-exclamation text-red-400";
            } else {
                box.classList.remove('border-red-500/30');
                box.classList.add('border-emerald-500/30');
                notifIcon.className = "fa-solid fa-circle-check text-emerald-400";
            }

            box.classList.remove('translate-y-20', 'opacity-0', 'pointer-events-none');
            
            setTimeout(() => {
                closeMsgBox();
            }, 4500);
        }

        function closeMsgBox() {
            const box = document.getElementById('statusMessageBox');
            box.classList.add('translate-y-20', 'opacity-0', 'pointer-events-none');
        }

        // --- Promo Coupon Logic ---
        function applyCoupon() {
            const input = document.getElementById('couponCodeInput').value.trim();
            if (input === 'ANYQWER20' || input.toUpperCase() === 'ANYQWER20') {
                if (discountApplied) {
                    showNotification("Купон уже применен", "Скидка уже рассчитана.", true);
                    return;
                }
                discountApplied = true;
                const discountCoeff = 0.8; // 20% OFF
                originalPrices.radar = (originalPrices.radar * discountCoeff).toFixed(2);
                originalPrices.premium = (originalPrices.premium * discountCoeff).toFixed(2);
                
                document.getElementById('priceRadarVal').innerText = `$${originalPrices.radar}`;
                document.getElementById('pricePremiumVal').innerText = `$${originalPrices.premium}`;
                
                showNotification("Промокод применен!", "Ваша скидка 20% успешно активирована на все тарифы.");
            } else {
                showNotification("Ошибка", "Неверный промокод. Попробуйте ANYQWER20", true);
            }
        }

        // --- Simulated Order Modals ---
        function openPaymentModal(price, planName) {
            activePlanPrice = price;
            document.getElementById('paymentPlanName').innerText = planName;
            document.getElementById('paymentFinalPrice').innerText = `$${price}`;
            
            const modal = document.getElementById('paymentModal');
            modal.classList.remove('opacity-0', 'pointer-events-none');
        }

        function closePaymentModal() {
            const modal = document.getElementById('paymentModal');
            modal.classList.add('opacity-0', 'pointer-events-none');
        }

        function confirmSimulatedPayment(method) {
            closePaymentModal();
            showNotification(
                "Заказ Сформирован", 
                `Оплата на сумму $${activePlanPrice} через ${method} успешно имитирована. Ключ отправлен на email.`
            );
        }

        // --- DX11 ESP Customizer Sandbox Logic ---
        const espMasterToggle = document.getElementById('espMasterToggle');
        const espTeamToggle = document.getElementById('espTeamToggle');
        const espBoxToggle = document.getElementById('espBoxToggle');
        const espFillToggle = document.getElementById('espFillToggle');
        const espSkeletonToggle = document.getElementById('espSkeletonToggle');
        
        const boxColorPicker = document.getElementById('boxColorPicker');
        const skeletonColorPicker = document.getElementById('skeletonColorPicker');
        const labelColorPicker = document.getElementById('labelColorPicker');
        
        const espSimulatedTarget = document.getElementById('espSimulatedTarget');
        const espSimulatedFill = document.getElementById('espSimulatedFill');
        const espSimulatedSkeleton = document.getElementById('espSimulatedSkeleton');

        espMasterToggle.addEventListener('change', () => {
            if (espMasterToggle.checked) {
                espSimulatedTarget.style.opacity = "1";
                showNotification("ESP Включено", "Дисплей ESP рендерит оверлей.");
            } else {
                espSimulatedTarget.style.opacity = "0";
                showNotification("ESP Выключено", "Все оверлеи скрыты.");
            }
        });

        espBoxToggle.addEventListener('change', () => {
            espSimulatedTarget.style.borderStyle = espBoxToggle.checked ? "solid" : "none";
        });

        espFillToggle.addEventListener('change', () => {
            espSimulatedFill.style.opacity = espFillToggle.checked ? "1" : "0";
        });

        espSkeletonToggle.addEventListener('change', () => {
            espSimulatedSkeleton.style.display = espSkeletonToggle.checked ? "block" : "none";
        });

        boxColorPicker.addEventListener('input', () => {
            const color = boxColorPicker.value;
            document.getElementById('boxColorHex').innerText = color;
            espSimulatedTarget.style.borderColor = color;
            espSimulatedFill.style.backgroundColor = color;
        });

        skeletonColorPicker.addEventListener('input', () => {
            const color = skeletonColorPicker.value;
            document.getElementById('skeletonColorHex').innerText = color;
            const lines = espSimulatedSkeleton.querySelectorAll('line, circle');
            lines.forEach(line => line.setAttribute('stroke', color));
        });

        labelColorPicker.addEventListener('input', () => {
            const color = labelColorPicker.value;
            document.getElementById('labelColorHex').innerText = color;
            document.getElementById('labelWeapon').style.color = color;
        });

        function toggleElementWidget(id, btn) {
            const el = document.getElementById(id);
            if (el.style.opacity === "0" || el.style.display === "none") {
                el.style.opacity = "1";
                el.style.display = "flex";
                btn.classList.add('bg-indigo-500/20', 'border-indigo-500/50');
            } else {
                el.style.opacity = "0";
                el.style.display = "none";
                btn.classList.remove('bg-indigo-500/20', 'border-indigo-500/50');
            }
        }

        // --- STREAMING_CHUNK: Injecting Draggable Map Dots & Math Predictor logic... ---
        // --- Advanced Interactive Live Radar with Map & Drag logic ---
        const radarMapContainer = document.getElementById('radarMapContainer');
        const toggleViewCones = document.getElementById('toggleViewCones');
        const toggleNames = document.getElementById('toggleNames');
        const btnSpawnEnemy = document.getElementById('btnSpawnEnemy');
        const btnThrowSmoke = document.getElementById('btnThrowSmoke');
        const btnPlantBomb = document.getElementById('btnPlantBomb');
        const btnTriggerDefuse = document.getElementById('btnTriggerDefuse');
        
        const enemyCountSpan = document.getElementById('enemyCount');
        const predictT = document.getElementById('predictT');
        const predictCT = document.getElementById('predictCT');
        
        const bombOnMap = document.getElementById('bombOnMap');
        const bombTimerWidget = document.getElementById('bombTimerWidget');
        const bombTimerSeconds = document.getElementById('bombTimerSeconds');
        
        const smokeOnMap = document.getElementById('smokeOnMap');
        const smokeTimerVal = document.getElementById('smokeTimerVal');
        const enemiesListContainer = document.getElementById('enemiesListContainer');

        let simulatedEnemies = [
            { id: 1, name: 's1mple', hp: 100, weapon: 'AK-47', val: 5400, left: 35, top: 35 },
            { id: 2, name: 'donk', hp: 72, weapon: 'Mac-10', val: 1900, left: 20, top: 25 },
            { id: 3, name: 'm0nesy', hp: 45, weapon: 'AWP', val: 6800, left: 42, top: 65 }
        ];

        let isBombPlanted = false;
        let isDefusing = false;
        let bombSecondsRemaining = 40.0;
        let defuseSecondsRemaining = 5.0;
        let bombTimerInterval = null;

        // Render Enemy list cards
        function renderEnemiesList() {
            enemiesListContainer.innerHTML = `
                <div class="text-[9px] font-black text-red-400 uppercase tracking-widest pb-1 border-b border-white/5 flex items-center gap-1">
                    <span class="w-1.5 h-1.5 rounded-full bg-red-400"></span> Terrorists (Враги)
                </div>
            `;
            simulatedEnemies.forEach(e => {
                const card = document.createElement('div');
                card.className = "bg-white/[0.01] border border-white/5 p-2 rounded-lg text-xs space-y-1";
                card.innerHTML = `
                    <div class="flex justify-between items-center text-[10px] font-bold text-gray-200">
                        <span class="truncate">${e.name}</span>
                        <span class="text-emerald-400 font-mono">$${e.val}</span>
                    </div>
                    <div class="flex items-center justify-between">
                        <span class="text-[8px] bg-red-500/10 text-red-400 px-1 py-0.2 rounded font-mono">${e.weapon}</span>
                        <span class="text-[8px] text-gray-400">HP: ${e.hp}</span>
                    </div>
                    <div class="w-full h-1 bg-red-950 rounded-full overflow-hidden">
                        <div class="h-full bg-red-500" style="width: ${e.hp}%"></div>
                    </div>
                `;
                enemiesListContainer.appendChild(card);
            });
        }

        // Win Predictor Math based on positions and entities
        function recalculateWinChances() {
            let tScore = 0;
            let ctScore = 200; // base CT weight

            simulatedEnemies.forEach(e => {
                // Closer to sites increases T score slightly
                let distToSiteA = Math.hypot(e.left - 35, e.top - 35);
                let positionModifier = Math.max(0, (100 - distToSiteA) * 0.8);
                
                let pScore = e.hp * 1.5;
                pScore += (e.weapon === 'AWP' ? 180 : 100);
                pScore += positionModifier;
                tScore += pScore;
            });

            if (isBombPlanted) {
                tScore += 450;
                if (isDefusing) ctScore += 300;
            }

            const total = tScore + ctScore;
            const tPercent = Math.min(95, Math.max(5, Math.round((tScore / total) * 100)));
            const ctPercent = 100 - tPercent;

            predictT.style.width = `${tPercent}%`;
            predictT.innerText = `T: ${tPercent}%`;
            predictCT.style.width = `${ctPercent}%`;
            predictCT.innerText = `CT: ${ctPercent}%`;
        }

        function updateRadarUI() {
            const cones = document.querySelectorAll('#radarMapContainer .view-cone');
            cones.forEach(cone => cone.style.display = toggleViewCones.checked ? 'block' : 'none');

            const names = document.querySelectorAll('#radarMapContainer .player-name');
            names.forEach(name => name.style.display = toggleNames.checked ? 'block' : 'none');

            enemyCountSpan.innerText = `${simulatedEnemies.length} врагов в игре`;
            recalculateWinChances();
            renderEnemiesList();
        }

        toggleViewCones.addEventListener('change', updateRadarUI);
        toggleNames.addEventListener('change', updateRadarUI);

        // --- Drag and Drop Logic implementation for Dots ---
        function makeDotDraggable(element) {
            let pos1 = 0, pos2 = 0, pos3 = 0, pos4 = 0;
            
            // Mouse Events
            element.onmousedown = dragMouseDown;
            // Touch Events
            element.addEventListener('touchstart', dragTouchStart, { passive: false });

            function dragMouseDown(e) {
                e = e || window.event;
                e.preventDefault();
                pos3 = e.clientX;
                pos4 = e.clientY;
                document.onmouseup = closeDragElement;
                document.onmousemove = elementDrag;
            }

            function dragTouchStart(e) {
                e = e || window.event;
                const touch = e.touches[0];
                pos3 = touch.clientX;
                pos4 = touch.clientY;
                document.addEventListener('touchend', closeDragTouch, { passive: false });
                document.addEventListener('touchmove', elementTouchDrag, { passive: false });
            }

            function elementDrag(e) {
                e = e || window.event;
                e.preventDefault();
                pos1 = pos3 - e.clientX;
                pos2 = pos4 - e.clientY;
                pos3 = e.clientX;
                pos4 = e.clientY;
                calculateNewPosition();
            }

            function elementTouchDrag(e) {
                const touch = e.touches[0];
                pos1 = pos3 - touch.clientX;
                pos2 = pos4 - touch.clientY;
                pos3 = touch.clientX;
                pos4 = touch.clientY;
                calculateNewPosition();
            }

            function calculateNewPosition() {
                const parentRect = radarMapContainer.getBoundingClientRect();
                let leftPercent = ((element.offsetLeft - pos1) / parentRect.width) * 100;
                let topPercent = ((element.offsetTop - pos2) / parentRect.height) * 100;

                // Bounds locks
                leftPercent = Math.max(5, Math.min(95, leftPercent));
                topPercent = Math.max(5, Math.min(95, topPercent));

                element.style.left = leftPercent.toFixed(1) + "%";
                element.style.top = topPercent.toFixed(1) + "%";

                // Sync the state
                if (element.id === 'localPlayerDot') {
                    // It's local player, no specific array update needed
                } else {
                    const idAttr = parseInt(element.getAttribute('data-id'));
                    const match = simulatedEnemies.find(item => item.id === idAttr);
                    if (match) {
                        match.left = parseFloat(leftPercent.toFixed(1));
                        match.top = parseFloat(topPercent.toFixed(1));
                    }
                }
                recalculateWinChances();
            }

            function closeDragElement() {
                document.onmouseup = null;
                document.onmousemove = null;
                showNotification("Позиция изменена", "Драйвер AnyQwer обновил асинхронные координаты.");
            }

            function closeDragTouch() {
                document.removeEventListener('touchend', closeDragTouch);
                document.removeEventListener('touchmove', elementTouchDrag);
                showNotification("Позиция изменена", "Драйвер AnyQwer обновил асинхронные координаты.");
            }
        }

        // Initialize drag on initial dots
        document.querySelectorAll('.radar-dot').forEach(dot => {
            makeDotDraggable(dot);
        });

        // Add Enemy Simulator Action
        let rawEnemyIndex = 3;
        btnSpawnEnemy.addEventListener('click', () => {
            if (simulatedEnemies.length >= 5) {
                showNotification("Лимит демо-режима", "В интерактивной версии можно спавнить до 5 врагов.", true);
                return;
            }

            rawEnemyIndex++;
            const name = `Enemy_#${rawEnemyIndex}`;
            const randomX = Math.floor(Math.random() * 40) + 15;
            const randomY = Math.floor(Math.random() * 50) + 20;

            const newEnemy = { 
                id: rawEnemyIndex, 
                name: name, 
                hp: 100, 
                weapon: 'AK-47', 
                val: 5100, 
                left: randomX, 
                top: randomY 
            };
            simulatedEnemies.push(newEnemy);

            // Append onto Map viewport
            const dotDiv = document.createElement('div');
            dotDiv.className = `radar-dot enemy-dot draggable-dot absolute flex flex-col items-center justify-center transform -translate-x-1/2 -translate-y-1/2 z-20`;
            dotDiv.style.left = `${randomX}%`;
            dotDiv.style.top = `${randomY}%`;
            dotDiv.setAttribute('data-id', rawEnemyIndex);
            dotDiv.innerHTML = `
                <div class="relative w-6 h-6 rounded-full bg-red-500 border-2 border-white flex items-center justify-center shadow-lg">
                    <span class="text-[8px] font-black text-white">E${rawEnemyIndex}</span>
                    <div class="view-cone absolute -top-6 -left-6 w-14 h-14 border-t-2 border-l-2 border-red-500/30 rounded-tl-full -rotate-45 pointer-events-none"></div>
                </div>
                <div class="player-info mt-1 flex flex-col items-center pointer-events-none">
                    <span class="player-name text-[8px] font-bold text-red-400 font-mono bg-slate-950/90 px-1 py-0.2 rounded border border-red-500/10">${name}</span>
                </div>
            `;
            radarMapContainer.appendChild(dotDiv);
            
            // Register drag on newly created element
            makeDotDraggable(dotDiv);

            showNotification("Трансляция API", `Драйвер успешно считал координаты нового врага: ${name}`);
            updateRadarUI();
        });

        // Smoke Action
        let smokeInterval = null;
        let smokeTime = 18.0;
        btnThrowSmoke.addEventListener('click', () => {
            if (smokeInterval) clearInterval(smokeInterval);
            
            smokeOnMap.style.opacity = "1";
            smokeTime = 18.0;
            smokeTimerVal.innerText = `${smokeTime.toFixed(1)}s`;

            smokeInterval = setInterval(() => {
                smokeTime -= 0.1;
                if (smokeTime <= 0) {
                    clearInterval(smokeInterval);
                    smokeOnMap.style.opacity = "0";
                    showNotification("Дым рассеялся", "Смоук-зона на пленте очищена.");
                } else {
                    smokeTimerVal.innerText = `${smokeTime.toFixed(1)}s`;
                }
            }, 100);
            
            showNotification("Смок активирован", "Дым развернут на миду de_mirage.");
        });

        // Bomb planting action
        btnPlantBomb.addEventListener('click', () => {
            if (isBombPlanted) {
                resetBombSim();
                return;
            }

            isBombPlanted = true;
            bombOnMap.classList.remove('opacity-0', 'scale-50');
            bombOnMap.classList.add('opacity-100', 'scale-100');
            bombTimerWidget.classList.remove('hidden');
            
            btnPlantBomb.innerHTML = `<i class="fa-solid fa-ban text-[10px]"></i> Сбросить C4`;
            btnTriggerDefuse.removeAttribute('disabled');
            
            bombSecondsRemaining = 40.0;
            bombTimerSeconds.innerText = `${bombSecondsRemaining.toFixed(1)}s`;

            showNotification("Бомба C4 заложена!", "Шансы Terrorists на раунд выросли.");
            recalculateWinChances();

            bombTimerInterval = setInterval(() => {
                bombSecondsRemaining -= 0.1;
                
                if (isDefusing) {
                    defuseSecondsRemaining -= 0.1;
                    if (defuseSecondsRemaining <= 0) {
                        clearInterval(bombTimerInterval);
                        showNotification("Бомба Обезврежена!", "Набор сапера сработал. Раунд за CT!");
                        resetBombSim();
                        return;
                    }
                }

                if (bombSecondsRemaining <= 0) {
                    clearInterval(bombTimerInterval);
                    showNotification("ВЗРЫВ C4!", "Точка Mirage А разрушена. Раунд за T.", true);
                    resetBombSim();
                } else {
                    if (isDefusing) {
                        bombTimerSeconds.innerHTML = `<span class="text-green-400 font-bold">DEFUSE ${defuseSecondsRemaining.toFixed(1)}s</span>`;
                    } else {
                        bombTimerSeconds.innerText = `${bombSecondsRemaining.toFixed(1)}s`;
                    }
                }
            }, 100);
        });

        btnTriggerDefuse.addEventListener('click', () => {
            if (!isBombPlanted || isDefusing) return;
            isDefusing = true;
            defuseSecondsRemaining = 5.0;
            btnTriggerDefuse.setAttribute('disabled', 'true');
            showNotification("Обезвреживание...", "Локальный игрок начал разминирование бомбы.");
        });

        function resetBombSim() {
            isBombPlanted = false;
            isDefusing = false;
            clearInterval(bombTimerInterval);
            bombOnMap.classList.remove('opacity-100', 'scale-100');
            bombOnMap.classList.add('opacity-0', 'scale-50');
            bombTimerWidget.classList.add('hidden');
            btnPlantBomb.innerHTML = `<i class="fa-solid fa-bomb text-[10px]"></i> Заложить C4`;
            btnTriggerDefuse.setAttribute('disabled', 'true');
            recalculateWinChances();
        }

        // --- Simulated Live Driver Logs Terminal Console ---
        const logEntries = [
            "Initializing kernel handle filter...",
            "Loading AnyQwer raw driver binary (v2.4.6)",
            "Searching CS2 client.dll base pointer...",
            "CS2 process found [PID: 14240]",
            "Matching memory signatures to structures...",
            "Kernel hook successful. Zero tracing active.",
            "Client.dll loaded at offset: 0x7FFA2B400000",
            "LocalPlayer offset matched: 0x1810F38",
            "EntityList base updated: 0x17C1530",
            "DirectX Present hook created (No-Lag mode)",
            "Websocket stream handler bound to port 8080",
            "Ready for incoming device connections.",
            "Latency stabilized: 0.118ms API loop"
        ];

        const logContainer = document.getElementById('driverLogOutput');

        function appendLogLines() {
            const time = new Date().toLocaleTimeString();
            const randomLine = logEntries[Math.floor(Math.random() * logEntries.length)];
            
            const logRow = document.createElement('div');
            logRow.innerHTML = `<span class="text-indigo-400">[${time}]</span> <span class="text-gray-300">${randomLine}</span>`;
            
            logContainer.appendChild(logRow);
            logContainer.scrollTop = logContainer.scrollHeight;
        }

        // --- Window Onload actions ---
        window.onload = function () {
            updateRadarUI();
            
            // Fill initial console log lines
            for (let i = 0; i < 5; i++) {
                setTimeout(appendLogLines, i * 400);
            }
            // Keep console logs alive
            setInterval(appendLogLines, 3000);
        };
    </script>
</body>
</html>
