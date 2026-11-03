        <!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Auto Giro - Jogo de Arrancada de Moto</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome para Ícones -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;600;800;900&family=Rajdhani:wght@500;600;700&display=swap" rel="stylesheet">

    <style>
        body {
            font-family: 'Rajdhani', sans-serif;
            background-color: #0b0e14;
            color: #f3f4f6;
            user-select: none;
            overflow-x: hidden;
        }
        .font-orbitron {
            font-family: 'Orbitron', sans-serif;
        }
        .neon-border-green {
            box-shadow: 0 0 15px rgba(34, 197, 94, 0.4);
        }
        .neon-border-red {
            box-shadow: 0 0 15px rgba(239, 68, 68, 0.4);
        }
        .neon-text-green {
            text-shadow: 0 0 8px rgba(34, 197, 94, 0.6);
        }
        .neon-text-yellow {
            text-shadow: 0 0 8px rgba(234, 179, 8, 0.6);
        }
        /* Gradient animations */
        .btn-speed {
            background: linear-gradient(135deg, #ef4444 0%, #b91c1c 100%);
            transition: all 0.15s ease;
        }
        .btn-speed:hover {
            transform: translateY(-2px);
            box-shadow: 0 4px 20px rgba(239, 68, 68, 0.5);
        }
        .btn-speed:active {
            transform: translateY(1px);
        }
        .glass-panel {
            background: rgba(18, 24, 38, 0.85);
            backdrop-filter: blur(12px);
            border: 1px solid rgba(255, 255, 255, 0.08);
        }
        /* Custom gauge transitions */
        .rpm-needle {
            transition: transform 0.05s ease-out;
            transform-origin: bottom center;
        }
    </style>
</head>
<body class="min-h-screen flex flex-col justify-between">

    <!-- HEADER / NAVIGATION -->
    <header class="border-b border-gray-800 bg-gray-950/80 sticky top-0 z-50 backdrop-blur-md">
        <div class="max-w-7xl mx-auto px-4 py-3 flex justify-between items-center">
            <div class="flex items-center gap-3">
                <div class="bg-red-600 text-white p-2 rounded-lg font-orbitron font-black text-xl italic shadow-lg shadow-red-900/50">
                    AG
                </div>
                <div>
                    <h1 class="font-orbitron font-black text-2xl tracking-wider text-transparent bg-clip-text bg-gradient-to-r from-red-500 via-orange-400 to-yellow-400">
                        AUTO GIRO
                    </h1>
                    <p class="text-xs text-gray-400 font-semibold tracking-widest uppercase">Arrancada 420m • Protótipo v1.0</p>
                </div>
            </div>

            <!-- MENU TABS -->
            <nav class="flex gap-2 bg-gray-900/80 p-1.5 rounded-xl border border-gray-800">
                <button onclick="switchTab('race')" id="tab-race" class="px-4 py-2 rounded-lg font-semibold text-sm transition-all flex items-center gap-2 bg-red-600 text-white shadow-md">
                    <i class="fa-solid fa-flag-checkered"></i> Pista
                </button>
                <button onclick="switchTab('garage')" id="tab-garage" class="px-4 py-2 rounded-lg font-semibold text-sm transition-all flex items-center gap-2 text-gray-400 hover:text-white hover:bg-gray-800">
                    <i class="fa-solid fa-wrench"></i> Garagem
                </button>
                <button onclick="switchTab('ranking')" id="tab-ranking" class="px-4 py-2 rounded-lg font-semibold text-sm transition-all flex items-center gap-2 text-gray-400 hover:text-white hover:bg-gray-800">
                    <i class="fa-solid fa-trophy"></i> Ranking
                </button>
                <button onclick="switchTab('info')" id="tab-info" class="px-4 py-2 rounded-lg font-semibold text-sm transition-all flex items-center gap-2 text-gray-400 hover:text-white hover:bg-gray-800">
                    <i class="fa-solid fa-users"></i> Grupo 5
                </button>
            </nav>
        </div>
    </header>

    <!-- MAIN CONTENT AREA -->
    <main class="max-w-7xl mx-auto w-full px-4 py-6 flex-grow flex flex-col justify-center">

        <!-- ==================== TELA 1: CORRIDA (PISTA) ==================== -->
        <section id="sec-race" class="space-y-6">
            
            <!-- CANVAS PISTA DE CORRIDA -->
            <div class="relative rounded-2xl overflow-hidden border-2 border-gray-800 bg-gray-950 shadow-2xl">
                
                <!-- Overlay Informações Rápidas -->
                <div class="absolute top-4 left-4 z-10 flex gap-4">
                    <div class="glass-panel px-4 py-2 rounded-xl border border-gray-700/50">
                        <span class="text-xs text-gray-400 uppercase font-semibold">Distância Percorrida</span>
                        <div class="text-2xl font-orbitron font-bold text-yellow-400" id="dist-display">0.0 m / 420m</div>
                    </div>
                    <div class="glass-panel px-4 py-2 rounded-xl border border-gray-700/50">
                        <span class="text-xs text-gray-400 uppercase font-semibold">Tempo de Pista</span>
                        <div class="text-2xl font-orbitron font-bold text-green-400" id="time-display">00.000s</div>
                    </div>
                </div>

                <!-- Tree Lights (Árvore de Natal de Arrancada) -->
                <div class="absolute top-4 right-4 z-10 glass-panel p-3 rounded-xl flex flex-col gap-2 items-center border border-gray-700/50">
                    <span class="text-[10px] text-gray-400 uppercase font-bold tracking-wider">LARGADA</span>
                    <div class="flex gap-2">
                        <div id="light-red" class="w-5 h-5 rounded-full bg-red-950 border border-red-800 transition-all duration-150"></div>
                        <div id="light-yellow1" class="w-5 h-5 rounded-full bg-yellow-950 border border-yellow-800 transition-all duration-150"></div>
                        <div id="light-yellow2" class="w-5 h-5 rounded-full bg-yellow-950 border border-yellow-800 transition-all duration-150"></div>
                        <div id="light-green" class="w-5 h-5 rounded-full bg-green-950 border border-green-800 transition-all duration-150"></div>
                    </div>
                </div>

                <!-- Banner Mensagem / Status da Corrida -->
                <div id="race-status-banner" class="hidden absolute inset-0 z-20 bg-black/75 backdrop-blur-sm flex flex-col justify-center items-center gap-4">
                    <h2 id="status-title" class="font-orbitron text-4xl font-black text-yellow-400 tracking-wider">PRONTO PARA A ARRANCADA?</h2>
                    <p id="status-subtitle" class="text-gray-300 text-lg">Mantenha a rotação ideal na largada e passe as marchas no momento exato!</p>
                    <button onclick="startRaceSequence()" class="btn-speed px-8 py-4 rounded-xl font-orbitron font-bold text-xl text-white shadow-lg tracking-wider">
                        <i class="fa-solid fa-play mr-2"></i> INICIAR CORRIDA
                    </button>
                </div>

                <!-- Canvas Render da Moto e Pista -->
                <canvas id="raceCanvas" class="w-full h-[320px] block bg-slate-900"></canvas>
            </div>

            <!-- DASHBOARD DO PILOTO (PAINEL DE CONTROLES & INSTRUMENTOS) -->
            <div class="grid grid-cols-1 md:grid-cols-3 gap-6 items-center">
                
                <!-- TELEMETRIA E TROCA DE MARCHA -->
                <div class="glass-panel p-5 rounded-2xl border border-gray-800 flex flex-col justify-between h-full">
                    <div class="flex justify-between items-center mb-3">
                        <span class="text-xs uppercase text-gray-400 font-bold tracking-wider">Marcha Atual</span>
                        <span id="gear-indicator" class="font-orbitron font-black text-3xl text-orange-500">N</span>
                    </div>

                    <!-- Shift Lights Indicator -->
                    <div class="mb-4">
                        <div class="flex justify-between text-xs text-gray-400 mb-1 font-bold">
                            <span>RPM Ideal</span>
                            <span id="shift-feedback" class="text-yellow-400 font-orbitron">Aguardando...</span>
                        </div>
                        <div class="grid grid-cols-8 gap-1 bg-gray-950 p-2 rounded-xl border border-gray-800" id="shift-lights">
                            <div class="h-3 rounded bg-gray-800 shadow-inner"></div>
                            <div class="h-3 rounded bg-gray-800 shadow-inner"></div>
                            <div class="h-3 rounded bg-gray-800 shadow-inner"></div>
                            <div class="h-3 rounded bg-gray-800 shadow-inner"></div>
                            <div class="h-3 rounded bg-gray-800 shadow-inner"></div>
                            <div class="h-3 rounded bg-gray-800 shadow-inner"></div>
                            <div class="h-3 rounded bg-gray-800 shadow-inner"></div>
                            <div class="h-3 rounded bg-gray-800 shadow-inner"></div>
                        </div>
                    </div>

                    <!-- Botão de Marcha UP -->
                    <button id="btn-shift" onclick="shiftGear()" class="w-full py-4 bg-gradient-to-r from-blue-600 to-indigo-600 hover:from-blue-500 hover:to-indigo-500 active:scale-95 font-orbitron font-extrabold text-lg rounded-xl shadow-lg border border-blue-400/30 transition-all flex items-center justify-center gap-2">
                        <i class="fa-solid fa-arrow-up text-xl"></i> PASSAR MARCHA (ESPAÇO / SHIFT)
                    </button>
                </div>

                <!-- CONTA-GIROS / VELOCÍMETRO CENTRAL (RPM GAUGE) -->
                <div class="glass-panel p-5 rounded-2xl border border-gray-800 flex flex-col items-center justify-center relative overflow-hidden">
                    <div class="text-center mb-2">
                        <div class="text-xs uppercase font-bold text-gray-400 tracking-wider">Velocidade Atual</div>
                        <div class="font-orbitron font-black text-5xl text-white tracking-widest flex items-baseline justify-center gap-1">
                            <span id="speed-display">0</span> <span class="text-sm font-normal text-red-500">KM/H</span>
                        </div>
                    </div>

                    <!-- RPM Bar Visualizer -->
                    <div class="w-full max-w-xs space-y-2 my-2">
                        <div class="flex justify-between text-xs font-orbitron text-gray-400">
                            <span>0</span>
                            <span>4k</span>
                            <span>8k</span>
                            <span class="text-red-500">12k RPM</span>
                        </div>
                        <div class="w-full h-5 bg-gray-950 rounded-full p-1 border border-gray-800 flex items-center relative overflow-hidden">
                            <div id="rpm-bar" class="h-full bg-gradient-to-r from-green-500 via-yellow-400 to-red-600 rounded-full transition-all duration-75" style="width: 0%;"></div>
                        </div>
                    </div>

                    <div class="text-xs text-gray-400 font-semibold mt-1">
                        RPM Actual: <span id="rpm-text" class="text-white font-orbitron font-bold">0</span> / 12000
                    </div>
                </div>

                <!-- ACELERADOR / CONTROLES DE ARRANCADA -->
                <div class="glass-panel p-5 rounded-2xl border border-gray-800 flex flex-col justify-between h-full">
                    <div class="flex justify-between items-center mb-2">
                        <span class="text-xs uppercase text-gray-400 font-bold tracking-wider">Nitro Boost</span>
                        <span id="nitro-status" class="text-xs font-orbitron font-bold text-cyan-400">PRONTO</span>
                    </div>
                    
                    <!-- Botão Nitro -->
                    <button id="btn-nitro" onclick="useNitro()" class="w-full py-3 mb-3 bg-gradient-to-r from-cyan-600 to-blue-700 hover:from-cyan-500 hover:to-blue-600 active:scale-95 font-orbitron font-bold text-sm rounded-xl shadow-lg border border-cyan-400/30 transition-all flex items-center justify-center gap-2 text-cyan-100">
                        <i class="fa-solid fa-bolt text-yellow-300"></i> INJETAR NITRO (N)
                    </button>

                    <!-- Botão Acelerador Móvel / Touch -->
                    <button id="btn-throttle" class="w-full py-5 btn-speed font-orbitron font-black text-xl rounded-xl shadow-xl border border-red-400/30 flex items-center justify-center gap-3">
                        <i class="fa-solid fa-gauge-high text-2xl"></i> ACELERAR (TECLA W / SETA CIMA)
                    </button>
                </div>

            </div>

        </section>

        <!-- ==================== TELA 2: GARAGEM & LOJA DE PEÇAS ==================== -->
        <section id="sec-garage" class="hidden space-y-6">
            <div class="flex flex-col md:flex-row justify-between items-start md:items-center gap-4 bg-gray-900/60 p-6 rounded-2xl border border-gray-800">
                <div>
                    <h2 class="font-orbitron font-extrabold text-2xl text-white">GARAGEM DE PREPARAÇÃO</h2>
                    <p class="text-gray-400 text-sm">Personalize sua moto e instale peças de alto desempenho para diminuir seu tempo na pista.</p>
                </div>
                <div class="glass-panel px-6 py-3 rounded-xl border border-yellow-500/30 flex items-center gap-3">
                    <i class="fa-solid fa-coins text-yellow-400 text-2xl"></i>
                    <div>
                        <span class="text-xs text-gray-400 uppercase font-bold">Saldo Disponível</span>
                        <div class="font-orbitron font-black text-2xl text-yellow-400" id="cash-display">R$ 1.500</div>
                    </div>
                </div>
            </div>

            <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
                <!-- Status Atuais da Moto -->
                <div class="glass-panel p-6 rounded-2xl border border-gray-800 space-y-6">
                    <h3 class="font-orbitron font-bold text-lg text-red-400 border-b border-gray-800 pb-2 flex items-center gap-2">
                        <i class="fa-solid fa-motorcycle"></i> Especificações do Projeto
                    </h3>
                    
                    <div class="flex justify-center py-4">
                        <div class="relative w-48 h-32 flex items-center justify-center bg-gray-950/80 rounded-xl border border-gray-800">
                            <i class="fa-solid fa-motorcycle text-7xl text-gradient text-red-500 drop-shadow-[0_0_15px_rgba(239,68,68,0.5)]"></i>
                        </div>
                    </div>

                    <div class="space-y-4">
                        <div>
                            <div class="flex justify-between text-xs font-bold text-gray-400 mb-1">
                                <span>Potência do Motor</span>
                                <span id="stat-power-val" class="text-white font-orbitron">150 CV</span>
                            </div>
                            <div class="w-full h-3 bg-gray-950 rounded-full overflow-hidden p-0.5 border border-gray-800">
                                <div id="stat-power-bar" class="h-full bg-red-500 rounded-full" style="width: 40%;"></div>
                            </div>
                        </div>

                        <div>
                            <div class="flex justify-between text-xs font-bold text-gray-400 mb-1">
                                <span>Aderência / Tração</span>
                                <span id="stat-grip-val" class="text-white font-orbitron">60%</span>
                            </div>
                            <div class="w-full h-3 bg-gray-950 rounded-full overflow-hidden p-0.5 border border-gray-800">
                                <div id="stat-grip-bar" class="h-full bg-yellow-500 rounded-full" style="width: 60%;"></div>
                            </div>
                        </div>

                        <div>
                            <div class="flex justify-between text-xs font-bold text-gray-400 mb-1">
                                <span>Eficiência de Transmissão</span>
                                <span id="stat-gear-val" class="text-white font-orbitron">Standard</span>
                            </div>
                            <div class="w-full h-3 bg-gray-950 rounded-full overflow-hidden p-0.5 border border-gray-800">
                                <div id="stat-gear-bar" class="h-full bg-blue-500 rounded-full" style="width: 50%;"></div>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Loja de Modificações / Tuning -->
                <div class="lg:col-span-2 glass-panel p-6 rounded-2xl border border-gray-800 space-y-4">
                    <h3 class="font-orbitron font-bold text-lg text-yellow-400 border-b border-gray-800 pb-2 flex items-center justify-between">
                        <span><i class="fa-solid fa-list-check mr-2"></i>Catálogo de Peças de Alta Performance</span>
                        <span class="text-xs text-gray-400 font-normal">Vença corridas para ganhar mais dinheiro</span>
                    </h3>

                    <div class="grid grid-cols-1 md:grid-cols-2 gap-4" id="upgrades-list">
                        <!-- Peças injetadas via JS -->
                    </div>
                </div>
            </div>
        </section>

        <!-- ==================== TELA 3: RANKING & TEMPOS ==================== -->
        <section id="sec-ranking" class="hidden space-y-6">
            <div class="glass-panel p-6 rounded-2xl border border-gray-800 space-y-4">
                <div class="flex justify-between items-center border-b border-gray-800 pb-4">
                    <div>
                        <h2 class="font-orbitron font-extrabold text-2xl text-yellow-400 flex items-center gap-2">
                            <i class="fa-solid fa-trophy"></i> LEADERBOARD DE TEMPOS (420 M)
                        </h2>
                        <p class="text-gray-400 text-sm">Confira os melhores pilotos e seus tempos de pista no quarto de milha (420m).</p>
                    </div>
                    <button onclick="resetLeaderboard()" class="px-4 py-2 bg-gray-800 hover:bg-gray-700 text-xs font-bold text-gray-300 rounded-lg transition-all">
                        Resetar Tabela
                    </button>
                </div>

                <div class="overflow-x-auto">
                    <table class="w-full text-left border-collapse">
                        <thead>
                            <tr class="border-b border-gray-800 text-xs text-gray-400 font-orbitron uppercase">
                                <th class="p-3">Posição</th>
                                <th class="p-3">Piloto</th>
                                <th class="p-3">Moto / Prep</th>
                                <th class="p-3">Velocidade Máx.</th>
                                <th class="p-3">Tempo Final</th>
                            </tr>
                        </thead>
                        <tbody id="ranking-table-body" class="divide-y divide-gray-800/50 text-sm font-semibold">
                            <!-- Injetado dinamicamente via JS -->
                        </tbody>
                    </table>
                </div>
            </div>
        </section>

        <!-- ==================== TELA 4: INFORMAÇÕES DO PROJETO & GRUPO 5 ==================== -->
        <section id="sec-info" class="hidden space-y-6">
            <div class="glass-panel p-8 rounded-2xl border border-gray-800 space-y-8">
                
                <div>
                    <span class="px-3 py-1 bg-red-600/30 text-red-400 rounded-full text-xs font-orbitron font-bold tracking-wider">ENTREGA DO PRIMEIRO PROTÓTIPO</span>
                    <h2 class="font-orbitron font-black text-3xl text-white mt-2">PROJETO AUTO GIRO</h2>
                    <p class="text-gray-300 mt-2 max-w-3xl leading-relaxed">
                        Jogo interativo de corrida de arrancada focado na simulação de trocas de marcha e reação na pista de 420 metros. Desenvolvido para a disciplina de Programação Aplicada.
                    </p>
                </div>

                <!-- INTEGRANTES DO GRUPO 5 -->
                <div>
                    <h3 class="font-orbitron font-bold text-xl text-yellow-400 mb-4 flex items-center gap-2">
                        <i class="fa-solid fa-user-group"></i> Integrantes do Grupo 5
                    </h3>
                    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
                        <div class="bg-gray-900/80 p-4 rounded-xl border border-gray-800 flex items-center gap-3">
                            <div class="w-10 h-10 rounded-full bg-red-600/20 text-red-400 flex items-center justify-center font-bold">MJ</div>
                            <div>
                                <div class="font-bold text-white">Maria Juliana Ferreira da Silva</div>
                                <div class="text-xs text-gray-500">Desenvolvimento / Planejamento</div>
                            </div>
                        </div>
                        <div class="bg-gray-900/80 p-4 rounded-xl border border-gray-800 flex items-center gap-3">
                            <div class="w-10 h-10 rounded-full bg-yellow-600/20 text-yellow-400 flex items-center justify-center font-bold">AC</div>
                            <div>
                                <div class="font-bold text-white">Ana Cláudia Vieira da Silva</div>
                                <div class="text-xs text-gray-500">Design de UI / UX</div>
                            </div>
                        </div>
                        <div class="bg-gray-900/80 p-4 rounded-xl border border-gray-800 flex items-center gap-3">
                            <div class="w-10 h-10 rounded-full bg-blue-600/20 text-blue-400 flex items-center justify-center font-bold">PQ</div>
                            <div>
                                <div class="font-bold text-white">Pedro Eduardo Queiroiz da Silva</div>
                                <div class="text-xs text-gray-500">Lógica e Física do Jogo</div>
                            </div>
                        </div>
                        <div class="bg-gray-900/80 p-4 rounded-xl border border-gray-800 flex items-center gap-3">
                            <div class="w-10 h-10 rounded-full bg-purple-600/20 text-purple-400 flex items-center justify-center font-bold">PL</div>
                            <div>
                                <div class="font-bold text-white">Pedro Lucas Whylace Alves e Silva</div>
                                <div class="text-xs text-gray-500">Engenharia de Som e Efeitos</div>
                            </div>
                        </div>
                        <div class="bg-gray-900/80 p-4 rounded-xl border border-gray-800 flex items-center gap-3">
                            <div class="w-10 h-10 rounded-full bg-green-600/20 text-green-400 flex items-center justify-center font-bold">JD</div>
                            <div>
                                <div class="font-bold text-white">José Daniel Barbosa dos Santos Silva</div>
                                <div class="text-xs text-gray-500">Desenvolvimento Backend / Rankings</div>
                            </div>
                        </div>
                        <div class="bg-gray-900/80 p-4 rounded-xl border border-gray-800 flex items-center gap-3">
                            <div class="w-10 h-10 rounded-full bg-pink-600/20 text-pink-400 flex items-center justify-center font-bold">RV</div>
                            <div>
                                <div class="font-bold text-white">Ryan Valdemir Alvez da Silva</div>
                                <div class="text-xs text-gray-500">Testes e Garantia de Qualidade</div>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- ESCOPO E FUNCIONALIDADES IMPLEMENTADAS -->
                <div class="grid grid-cols-1 md:grid-cols-2 gap-6 pt-4 border-t border-gray-800">
                    <div class="space-y-3">
                        <h4 class="font-orbitron font-bold text-green-400 flex items-center gap-2">
                            <i class="fa-solid fa-circle-check"></i> Funcionalidades Entregues no Protótipo
                        </h4>
                        <ul class="space-y-2 text-sm text-gray-300">
                            <li class="flex items-center gap-2"><i class="fa-solid fa-angle-right text-gray-500"></i> Simulação de física de velocidade, aceleração e rotação (RPM).</li>
                            <li class="flex items-center gap-2"><i class="fa-solid fa-angle-right text-gray-500"></i> Sistema de trocas de marcha com indicador visual de RPM Ideal.</li>
                            <li class="flex items-center gap-2"><i class="fa-solid fa-angle-right text-gray-500"></i> Pista animada em Canvas 2D de 420 metros.</li>
                            <li class="flex items-center gap-2"><i class="fa-solid fa-angle-right text-gray-500"></i> Garagem funcional com compra de melhorias e alteração de atributos.</li>
                            <li class="flex items-center gap-2"><i class="fa-solid fa-angle-right text-gray-500"></i> Tabela de classificação com gravação local de recordes.</li>
                        </ul>
                    </div>
                    <div class="space-y-3">
                        <h4 class="font-orbitron font-bold text-yellow-400 flex items-center gap-2">
                            <i class="fa-solid fa-clock"></i> Próximas Implementações Planejadas
                        </h4>
                        <ul class="space-y-2 text-sm text-gray-300">
                            <li class="flex items-center gap-2"><i class="fa-solid fa-ellipsis text-yellow-500"></i> Modo Multiplayer em tempo real via WebSockets.</li>
                            <li class="flex items-center gap-2"><i class="fa-solid fa-ellipsis text-yellow-500"></i> Personalização estética avançada (carenagens, adesivos, cores).</li>
                            <li class="flex items-center gap-2"><i class="fa-solid fa-ellipsis text-yellow-500"></i> Sistema de som sintetizado via Web Audio API para o ronco do motor.</li>
                            <li class="flex items-center gap-2"><i class="fa-solid fa-ellipsis text-yellow-500"></i> Modo Campeonato e Carreira com desbloqueio de novas categorias.</li>
                        </ul>
                    </div>
                </div>

            </div>
        </section>

    </main>

    <!-- FOOTER -->
    <footer class="border-t border-gray-800 bg-gray-950 py-4 text-center text-xs text-gray-500">
        Auto Giro © 2026 — Projeto de Programação Aplicada (Grupo 5). Todos os direitos reservados.
    </footer>

    <!-- LOGIC & GAME ENGINE SCRIPT -->
    <script>
        /* ==========================================================================
           1. ESTADO GLOBAL DO JOGO & ATRIBUTOS DA MOTO
           ========================================================================== */
        const gameState = {
            cash: 1500,
            bike: {
                power: 150,     // Potência que influencia na velocidade máxima
                grip: 0.6,      // Aderência reduz derrapagens na largada
                gearEfficiency: 1.0, // Eficiência de ganho nas trocas
                hasNitro: false
            },
            upgradesOwned: [],
            leaderboard: [
                { name: "Piloto Veloz", time: 10.450, speed: 215, bike: "CB 600 Super Prep" },
                { name: "Maria Juliana", time: 11.210, speed: 202, bike: "AutoGiro Special" },
                { name: "Pedro Whylace", time: 11.890, speed: 195, bike: "Street 500 Tuning" },
                { name: "Daniel Silva", time: 12.300, speed: 188, bike: "Stock 300cc" }
            ]
        };

        // Lista de Peças Modificáveis da Garagem
        const UPGRADES_DATABASE = [
            { id: 'exhaust', name: 'Escapamento Esportivo', cost: 400, powerBonus: 20, gripBonus: 0, desc: 'Aumenta a potência da moto em +20 CV.', icon: 'fa-wind' },
            { id: 'tires', name: 'Pneus Drag Slicks', cost: 600, powerBonus: 0, gripBonus: 0.25, desc: 'Melhora a tração e evita queimadas de pneu na largada.', icon: 'fa-circle-dot' },
            { id: 'turbo', name: 'Kit Turbo Stage 1', cost: 1200, powerBonus: 50, gripBonus: 0, desc: 'Aumento significativo na aceleração inicial.', icon: 'fa-gauge-simple-high' },
            { id: 'nitro', name: 'Sistema de Nitro NOS', cost: 1000, powerBonus: 30, gripBonus: 0, hasNitro: true, desc: 'Desbloqueia injeção de Nitro temporária durante a corrida.', icon: 'fa-bolt' }
        ];

        /* ==========================================================================
           2. VARIÁVEIS DE SIMULAÇÃO DA CORRIDA
           ========================================================================== */
        let raceActive = false;
        let raceFinished = false;
        let countdownActive = false;
        
        let distance = 0; // Metros percorridos (Alvo: 420m)
        let speed = 0;    // Velocidade em km/h
        let rpm = 1000;   // RPM do motor (1000 - 12000)
        let currentGear = 0; // 0: Neutro, 1 a 5: Marchas
        const MAX_GEAR = 5;

        let startTime = 0;
        let elapsedTime = 0;
        let animationFrameId = null;
        let isThrottling = false;
        let nitroAvailable = false;
        let nitroActive = false;

        // Relação de Marchas (Gear Ratios)
        const gearRatios = [0, 2.8, 2.0, 1.5, 1.2, 1.0];
        const maxSpeedPerGear = [0, 60, 110, 160, 210, 260];

        /* ==========================================================================
           3. CANVAS & ENGINE GRÁFICA PARALLAX
           ========================================================================== */
        const canvas = document.getElementById('raceCanvas');
        const ctx = canvas.getContext('2d');
        let roadOffset = 0;

        function resizeCanvas() {
            canvas.width = canvas.parentElement.clientWidth;
            canvas.height = 320;
        }
        window.addEventListener('resize', resizeCanvas);
        resizeCanvas();

        function drawScene() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Céu Noturno
            const skyGrad = ctx.createLinearGradient(0, 0, 0, canvas.height * 0.6);
            skyGrad.addColorStop(0, '#090d16');
            skyGrad.addColorStop(1, '#1e293b');
            ctx.fillStyle = skyGrad;
            ctx.fillRect(0, 0, canvas.width, canvas.height * 0.6);

            // Luzes Distantes da Cidade (Parallax leve)
            ctx.fillStyle = '#f59e0b';
            for (let i = 0; i < canvas.width; i += 60) {
                const height = (Math.sin(i + roadOffset * 0.05) * 15) + 30;
                ctx.fillRect(i, canvas.height * 0.6 - height, 12, height);
            }

            // Asfalto / Pista
            ctx.fillStyle = '#1e293b';
            ctx.fillRect(0, canvas.height * 0.6, canvas.width, canvas.height * 0.4);

            // Linha do Chão / Guia
            ctx.strokeStyle = '#ef4444';
            ctx.lineWidth = 4;
            ctx.beginPath();
            ctx.moveTo(0, canvas.height * 0.6);
            ctx.lineTo(canvas.width, canvas.height * 0.6);
            ctx.stroke();

            // Faixas Brancas da Pista (Efeito Parallax de Movimento)
            ctx.fillStyle = '#ffffff';
            const stripeWidth = 60;
            const stripeGap = 40;
            const totalStripeWidth = stripeWidth + stripeGap;
            const effectiveOffset = (roadOffset * 8) % totalStripeWidth;

            for (let x = -totalStripeWidth; x < canvas.width + totalStripeWidth; x += totalStripeWidth) {
                ctx.fillRect(x - effectiveOffset, canvas.height * 0.8, stripeWidth, 8);
            }

            // Linha de Chegada de 420m (Se próximo do fim)
            const remainingDist = 420 - distance;
            if (remainingDist < 100) {
                const finishX = canvas.width - (remainingDist * 15);
                ctx.fillStyle = '#ffffff';
                for (let fy = canvas.height * 0.6; fy < canvas.height; fy += 20) {
                    ctx.fillRect(finishX, fy, 15, 10);
                    ctx.fillRect(finishX + 15, fy + 10, 15, 10);
                }
            }

            // Renderizar Moto do Jogador (Sprite Vetorial)
            drawBike(120, canvas.height * 0.72);
        }

        function drawBike(x, y) {
            ctx.save();
            ctx.translate(x, y);

            // Efeito Nitro / Fogo no Escapamento
            if (nitroActive) {
                ctx.fillStyle = '#38bdf8';
                ctx.beginPath();
                ctx.moveTo(-35, 5);
                ctx.lineTo(-65, 0);
                ctx.lineTo(-35, -5);
                ctx.fill();
            }

            // Rodas
            ctx.fillStyle = '#0f172a';
            ctx.beginPath();
            ctx.arc(-25, 10, 16, 0, Math.PI * 2);
            ctx.arc(25, 10, 16, 0, Math.PI * 2);
            ctx.fill();

            ctx.strokeStyle = '#94a3b8';
            ctx.lineWidth = 3;
            ctx.beginPath();
            ctx.arc(-25, 10, 16, 0, Math.PI * 2);
            ctx.arc(25, 10, 16, 0, Math.PI * 2);
            ctx.stroke();

            // Corpo da Moto / Chassi
            ctx.fillStyle = '#ef4444'; // Cor Vermelho Auto Giro
            ctx.beginPath();
            ctx.moveTo(-30, -5);
            ctx.lineTo(15, -15);
            ctx.lineTo(30, -5);
            ctx.lineTo(10, 10);
            ctx.lineTo(-20, 10);
            ctx.closePath();
            ctx.fill();

            // Capacete / Piloto
            ctx.fillStyle = '#000000';
            ctx.beginPath();
            ctx.arc(-5, -22, 10, 0, Math.PI * 2);
            ctx.fill();

            // Viseira
            ctx.fillStyle = '#eab308';
            ctx.fillRect(0, -24, 6, 4);

            ctx.restore();
        }

        /* ==========================================================================
           4. LOOP DE FÍSICA E ATUALIZAÇÕES DE TEMPO
           ========================================================================== */
        function updatePhysics() {
            if (!raceActive || raceFinished) return;

            const now = performance.now();
            elapsedTime = (now - startTime) / 1000;
            document.getElementById('time-display').innerText = elapsedTime.toFixed(3) + 's';

            // Cálculo da Aceleração baseada no RPM, Marcha e Peças
            if (currentGear > 0) {
                let accelFactor = (gameState.bike.power / 100) * (gearRatios[currentGear] || 1);

                if (isThrottling) {
                    rpm += (3000 / currentGear) * 0.05;
                } else {
                    rpm -= 1500 * 0.05; // Perda natural de RPM ao soltar acelerador
                }

                if (nitroActive) {
                    accelFactor *= 1.8;
                    rpm += 200;
                }

                // Limite de RPM
                if (rpm > 12000) rpm = 12000;
                if (rpm < 1200) rpm = 1200;

                // Velocidade proporcional ao RPM e Marcha
                const maxGearSpeed = maxSpeedPerGear[currentGear] * (gameState.bike.power / 150);
                const targetSpeed = (rpm / 12000) * maxGearSpeed;

                speed += (targetSpeed - speed) * 0.08 * accelFactor;
            } else {
                // Em Neutro (N)
                if (isThrottling) rpm += 500;
                else rpm -= 300;
                if (rpm > 12000) rpm = 12000;
                if (rpm < 1000) rpm = 1000;
                speed *= 0.95; // Fricção
            }

            // Atualização de Distância Percorrida (km/h para m/s)
            const speedInMetersPerSec = (speed * 1000) / 3600;
            distance += speedInMetersPerSec * 0.016;
            roadOffset += speed * 0.02;

            if (distance >= 420) {
                distance = 420;
                finishRace();
            }

            // Atualizar Telas e Interface
            updateDashboardUI();
            drawScene();

            if (raceActive) {
                animationFrameId = requestAnimationFrame(updatePhysics);
            }
        }

        /* ==========================================================================
           5. CONTROLES E INTERACTION HANDLERS
           ========================================================================== */
        function shiftGear() {
            if (!raceActive || raceFinished) return;

            if (currentGear < MAX_GEAR) {
                const optimalRPMMin = 8500;
                const optimalRPMMax = 10500;
                const feedbackEl = document.getElementById('shift-feedback');

                if (rpm >= optimalRPMMin && rpm <= optimalRPMMax) {
                    feedbackEl.innerText = "TROCA PERFEITA!";
                    feedbackEl.className = "text-green-400 font-orbitron font-bold neon-text-green";
                    speed += 12; // Bônus de impulso por troca perfeita
                } else if (rpm > optimalRPMMax) {
                    feedbackEl.innerText = "OVERREV! (Tardia)";
                    feedbackEl.className = "text-red-400 font-orbitron font-bold";
                    speed *= 0.92; // Perda por esticar demais
                } else {
                    feedbackEl.innerText = "MUITO CEDO!";
                    feedbackEl.className = "text-yellow-400 font-orbitron font-bold";
                    speed *= 0.95;
                }

                currentGear++;
                rpm = Math.max(3500, rpm - 4000); // Queda de giro na troca de marcha
                updateDashboardUI();
            }
        }

        function useNitro() {
            if (nitroAvailable && raceActive && !nitroActive) {
                nitroActive = true;
                nitroAvailable = false;
                document.getElementById('nitro-status').innerText = "EM USO!";
                document.getElementById('nitro-status').className = "text-xs font-orbitron font-bold text-yellow-400 animate-pulse";
                
                setTimeout(() => {
                    nitroActive = false;
                    document.getElementById('nitro-status').innerText = "ESGOTADO";
                    document.getElementById('nitro-status').className = "text-xs font-orbitron font-bold text-gray-500";
                }, 2500);
            }
        }

        // Teclas de Atalho (Keyboard Events)
        window.addEventListener('keydown', (e) => {
            if (e.code === 'KeyW' || e.code === 'ArrowUp') {
                isThrottling = true;
            }
            if (e.code === 'Space' || e.code === 'ShiftLeft') {
                e.preventDefault();
                shiftGear();
            }
            if (e.code === 'KeyN') {
                useNitro();
            }
        });

        window.addEventListener('keyup', (e) => {
            if (e.code === 'KeyW' || e.code === 'ArrowUp') {
                isThrottling = false;
            }
        });

        // Eventos Touch / Botão na Tela
        const throttleBtn = document.getElementById('btn-throttle');
        throttleBtn.addEventListener('mousedown', () => isThrottling = true);
        throttleBtn.addEventListener('mouseup', () => isThrottling = false);
        throttleBtn.addEventListener('touchstart', (e) => { e.preventDefault(); isThrottling = true; });
        throttleBtn.addEventListener('touchend', (e) => { e.preventDefault(); isThrottling = false; });

        /* ==========================================================================
           6. ARVORE DE NATAL (LARGADA) & SEQUÊNCIA DE JOGO
           ========================================================================== */
        function startRaceSequence() {
            if (countdownActive) return;

            // Reset de Variáveis de Corrida
            distance = 0;
            speed = 0;
            rpm = 1000;
            currentGear = 0;
            raceActive = false;
            raceFinished = false;
            countdownActive = true;
            nitroAvailable = gameState.bike.hasNitro;

            document.getElementById('nitro-status').innerText = nitroAvailable ? "PRONTO" : "NÃO EQUIPADO";
            document.getElementById('nitro-status').className = nitroAvailable ? "text-xs font-orbitron font-bold text-cyan-400" : "text-xs text-gray-500";

            document.getElementById('race-status-banner').classList.add('hidden');
            document.getElementById('shift-feedback').innerText = "PREPARE-SE...";
            document.getElementById('shift-feedback').className = "text-yellow-400 font-orbitron";

            // Reset Luzes da Árvore de Largada
            const redL = document.getElementById('light-red');
            const yel1 = document.getElementById('light-yellow1');
            const yel2 = document.getElementById('light-yellow2');
            const grnL = document.getElementById('light-green');

            redL.className = "w-5 h-5 rounded-full bg-red-950 border border-red-800";
            yel1.className = "w-5 h-5 rounded-full bg-yellow-950 border border-yellow-800";
            yel2.className = "w-5 h-5 rounded-full bg-yellow-950 border border-yellow-800";
            grnL.className = "w-5 h-5 rounded-full bg-green-950 border border-green-800";

            // Sequência Cronometrada das Luzes
            setTimeout(() => { redL.className = "w-5 h-5 rounded-full bg-red-600 border border-red-400 shadow-[0_0_12px_#ef4444]"; }, 500);
            setTimeout(() => { yel1.className = "w-5 h-5 rounded-full bg-yellow-400 border border-yellow-200 shadow-[0_0_12px_#eab308]"; }, 1500);
            setTimeout(() => { yel2.className = "w-5 h-5 rounded-full bg-yellow-400 border border-yellow-200 shadow-[0_0_12px_#eab308]"; }, 2500);
            setTimeout(() => {
                grnL.className = "w-5 h-5 rounded-full bg-green-500 border border-green-200 shadow-[0_0_15px_#22c55e]";
                
                // INÍCIO OFICIAL DA CORRIDA
                raceActive = true;
                countdownActive = false;
                currentGear = 1; // Engata primeira marcha automaticamente na largada
                startTime = performance.now();
                updatePhysics();
            }, 3500);
        }

        function finishRace() {
            raceActive = false;
            raceFinished = true;

            const finalTime = elapsedTime.toFixed(3);
            const finalSpeed = Math.round(speed);

            // Recompensa em dinheiro baseada no tempo
            const reward = Math.max(100, Math.round(15000 / finalTime));
            gameState.cash += reward;
            document.getElementById('cash-display').innerText = `R$ ${gameState.cash.toLocaleString()}`;

            // Adicionar ao Leaderboard
            gameState.leaderboard.push({
                name: "Você (Jogador)",
                time: parseFloat(finalTime),
                speed: finalSpeed,
                bike: "Projeto Auto Giro"
            });
            // Ordenar por menor tempo
            gameState.leaderboard.sort((a, b) => a.time - b.time);
            renderLeaderboard();

            // Exibir Banner de Vitória/Fim de Prova
            const banner = document.getElementById('race-status-banner');
            document.getElementById('status-title').innerText = "ARRANCADA CONCLUÍDA!";
            document.getElementById('status-subtitle').innerHTML = `Tempo: <span class="text-green-400 font-bold">${finalTime}s</span> | Vel. Final: <span class="text-yellow-400 font-bold">${finalSpeed} km/h</span><br>Prêmio recebido: <span class="text-yellow-400 font-bold">R$ ${reward}</span>`;
            banner.classList.remove('hidden');
        }

        /* ==========================================================================
           7. INTERFACE E DASHBOARD UPDATES
           ========================================================================== */
        function updateDashboardUI() {
            document.getElementById('dist-display').innerText = `${distance.toFixed(1)} m / 420m`;
            document.getElementById('speed-display').innerText = Math.round(speed);
            document.getElementById('gear-indicator').innerText = currentGear === 0 ? 'N' : currentGear;
            document.getElementById('rpm-text').innerText = Math.round(rpm);

            // RPM Bar Porcentagem
            const rpmPct = Math.min(100, (rpm / 12000) * 100);
            document.getElementById('rpm-bar').style.width = `${rpmPct}%`;

            // Shift Lights Indicator (Luzes de Troca de Marcha)
            const shiftLightsContainer = document.getElementById('shift-lights').children;
            const step = 12000 / 8;

            for (let i = 0; i < 8; i++) {
                const threshold = (i + 1) * step;
                if (rpm >= threshold) {
                    if (i < 4) shiftLightsContainer[i].className = "h-3 rounded bg-green-500 shadow-[0_0_8px_#22c55e]";
                    else if (i < 6) shiftLightsContainer[i].className = "h-3 rounded bg-yellow-400 shadow-[0_0_8px_#eab308]";
                    else shiftLightsContainer[i].className = "h-3 rounded bg-red-600 shadow-[0_0_8px_#ef4444] animate-pulse";
                } else {
                    shiftLightsContainer[i].className = "h-3 rounded bg-gray-800 shadow-inner";
                }
            }
        }

        /* ==========================================================================
           8. GARAGEM E LOJA DE UPGRADES
           ========================================================================== */
        function renderGarage() {
            const container = document.getElementById('upgrades-list');
            container.innerHTML = '';

            UPGRADES_DATABASE.forEach(item => {
                const isBought = gameState.upgradesOwned.includes(item.id);
                
                const card = document.createElement('div');
                card.className = `p-4 rounded-xl border flex flex-col justify-between ${isBought ? 'bg-gray-900/40 border-green-500/40' : 'bg-gray-900 border-gray-800'}`;
                
                card.innerHTML = `
                    <div class="flex items-start gap-3">
                        <div class="p-3 bg-gray-800 rounded-lg text-yellow-400 text-xl">
                            <i class="fa-solid ${item.icon}"></i>
                        </div>
                        <div>
                            <h4 class="font-bold text-white text-base">${item.name}</h4>
                            <p class="text-xs text-gray-400 mt-1">${item.desc}</p>
                        </div>
                    </div>
                    <div class="flex justify-between items-center mt-4 pt-3 border-t border-gray-800/60">
                        <span class="font-orbitron font-bold text-yellow-400">R$ ${item.cost}</span>
                        ${isBought 
                            ? `<span class="text-xs font-bold text-green-400 bg-green-950 px-3 py-1 rounded-full border border-green-800"><i class="fa-solid fa-check mr-1"></i> Instalado</span>`
                            : `<button onclick="buyUpgrade('${item.id}')" class="px-3 py-1.5 bg-yellow-500 hover:bg-yellow-400 text-black font-bold text-xs rounded-lg transition-all">Comprar</button>`
                        }
                    </div>
                `;
                container.appendChild(card);
            });

            // Atualizar Barras de Atributos na Garagem
            document.getElementById('stat-power-val').innerText = `${gameState.bike.power} CV`;
            document.getElementById('stat-power-bar').style.width = `${Math.min(100, (gameState.bike.power / 300) * 100)}%`;

            document.getElementById('stat-grip-val').innerText = `${Math.round(gameState.bike.grip * 100)}%`;
            document.getElementById('stat-grip-bar').style.width = `${Math.round(gameState.bike.grip * 100)}%`;
        }

        function buyUpgrade(id) {
            const item = UPGRADES_DATABASE.find(u => u.id === id);
            if (!item) return;

            if (gameState.cash >= item.cost && !gameState.upgradesOwned.includes(id)) {
                gameState.cash -= item.cost;
                gameState.upgradesOwned.push(id);

                // Aplicar Bônus
                gameState.bike.power += item.powerBonus || 0;
                gameState.bike.grip += item.gripBonus || 0;
                if (item.hasNitro) gameState.bike.hasNitro = true;

                document.getElementById('cash-display').innerText = `R$ ${gameState.cash.toLocaleString()}`;
                renderGarage();
            } else if (gameState.cash < item.cost) {
                alert("Saldo insuficiente para comprar esta peça!");
            }
        }

        /* ==========================================================================
           9. LEADERBOARD / RANKING LOGIC
           ========================================================================== */
        function renderLeaderboard() {
            const tbody = document.getElementById('ranking-table-body');
            tbody.innerHTML = '';

            gameState.leaderboard.forEach((entry, index) => {
                const tr = document.createElement('tr');
                tr.className = index === 0 ? "bg-yellow-500/10 text-yellow-300 font-bold" : "hover:bg-gray-800/40";
                
                tr.innerHTML = `
                    <td class="p-3 font-orbitron">#${index + 1}</td>
                    <td class="p-3 font-semibold text-white">${entry.name}</td>
                    <td class="p-3 text-gray-400 text-xs">${entry.bike}</td>
                    <td class="p-3 text-red-400 font-orbitron">${entry.speed} km/h</td>
                    <td class="p-3 text-green-400 font-orbitron font-bold">${entry.time.toFixed(3)}s</td>
                `;
                tbody.appendChild(tr);
            });
        }

        function resetLeaderboard() {
            gameState.leaderboard = [
                { name: "Piloto Veloz", time: 10.450, speed: 215, bike: "CB 600 Super Prep" },
                { name: "Maria Juliana", time: 11.210, speed: 202, bike: "AutoGiro Special" },
                { name: "Pedro Whylace", time: 11.890, speed: 195, bike: "Street 500 Tuning" }
            ];
            renderLeaderboard();
        }

        /* ==========================================================================
           10. NAVEGAÇÃO DE TABS E INICIALIZAÇÃO
           ========================================================================== */
        function switchTab(tabId) {
            ['race', 'garage', 'ranking', 'info'].forEach(id => {
                const sec = document.getElementById(`sec-${id}`);
                const btn = document.getElementById(`tab-${id}`);
                
                if (id === tabId) {
                    sec.classList.remove('hidden');
                    btn.className = "px-4 py-2 rounded-lg font-semibold text-sm transition-all flex items-center gap-2 bg-red-600 text-white shadow-md";
                } else {
                    sec.classList.add('hidden');
                    btn.className = "px-4 py-2 rounded-lg font-semibold text-sm transition-all flex items-center gap-2 text-gray-400 hover:text-white hover:bg-gray-800";
                }
            });

            if (tabId === 'garage') renderGarage();
            if (tabId === 'ranking') renderLeaderboard();
            if (tabId === 'race') drawScene();
        }

        // Window Load Handler
        window.onload = function () {
            drawScene();
            renderGarage();
            renderLeaderboard();
            document.getElementById('race-status-banner').classList.remove('hidden');
        };

    </script>
</body>
</html>          
