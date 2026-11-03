<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Auto Giro - Jogo de Arrancada 420m</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome para Ícones -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Fonte Orbitron para visual racing/gamer -->
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;600;800;900&family=Rajdhani:wght@500;700&display=swap" rel="stylesheet">
    
    <style>
        * {
            font-family: 'Rajdhani', sans-serif;
            user-select: none;
            -webkit-user-select: none;
            touch-action: manipulation;
        }
        .font-orbitron {
            font-family: 'Orbitron', sans-serif;
        }
        /* Custom Scrollbar */
        ::-webkit-scrollbar {
            width: 6px;
        }
        ::-webkit-scrollbar-track {
            background: #0f172a;
        }
        ::-webkit-scrollbar-thumb {
            background: #06b6d4;
            border-radius: 3px;
        }
        /* Glassmorphic Panel */
        .glass-panel {
            background: rgba(15, 23, 42, 0.85);
            backdrop-filter: blur(12px);
            border: 1px solid rgba(255, 255, 255, 0.1);
        }
        /* Neon Glows */
        .glow-cyan {
            box-shadow: 0 0 15px rgba(6, 182, 212, 0.5);
        }
        .glow-red {
            box-shadow: 0 0 15px rgba(239, 68, 68, 0.6);
        }
        .glow-green {
            box-shadow: 0 0 15px rgba(34, 197, 94, 0.6);
        }
        /* Canvas container positioning */
        #gameCanvas {
            width: 100%;
            height: 100%;
            display: block;
        }
    </style>
