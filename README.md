<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>AUTO GIRO 3D - Simulador de Arrancada (Grupo 5)</title>
  <!-- Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- FontAwesome Icons -->
  <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" rel="stylesheet">
  <!-- Three.js Library -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@400;600;800;900&family=Rajdhani:wght@500;600;700&display=swap');

    body {
      font-family: 'Rajdhani', sans-serif;
      background-color: #030712;
      color: #f3f4f6;
      overflow-x: hidden;
      user-select: none;
    }

    .font-orbitron {
      font-family: 'Orbitron', sans-serif;
    }

    .btn-action {
      transition: all 0.15s cubic-bezier(0.4, 0, 0.2, 1);
    }
    .btn-action:active {
      transform: translateY(2px) scale(0.97);
    }

    #webgl-container {
      width: 100%;
      height: 100%;
      position: absolute;
      top: 0;
      left: 0;
      z-index: 1;
    }

    .ui-overlay {
      position: relative;
      z-index: 10;
      pointer-events: none;
    }

    .interactive-ui {
      pointer-events: auto;
    }

    /* Custom Glassmorphism */
    .glass-panel {
      background: rgba(15, 23, 42, 0.85);
      backdrop-filter: blur(12px);
      -webkit-backdrop-filter: blur(12px);
      border: 1px solid rgba(51, 65, 85, 0.6);
    }
  </style>