</head>
<body class="bg-slate-950 text-white min-h-screen flex flex-col justify-between overflow-x-hidden selection:bg-cyan-500 selection:text-black">

    <!-- TOP BAR / HEADER -->
    <header class="w-full bg-slate-900/90 border-b border-slate-800 px-4 py-3 flex justify-between items-center z-20 shadow-lg">
        <div class="flex items-center gap-3">
            <div class="bg-gradient-to-r from-red-600 to-amber-500 p-2 rounded-lg shadow-md">
                <i class="fa-solid font-bold fa-gauge-high text-xl text-black"></i>
            </div>
            <div>
                <h1 class="font-orbitron font-black text-xl tracking-wider text-transparent bg-clip-text bg-gradient-to-r from-cyan-400 via-amber-400 to-red-500">
                    AUTO GIRO <span class="text-xs text-slate-400 font-normal">| Grupo 5</span>
                </h1>
                <p class="text-xs text-slate-400 font-semibold hidden sm:block">Desafio de Arrancada 420m</p>
            </div>
        </div>

        <div class="flex items-center gap-4">
            <!-- Dinheiro / Moedas do Piloto -->
            <div class="flex items-center gap-2 bg-slate-800/80 px-3 py-1.5 rounded-full border border-amber-500/30">
                <i class="fa-solid fa-coins text-amber-400 text-sm"></i>
                <span id="playerMoney" class="font-orbitron font-bold text-amber-300 text-sm sm:text-base">R$ 1.500</span>
            </div>

            <!-- Navegação de Telas -->
            <nav class="flex gap-2">
                <button id="btnNavRace" onclick="switchTab('race')" class="px-3 py-1.5 rounded-lg bg-cyan-600 hover:bg-cyan-500 text-white font-bold text-sm transition flex items-center gap-1.5 glow-cyan">
                    <i class="fa-solid fa-flag-checkered"></i> <span class="hidden sm:inline">Pista</span>
                </button>
                <button id="btnNavGarage" onclick="switchTab('garage')" class="px-3 py-1.5 rounded-lg bg-slate-800 hover:bg-slate-700 text-slate-200 font-bold text-sm transition flex items-center gap-1.5 border border-slate-700">
                    <i class="fa-solid fa-warehouse"></i> <span class="hidden sm:inline">Garagem</span>
                </button>
                <button id="btnNavRank" onclick="switchTab('ranking')" class="px-3 py-1.5 rounded-lg bg-slate-800 hover:bg-slate-700 text-slate-200 font-bold text-sm transition flex items-center gap-1.5 border border-slate-700">
                    <i class="fa-solid fa-trophy"></i> <span class="hidden sm:inline">Ranking</span>
                </button>
            </nav>
        </div>
    </header>

    <!-- CONTAINER PRINCIPAL DE TELAS -->
    <main class="flex-1 relative flex flex-col justify-center items-center w-full max-w-7xl mx-auto p-2 sm:p-4">

        <!-- ==================== TELA 1: PISTA DE CORRIDA (CANVAS) ==================== -->
        <section id="tabRace" class="w-full flex flex-col items-center gap-3">
            
            <!-- CANVAS CONTAINER -->
            <div class="relative w-full aspect-[16/9] sm:aspect-[21/9] max-h-[480px] bg-slate-900 rounded-xl overflow-hidden border-2 border-slate-700 shadow-2xl">
                
                <canvas id="gameCanvas"></canvas>

                <!-- OVERLAY: SEMÁFORO DE LARGADA (TREE LIGHTS) -->
                <div id="treeLightsOverlay" class="absolute top-4 left-1/2 -translate-x-1/2 glass-panel p-2.5 rounded-xl border border-slate-600/50 flex gap-3 shadow-2xl z-10 transition-all duration-300">
                    <div id="lightRed" class="w-6 h-6 sm:w-8 sm:h-8 rounded-full bg-red-950 border-2 border-red-900 transition-all duration-150"></div>
                    <div id="lightYellow1" class="w-6 h-6 sm:w-8 sm:h-8 rounded-full bg-amber-950 border-2 border-amber-900 transition-all duration-150"></div>
                    <div id="lightYellow2" class="w-6 h-6 sm:w-8 sm:h-8 rounded-full bg-amber-950 border-2 border-amber-900 transition-all duration-150"></div>
                    <div id="lightGreen" class="w-6 h-6 sm:w-8 sm:h-8 rounded-full bg-emerald-950 border-2 border-emerald-900 transition-all duration-150"></div>
                </div>

                <!-- OVERLAY: MENSAGEM / STATUS DE LARGADA -->
                <div id="statusMsgOverlay" class="absolute top-1/3 left-1/2 -translate-x-1/2 -translate-y-1/2 text-center pointer-events-none z-10">
                    <h2 id="statusTitle" class="font-orbitron font-black text-2xl sm:text-4xl text-yellow-400 drop-shadow-[0_4px_8px_rgba(0,0,0,0.8)] tracking-wider">PREPARE-SE!</h2>
                    <p id="statusSubtitle" class="text-sm sm:text-base font-bold text-slate-200 drop-shadow">Aguarde o sinal verde para acelerar</p>
                </div>

                <!-- OVERLAY: PAINEL DE TELEMETRIA NA PISTA (SUPERIOR ESQUERDA) -->
                <div class="absolute top-3 left-3 glass-panel px-3 py-2 rounded-lg text-left border border-slate-700/60 pointer-events-none">
                    <div class="text-[10px] text-slate-400 font-bold uppercase tracking-wider">Distância Percorrida</div>
                    <div class="font-orbitron font-extrabold text-lg sm:text-xl text-cyan-400">
                        <span id="hudDistance">0.0</span> / 420m
                    </div>
                    <div class="w-full bg-slate-800 rounded-full h-1.5 mt-1 overflow-hidden">
                        <div id="distanceBar" class="bg-cyan-400 h-full w-0 transition-all duration-75"></div>
                    </div>
                </div>

                <!-- OVERLAY: CRONÔMETRO DE PRECISÃO (SUPERIOR DIREITA) -->
                <div class="absolute top-3 right-3 glass-panel px-3 py-2 rounded-lg text-right border border-slate-700/60 pointer-events-none">
                    <div class="text-[10px] text-slate-400 font-bold uppercase tracking-wider">Tempo de Prova</div>
                    <div id="hudTimer" class="font-orbitron font-black text-xl sm:text-2xl text-amber-400 tracking-wider">
                        00.000s
                    </div>
                </div>

                <!-- OVERLAY: PAINEL PÓS-CORRIDA (RESULTADOS) -->
                <div id="modalResult" class="hidden absolute inset-0 bg-slate-950/90 backdrop-blur-md z-30 flex flex-col items-center justify-center p-4">
                    <div class="glass-panel p-6 rounded-2xl border border-cyan-500/40 max-w-md w-full text-center space-y-4 shadow-2xl animate-fade-in">
                        <h2 id="resTitle" class="font-orbitron font-black text-3xl text-emerald-400 tracking-wider">VITÓRIA!</h2>
                        <p id="resSubtitle" class="text-sm text-slate-300">Você completou a prova dos 420 metros!</p>
                        
                        <div class="grid grid-cols-2 gap-3 py-2 text-left">
                            <div class="bg-slate-900/80 p-3 rounded-lg border border-slate-800">
                                <span class="text-xs text-slate-400 block font-semibold">Tempo Final</span>
                                <span id="resTime" class="font-orbitron font-bold text-xl text-amber-400">00.000s</span>
                            </div>
                            <div class="bg-slate-900/80 p-3 rounded-lg border border-slate-800">
                                <span class="text-xs text-slate-400 block font-semibold">Velocidade Máxima</span>
                                <span id="resSpeed" class="font-orbitron font-bold text-xl text-cyan-400">0 km/h</span>
                            </div>
                            <div class="bg-slate-900/80 p-3 rounded-lg border border-slate-800">
                                <span class="text-xs text-slate-400 block font-semibold">Tempo de Reação</span>
                                <span id="resReaction" class="font-orbitron font-bold text-base text-slate-200">0.00s</span>
                            </div>
                            <div class="bg-slate-900/80 p-3 rounded-lg border border-slate-800">
                                <span class="text-xs text-slate-400 block font-semibold">Prêmio da Prova</span>
                                <span id="resPrize" class="font-orbitron font-bold text-base text-emerald-400">+ R$ 0</span>
                            </div>
                        </div>

                        <div class="flex gap-3 pt-2">
                            <button onclick="resetRace()" class="flex-1 py-3 bg-gradient-to-r from-emerald-600 to-teal-600 hover:from-emerald-500 hover:to-teal-500 font-bold font-orbitron rounded-xl transition shadow-lg text-sm sm:text-base">
                                <i class="fa-solid fa-rotate-right mr-1"></i> NOVA CORRIDA
                            </button>
                            <button onclick="switchTab('garage')" class="flex-1 py-3 bg-slate-800 hover:bg-slate-700 font-bold font-orbitron rounded-xl transition border border-slate-700 text-sm sm:text-base">
                                <i class="fa-solid fa-wrench mr-1"></i> GARAGEM
                            </button>
                        </div>
                    </div>
                </div>

            </div>

            <!-- DASHBOARD DE CONTROLE / TACÔMETRO E VELOCÍMETRO -->
            <div class="w-full bg-slate-900/90 border border-slate-800 rounded-xl p-3 sm:p-4 grid grid-cols-1 md:grid-cols-12 gap-4 items-center">
                
                <!-- OPONENTE INFO -->
                <div class="md:col-span-3 flex flex-col justify-center bg-slate-950/60 p-3 rounded-xl border border-slate-800">
                    <span class="text-xs text-slate-400 font-bold uppercase tracking-wider mb-1">Adversário Selecionado</span>
                    <div class="flex items-center gap-3">
                        <div class="w-10 h-10 rounded-full bg-slate-800 border border-slate-700 flex items-center justify-center text-cyan-400 font-bold text-lg">
                            <i class="fa-solid fa-user-ninja"></i>
                        </div>
                        <div>
                            <select id="selectOpponent" onchange="changeOpponent()" class="bg-slate-900 text-sm font-bold text-cyan-300 border border-slate-700 rounded px-2 py-1 focus:outline-none focus:border-cyan-500">
                                <!-- Preenchido via JS -->
                            </select>
                            <p id="oppMotoName" class="text-xs text-slate-400">Titan 160 Fuçada</p>
                        </div>
                    </div>
                </div>

                <!-- TACÔMETRO & SHIFT LIGHT (CENTRO) -->
                <div class="md:col-span-6 flex flex-col items-center justify-center bg-slate-950/80 p-3 rounded-xl border border-slate-800">
                    
                    <div class="w-full flex justify-between items-center mb-1 px-2">
                        <!-- VELOCÍMETRO -->
                        <div class="flex items-baseline gap-1">
                            <span id="hudSpeed" class="font-orbitron font-black text-3xl sm:text-4xl text-cyan-400">0</span>
                            <span class="text-xs font-bold text-slate-400">KM/H</span>
                        </div>

                        <!-- SHIFT LIGHT INDICATOR -->
                        <div id="shiftLight" class="px-4 py-1 rounded-md font-orbitron font-extrabold text-xs tracking-wider uppercase bg-slate-800 text-slate-500 border border-slate-700">
                            RPM NORMAL
                        </div>

                        <!-- MARCHA ATUAL -->
                        <div class="flex items-center gap-1 bg-slate-900 px-3 py-1 rounded-lg border border-slate-800">
                            <span class="text-xs text-slate-400 font-semibold">MARCHA:</span>
                            <span id="hudGear" class="font-orbitron font-black text-2xl text-amber-400">N</span>
                        </div>
                    </div>

                    <!-- BARRA DE RPM / CONTA GIROS -->
                    <div class="w-full bg-slate-900 rounded-lg p-1.5 border border-slate-800 relative">
                        <!-- Marcador da Zona Perfeita de Troca -->
                        <div class="absolute top-0 bottom-0 right-[15%] w-[12%] bg-emerald-500/20 border-x border-emerald-500/50 pointer-events-none z-0"></div>
                        <div class="absolute top-0 bottom-0 right-[3%] w-[12%] bg-red-600/30 border-x border-red-500/50 pointer-events-none z-0"></div>

                        <div class="h-5 sm:h-6 w-full bg-slate-950 rounded overflow-hidden flex relative z-10">
                            <div id="rpmBar" class="h-full w-0 bg-gradient-to-r from-cyan-500 via-amber-400 to-emerald-400 transition-all duration-75"></div>
                        </div>

                        <div class="flex justify-between text-[10px] text-slate-500 font-mono mt-1 font-semibold">
                            <span>0</span>
                            <span>3k</span>
                            <span>6k</span>
                            <span>9k (PERFEITO)</span>
                            <span>11k (CORTE)</span>
                        </div>
                    </div>
                </div>

                <!-- CONTROLES NA TELA / BOTÕES (DIREITA OU TOUCH MOBILE) -->
                <div class="md:col-span-3 flex gap-2 h-full">
                    <!-- BOTÃO TROCAR MARCHA -->
                    <button id="btnShift" onmousedown="triggerShiftGear()" onclick="triggerShiftGear()" class="flex-1 py-3 px-2 bg-gradient-to-b from-amber-500 to-amber-700 hover:from-amber-400 hover:to-amber-600 active:scale-95 text-black font-orbitron font-black text-sm rounded-xl shadow-lg border-b-4 border-amber-900 flex flex-col items-center justify-center gap-1 transition">
                        <i class="fa-solid fa-angles-up text-lg"></i>
                        <span>MARCHA</span>
                        <span class="text-[10px] opacity-75 font-sans">(Shift / Espaço)</span>
                    </button>

                    <!-- BOTÃO ACELERADOR -->
                    <button id="btnGas" onmousedown="startGas()" onmouseup="stopGas()" ontouchstart="startGas(event)" ontouchend="stopGas(event)" class="flex-1 py-3 px-2 bg-gradient-to-b from-emerald-500 to-emerald-700 hover:from-emerald-400 hover:to-emerald-600 active:scale-95 text-black font-orbitron font-black text-sm rounded-xl shadow-lg border-b-4 border-emerald-900 flex flex-col items-center justify-center gap-1 transition">
                        <i class="fa-solid fa-gauge-high text-lg"></i>
                        <span>ACELERAR</span>
                        <span class="text-[10px] opacity-75 font-sans">(Seta P/ Cima)</span>
                    </button>
                </div>

            </div>

        </section>

        <!-- ==================== TELA 2: GARAGEM & LOJA DE PEÇAS ==================== -->
        <section id="tabGarage" class="w-full hidden flex-col gap-4">
            
            <div class="flex flex-col md:flex-row gap-6">
                
                <!-- ESQUERDA: SELEÇÃO DE MOTOS -->
                <div class="w-full md:w-1/2 glass-panel p-5 rounded-2xl border border-slate-800 flex flex-col justify-between">
                    <div>
                        <div class="flex justify-between items-center mb-4">
                            <h2 class="font-orbitron font-bold text-xl text-cyan-400 flex items-center gap-2">
                                <i class="fa-solid fa-motorcycle"></i> Minhas Motos
                            </h2>
                            <span class="text-xs bg-slate-800 text-slate-300 px-2.5 py-1 rounded-full border border-slate-700 font-semibold" id="garageCount">1 / 4 Desbloqueadas</span>
                        </div>

                        <!-- CARROSEL / SELETOR DE MOTO -->
                        <div class="relative bg-slate-950 p-6 rounded-xl border border-slate-800 text-center mb-4 min-h-[220px] flex flex-col justify-center items-center">
                            <!-- Canvas Preview da Moto na Garagem -->
                            <canvas id="garageCanvas" width="280" height="110" class="mb-2"></canvas>
                            
                            <h3 id="garageMotoName" class="font-orbitron font-extrabold text-2xl text-amber-400">Pop 110 Sport</h3>
                            <p id="garageMotoDesc" class="text-xs text-slate-400 mt-1">Moto leve ideal para início de carreira.</p>

                            <!-- Botões de Navegação da Garagem -->
                            <button onclick="cycleMoto(-1)" class="absolute left-2 top-1/2 -translate-y-1/2 w-9 h-9 rounded-full bg-slate-800 hover:bg-slate-700 text-cyan-400 flex items-center justify-center border border-slate-700">
                                <i class="fa-solid fa-chevron-left"></i>
                            </button>
                            <button onclick="cycleMoto(1)" class="absolute right-2 top-1/2 -translate-y-1/2 w-9 h-9 rounded-full bg-slate-800 hover:bg-slate-700 text-cyan-400 flex items-center justify-center border border-slate-700">
                                <i class="fa-solid fa-chevron-right"></i>
                            </button>
                        </div>

                        <!-- BARRA DE STATUS DA MOTO -->
                        <div class="space-y-3 bg-slate-900/60 p-4 rounded-xl border border-slate-800">
                            <div>
                                <div class="flex justify-between text-xs font-bold mb-1">
                                    <span class="text-slate-400">Potência Bruta (CV)</span>
                                    <span id="statPower" class="text-amber-400">12 CV</span>
                                </div>
                                <div class="w-full bg-slate-800 h-2 rounded-full overflow-hidden">
                                    <div id="barPower" class="bg-amber-400 h-full w-1/4"></div>
                                </div>
                            </div>

                            <div>
                                <div class="flex justify-between text-xs font-bold mb-1">
                                    <span class="text-slate-400">Velocidade Máxima Estimada</span>
                                    <span id="statTopSpeed" class="text-cyan-400">110 km/h</span>
                                </div>
                                <div class="w-full bg-slate-800 h-2 rounded-full overflow-hidden">
                                    <div id="barTopSpeed" class="bg-cyan-400 h-full w-1/4"></div>
                                </div>
                            </div>
                        </div>
                    </div>

                    <!-- BOTÃO DE SELECIONAR OU COMPRAR MOTO -->
                    <div class="mt-4">
                        <button id="btnSelectOrBuyMoto" onclick="handleMotoAction()" class="w-full py-3 bg-gradient-to-r from-cyan-600 to-blue-600 hover:from-cyan-500 hover:to-blue-500 font-orbitron font-bold rounded-xl transition shadow-lg text-center">
                            EQUIPADA
                        </button>
                    </div>

                </div>

                <!-- DIREITA: TUNING & PEÇAS DE DESEMPENHO -->
                <div class="w-full md:w-1/2 glass-panel p-5 rounded-2xl border border-slate-800">
                    <h2 class="font-orbitron font-bold text-xl text-amber-400 mb-4 flex items-center gap-2">
                        <i class="fa-solid fa-screwdriver-wrench"></i> Peças & Preparação (Tuning)
                    </h2>

                    <div class="space-y-3 max-h-[380px] overflow-y-auto pr-1">
                        
                        <!-- PEÇA 1: ESCAPAMENTO ESPORTIVO -->
                        <div class="bg-slate-900/80 p-3.5 rounded-xl border border-slate-800 flex justify-between items-center">
                            <div class="flex items-center gap-3">
                                <div class="w-10 h-10 rounded-lg bg-amber-500/10 border border-amber-500/30 flex items-center justify-center text-amber-400 text-lg">
                                    <i class="fa-solid fa-fire text-amber-500"></i>
                                </div>
                                <div>
                                    <h4 class="font-bold text-sm text-slate-200">Escapamento Esportivo (Corte)</h4>
                                    <p class="text-xs text-slate-400">+5 CV | Resposta de RPM mais rápida</p>
                                </div>
                            </div>
                            <button id="btnUpgradeExhaust" onclick="buyUpgrade('exhaust')" class="px-3 py-1.5 bg-amber-500/20 hover:bg-amber-500/30 text-amber-300 font-bold text-xs rounded-lg border border-amber-500/40 transition">
                                R$ 400
                            </button>
                        </div>

                        <!-- PEÇA 2: PNEU SLICK ARRANCADA -->
                        <div class="bg-slate-900/80 p-3.5 rounded-xl border border-slate-800 flex justify-between items-center">
                            <div class="flex items-center gap-3">
                                <div class="w-10 h-10 rounded-lg bg-cyan-500/10 border border-cyan-500/30 flex items-center justify-center text-cyan-400 text-lg">
                                    <i class="fa-solid fa-circle-notch"></i>
                                </div>
                                <div>
                                    <h4 class="font-bold text-sm text-slate-200">Pneu Slick de Arrancada</h4>
                                    <p class="text-xs text-slate-400">Grip Máximo na Largada (0% Patinar)</p>
                                </div>
                            </div>
                            <button id="btnUpgradeTires" onclick="buyUpgrade('tires')" class="px-3 py-1.5 bg-cyan-500/20 hover:bg-cyan-500/30 text-cyan-300 font-bold text-xs rounded-lg border border-cyan-500/40 transition">
                                R$ 600
                            </button>
                        </div>

                        <!-- PEÇA 3: REMAP DE INJEÇÃO & TURBO -->
                        <div class="bg-slate-900/80 p-3.5 rounded-xl border border-slate-800 flex justify-between items-center">
                            <div class="flex items-center gap-3">
                                <div class="w-10 h-10 rounded-lg bg-red-500/10 border border-red-500/30 flex items-center justify-center text-red-400 text-lg">
                                    <i class="fa-solid fa-bolt"></i>
                                </div>
                                <div>
                                    <h4 class="font-bold text-sm text-slate-200">Kit Turbo & Remap ECU</h4>
                                    <p class="text-xs text-slate-400">+15 CV | Velocidade Final Elevada</p>
                                </div>
                            </div>
                            <button id="btnUpgradeTurbo" onclick="buyUpgrade('turbo')" class="px-3 py-1.5 bg-red-500/20 hover:bg-red-500/30 text-red-300 font-bold text-xs rounded-lg border border-red-500/40 transition">
                                R$ 1.200
                            </button>
                        </div>

                    </div>
                </div>

            </div>

        </section>

        <!-- ==================== TELA 3: LEADERBOARD / RANKING DOS JOGADORES ==================== -->
        <section id="tabRanking" class="w-full hidden flex-col gap-4">
            <div class="glass-panel p-6 rounded-2xl border border-slate-800 max-w-3xl mx-auto w-full">
                
                <div class="flex justify-between items-center mb-6">
                    <div>
                        <h2 class="font-orbitron font-extrabold text-2xl text-amber-400 flex items-center gap-2">
                            <i class="fa-solid fa-trophy"></i> Ranking dos Pilotos — 420 Metros
                        </h2>
                        <p class="text-xs text-slate-400 mt-0.5">Recordes de arrancada registrados no Auto Giro</p>
                    </div>
                    <button onclick="loadLeaderboard()" class="px-3 py-1.5 bg-slate-800 hover:bg-slate-700 rounded-lg border border-slate-700 text-xs text-slate-300 font-bold">
                        <i class="fa-solid fa-rotate"></i> Atualizar
                    </button>
                </div>

                <!-- TABELA DE CLASSIFICAÇÃO -->
                <div class="overflow-x-auto">
                    <table class="w-full text-left border-collapse">
                        <thead>
                            <tr class="border-b border-slate-800 text-xs text-slate-400 uppercase font-orbitron">
                                <th class="py-3 px-3"># Pos</th>
                                <th class="py-3 px-3">Piloto (Participante)</th>
                                <th class="py-3 px-3">Moto</th>
                                <th class="py-3 px-3 text-right">Tempo (420m)</th>
                                <th class="py-3 px-3 text-right">Vel. Máx</th>
                            </tr>
                        </thead>
                        <tbody id="leaderboardBody" class="divide-y divide-slate-800/50 text-sm font-semibold">
                            <!-- Preenchido via JS -->
                        </tbody>
                    </table>
                </div>

            </div>
        </section>

    </main>

    <!-- FOOTER COM CRÉDITOS DAS INTEGRANTES DO GRUPO 5 -->
    <footer class="w-full bg-slate-950 border-t border-slate-800 py-3 px-4 text-center text-xs text-slate-500">
        <p class="font-semibold text-slate-400">Auto Giro &copy; Grupo 5 — Programação Aplicada</p>
        <p class="mt-0.5 text-[11px] text-slate-500">
            Equipe: Maria Juliana | Ana Cláudia | Pedro Eduardo | Pedro Lucas Whylace | José Daniel | Ryan Valdemir
        </p>
    </footer>

    <!-- ==================== LOGICA JAVASCRIPT DO JOGO ==================== -->
    <script>
        /* ==================== ESTADO GLOBAL E DADOS ==================== */
        const TRACK_LENGTH_METERS = 420;

        // Dados das Motos Disponíveis
        const MOTOS_DATA = [
            {
                id: 'pop110',
                name: 'Pop 110 Sport',
                desc: 'Moto de entrada leve e ágil. Ótima para pegar o jeito no semáforo.',
                basePower: 12,
                topSpeed: 125,
                price: 0,
                color: '#ef4444', // Vermelho
                unlocked: true
            },
            {
                id: 'titan160',
                name: 'Titan 160 Fuçada',
                desc: 'Clássica da cidade com kit 190cc e comando preparado.',
                basePower: 22,
                topSpeed: 165,
                price: 1200,
                color: '#3b82f6', // Azul
                unlocked: false
            },
            {
                id: 'hornet600',
                name: 'Hornet 600 Carburada',
                desc: 'Motor 4 cilindros com ronco inconfundível e forte aceleração.',
                basePower: 48,
                topSpeed: 215,
                price: 3500,
                color: '#eab308', // Amarelo
                unlocked: false
            },
            {
                id: 'xj6turbo',
                name: 'XJ6 Turbo Pro',
                desc: 'Monstro das pistas de 420m. Equipada com caracol de turbo.',
                basePower: 85,
                topSpeed: 270,
                price: 7000,
                color: '#06b6d4', // Ciano Neon
                unlocked: false
            }
        ];

        // Rivais / Lista do Grupo 5
        const OPPONENTS = [
            { name: 'Pedro Lucas (Rival)', motoId: 'titan160', baseTime: 11.2 },
            { name: 'Ryan Valdemir', motoId: 'pop110', baseTime: 13.5 },
            { name: 'José Daniel', motoId: 'hornet600', baseTime: 9.8 },
            { name: 'Ana Cláudia', motoId: 'titan160', baseTime: 11.8 },
            { name: 'Maria Juliana', motoId: 'xj6turbo', baseTime: 8.5 },
            { name: 'Pedro Eduardo', motoId: 'hornet600', baseTime: 9.4 }
        ];

        // Estado do Jogador
        let player = {
            money: 1500,
            currentMotoIdx: 0,
            upgrades: {
                exhaust: false,
                tires: false,
                turbo: false
            }
        };

        // Estado da Prova / Simulação Física
        let raceState = 'IDLE'; // IDLE, COUNTDOWN, RACING, FINISHED, FALSE_START
        let treeStep = 0;
        let treeTimer = null;
        let raceStartTime = 0;
        let finalTime = 0;
        let maxSpeedReached = 0;
        let reactionTime = 0;
        let selectedOpponent = OPPONENTS[0];

        // Física da Moto do Jogador
        let playerBike = {
            dist: 0, // metros
            speed: 0, // km/h
            gear: 1,
            rpm: 1000,
            isAccelerating: false,
            falseStart: false
        };

        // Física do Oponente
        let oppBike = {
            dist: 0,
            speed: 0,
            targetTime: 11.0
        };

        // Elementos Canvas
        let canvas, ctx;
        let garageCanvas, garageCtx;

        /* ==================== INICIALIZAÇÃO DO JOGO ==================== */
        window.onload = function() {
            canvas = document.getElementById('gameCanvas');
            ctx = canvas.getContext('2d');

            garageCanvas = document.getElementById('garageCanvas');
            garageCtx = garageCanvas.getContext('2d');

            resizeCanvas();
            window.addEventListener('resize', resizeCanvas);

            // Setup de Eventos de Teclado
            setupKeyboardControls();

            // Popular Select de Adversários
            populateOpponentsSelect();

            // Atualizar UI Inicial
            updatePlayerUI();
            drawGaragePreview();
            loadLeaderboard();

            // Iniciar Loop do Canvas
            requestAnimationFrame(gameLoop);
        };

        function resizeCanvas() {
            if (!canvas) return;
            const container = canvas.parentElement;
            canvas.width = container.clientWidth;
            canvas.height = container.clientHeight;
        }

        /* ==================== SISTEMA DE CORRIDA E FÍSICA ==================== */

        function startRaceCountdown() {
            if (raceState === 'RACING' || raceState === 'COUNTDOWN') return;

            // Resetar Variáveis da Moto
            playerBike.dist = 0;
            playerBike.speed = 0;
            playerBike.gear = 1;
            playerBike.rpm = 1000;
            playerBike.falseStart = false;

            oppBike.dist = 0;
            oppBike.speed = 0;

            document.getElementById('modalResult').classList.add('hidden');
            raceState = 'COUNTDOWN';
            treeStep = 0;
            updateTreeLights();

            updateStatusMsg('PREPARE-SE!', 'text-yellow-400', 'Aguarde o sinal verde...');

            // Sequência do Semáforo (Amarelo 1, 2, 3 -> Verde)
            if (treeTimer) clearInterval(treeTimer);
            
            const countdownStartTime = performance.now();

            treeTimer = setInterval(() => {
                treeStep++;
                updateTreeLights();

                if (treeStep === 4) { // VERDE!
                    clearInterval(treeTimer);
                    raceState = 'RACING';
                    raceStartTime = performance.now();
                    updateStatusMsg('VAI! VAI! VAI!', 'text-emerald-400', 'Acelere ao máximo!');
                    
                    // Se o jogador já estava segurando acelerador na largada
                    if (playerBike.isAccelerating) {
                        reactionTime = 0.05;
                    } else {
                        reactionTime = 0.25; // Reação média estimada se apertar depois
                    }
                }
            }, 750);
        }

        function triggerFalseStart() {
            clearInterval(treeTimer);
            raceState = 'FALSE_START';
            playerBike.falseStart = true;
            updateStatusMsg('QUEIMA DE LARGADA!', 'text-red-500', 'Você acelerou antes do verde!');
            
            // Ativar luzes vermelhas no semáforo
            document.getElementById('lightRed').className = 'w-6 h-6 sm:w-8 sm:h-8 rounded-full bg-red-600 border-2 border-white glow-red';

            setTimeout(() => {
                resetRace();
            }, 2500);
        }

        function resetRace() {
            raceState = 'IDLE';
            treeStep = 0;
            playerBike.dist = 0;
            playerBike.speed = 0;
            playerBike.gear = 1;
            playerBike.rpm = 1000;
            oppBike.dist = 0;
            document.getElementById('modalResult').classList.add('hidden');
            updateTreeLights();
            updateStatusMsg('PRONTO PARA LARGAR', 'text-cyan-400', 'Clique em ACELERAR para iniciar o semáforo');
        }

        // Loop Principal de Atualização e Desenho
        let lastTimestamp = 0;
        function gameLoop(timestamp) {
            const dt = Math.min((timestamp - lastTimestamp) / 1000, 0.1);
            lastTimestamp = timestamp;

            updatePhysics(dt);
            drawRaceScene();
            updateHUD();

            requestAnimationFrame(gameLoop);
        }

        function updatePhysics(dt) {
            if (raceState === 'COUNTDOWN') {
                // Se acelerar durante o semáforo amarelo = Queima de Largada
                if (playerBike.isAccelerating && treeStep < 4 && treeStep > 0) {
                    triggerFalseStart();
                }
            }

            if (raceState === 'RACING') {
                const currentMoto = MOTOS_DATA[player.currentMotoIdx];

                // Calcular Potência com Upgrades
                let totalPower = currentMoto.basePower;
                if (player.upgrades.exhaust) totalPower += 5;
                if (player.upgrades.turbo) totalPower += 15;

                let maxSpeed = currentMoto.topSpeed;
                if (player.upgrades.turbo) maxSpeed += 30;

                // Dinâmica de RPM e Aceleração por Marcha
                const gearRatios = [0, 35, 65, 100, 140, 185, maxSpeed]; // Velocidades máximas de cada marcha
                const maxGearSpeed = gearRatios[playerBike.gear] || maxSpeed;

                if (playerBike.isAccelerating) {
                    // Aumentar RPM
                    let rpmGain = 6000 * (totalPower / 25) * dt;
                    playerBike.rpm = Math.min(playerBike.rpm + rpmGain, 11500);

                    // Se a velocidade está abaixo do limite da marcha atual, ganha velocidade
                    let accelFactor = (totalPower / 12) * (1 - (playerBike.speed / maxGearSpeed));
                    if (playerBike.rpm > 10500) accelFactor *= 0.3; // Perda de potência no corte de giro

                    playerBike.speed += accelFactor * 45 * dt;
                } else {
                    // Desacelerar / Freio de motor
                    playerBike.rpm = Math.max(playerBike.rpm - 5000 * dt, 1000);
                    playerBike.speed = Math.max(playerBike.speed - 15 * dt, 0);
                }

                // Limitar à velocidade máxima da moto
                playerBike.speed = Math.min(playerBike.speed, maxSpeed);

                // Converter km/h para metros/segundo (1 km/h = 0.277778 m/s)
                const metersPerSec = playerBike.speed * 0.277778;
                playerBike.dist += metersPerSec * dt;

                if (playerBike.speed > maxSpeedReached) {
                    maxSpeedReached = Math.round(playerBike.speed);
                }

                // Simulação de Física do Oponente
                const oppSpeedMetersPerSec = (TRACK_LENGTH_METERS / selectedOpponent.baseTime);
                oppBike.dist += oppSpeedMetersPerSec * dt;

                // Verificação de Linha de Chegada (420 metros)
                if (playerBike.dist >= TRACK_LENGTH_METERS && raceState === 'RACING') {
                    finishRace();
                }
            }
        }

        function triggerShiftGear() {
            if (raceState === 'IDLE') {
                startRaceCountdown();
                return;
            }

            if (raceState !== 'RACING') return;

            if (playerBike.gear < 6) {
                // Verificar se a troca foi na "Zona Perfeita" (entre 8800 RPM e 10200 RPM)
                const isPerfectShift = playerBike.rpm >= 8800 && playerBike.rpm <= 10200;
                
                playerBike.gear++;
                
                // Queda de RPM ao engatar marcha mais alta
                playerBike.rpm = Math.max(playerBike.rpm - 3500, 3000);

                if (isPerfectShift) {
                    // Bônus de arrancada instantânea na troca perfeita
                    playerBike.speed += 8;
                    showFloatingText("TROCA PERFEITA! +KM/H", "#22c55e");
                }
            }
        }

        function finishRace() {
            raceState = 'FINISHED';
            finalTime = (performance.now() - raceStartTime) / 1000;
            
            const isWinner = playerBike.dist >= oppBike.dist;
            const prizeMoney = isWinner ? 500 : 150;
            player.money += prizeMoney;

            // Atualizar Modal de Resultados
            document.getElementById('resTitle').textContent = isWinner ? 'VITÓRIA SENSACIONAL!' : 'DERROTA POR POUCO!';
            document.getElementById('resTitle').className = `font-orbitron font-black text-3xl tracking-wider ${isWinner ? 'text-emerald-400' : 'text-red-400'}`;
            document.getElementById('resTime').textContent = finalTime.toFixed(3) + 's';
            document.getElementById('resSpeed').textContent = maxSpeedReached + ' km/h';
            document.getElementById('resReaction').textContent = reactionTime.toFixed(2) + 's';
            document.getElementById('resPrize').textContent = '+ R$ ' + prizeMoney;

            document.getElementById('modalResult').classList.remove('hidden');
            updatePlayerUI();

            // Salvar no Leaderboard local se for um bom tempo
            saveRecord(MOTOS_DATA[player.currentMotoIdx].name, finalTime, maxSpeedReached);
        }

        /* ==================== RENDERING / DESENHO NO CANVAS ==================== */

        function drawRaceScene() {
            if (!ctx) return;
            const w = canvas.width;
            const h = canvas.height;

            // 1. Céu / Gradiente Urbano Noturno
            const skyGrad = ctx.createLinearGradient(0, 0, 0, h * 0.5);
            skyGrad.addColorStop(0, '#020617');
            skyGrad.addColorStop(1, '#0f172a');
            ctx.fillStyle = skyGrad;
            ctx.fillRect(0, 0, w, h);

            // 2. Cenário ao Fundo (Arquibancadas & Árvores/Postes em Movimento)
            const trackOffset = (playerBike.dist * 20) % w;

            // Arquibancada / Edifícios distantes
            ctx.fillStyle = '#1e293b';
            for (let x = -trackOffset; x < w; x += 120) {
                ctx.fillRect(x, h * 0.25, 80, h * 0.25);
            }

            // Postes de Luz / Cenário
            ctx.strokeStyle = '#334155';
            ctx.lineWidth = 3;
            for (let x = -trackOffset; x < w; x += 180) {
                ctx.beginPath();
                ctx.moveTo(x, h * 0.5);
                ctx.lineTo(x, h * 0.2);
                ctx.stroke();
                
                // Luz Amarela do Poste
                ctx.fillStyle = '#fef08a';
                ctx.beginPath();
                ctx.arc(x, h * 0.2, 5, 0, Math.PI * 2);
                ctx.fill();
            }

            // 3. Asfalto da Pista
            const roadY = h * 0.48;
            const roadHeight = h * 0.52;

            ctx.fillStyle = '#0f172a';
            ctx.fillRect(0, roadY, w, roadHeight);

            // Linhas Brancas Laterais
            ctx.fillStyle = '#cbd5e1';
            ctx.fillRect(0, roadY, w, 4);
            ctx.fillRect(0, h - 10, w, 6);

            // Linha Central Tracejada (Sensação de Velocidade)
            ctx.fillStyle = '#e2e8f0';
            const dashWidth = 40;
            const dashGap = 30;
            const dashOffset = (playerBike.dist * 35) % (dashWidth + dashGap);
            for (let x = -dashOffset; x < w; x += dashWidth + dashGap) {
                ctx.fillRect(x, roadY + roadHeight * 0.48, dashWidth, 4);
            }

            // 4. Marcação de Metros no Asfalto (0m, 100m, 200m, 300m, 420m)
            ctx.font = 'bold 16px Orbitron';
            ctx.fillStyle = 'rgba(255, 255, 255, 0.4)';
            for (let m = 0; m <= TRACK_LENGTH_METERS; m += 100) {
                const markerX = w * 0.15 + (m - playerBike.dist) * (w / 150);
                if (markerX > -50 && markerX < w + 50) {
                    ctx.fillRect(markerX, roadY, 3, roadHeight);
                    ctx.fillText(`${m}M`, markerX + 6, roadY + 25);
                }
            }

            // LINHA DE CHEGADA (420m) - Bandeira Quadriculada
            const finishX = w * 0.15 + (TRACK_LENGTH_METERS - playerBike.dist) * (w / 150);
            if (finishX > -50 && finishX < w + 100) {
                // Desenhar Xadrez da Chegada
                const squareSize = 12;
                for (let ry = roadY; ry < h; ry += squareSize) {
                    for (let rx = 0; rx < 36; rx += squareSize) {
                        const isBlack = (Math.floor(ry / squareSize) + Math.floor(rx / squareSize)) % 2 === 0;
                        ctx.fillStyle = isBlack ? '#000000' : '#ffffff';
                        ctx.fillRect(finishX + rx, ry, squareSize, squareSize);
                    }
                }
                ctx.fillStyle = '#22c55e';
                ctx.fillText('FINISH 420M', finishX - 20, roadY - 10);
            }

            // 5. DESENHAR MOTO DO OPONENTE (Pista de Cima)
            const oppX = w * 0.15 + (oppBike.dist - playerBike.dist) * (w / 150);
            const oppY = roadY + roadHeight * 0.2;
            drawBikeSprite(ctx, oppX, oppY, '#64748b', 'RIVAL');

            // 6. DESENHAR MOTO DO JOGADOR (Pista de Baixo - Fixa no X)
            const playerX = w * 0.15;
            const playerY = roadY + roadHeight * 0.68;
            const currentMotoColor = MOTOS_DATA[player.currentMotoIdx].color;
            drawBikeSprite(ctx, playerX, playerY, currentMotoColor, 'VOCÊ', true);

            // Efeito de Fumaça / Poeira em alta velocidade
            if (raceState === 'RACING' && playerBike.speed > 50) {
                ctx.fillStyle = 'rgba(255, 255, 255, 0.2)';
                for (let i = 0; i < 5; i++) {
                    ctx.beginPath();
                    ctx.arc(playerX - 20 - Math.random() * 30, playerY + 10 + Math.random() * 6, Math.random() * 8, 0, Math.PI * 2);
                    ctx.fill();
                }
            }
        }

        // Desenhar Sprite Vetorial de Moto
        function drawBikeSprite(targetCtx, x, y, mainColor, label, isPlayer = false) {
            targetCtx.save();
            targetCtx.translate(x, y);

            // Sombra
            targetCtx.fillStyle = 'rgba(0, 0, 0, 0.5)';
            targetCtx.beginPath();
            targetCtx.ellipse(15, 12, 35, 6, 0, 0, Math.PI * 2);
            targetCtx.fill();

            // Rodas (Traseira e Dianteira)
            targetCtx.strokeStyle = '#334155';
            targetCtx.lineWidth = 5;
            
            // Roda Traseira
            targetCtx.beginPath();
            targetCtx.arc(-15, 0, 12, 0, Math.PI * 2);
            targetCtx.stroke();

            // Roda Dianteira
            targetCtx.beginPath();
            targetCtx.arc(45, 0, 12, 0, Math.PI * 2);
            targetCtx.stroke();

            // Carenagem da Moto
            targetCtx.fillStyle = mainColor;
            targetCtx.beginPath();
            targetCtx.moveTo(-15, -8);
            targetCtx.lineTo(10, -18);
            targetCtx.lineTo(35, -12);
            targetCtx.lineTo(40, -4);
            targetCtx.lineTo(15, -2);
            targetCtx.closePath();
            targetCtx.fill();

            // Piloto em posição aerodinâmica de arrancada
            targetCtx.fillStyle = '#0f172a'; // Macacão Escuro
            targetCtx.beginPath();
            targetCtx.arc(5, -22, 7, 0, Math.PI * 2); // Capacete
            targetCtx.fill();

            // Corpo debruçado
            targetCtx.strokeStyle = '#0f172a';
            targetCtx.lineWidth = 4;
            targetCtx.beginPath();
            targetCtx.moveTo(5, -15);
            targetCtx.lineTo(25, -10);
            targetCtx.stroke();

            // Rótulo / Nome
            targetCtx.font = 'bold 10px Orbitron';
            targetCtx.fillStyle = isPlayer ? '#38bdf8' : '#cbd5e1';
            targetCtx.fillText(label, -10, -32);

            targetCtx.restore();
        }

        /* ==================== INTERFACE E CONTROLES ==================== */

        function setupKeyboardControls() {
            window.addEventListener('keydown', (e) => {
                if (e.code === 'ArrowUp' || e.code === 'KeyW') {
                    startGas();
                }
                if (e.code === 'Space' || e.code === 'ShiftLeft' || e.code === 'ArrowRight') {
                    triggerShiftGear();
                }
            });

            window.addEventListener('keyup', (e) => {
                if (e.code === 'ArrowUp' || e.code === 'KeyW') {
                    stopGas();
                }
            });
        }

        function startGas(e) {
            if (e) e.preventDefault();
            playerBike.isAccelerating = true;
            document.getElementById('btnGas').classList.add('brightness-125', 'scale-95');

            if (raceState === 'IDLE') {
                startRaceCountdown();
            }
        }

        function stopGas(e) {
            if (e) e.preventDefault();
            playerBike.isAccelerating = false;
            document.getElementById('btnGas').classList.remove('brightness-125', 'scale-95');
        }

        function updateHUD() {
            // Atualizar Velocímetro e Marcha
            document.getElementById('hudSpeed').textContent = Math.round(playerBike.speed);
            document.getElementById('hudGear').textContent = playerBike.gear;

            // Atualizar Cronômetro
            if (raceState === 'RACING') {
                const elapsed = (performance.now() - raceStartTime) / 1000;
                document.getElementById('hudTimer').textContent = elapsed.toFixed(3) + 's';
            } else if (raceState === 'IDLE') {
                document.getElementById('hudTimer').textContent = '00.000s';
            }

            // Atualizar Telemetria de Distância
            const distClamped = Math.min(playerBike.dist, TRACK_LENGTH_METERS);
            document.getElementById('hudDistance').textContent = distClamped.toFixed(1);
            document.getElementById('distanceBar').style.width = `${(distClamped / TRACK_LENGTH_METERS) * 100}%`;

            // Atualizar Barra de RPM / Conta Giros
            const rpmPct = Math.min((playerBike.rpm / 11500) * 100, 100);
            const rpmBar = document.getElementById('rpmBar');
            rpmBar.style.width = `${rpmPct}%`;

            const shiftLight = document.getElementById('shiftLight');

            if (playerBike.rpm >= 8800 && playerBike.rpm <= 10200) {
                // Zona Perfeita de Troca!
                shiftLight.textContent = 'TROQUE DE MARCHA!';
                shiftLight.className = 'px-4 py-1 rounded-md font-orbitron font-extrabold text-xs tracking-wider uppercase bg-emerald-500 text-black glow-green animate-pulse';
            } else if (playerBike.rpm > 10200) {
                // Corte de Giro
                shiftLight.textContent = 'CORTE DE GIRO!';
                shiftLight.className = 'px-4 py-1 rounded-md font-orbitron font-extrabold text-xs tracking-wider uppercase bg-red-600 text-white glow-red';
            } else {
                shiftLight.textContent = 'RPM NORMAL';
                shiftLight.className = 'px-4 py-1 rounded-md font-orbitron font-extrabold text-xs tracking-wider uppercase bg-slate-800 text-slate-500 border border-slate-700';
            }
        }

        function updateTreeLights() {
            const r = document.getElementById('lightRed');
            const y1 = document.getElementById('lightYellow1');
            const y2 = document.getElementById('lightYellow2');
            const g = document.getElementById('lightGreen');

            // Resetar Luzes
            r.className = 'w-6 h-6 sm:w-8 sm:h-8 rounded-full bg-red-950 border-2 border-red-900';
            y1.className = 'w-6 h-6 sm:w-8 sm:h-8 rounded-full bg-amber-950 border-2 border-amber-900';
            y2.className = 'w-6 h-6 sm:w-8 sm:h-8 rounded-full bg-amber-950 border-2 border-amber-900';
            g.className = 'w-6 h-6 sm:w-8 sm:h-8 rounded-full bg-emerald-950 border-2 border-emerald-900';

            if (treeStep >= 1) y1.className = 'w-6 h-6 sm:w-8 sm:h-8 rounded-full bg-amber-400 border-2 border-white glow-cyan';
            if (treeStep >= 2) y2.className = 'w-6 h-6 sm:w-8 sm:h-8 rounded-full bg-amber-400 border-2 border-white glow-cyan';
            if (treeStep >= 3) r.className = 'w-6 h-6 sm:w-8 sm:h-8 rounded-full bg-red-500 border-2 border-white glow-red';
            if (treeStep >= 4) g.className = 'w-6 h-6 sm:w-8 sm:h-8 rounded-full bg-emerald-500 border-2 border-white glow-green';
        }

        function updateStatusMsg(title, titleClass, subtitle) {
            const st = document.getElementById('statusTitle');
            const ss = document.getElementById('statusSubtitle');
            st.textContent = title;
            st.className = `font-orbitron font-black text-2xl sm:text-4xl drop-shadow-[0_4px_8px_rgba(0,0,0,0.8)] tracking-wider ${titleClass}`;
            ss.textContent = subtitle;
        }

        /* ==================== TELA DA GARAGEM & COMPRAS ==================== */

        function cycleMoto(dir) {
            player.currentMotoIdx = (player.currentMotoIdx + dir + MOTOS_DATA.length) % MOTOS_DATA.length;
            updateGarageView();
        }

        function updateGarageView() {
            const moto = MOTOS_DATA[player.currentMotoIdx];

            document.getElementById('garageMotoName').textContent = moto.name;
            document.getElementById('garageMotoDesc').textContent = moto.desc;
            document.getElementById('statPower').textContent = `${moto.basePower} CV`;
            document.getElementById('statTopSpeed').textContent = `${moto.topSpeed} km/h`;

            document.getElementById('barPower').style.width = `${(moto.basePower / 90) * 100}%`;
            document.getElementById('barTopSpeed').style.width = `${(moto.topSpeed / 280) * 100}%`;

            const btn = document.getElementById('btnSelectOrBuyMoto');
            if (moto.unlocked) {
                btn.textContent = 'MOTO EQUIPADA';
                btn.className = 'w-full py-3 bg-emerald-600 hover:bg-emerald-500 font-orbitron font-bold rounded-xl transition shadow-lg text-center';
            } else {
                btn.textContent = `COMPRAR POR R$ ${moto.price}`;
                btn.className = 'w-full py-3 bg-gradient-to-r from-amber-500 to-amber-700 hover:from-amber-400 hover:to-amber-600 font-orbitron font-bold rounded-xl transition shadow-lg text-center text-black';
            }

            drawGaragePreview();
        }

        function handleMotoAction() {
            const moto = MOTOS_DATA[player.currentMotoIdx];
            if (!moto.unlocked) {
                if (player.money >= moto.price) {
                    player.money -= moto.price;
                    moto.unlocked = true;
                    updatePlayerUI();
                    updateGarageView();
                    showFloatingText("MOTO ADQUIRIDA COM SUCESSO!", "#22c55e");
                } else {
                    showFloatingText("SALDO INSUFICIENTE!", "#ef4444");
                }
            }
        }

        function buyUpgrade(type) {
            const prices = { exhaust: 400, tires: 600, turbo: 1200 };
            const price = prices[type];

            if (player.upgrades[type]) {
                showFloatingText("PEÇA JÁ EQUIPADA!", "#38bdf8");
                return;
            }

            if (player.money >= price) {
                player.money -= price;
                player.upgrades[type] = true;

                const btnId = type === 'exhaust' ? 'btnUpgradeExhaust' : type === 'tires' ? 'btnUpgradeTires' : 'btnUpgradeTurbo';
                const btn = document.getElementById(btnId);
                btn.textContent = 'EQUIPADO';
                btn.className = 'px-3 py-1.5 bg-emerald-500/20 text-emerald-400 font-bold text-xs rounded-lg border border-emerald-500/40 cursor-default';

                updatePlayerUI();
                updateGarageView();
                showFloatingText("UPGRADE INSTALADO!", "#22c55e");
            } else {
                showFloatingText("SALDO INSUFICIENTE!", "#ef4444");
            }
        }

        function drawGaragePreview() {
            if (!garageCtx) return;
            const w = garageCanvas.width;
            const h = garageCanvas.height;

            garageCtx.clearRect(0, 0, w, h);
            const currentMoto = MOTOS_DATA[player.currentMotoIdx];

            drawBikeSprite(garageCtx, w * 0.4, h * 0.7, currentMoto.color, currentMoto.name, true);
        }

        function updatePlayerUI() {
            document.getElementById('playerMoney').textContent = `R$ ${player.money.toLocaleString('pt-BR')}`;
            const unlockedCount = MOTOS_DATA.filter(m => m.unlocked).length;
            document.getElementById('garageCount').textContent = `${unlockedCount} / ${MOTOS_DATA.length} Desbloqueadas`;
        }

        /* ==================== MULTIPLAYER & RIVAIS / LEADERBOARD ==================== */

        function populateOpponentsSelect() {
            const select = document.getElementById('selectOpponent');
            select.innerHTML = '';
            OPPONENTS.forEach((opp, idx) => {
                const opt = document.createElement('option');
                opt.value = idx;
                opt.textContent = `${opp.name}`;
                select.appendChild(opt);
            });
            changeOpponent();
        }

        function changeOpponent() {
            const idx = document.getElementById('selectOpponent').value;
            selectedOpponent = OPPONENTS[idx];
            const moto = MOTOS_DATA.find(m => m.id === selectedOpponent.motoId) || MOTOS_DATA[1];
            document.getElementById('oppMotoName').textContent = moto.name;
        }

        function saveRecord(motoName, time, speed) {
            let records = JSON.parse(localStorage.getItem('autoGiroLeaderboard')) || [
                { name: 'José Daniel', moto: 'Hornet 600', time: 9.820, speed: 210 },
                { name: 'Pedro Eduardo', moto: 'Hornet 600', time: 9.940, speed: 208 },
                { name: 'Pedro Lucas', moto: 'Titan 160 Fuçada', time: 11.150, speed: 162 },
                { name: 'Ana Cláudia', moto: 'Titan 160 Fuçada', time: 11.420, speed: 159 },
                { name: 'Maria Juliana', moto: 'Pop 110 Sport', time: 13.110, speed: 122 },
                { name: 'Ryan Valdemir', moto: 'Pop 110 Sport', time: 13.450, speed: 119 }
            ];

            records.push({
                name: 'Você (Piloto)',
                moto: motoName,
                time: parseFloat(time.toFixed(3)),
                speed: speed
            });

            // Ordenar por menor tempo
            records.sort((a, b) => a.time - b.time);
            records = records.slice(0, 8); // Manter top 8

            localStorage.setItem('autoGiroLeaderboard', JSON.stringify(records));
            loadLeaderboard();
        }

        function loadLeaderboard() {
            const tbody = document.getElementById('leaderboardBody');
            tbody.innerHTML = '';

            let records = JSON.parse(localStorage.getItem('autoGiroLeaderboard')) || [
                { name: 'José Daniel', moto: 'Hornet 600', time: 9.820, speed: 210 },
                { name: 'Pedro Eduardo', moto: 'Hornet 600', time: 9.940, speed: 208 },
                { name: 'Pedro Lucas', moto: 'Titan 160 Fuçada', time: 11.150, speed: 162 },
                { name: 'Ana Cláudia', moto: 'Titan 160 Fuçada', time: 11.420, speed: 159 },
                { name: 'Maria Juliana', moto: 'Pop 110 Sport', time: 13.110, speed: 122 },
                { name: 'Ryan Valdemir', moto: 'Pop 110 Sport', time: 13.450, speed: 119 }
            ];

            records.forEach((rec, idx) => {
                const tr = document.createElement('tr');
                const isTop3 = idx < 3;
                const medal = idx === 0 ? '🥇' : idx === 1 ? '🥈' : idx === 2 ? '🥉' : `#${idx + 1}`;

                tr.className = idx % 2 === 0 ? 'bg-slate-900/40' : 'bg-slate-900/80';
                tr.innerHTML = `
                    <td class="py-3 px-3 font-orbitron ${isTop3 ? 'text-amber-400 font-bold' : 'text-slate-400'}">${medal}</td>
                    <td class="py-3 px-3 font-bold text-slate-200">${rec.name}</td>
                    <td class="py-3 px-3 text-slate-400 text-xs">${rec.moto}</td>
                    <td class="py-3 px-3 text-right font-orbitron text-amber-400 font-bold">${rec.time.toFixed(3)}s</td>
                    <td class="py-3 px-3 text-right font-orbitron text-cyan-400">${rec.speed} km/h</td>
                `;
                tbody.appendChild(tr);
            });
        }

        /* ==================== NAVEGAÇÃO DE ABAS ==================== */

        function switchTab(tabName) {
            document.getElementById('tabRace').classList.add('hidden');
            document.getElementById('tabGarage').classList.add('hidden');
            document.getElementById('tabRanking').classList.add('hidden');

            document.getElementById('btnNavRace').className = 'px-3 py-1.5 rounded-lg bg-slate-800 hover:bg-slate-700 text-slate-200 font-bold text-sm transition border border-slate-700';
            document.getElementById('btnNavGarage').className = 'px-3 py-1.5 rounded-lg bg-slate-800 hover:bg-slate-700 text-slate-200 font-bold text-sm transition border border-slate-700';
            document.getElementById('btnNavRank').className = 'px-3 py-1.5 rounded-lg bg-slate-800 hover:bg-slate-700 text-slate-200 font-bold text-sm transition border border-slate-700';

            if (tabName === 'race') {
                document.getElementById('tabRace').classList.remove('hidden');
                document.getElementById('btnNavRace').className = 'px-3 py-1.5 rounded-lg bg-cyan-600 hover:bg-cyan-500 text-white font-bold text-sm transition flex items-center gap-1.5 glow-cyan';
                resizeCanvas();
            } else if (tabName === 'garage') {
                document.getElementById('tabGarage').classList.remove('hidden');
                document.getElementById('tabGarage').classList.add('flex');
                document.getElementById('btnNavGarage').className = 'px-3 py-1.5 rounded-lg bg-cyan-600 hover:bg-cyan-500 text-white font-bold text-sm transition flex items-center gap-1.5 glow-cyan';
                updateGarageView();
            } else if (tabName === 'ranking') {
                document.getElementById('tabRanking').classList.remove('hidden');
                document.getElementById('tabRanking').classList.add('flex');
                document.getElementById('btnNavRank').className = 'px-3 py-1.5 rounded-lg bg-cyan-600 hover:bg-cyan-500 text-white font-bold text-sm transition flex items-center gap-1.5 glow-cyan';
                loadLeaderboard();
            }
        }

        /* ==================== UTILITÁRIOS ==================== */

        function showFloatingText(text, color) {
            const statusSubtitle = document.getElementById('statusSubtitle');
            if (statusSubtitle) {
                statusSubtitle.textContent = text;
                statusSubtitle.style.color = color;
                setTimeout(() => {
                    statusSubtitle.style.color = '#e2e8f0';
                }, 1500);
            }
        }
    </script>
</body>
</html>