</head>
<body class="min-h-screen flex flex-col justify-between bg-slate-950 text-slate-100 overflow-hidden">

  <!-- TOP NAVIGATION HEADER -->
  <header class="glass-panel sticky top-0 z-50 px-4 py-3 border-b border-slate-800">
    <div class="max-w-7xl mx-auto flex flex-wrap items-center justify-between gap-3">
      
      <!-- Logo -->
      <div class="flex items-center space-x-3">
        <div class="bg-gradient-to-tr from-cyan-500 via-teal-400 to-emerald-400 p-2.5 rounded-xl shadow-lg shadow-cyan-500/20">
          <i class="fa-solid fa-cube text-2xl text-slate-950"></i>
        </div>
        <div>
          <h1 class="font-orbitron text-2xl font-black italic tracking-wider bg-gradient-to-r from-cyan-400 via-teal-300 to-emerald-400 bg-clip-text text-transparent">
            AUTO GIRO 3D
          </h1>
          <p class="text-[10px] text-slate-400 font-semibold tracking-widest uppercase">Simulador de Moto 3D — Grupo 5</p>
        </div>
      </div>

      <!-- Account Badge -->
      <div class="flex items-center space-x-3 bg-slate-950 p-2 px-3.5 rounded-xl border border-slate-800">
        <div class="w-9 h-9 rounded-full bg-gradient-to-br from-cyan-500 to-emerald-400 flex items-center justify-center font-orbitron font-extrabold text-slate-950 text-xs shadow-md" id="navAvatar">
          G5
        </div>
        <div class="text-left">
          <div id="navDriverName" class="text-xs font-orbitron font-bold text-slate-200">Piloto_G5</div>
          <div class="text-[10px] text-cyan-400 font-bold" id="navStats">R$ <span id="navCash">2.500</span> | NVL <span id="navLevel">1</span></div>
        </div>
        <button onclick="openAuthModal()" class="ml-2 p-1.5 text-slate-400 hover:text-cyan-400 transition-colors" title="Perfil e Configurações">
          <i class="fa-solid fa-user-gear text-base"></i>
        </button>
      </div>

      <!-- Menu Tabs -->
      <nav class="flex items-center space-x-1 bg-slate-950/90 p-1 rounded-xl border border-slate-800">
        <button id="tabGarageBtn" onclick="switchTab('garage')" class="px-3.5 py-2 rounded-lg text-xs font-bold transition-all bg-cyan-500 text-slate-950 font-orbitron shadow-md">
          <i class="fa-solid fa-warehouse mr-1.5"></i> GARAGEM 3D
        </button>
        <button id="tabRaceBtn" onclick="switchTab('race')" class="px-3.5 py-2 rounded-lg text-xs font-bold transition-all text-slate-400 hover:text-slate-200 font-orbitron">
          <i class="fa-solid fa-flag-checkered mr-1.5"></i> PISTA 420M
        </button>
        <button id="tabLobbyBtn" onclick="switchTab('lobby')" class="px-3.5 py-2 rounded-lg text-xs font-bold transition-all text-slate-400 hover:text-slate-200 font-orbitron">
          <i class="fa-solid fa-globe mr-1.5"></i> RANKING
        </button>
        <button id="tabTeamBtn" onclick="switchTab('team')" class="px-3.5 py-2 rounded-lg text-xs font-bold transition-all text-slate-400 hover:text-slate-200 font-orbitron">
          <i class="fa-solid fa-users mr-1.5"></i> GRUPO 5
        </button>
      </nav>

    </div>
  </header>

  <!-- MAIN VIEWPORT CONTAINER FOR CANVAS & UI OVERLAYS -->
  <main class="relative flex-grow w-full max-w-7xl mx-auto h-[calc(100vh-80px)] flex flex-col justify-between p-3 sm:p-4">

    <!-- THREE.JS CANVAS CONTAINER -->
    <div id="webgl-container" class="rounded-2xl overflow-hidden border border-slate-800 shadow-2xl"></div>

    <!-- UI OVERLAY CONTENT -->
    <div class="ui-overlay h-full flex flex-col justify-between">

      <!-- TAB 1: 3D GARAGE VIEW OVERLAY -->
      <section id="viewGarage" class="h-full flex flex-col justify-between p-2">
        
        <!-- Bike Selector & Specs Top Card -->
        <div class="interactive-ui flex flex-wrap justify-between items-start gap-4">
          <div class="glass-panel p-4 rounded-2xl max-w-md w-full shadow-2xl space-y-2">
            <div class="flex justify-between items-center">
              <span id="selectedBikeCategory" class="text-[10px] font-orbitron font-extrabold text-cyan-400 tracking-wider uppercase">STREET 160CC</span>
              <span id="selectedBikePower" class="px-2.5 py-0.5 bg-cyan-950 text-cyan-300 border border-cyan-800 rounded-lg text-[11px] font-orbitron font-bold">52 HP</span>
            </div>
            <h2 id="selectedBikeTitle" class="text-2xl font-orbitron font-black text-white">HONDA TITAN 160 FUÇADA</h2>
            <p id="selectedBikeDesc" class="text-xs text-slate-400">Motor preparado, cinto de tração, aro esportivo e corte de giro no limite.</p>
            
            <!-- Specs Breakdown -->
            <div class="grid grid-cols-3 gap-2 pt-2 border-t border-slate-800 text-[11px] font-orbitron">
              <div>
                <span class="text-slate-500 block">VEL. MÁX</span>
                <strong id="statTopSpeed" class="text-yellow-400">185 km/h</strong>
              </div>
              <div>
                <span class="text-slate-500 block">TORQUE</span>
                <strong id="statTorque" class="text-emerald-400">4.8 kgfm</strong>
              </div>
              <div>
                <span class="text-slate-500 block">CILINDRADA</span>
                <strong id="statEngine" class="text-cyan-400">160 cc</strong>
              </div>
            </div>
          </div>

          <!-- Bike Selection Cards Strip -->
          <div class="glass-panel p-3 rounded-2xl flex space-x-2 overflow-x-auto max-w-xl" id="bikes3dStrip">
            <!-- Dynamically Rendered -->
          </div>
        </div>

        <!-- 3D Customization & Controls Bottom Panel -->
        <div class="interactive-ui grid grid-cols-1 md:grid-cols-3 gap-4 items-end">
          
          <!-- Painting Customization -->
          <div class="glass-panel p-4 rounded-2xl space-y-3">
            <h3 class="text-xs font-orbitron font-bold text-cyan-400 uppercase tracking-wider flex items-center">
              <i class="fa-solid fa-palette mr-2"></i>Pintura & Estilo 3D
            </h3>

            <div class="space-y-2 text-xs font-orbitron">
              <div>
                <span class="text-slate-400 block mb-1">Carenagem Principal:</span>
                <div class="flex space-x-2">
                  <button onclick="setBikeColor3D(0x06b6d4)" class="w-6 h-6 rounded-full bg-cyan-500 border border-white/50 hover:scale-110 transition-transform"></button>
                  <button onclick="setBikeColor3D(0xef4444)" class="w-6 h-6 rounded-full bg-red-500 border border-white/50 hover:scale-110 transition-transform"></button>
                  <button onclick="setBikeColor3D(0x10b981)" class="w-6 h-6 rounded-full bg-emerald-500 border border-white/50 hover:scale-110 transition-transform"></button>
                  <button onclick="setBikeColor3D(0xeab308)" class="w-6 h-6 rounded-full bg-yellow-500 border border-white/50 hover:scale-110 transition-transform"></button>
                  <button onclick="setBikeColor3D(0xa855f7)" class="w-6 h-6 rounded-full bg-purple-500 border border-white/50 hover:scale-110 transition-transform"></button>
                  <button onclick="setBikeColor3D(0x0f172a)" class="w-6 h-6 rounded-full bg-slate-900 border border-white/50 hover:scale-110 transition-transform"></button>
                </div>
              </div>

              <div>
                <span class="text-slate-400 block mb-1">Rodas & Aros:</span>
                <div class="flex space-x-2">
                  <button onclick="setWheelColor3D(0xe2e8f0)" class="w-6 h-6 rounded-full bg-slate-200 border border-white/50 hover:scale-110 transition-transform"></button>
                  <button onclick="setWheelColor3D(0xeab308)" class="w-6 h-6 rounded-full bg-yellow-500 border border-white/50 hover:scale-110 transition-transform"></button>
                  <button onclick="setWheelColor3D(0x020617)" class="w-6 h-6 rounded-full bg-slate-950 border border-white/50 hover:scale-110 transition-transform"></button>
                  <button onclick="setWheelColor3D(0xef4444)" class="w-6 h-6 rounded-full bg-red-500 border border-white/50 hover:scale-110 transition-transform"></button>
                </div>
              </div>

              <div>
                <span class="text-slate-400 block mb-1">Traje do Piloto:</span>
                <div class="flex space-x-2">
                  <button onclick="setRiderColor3D(0xf97316)" class="w-6 h-6 rounded-full bg-orange-500 border border-white/50 hover:scale-110 transition-transform"></button>
                  <button onclick="setRiderColor3D(0x3b82f6)" class="w-6 h-6 rounded-full bg-blue-500 border border-white/50 hover:scale-110 transition-transform"></button>
                  <button onclick="setRiderColor3D(0x10b981)" class="w-6 h-6 rounded-full bg-emerald-500 border border-white/50 hover:scale-110 transition-transform"></button>
                  <button onclick="setRiderColor3D(0xec4899)" class="w-6 h-6 rounded-full bg-pink-500 border border-white/50 hover:scale-110 transition-transform"></button>
                </div>
              </div>
            </div>
          </div>

          <!-- Camera Angle Toggle -->
          <div class="glass-panel p-4 rounded-2xl text-center space-y-2">
            <span class="text-xs font-orbitron font-bold text-slate-300 block uppercase">Ângulo da Câmera 3D</span>
            <div class="grid grid-cols-2 gap-2">
              <button onclick="setCameraView('chase')" class="py-2 bg-slate-800 hover:bg-slate-700 font-orbitron text-xs rounded-xl font-bold border border-slate-700">
                <i class="fa-solid fa-video mr-1"></i> VISÃO TRASEIRA
              </button>
              <button onclick="setCameraView('side')" class="py-2 bg-slate-800 hover:bg-slate-700 font-orbitron text-xs rounded-xl font-bold border border-slate-700">
                <i class="fa-solid fa-film mr-1"></i> VISÃO LATERAL
              </button>
            </div>
          </div>

          <!-- Enter Track Button -->
          <button onclick="switchTab('race')" class="w-full py-4 bg-gradient-to-r from-cyan-500 via-teal-400 to-emerald-400 text-slate-950 font-orbitron font-black text-lg rounded-2xl shadow-xl shadow-cyan-500/20 btn-action flex items-center justify-center space-x-2">
            <span>IR PARA A PISTA 420M</span>
            <i class="fa-solid fa-play"></i>
          </button>

        </div>
      </section>

      <!-- TAB 2: 3D RACE TRACK HUD & CONTROLS -->
      <section id="viewRace" class="hidden h-full flex-col justify-between p-2">
        
        <!-- Live HUD Telemetry Header -->
        <div class="interactive-ui grid grid-cols-2 sm:grid-cols-4 gap-2 font-orbitron">
          <div class="glass-panel p-2.5 rounded-xl text-center border border-slate-800">
            <span class="text-[9px] text-slate-400 block font-bold">DISTÂNCIA</span>
            <span id="hudDistance" class="text-xl font-black text-cyan-400">0.0 <span class="text-xs text-slate-400">/ 420m</span></span>
          </div>

          <div class="glass-panel p-2.5 rounded-xl text-center border border-slate-800">
            <span class="text-[9px] text-slate-400 block font-bold">TEMPO</span>
            <span id="hudTimer" class="text-xl font-black text-emerald-400">0.000 <span class="text-xs text-slate-400">s</span></span>
          </div>

          <div class="glass-panel p-2.5 rounded-xl text-center border border-slate-800">
            <span class="text-[9px] text-slate-400 block font-bold">VELOCIDADE</span>
            <span id="hudSpeed" class="text-xl font-black text-yellow-400">0 <span class="text-xs text-slate-400">km/h</span></span>
          </div>

          <div class="glass-panel p-2.5 rounded-xl text-center border border-slate-800">
            <span class="text-[9px] text-slate-400 block font-bold">MARCHA</span>
            <span id="hudGear" class="text-xl font-black text-purple-400">N</span>
          </div>
        </div>

        <!-- Shift Alert & Tree Notification Overlay -->
        <div class="flex flex-col items-center space-y-2 my-auto">
          <div id="shiftFeedback" class="glass-panel px-6 py-2 rounded-xl font-orbitron font-black text-base opacity-0 transition-all pointer-events-none shadow-2xl border">
            PREPARE-SE
          </div>

          <div id="redlineAlert" class="hidden bg-red-600/90 text-white font-orbitron font-black text-xs px-4 py-1.5 rounded-full animate-bounce shadow-lg border border-red-400">
            💥 CORTE DE GIRO! TROQUE A MARCHA!
          </div>
        </div>

        <!-- Bottom Controls & Tachometer Bar -->
        <div class="interactive-ui grid grid-cols-1 lg:grid-cols-3 gap-3 items-end">
          
          <!-- Downshift Button -->
          <button id="btnShiftDown" onclick="handleShiftGear(-1)" class="glass-panel py-4 text-yellow-400 hover:bg-slate-800 font-orbitron font-black text-sm rounded-2xl btn-action flex items-center justify-center space-x-2 shadow-lg">
            <i class="fa-solid fa-arrow-left text-xl"></i>
            <span>REDUZIR (←)</span>
          </button>

          <!-- Digital RPM Tachometer Gauge -->
          <div class="glass-panel p-3 rounded-2xl flex flex-col items-center">
            <div class="w-full flex justify-between items-center text-[11px] font-orbitron mb-1.5">
              <span class="text-slate-400">RPM: <strong id="rpmDigit" class="text-white font-bold">1200</strong></span>
              <span class="text-emerald-400 font-bold">PONTO DOCE: 8500-10500</span>
            </div>

            <div class="relative w-full h-5 bg-slate-950 rounded-full border border-slate-800 overflow-hidden p-0.5">
              <div id="rpmBar" class="h-full rounded-full bg-gradient-to-r from-cyan-500 via-emerald-400 via-yellow-400 to-red-600 transition-all duration-75" style="width: 10%"></div>
              <div class="absolute top-0 bottom-0 left-[75%] w-[15%] bg-emerald-400/25 border-x border-emerald-400 pointer-events-none"></div>
            </div>
          </div>

          <!-- Upshift & Throttle Controls -->
          <div class="grid grid-cols-2 gap-2">
            <button id="btnShiftUp" onclick="handleShiftGear(1)" class="py-4 bg-gradient-to-r from-emerald-600 to-teal-500 hover:from-emerald-500 text-slate-950 font-orbitron font-black text-sm rounded-2xl shadow-lg btn-action flex items-center justify-center space-x-1">
              <span>SUBIR (→)</span>
              <i class="fa-solid fa-arrow-right text-xl"></i>
            </button>

            <button id="btnThrottle" class="py-4 bg-gradient-to-r from-cyan-500 to-blue-600 hover:from-cyan-400 text-slate-950 font-orbitron font-black text-sm rounded-2xl shadow-lg btn-action flex items-center justify-center space-x-1">
              <i class="fa-solid fa-arrow-up text-xl"></i>
              <span>ACELERAR</span>
            </button>
          </div>

        </div>

        <!-- Finish Race Telemetry Overlay -->
        <div id="finishOverlay" class="hidden fixed inset-0 bg-slate-950/90 backdrop-blur-md flex items-center justify-center z-50 p-4">
          <div class="glass-panel p-6 sm:p-8 rounded-3xl max-w-md w-full shadow-2xl text-center space-y-4">
            <div id="finishBadge" class="text-3xl font-orbitron font-black text-emerald-400">
              VITÓRIA!
            </div>
            
            <p class="text-xs text-slate-300 font-orbitron">Telemetria de Arrancada 3D — 420 Metros</p>

            <div class="grid grid-cols-2 gap-2.5 bg-slate-950 p-4 rounded-xl border border-slate-800 text-left font-orbitron text-xs">
              <div>
                <span class="text-slate-500 block">TEMPO TOTAL</span>
                <strong id="resPlayerTime" class="text-emerald-400 text-base">0.000s</strong>
              </div>
              <div>
                <span class="text-slate-500 block">TEMPO REAÇÃO</span>
                <strong id="resReactionTime" class="text-cyan-400 text-base">0.180s</strong>
              </div>
              <div>
                <span class="text-slate-500 block">VELOCIDADE FINAL</span>
                <strong id="resTopSpeed" class="text-yellow-400 text-base">0 km/h</strong>
              </div>
              <div>
                <span class="text-slate-500 block">TROCAS PRECISAS</span>
                <strong id="resShiftAccuracy" class="text-purple-400 text-base">100%</strong>
              </div>
            </div>

            <div class="flex space-x-3 pt-2">
              <button onclick="resetRace()" class="flex-1 py-3.5 bg-cyan-500 hover:bg-cyan-400 text-slate-950 font-orbitron font-bold rounded-xl shadow-lg btn-action">
                <i class="fa-solid fa-rotate-right mr-2"></i>REVANCHE
              </button>
              <button onclick="switchTab('garage')" class="px-5 py-3.5 bg-slate-800 hover:bg-slate-700 text-slate-200 font-orbitron font-bold rounded-xl">
                GARAGEM
              </button>
            </div>
          </div>
        </div>

      </section>

      <!-- TAB 3: LEADERBOARD & LOBBY OVERLAY -->
      <section id="viewLobby" class="hidden h-full flex-col justify-start space-y-4 p-2 overflow-y-auto interactive-ui">
        <div class="glass-panel p-5 rounded-2xl flex flex-col md:flex-row justify-between items-center gap-4">
          <div>
            <span class="px-3 py-1 bg-cyan-500/10 text-cyan-400 border border-cyan-500/30 rounded-full text-xs font-bold font-orbitron">MULTIPLAYER LOBBY 3D</span>
            <h2 class="text-xl font-orbitron font-black text-white mt-2">Salas de Arrancada & Ranking Global</h2>
            <p class="text-xs text-slate-400 mt-0.5">Desafie pilotos reais na reta de 420 metros.</p>
          </div>
          <button onclick="alert('Criando nova sala de arrancada 3D...')" class="px-5 py-3 bg-gradient-to-r from-cyan-500 to-emerald-400 text-slate-950 font-orbitron font-black text-xs rounded-xl shadow-lg btn-action">
            <i class="fa-solid fa-plus-circle mr-2"></i>CRIAR SALA 3D
          </button>
        </div>

        <div class="grid grid-cols-1 lg:grid-cols-3 gap-4">
          <!-- Rooms List -->
          <div class="lg:col-span-2 glass-panel p-4 rounded-2xl space-y-3">
            <h3 class="text-sm font-orbitron font-bold text-cyan-400 border-b border-slate-800 pb-2"><i class="fa-solid fa-server mr-2"></i>Salas Online</h3>
            <div class="space-y-2.5 font-orbitron text-xs" id="roomsList3d"></div>
          </div>

          <!-- Global Ranking -->
          <div class="glass-panel p-4 rounded-2xl space-y-3">
            <h3 class="text-sm font-orbitron font-bold text-yellow-400 border-b border-slate-800 pb-2"><i class="fa-solid fa-trophy mr-2"></i>Recordes 420m</h3>
            <div class="space-y-2 font-orbitron text-xs" id="leaderboardList3d"></div>
          </div>
        </div>
      </section>

      <!-- TAB 4: GROUP 5 TEAM OVERLAY -->
      <section id="viewTeam" class="hidden h-full flex-col justify-start p-2 overflow-y-auto interactive-ui">
        <div class="glass-panel p-6 rounded-2xl space-y-6">
          <div>
            <span class="px-3 py-1 bg-cyan-500/10 text-cyan-400 border border-cyan-500/30 rounded-full text-xs font-bold font-orbitron">
              PROGRAMAÇÃO APLICADA — PROTÓTIPO 3D
            </span>
            <h2 class="text-2xl font-orbitron font-black text-white mt-2">AUTO GIRO 3D — GRUPO 5</h2>
            <p class="text-slate-300 text-xs sm:text-sm mt-2">
              Desenvolvido em WebGL (Three.js) com física de rotação, conta-giros, corte de giro com efeito de labareda no escapamento, e modelos 3D inspirados nos maiores clássicos de arrancada do Brasil.
            </p>
          </div>

          <div>
            <h3 class="text-base font-orbitron font-bold text-slate-200 border-b border-slate-800 pb-2 mb-3"><i class="fa-solid fa-users text-cyan-400 mr-2"></i>Integrantes do Grupo 5</h3>
            <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-3">
              <div class="bg-slate-950/80 p-3 rounded-xl border border-slate-800 flex items-center space-x-3">
                <div class="w-8 h-8 rounded-full bg-cyan-500 text-slate-950 font-orbitron font-bold flex items-center justify-center text-xs">MJ</div>
                <div><div class="font-bold text-slate-200 text-xs">Maria Juliana</div><div class="text-[10px] text-slate-400">Ferreira da Silva</div></div>
              </div>
              <div class="bg-slate-950/80 p-3 rounded-xl border border-slate-800 flex items-center space-x-3">
                <div class="w-8 h-8 rounded-full bg-cyan-500 text-slate-950 font-orbitron font-bold flex items-center justify-center text-xs">AC</div>
                <div><div class="font-bold text-slate-200 text-xs">Ana Cláudia</div><div class="text-[10px] text-slate-400">Vieira da Silva</div></div>
              </div>
              <div class="bg-slate-950/80 p-3 rounded-xl border border-slate-800 flex items-center space-x-3">
                <div class="w-8 h-8 rounded-full bg-cyan-500 text-slate-950 font-orbitron font-bold flex items-center justify-center text-xs">PQ</div>
                <div><div class="font-bold text-slate-200 text-xs">Pedro Eduardo</div><div class="text-[10px] text-slate-400">Queiroz da Silva</div></div>
              </div>
              <div class="bg-slate-950/80 p-3 rounded-xl border border-slate-800 flex items-center space-x-3">
                <div class="w-8 h-8 rounded-full bg-cyan-500 text-slate-950 font-orbitron font-bold flex items-center justify-center text-xs">PL</div>
                <div><div class="font-bold text-slate-200 text-xs">Pedro Lucas Whylace</div><div class="text-[10px] text-slate-400">Alves e Silva</div></div>
              </div>
              <div class="bg-slate-950/80 p-3 rounded-xl border border-slate-800 flex items-center space-x-3">
                <div class="w-8 h-8 rounded-full bg-cyan-500 text-slate-950 font-orbitron font-bold flex items-center justify-center text-xs">JD</div>
                <div><div class="font-bold text-slate-200 text-xs">José Daniel</div><div class="text-[10px] text-slate-400">Barbosa dos Santos Silva</div></div>
              </div>
              <div class="bg-slate-950/80 p-3 rounded-xl border border-slate-800 flex items-center space-x-3">
                <div class="w-8 h-8 rounded-full bg-cyan-500 text-slate-950 font-orbitron font-bold flex items-center justify-center text-xs">RV</div>
                <div><div class="font-bold text-slate-200 text-xs">Ryan Valdemir</div><div class="text-[10px] text-slate-400">Alvez da Silva</div></div>
              </div>
            </div>
          </div>
        </div>
      </section>

    </div>
  </main>

  <!-- ACCOUNT AUTH MODAL -->
  <div id="authModal" class="hidden fixed inset-0 bg-slate-950/80 backdrop-blur-md flex items-center justify-center z-50 p-4">
    <div class="glass-panel p-6 rounded-3xl max-w-sm w-full shadow-2xl space-y-4">
      <div class="flex justify-between items-center border-b border-slate-800 pb-3">
        <h3 class="font-orbitron font-bold text-base text-white"><i class="fa-solid fa-user-plus mr-2 text-cyan-400"></i>Perfil de Conta</h3>
        <button onclick="closeAuthModal()" class="text-slate-400 hover:text-white"><i class="fa-solid fa-xmark text-lg"></i></button>
      </div>

      <div class="space-y-3 font-orbitron">
        <div>
          <label class="text-xs font-bold text-slate-400 block mb-1">Nome de Piloto:</label>
          <input id="inputNickname" type="text" placeholder="Ex: Titan_Bolada_G5" class="w-full bg-slate-950 border border-slate-800 rounded-xl p-3 text-xs focus:outline-none focus:border-cyan-500 text-white">
        </div>
      </div>

      <button onclick="saveAccountProfile()" class="w-full py-3 bg-cyan-500 hover:bg-cyan-400 text-slate-950 font-orbitron font-bold rounded-xl shadow-lg btn-action">
        SALVAR PERFIL
      </button>
    </div>
  </div>

  <footer class="glass-panel py-2 px-4 text-center text-[11px] text-slate-500 font-orbitron relative z-50">
    AUTO GIRO 3D &copy; 2026 — Grupo 5 | Simulador de Arrancada em WebGL
  </footer>

  <!-- THREE.JS & GAME ENGINE CODE -->
  <script>
    /* ==========================================================================
       1. DATA MODELS & STATE
       ========================================================================== */
    const TRACK_LENGTH = 420;
    const MAX_RPM = 13500;
    const IDLE_RPM = 1200;
    const SWEET_RPM_MIN = 8500;
    const SWEET_RPM_MAX = 10500;
    const REDLINE_RPM = 11800;

    const REAL_BIKES_3D = [
      {
        id: 'titan160',
        name: 'HONDA TITAN 160 FUÇADA',
        category: 'Street 160cc',
        power: 52,
        powerMult: 1.0,
        maxSpeed: 185,
        engine: '160 cc',
        torque: '4.8 kgfm',
        desc: 'Comando bravo, pistão KMP taxado e escapamento estralador de titânio.',
        color: 0x06b6d4,
        type: 'street'
      },
      {
        id: 'hornet600',
        name: 'HONDA CB 600F HORNET',
        category: 'Naked 600cc',
        power: 102,
        powerMult: 1.48,
        maxSpeed: 250,
        engine: '600 cc 4-Cil',
        torque: '6.5 kgfm',
        desc: 'O ronco inconfundível do motor 4 cilindros carburado com ponteira Jeskap.',
        color: 0xeab308,
        type: 'naked'
      },
      {
        id: 'xj6n',
        name: 'YAMAHA XJ6 N',
        category: 'Naked 600cc',
        power: 118,
        powerMult: 1.62,
        maxSpeed: 265,
        engine: '600 cc DOHC',
        torque: '6.9 kgfm',
        desc: 'Chassi tubular em diamante e entrega constante de torque na pista.',
        color: 0xa855f7,
        type: 'naked'
      },
      {
        id: 's1000rr',
        name: 'BMW S1000RR',
        category: 'Superbike 1000cc',
        power: 207,
        powerMult: 2.2,
        maxSpeed: 330,
        engine: '999 cc ShiftCam',
        torque: '11.5 kgfm',
        desc: 'Superbike de corrida com asas aerodinâmicas e quickshifter bidirecional.',
        color: 0x10b981,
        type: 'superbike'
      }
    ];

    let userAccount = {
      nickname: 'Piloto_G5',
      cash: 2500,
      level: 1,
      bestTime: 8.940
    };

    let selectedBikeIndex = 0;
    let currentBikeColor = 0x06b6d4;
    let currentWheelColor = 0xe2e8f0;
    let currentRiderColor = 0xf97316;

    let raceState = {
      active: false,
      countdownPhase: 'STOPPED',
      startTime: 0,
      reactionTime: 0,
      elapsedTime: 0,
      distance: 0,
      speed: 0,
      rpm: IDLE_RPM,
      gear: 0,
      isThrottling: false,
      topSpeed: 0,
      isRedlining: false,
      perfectShiftsCount: 0,
      totalShiftsCount: 0,

      oppDistance: 0,
      oppSpeed: 0
    };

    /* ==========================================================================
       2. THREE.JS ENGINE SETUP & 3D MODELS
       ========================================================================== */
    let scene, camera, renderer;
    let playerBikeGroup, oppBikeGroup;
    let playerWheels = [], oppWheels = [];
    let exhaustFlameMesh;
    let currentViewMode = 'garage'; // 'garage' or 'race'
    let cameraPreset = 'chase';

    function initThreeEngine() {
      const container = document.getElementById('webgl-container');
      
      // Scene
      scene = new THREE.Scene();
      scene.background = new THREE.Color(0x030712);
      scene.fog = new THREE.FogExp2(0x030712, 0.008);

      // Camera
      camera = new THREE.PerspectiveCamera(60, container.clientWidth / container.clientHeight, 0.1, 1000);

      // Renderer
      renderer = new THREE.WebGLRenderer({ antialias: true });
      renderer.setSize(container.clientWidth, container.clientHeight);
      renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
      renderer.shadowMap.enabled = true;
      container.appendChild(renderer.domElement);

      // Lighting
      const ambientLight = new THREE.AmbientLight(0xffffff, 0.8);
      scene.add(ambientLight);

      const dirLight = new THREE.DirectionalLight(0xffffff, 1.4);
      dirLight.position.set(20, 40, 20);
      dirLight.castShadow = true;
      scene.add(dirLight);

      // Build 3D Drag Track
      build3DTrack();

      // Build 3D Bikes
      playerBikeGroup = build3DBikeMesh(currentBikeColor, currentWheelColor, currentRiderColor, playerWheels);
      playerBikeGroup.position.set(-1.8, 0, 0);
      scene.add(playerBikeGroup);

      oppBikeGroup = build3DBikeMesh(0xef4444, 0xe2e8f0, 0x3b82f6, oppWheels);
      oppBikeGroup.position.set(1.8, 0, 0);
      scene.add(oppBikeGroup);

      // Create Backfire Exhaust Flame Particle
      const flameGeo = new THREE.ConeGeometry(0.18, 0.8, 8);
      flameGeo.rotateX(-Math.PI / 2);
      const flameMat = new THREE.MeshBasicMaterial({ color: 0xff4500 });
      exhaustFlameMesh = new THREE.Mesh(flameGeo, flameMat);
      exhaustFlameMesh.position.set(0, 0.4, -1.6);
      exhaustFlameMesh.visible = false;
      playerBikeGroup.add(exhaustFlameMesh);

      window.addEventListener('resize', onWindowResize);
      setupGarageCamera();
    }

    function build3DTrack() {
      // Asphalt Track
      const trackGeo = new THREE.PlaneGeometry(16, 600);
      const trackMat = new THREE.MeshStandardMaterial({ color: 0x111827, roughness: 0.8 });
      const track = new THREE.Mesh(trackGeo, trackMat);
      track.rotation.x = -Math.PI / 2;
      track.position.set(0, 0, 250);
      scene.add(track);

      // White Center & Side Lines
      const lineGeo = new THREE.PlaneGeometry(0.3, 600);
      const lineMat = new THREE.MeshBasicMaterial({ color: 0xffffff });
      const centerLine = new THREE.Mesh(lineGeo, lineMat);
      centerLine.rotation.x = -Math.PI / 2;
      centerLine.position.set(0, 0.01, 250);
      scene.add(centerLine);

      // Distance Markers
      for (let d = 50; d <= 420; d += 50) {
        const markerGeo = new THREE.BoxGeometry(0.2, 3, 0.2);
        const markerMat = new THREE.MeshBasicMaterial({ color: 0x06b6d4 });
        const marker = new THREE.Mesh(markerGeo, markerMat);
        marker.position.set(-8, 1.5, d);
        scene.add(marker);
      }
    }

    /* Create Detailed Procedural 3D Bike Mesh */
    function build3DBikeMesh(bodyCol, wheelCol, riderCol, wheelArray) {
      const bikeGroup = new THREE.Group();

      // Main Fairing Body
      const bodyGeo = new THREE.BoxGeometry(0.6, 0.7, 1.8);
      const bodyMat = new THREE.MeshStandardMaterial({ color: bodyCol, metalness: 0.5, roughness: 0.3 });
      const body = new THREE.Mesh(bodyGeo, bodyMat);
      body.position.set(0, 0.7, 0);
      bikeGroup.add(body);

      // Gas Tank
      const tankGeo = new THREE.SphereGeometry(0.4, 16, 16);
      tankGeo.scale(1, 0.8, 1.4);
      const tank = new THREE.Mesh(tankGeo, bodyMat);
      tank.position.set(0, 0.9, 0.2);
      bikeGroup.add(tank);

      // Wheels (Front and Rear)
      const wheelGeo = new THREE.CylinderGeometry(0.45, 0.45, 0.25, 24);
      wheelGeo.rotateZ(Math.PI / 2);
      const wheelMat = new THREE.MeshStandardMaterial({ color: wheelCol, metalness: 0.8 });

      const frontWheel = new THREE.Mesh(wheelGeo, wheelMat);
      frontWheel.position.set(0, 0.45, 1.1);
      bikeGroup.add(frontWheel);

      const rearWheel = new THREE.Mesh(wheelGeo, wheelMat);
      rearWheel.position.set(0, 0.45, -1.0);
      bikeGroup.add(rearWheel);

      wheelArray.push(frontWheel, rearWheel);

      // Engine Block
      const engineGeo = new THREE.BoxGeometry(0.5, 0.5, 0.7);
      const engineMat = new THREE.MeshStandardMaterial({ color: 0x334155, metalness: 0.9 });
      const engine = new THREE.Mesh(engineGeo, engineMat);
      engine.position.set(0, 0.4, 0.1);
      bikeGroup.add(engine);

      // Exhaust Pipe
      const exhaustGeo = new THREE.CylinderGeometry(0.08, 0.12, 1.2, 12);
      exhaustGeo.rotateX(Math.PI / 2);
      const exhaustMat = new THREE.MeshStandardMaterial({ color: 0x94a3b8, metalness: 0.9 });
      const exhaust = new THREE.Mesh(exhaustGeo, exhaustMat);
      exhaust.position.set(0.3, 0.35, -0.7);
      bikeGroup.add(exhaust);

      // 3D Rider Figure
      const riderGroup = new THREE.Group();
      
      // Torso
      const torsoGeo = new THREE.BoxGeometry(0.5, 0.7, 0.4);
      const suitMat = new THREE.MeshStandardMaterial({ color: riderCol, roughness: 0.6 });
      const torso = new THREE.Mesh(torsoGeo, suitMat);
      torso.position.set(0, 1.1, -0.2);
      torso.rotation.x = 0.4; // Tucked racing position
      riderGroup.add(torso);

      // Helmet
      const helmetGeo = new THREE.SphereGeometry(0.22, 16, 16);
      const helmetMat = new THREE.MeshStandardMaterial({ color: 0x0f172a, roughness: 0.2 });
      const helmet = new THREE.Mesh(helmetGeo, helmetMat);
      helmet.position.set(0, 1.45, 0.05);
      riderGroup.add(helmet);

      bikeGroup.add(riderGroup);

      return bikeGroup;
    }

    function updateBikeColors3D() {
      playerBikeGroup.children[0].material.color.setHex(currentBikeColor);
      playerBikeGroup.children[1].material.color.setHex(currentBikeColor);
      playerWheels[0].material.color.setHex(currentWheelColor);
      playerWheels[1].material.color.setHex(currentWheelColor);
    }

    /* ==========================================================================
       3. ENGINE ANIMATION & CAMERA LOGIC
       ========================================================================== */
    function animateThreeEngine() {
      requestAnimationFrame(animateThreeEngine);

      if (currentViewMode === 'garage') {
        playerBikeGroup.rotation.y += 0.008;
      } else if (currentViewMode === 'race') {
        // Rotate wheels when moving
        const rotSpeed = raceState.speed * 0.08;
        playerWheels.forEach(w => w.rotation.x += rotSpeed);
        oppWheels.forEach(w => w.rotation.x += raceState.oppSpeed * 0.08);

        // Update 3D Positions on Track
        playerBikeGroup.position.z = raceState.distance;
        oppBikeGroup.position.z = raceState.oppDistance;

        // Exhaust backfire visibility
        if (exhaustFlameMesh) {
          exhaustFlameMesh.visible = raceState.isRedlining || (raceState.isThrottling && Math.random() < 0.1);
        }

        // Camera Follow
        if (cameraPreset === 'chase') {
          camera.position.set(playerBikeGroup.position.x, 2.8, playerBikeGroup.position.z - 5.5);
          camera.lookAt(playerBikeGroup.position.x, 1.2, playerBikeGroup.position.z + 10);
        } else if (cameraPreset === 'side') {
          camera.position.set(playerBikeGroup.position.x - 7, 2.0, playerBikeGroup.position.z + 2);
          camera.lookAt(playerBikeGroup.position.x, 1.0, playerBikeGroup.position.z + 2);
        }
      }

      renderer.render(scene, camera);
    }

    function setupGarageCamera() {
      currentViewMode = 'garage';
      playerBikeGroup.position.set(0, 0, 0);
      oppBikeGroup.visible = false;
      camera.position.set(0, 1.8, 4.0);
      camera.lookAt(0, 0.8, 0);
    }

    function setupRaceCamera() {
      currentViewMode = 'race';
      playerBikeGroup.rotation.y = 0;
      playerBikeGroup.position.set(-1.8, 0, 0);
      oppBikeGroup.visible = true;
      oppBikeGroup.position.set(1.8, 0, 0);
    }

    function onWindowResize() {
      const container = document.getElementById('webgl-container');
      camera.aspect = container.clientWidth / container.clientHeight;
      camera.updateProjectionMatrix();
      renderer.setSize(container.clientWidth, container.clientHeight);
    }

    /* ==========================================================================
       4. RACE PHYSICS & DRAG SIMULATION
       ========================================================================== */
    function resetRace() {
      const bike = REAL_BIKES_3D[selectedBikeIndex];

      raceState = {
        active: true,
        countdownPhase: 'STOPPED',
        startTime: 0,
        reactionTime: 0,
        elapsedTime: 0,
        distance: 0,
        speed: 0,
        rpm: IDLE_RPM,
        gear: 0,
        isThrottling: false,
        topSpeed: 0,
        isRedlining: false,
        perfectShiftsCount: 0,
        totalShiftsCount: 0,
        oppDistance: 0,
        oppSpeed: 0
      };

      setupRaceCamera();
      document.getElementById('finishOverlay').classList.add('hidden');
      document.getElementById('redlineAlert').classList.add('hidden');
      showShiftFeedback("PREPARE-SE NA LINHA", "border-slate-700 bg-slate-900 text-slate-300");

      startCountdownSequence();
      let lastTime = performance.now();

      function racePhysicsLoop(now) {
        let dt = (now - lastTime) / 1000;
        if (dt > 0.1) dt = 0.1;
        lastTime = now;

        if (raceState.active) {
          updatePhysics(dt, bike);
          updateHUD();
          requestAnimationFrame(racePhysicsLoop);
        }
      }

      requestAnimationFrame(racePhysicsLoop);
    }

    function startCountdownSequence() {
      raceState.countdownPhase = 'RED';
      showShiftFeedback("ATENÇÃO... (SINAL VERMELHO)", "border-red-500 bg-red-950 text-red-400");

      setTimeout(() => {
        if (!raceState.active) return;
        raceState.countdownPhase = 'YELLOW';
        showShiftFeedback("PRONTO... (SINAL AMARELO)", "border-yellow-500 bg-yellow-950 text-yellow-400");

        setTimeout(() => {
          if (!raceState.active) return;
          raceState.countdownPhase = 'RUNNING';
          raceState.startTime = performance.now();

          if (raceState.rpm >= SWEET_RPM_MIN && raceState.rpm <= SWEET_RPM_MAX) {
            raceState.gear = 1;
            raceState.speed = 42 * REAL_BIKES_3D[selectedBikeIndex].powerMult;
            raceState.reactionTime = 0.12;
            showShiftFeedback("LARGADA PERFEITA! 🚀", "border-emerald-500 bg-emerald-950 text-emerald-400");
          } else {
            raceState.gear = 1;
            raceState.speed = 22;
            raceState.reactionTime = 0.28;
            showShiftFeedback("LARGADA REGULAR", "border-yellow-500 bg-yellow-950 text-yellow-400");
          }
        }, 1000);
      }, 1000);
    }

    function updatePhysics(dt, bike) {
      if (raceState.countdownPhase === 'RED' || raceState.countdownPhase === 'YELLOW') {
        if (raceState.isThrottling) raceState.rpm += 14000 * dt;
        else raceState.rpm -= 9000 * dt;
        raceState.rpm = Math.max(IDLE_RPM, Math.min(MAX_RPM, raceState.rpm));
        return;
      }

      if (raceState.countdownPhase === 'RUNNING') {
        raceState.elapsedTime = (performance.now() - raceState.startTime) / 1000;
        let maxSpeedInGear = bike.maxSpeed * (raceState.gear / 6);

        if (raceState.isThrottling) {
          if (raceState.gear > 0) {
            if (raceState.rpm >= REDLINE_RPM) {
              raceState.isRedlining = true;
              document.getElementById('redlineAlert').classList.remove('hidden');
              raceState.speed -= 10 * dt;
            } else {
              raceState.isRedlining = false;
              document.getElementById('redlineAlert').classList.add('hidden');
              let accel = (55 * bike.powerMult);
              if (raceState.speed < maxSpeedInGear) raceState.speed += accel * dt;
            }

            let fillPct = raceState.speed / Math.max(1, maxSpeedInGear);
            raceState.rpm = IDLE_RPM + fillPct * (MAX_RPM - IDLE_RPM);
          } else {
            raceState.rpm += 14000 * dt;
          }
        } else {
          raceState.isRedlining = false;
          document.getElementById('redlineAlert').classList.add('hidden');
          raceState.speed -= 12 * dt;
          raceState.rpm -= 8000 * dt;
        }

        raceState.speed = Math.max(0, raceState.speed);
        raceState.rpm = Math.max(IDLE_RPM, Math.min(MAX_RPM, raceState.rpm));
        raceState.distance += (raceState.speed / 3.6) * dt;

        if (raceState.speed > raceState.topSpeed) raceState.topSpeed = raceState.speed;

        // Opponent Physics
        if (raceState.oppDistance < TRACK_LENGTH) {
          raceState.oppSpeed += (30 * bike.powerMult) * dt;
          if (raceState.oppSpeed > bike.maxSpeed * 0.95) raceState.oppSpeed = bike.maxSpeed * 0.95;
          raceState.oppDistance += (raceState.oppSpeed / 3.6) * dt;
        }

        if (raceState.distance >= TRACK_LENGTH) {
          raceState.distance = TRACK_LENGTH;
          raceState.countdownPhase = 'FINISHED';
          finishRace();
        }
      }
    }

    function handleShiftGear(direction) {
      if (raceState.countdownPhase !== 'RUNNING') return;

      if (direction === 1 && raceState.gear < 6) {
        raceState.totalShiftsCount++;
        const currentRpm = raceState.rpm;
        raceState.gear++;

        if (currentRpm >= SWEET_RPM_MIN && currentRpm <= SWEET_RPM_MAX) {
          raceState.perfectShiftsCount++;
          showShiftFeedback("MARCHA PERFEITA! ⚡", "border-emerald-500 bg-emerald-950 text-emerald-400");
          raceState.speed += 12;
          raceState.rpm = 7200;
        } else if (currentRpm >= REDLINE_RPM) {
          showShiftFeedback("CORTE DE GIRO! ⚠️", "border-red-500 bg-red-950 text-red-400");
          raceState.rpm = 7800;
        } else {
          showShiftFeedback("MARCHA PRECOCE", "border-yellow-500 bg-yellow-950 text-yellow-400");
          raceState.rpm = 5000;
        }
      } else if (direction === -1 && raceState.gear > 1) {
        raceState.gear--;
        raceState.rpm = Math.min(MAX_RPM, raceState.rpm + 2800);
      }
    }

    function finishRace() {
      const shiftAccuracy = raceState.totalShiftsCount > 0 
        ? Math.round((raceState.perfectShiftsCount / raceState.totalShiftsCount) * 100) 
        : 100;

      document.getElementById('resPlayerTime').innerText = raceState.elapsedTime.toFixed(3) + 's';
      document.getElementById('resReactionTime').innerText = raceState.reactionTime.toFixed(3) + 's';
      document.getElementById('resTopSpeed').innerText = Math.round(raceState.topSpeed) + ' km/h';
      document.getElementById('resShiftAccuracy').innerText = shiftAccuracy + '%';

      document.getElementById('finishOverlay').classList.remove('hidden');
    }

    /* ==========================================================================
       5. CONTROLS, UI & LIFECYCLE
       ========================================================================== */
    window.onload = function() {
      initThreeEngine();
      animateThreeEngine();

      renderBikeStrip();
      setupControls();
      renderLobbyData3D();
    };

    function setupControls() {
      window.addEventListener('keydown', (e) => {
        if (e.key === 'ArrowUp') raceState.isThrottling = true;
        if (e.key === 'ArrowRight') handleShiftGear(1);
        if (e.key === 'ArrowLeft') handleShiftGear(-1);
      });

      window.addEventListener('keyup', (e) => {
        if (e.key === 'ArrowUp') raceState.isThrottling = false;
      });

      const btnThrottle = document.getElementById('btnThrottle');
      btnThrottle.addEventListener('mousedown', () => raceState.isThrottling = true);
      btnThrottle.addEventListener('mouseup', () => raceState.isThrottling = false);
      btnThrottle.addEventListener('mouseleave', () => raceState.isThrottling = false);

      btnThrottle.addEventListener('touchstart', (e) => { e.preventDefault(); raceState.isThrottling = true; });
      btnThrottle.addEventListener('touchend', (e) => { e.preventDefault(); raceState.isThrottling = false; });
    }

    function renderBikeStrip() {
      const container = document.getElementById('bikes3dStrip');
      container.innerHTML = REAL_BIKES_3D.map((bike, idx) => `
        <div onclick="selectBike3D(${idx})" class="cursor-pointer p-2.5 rounded-xl border min-w-[140px] ${idx === selectedBikeIndex ? 'bg-slate-900 border-cyan-500' : 'bg-slate-950/60 border-slate-800'} transition-all">
          <div class="text-[9px] font-orbitron text-cyan-400 font-bold uppercase">${bike.category}</div>
          <div class="text-xs font-orbitron font-extrabold text-white truncate">${bike.name}</div>
          <div class="text-[10px] font-orbitron text-slate-400 mt-1">${bike.power} HP | ${bike.maxSpeed} km/h</div>
        </div>
      `).join('');
    }

    function selectBike3D(index) {
      selectedBikeIndex = index;
      const bike = REAL_BIKES_3D[index];
      currentBikeColor = bike.color;

      document.getElementById('selectedBikeCategory').innerText = bike.category;
      document.getElementById('selectedBikeTitle').innerText = bike.name;
      document.getElementById('selectedBikeDesc').innerText = bike.desc;
      document.getElementById('selectedBikePower').innerText = bike.power + ' HP';

      document.getElementById('statTopSpeed').innerText = bike.maxSpeed + ' km/h';
      document.getElementById('statTorque').innerText = bike.torque;
      document.getElementById('statEngine').innerText = bike.engine;

      updateBikeColors3D();
      renderBikeStrip();
    }

    function setBikeColor3D(hex) { currentBikeColor = hex; updateBikeColors3D(); }
    function setWheelColor3D(hex) { currentWheelColor = hex; updateBikeColors3D(); }
    function setRiderColor3D(hex) { currentRiderColor = hex; updateBikeColors3D(); }

    function setCameraView(preset) {
      cameraPreset = preset;
    }

    function renderLobbyData3D() {
      const roomsList = document.getElementById('roomsList3d');
      const leaderboardList = document.getElementById('leaderboardList3d');

      roomsList.innerHTML = `
        <div class="bg-slate-950/80 p-3 rounded-xl border border-slate-800 flex justify-between items-center">
          <div><div class="font-bold text-white">Rua do Grau 3D (420m)</div><div class="text-slate-400">Piloto: Titan_Corte | Moto: TITAN 160</div></div>
          <button onclick="switchTab('race')" class="px-3 py-1.5 bg-cyan-500 text-slate-950 font-bold rounded-lg">DESAFIAR</button>
        </div>
        <div class="bg-slate-950/80 p-3 rounded-xl border border-slate-800 flex justify-between items-center">
          <div><div class="font-bold text-white">Hornet vs S1000RR Superbike</div><div class="text-slate-400">Piloto: Speed_King | Moto: S1000RR</div></div>
          <button onclick="switchTab('race')" class="px-3 py-1.5 bg-cyan-500 text-slate-950 font-bold rounded-lg">DESAFIAR</button>
        </div>
      `;

      leaderboardList.innerHTML = `
        <div class="bg-slate-950/80 p-2 rounded-xl border border-slate-800 flex justify-between">
          <span>1. Pedro_Lucas (S1000RR)</span><strong class="text-emerald-400">8.120s</strong>
        </div>
        <div class="bg-slate-950/80 p-2 rounded-xl border border-slate-800 flex justify-between">
          <span>2. Ryan_Valdemir (ZX10R)</span><strong class="text-emerald-400">8.340s</strong>
        </div>
        <div class="bg-slate-950/80 p-2 rounded-xl border border-slate-800 flex justify-between">
          <span>3. ${userAccount.nickname}</span><strong class="text-emerald-400">${userAccount.bestTime.toFixed(3)}s</strong>
        </div>
      `;
    }

    function updateHUD() {
      document.getElementById('hudDistance').innerHTML = `${raceState.distance.toFixed(1)} <span class="text-xs text-slate-400">/ 420m</span>`;
      document.getElementById('hudTimer').innerHTML = `${raceState.elapsedTime.toFixed(3)} <span class="text-xs text-slate-400">s</span>`;
      document.getElementById('hudSpeed').innerHTML = `${Math.round(raceState.speed)} <span class="text-xs text-slate-400">km/h</span>`;
      document.getElementById('hudGear').innerText = raceState.gear === 0 ? 'N' : raceState.gear;

      document.getElementById('rpmDigit').innerText = Math.round(raceState.rpm);
      const rpmPercent = Math.min(100, (raceState.rpm / MAX_RPM) * 100);
      document.getElementById('rpmBar').style.width = rpmPercent + '%';
    }

    function showShiftFeedback(text, styleClasses) {
      const el = document.getElementById('shiftFeedback');
      el.innerText = text;
      el.className = `glass-panel px-6 py-2 rounded-xl font-orbitron font-black text-sm transition-all pointer-events-none z-20 shadow-2xl border ${styleClasses} opacity-100 scale-105`;

      setTimeout(() => { el.classList.add('opacity-0'); }, 900);
    }

    function switchTab(tab) {
      document.getElementById('viewGarage').classList.add('hidden');
      document.getElementById('viewRace').classList.add('hidden');
      document.getElementById('viewLobby').classList.add('hidden');
      document.getElementById('viewTeam').classList.add('hidden');

      document.getElementById('tabGarageBtn').className = "px-3.5 py-2 rounded-lg text-xs font-bold transition-all text-slate-400 hover:text-slate-200 font-orbitron";
      document.getElementById('tabRaceBtn').className = "px-3.5 py-2 rounded-lg text-xs font-bold transition-all text-slate-400 hover:text-slate-200 font-orbitron";
      document.getElementById('tabLobbyBtn').className = "px-3.5 py-2 rounded-lg text-xs font-bold transition-all text-slate-400 hover:text-slate-200 font-orbitron";
      document.getElementById('tabTeamBtn').className = "px-3.5 py-2 rounded-lg text-xs font-bold transition-all text-slate-400 hover:text-slate-200 font-orbitron";

      if (tab === 'garage') {
        document.getElementById('viewGarage').classList.remove('hidden');
        document.getElementById('tabGarageBtn').className = "px-3.5 py-2 rounded-lg text-xs font-bold transition-all bg-cyan-500 text-slate-950 font-orbitron shadow-md";
        setupGarageCamera();
      } else if (tab === 'race') {
        document.getElementById('viewRace').classList.remove('hidden');
        document.getElementById('viewRace').classList.add('flex');
        document.getElementById('tabRaceBtn').className = "px-3.5 py-2 rounded-lg text-xs font-bold transition-all bg-cyan-500 text-slate-950 font-orbitron shadow-md";
        resetRace();
      } else if (tab === 'lobby') {
        document.getElementById('viewLobby').classList.remove('hidden');
        document.getElementById('tabLobbyBtn').className = "px-3.5 py-2 rounded-lg text-xs font-bold transition-all bg-cyan-500 text-slate-950 font-orbitron shadow-md";
      } else if (tab === 'team') {
        document.getElementById('viewTeam').classList.remove('hidden');
        document.getElementById('tabTeamBtn').className = "px-3.5 py-2 rounded-lg text-xs font-bold transition-all bg-cyan-500 text-slate-950 font-orbitron shadow-md";
      }
    }

    function openAuthModal() { document.getElementById('authModal').classList.remove('hidden'); }
    function closeAuthModal() { document.getElementById('authModal').classList.add('hidden'); }
    function saveAccountProfile() {
      const name = document.getElementById('inputNickname').value.trim();
      if (name) {
        userAccount.nickname = name;
        document.getElementById('navDriverName').innerText = name;
        closeAuthModal();
      }
    }
  </script>
</body>
</html>
