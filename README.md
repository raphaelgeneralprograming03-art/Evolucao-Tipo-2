
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Simulador de Civilização Tipo II - Escala Kardashev</title>
    <style>
        :root {
            --bg-color: #02040a;
            --panel-bg: rgba(10, 15, 30, 0.90);
            --border-color: rgba(245, 158, 11, 0.35);
            --accent-gold: #f59e0b;
            --accent-orange: #f97316;
            --accent-cyan: #38bdf8;
            --accent-green: #10b981;
            --text-main: #f8fafc;
            --text-muted: #94a3b8;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', system-ui, -apple-system, sans-serif;
            user-select: none;
        }

        body {
            background-color: var(--bg-color);
            color: var(--text-main);
            height: 100vh;
            overflow: hidden;
            display: flex;
            flex-direction: column;
        }

        header {
            height: 60px;
            background: linear-gradient(180deg, rgba(15, 23, 42, 0.95) 0%, rgba(2, 4, 10, 0.8) 100%);
            border-bottom: 1px solid var(--border-color);
            display: flex;
            align-items: center;
            justify-content: space-between;
            padding: 0 25px;
            z-index: 10;
        }

        header h1 {
            font-size: 1.05rem;
            letter-spacing: 2px;
            color: var(--accent-gold);
            text-transform: uppercase;
        }

        .status-badge {
            font-size: 0.75rem;
            padding: 4px 12px;
            border-radius: 12px;
            background: rgba(245, 158, 11, 0.15);
            border: 1px solid var(--accent-gold);
            color: var(--accent-gold);
            letter-spacing: 1px;
        }

        .main-container {
            display: grid;
            grid-template-columns: 360px 1fr;
            height: calc(100vh - 60px);
            position: relative;
        }

        /* PAINEL LATERAL DE CONTROLE */
        .control-panel {
            background: var(--panel-bg);
            border-right: 1px solid var(--border-color);
            backdrop-filter: blur(12px);
            padding: 20px;
            display: flex;
            flex-direction: column;
            gap: 16px;
            overflow-y: auto;
            z-index: 5;
        }

        .kardashev-box {
            background: radial-gradient(circle, rgba(245, 158, 11, 0.2) 0%, rgba(2, 4, 10, 0.7) 100%);
            border: 1px solid var(--accent-gold);
            border-radius: 10px;
            padding: 16px;
            text-align: center;
            box-shadow: 0 0 22px rgba(245, 158, 11, 0.18);
        }

        .kardashev-score {
            font-size: 2.3rem;
            font-family: monospace;
            font-weight: bold;
            color: #fff;
            text-shadow: 0 0 15px var(--accent-gold);
            margin: 4px 0;
        }

        .section-title {
            font-size: 0.78rem;
            text-transform: uppercase;
            letter-spacing: 1.5px;
            color: var(--accent-gold);
            border-bottom: 1px solid var(--border-color);
            padding-bottom: 6px;
        }

        .metric-group {
            display: flex;
            flex-direction: column;
            gap: 6px;
        }

        .metric-header {
            display: flex;
            justify-content: space-between;
            font-size: 0.8rem;
        }

        .metric-value {
            font-family: monospace;
            font-weight: bold;
            color: var(--accent-gold);
        }

        input[type="range"] {
            width: 100%;
            height: 6px;
            border-radius: 3px;
            background: rgba(255, 255, 255, 0.1);
            outline: none;
            accent-color: var(--accent-gold);
            cursor: pointer;
        }

        .toggle-box {
            display: flex;
            align-items: center;
            justify-content: space-between;
            background: rgba(255, 255, 255, 0.03);
            border: 1px solid rgba(255, 255, 255, 0.08);
            border-radius: 6px;
            padding: 8px 12px;
            font-size: 0.78rem;
        }

        .switch {
            position: relative;
            display: inline-block;
            width: 36px;
            height: 18px;
        }

        .switch input { opacity: 0; width: 0; height: 0; }

        .slider {
            position: absolute; cursor: pointer; top: 0; left: 0; right: 0; bottom: 0;
            background-color: rgba(255,255,255,0.2); transition: .3s; border-radius: 18px;
        }

        .slider:before {
            position: absolute; content: ""; height: 12px; width: 12px; left: 3px; bottom: 3px;
            background-color: white; transition: .3s; border-radius: 50%;
        }

        input:checked + .slider { background-color: var(--accent-gold); }
        input:checked + .slider:before { transform: translateX(18px); }

        .info-card {
            background: rgba(0, 0, 0, 0.4);
            border: 1px solid rgba(255, 255, 255, 0.08);
            border-radius: 8px;
            padding: 12px;
            font-size: 0.8rem;
            line-height: 1.45;
            color: var(--text-muted);
        }

        .info-card strong {
            color: var(--text-main);
        }

        /* VIEWPORT CANVAS */
        .viewport {
            position: relative;
            width: 100%;
            height: 100%;
            background: radial-gradient(circle at center, #0d0c1d 0%, #02040a 100%);
            overflow: hidden;
        }

        canvas {
            width: 100%;
            height: 100%;
            display: block;
        }

        .hud-overlay {
            position: absolute;
            bottom: 20px;
            right: 20px;
            background: var(--panel-bg);
            border: 1px solid var(--border-color);
            border-radius: 8px;
            padding: 15px;
            font-family: monospace;
            font-size: 0.75rem;
            pointer-events: none;
            display: flex;
            flex-direction: column;
            gap: 6px;
            backdrop-filter: blur(10px);
        }

        .hud-line {
            display: flex;
            justify-content: space-between;
            gap: 20px;
        }

        ::-webkit-scrollbar { width: 5px; }
        ::-webkit-scrollbar-track { background: transparent; }
        ::-webkit-scrollbar-thumb { background: var(--border-color); border-radius: 3px; }
    </style>
</head>
<body>

    <header>
        <h1>CIVILIZAÇÃO TIPO II: DOMÍNIO ESTELAR</h1>
        <div class="status-badge">ENXAME DE DYSON: OPERACIONAL</div>
    </header>

    <div class="main-container">
        <!-- PAINEL LATERAL -->
        <aside class="control-panel">
            <div class="kardashev-box">
                <div style="font-size: 0.72rem; color: var(--text-muted); text-transform: uppercase;">Métrica de Kardashev</div>
                <div class="kardashev-score" id="k-score">K 2.000</div>
                <div style="font-size: 0.78rem; color: var(--accent-gold);" id="power-display">1.00 × 10²⁶ W</div>
            </div>

            <div class="section-title">1. Configuração da Esfera de Dyson</div>
            <div class="metric-group">
                <div class="metric-header">
                    <span>Densidade de Coletores (Unidades)</span>
                    <span class="metric-value" id="val-collectors">350</span>
                </div>
                <input type="range" id="input-collectors" min="50" max="600" value="350">
            </div>

            <div class="metric-group">
                <div class="metric-header">
                    <span>Raio Orbital do Enxame (AU)</span>
                    <span class="metric-value" id="val-radius">1.0 AU</span>
                </div>
                <input type="range" id="input-radius" min="0.5" max="2.0" step="0.1" value="1.0">
            </div>

            <div class="metric-group">
                <div class="metric-header">
                    <span>Ajuste da Demanda (x10²⁵ W)</span>
                    <span class="metric-value" id="val-demand">10.0 W</span>
                </div>
                <input type="range" id="input-demand" min="0.1" max="20.0" step="0.1" value="10.0">
            </div>

            <div class="section-title">2. Distribuição e Segurança</div>
            
            <div class="toggle-box">
                <span>Rede de Transmissão por Laser/Micro-ondas</span>
                <label class="switch"><input type="checkbox" id="sw-beaming" checked><span class="slider"></span></label>
            </div>

            <div class="toggle-box">
                <span>Habitat Multiplanetário do Sistema Solar</span>
                <label class="switch"><input type="checkbox" id="sw-colonies" checked><span class="slider"></span></label>
            </div>

            <div class="section-title">3. Diagnóstico do Impacto</div>
            <div class="info-card">
                <strong>Status de Extinção:</strong><br>
                • Imunidade absoluta a cataclismos planetários.<br>
                • Matriz energética ilimitada para terraformação.<br>
                • Redes de mineração de plasma na fotosfera.<br>
                • Expansão para a nuvem de Oort e exoplanetas.
            </div>
        </aside>

        <!-- VIEWPORT CANVAS SIMULAÇÃO -->
        <main class="viewport">
            <canvas id="simCanvas"></canvas>

            <div class="hud-overlay">
                <div class="hud-line"><span>FLUXO RADIATIVO CAPTURADO:</span><span id="hud-capture" style="color: var(--accent-gold)">100.0%</span></div>
                <div class="hud-line"><span>RISCO DE EXTINÇÃO PLANETÁRIA:</span><span id="hud-risk" style="color: var(--accent-green)">0.0% (IMUNE)</span></div>
                <div class="hud-line"><span>TEMPERATURA DO NÚCLEO ESTELAR:</span><span style="color: var(--accent-orange)">15.7M K</span></div>
                <div class="hud-line"><span>HABITATS SISTÊMICOS ATIVOS:</span><span id="hud-habitats" style="color: var(--accent-cyan)">8 PLANETAS + 120 LUAS</span></div>
            </div>
        </main>
    </div>

    <script>
        const canvas = document.getElementById('simCanvas');
        const ctx = canvas.getContext('2d');

        // Estado da Simulação
        const state = {
            powerWatts: 1e26,
            numCollectors: 350,
            orbitRadiusFactor: 1.0,
            kardashevScale: 2.0,
            energyBeaming: true,
            coloniesActive: true,
            starPulse: 0
        };

        function resizeCanvas() {
            canvas.width = canvas.parentElement.clientWidth;
            canvas.height = canvas.parentElement.clientHeight;
        }
        window.addEventListener('resize', resizeCanvas);
        resizeCanvas();

        // Elementos de Entrada
        const inputCollectors = document.getElementById('input-collectors');
        const inputRadius = document.getElementById('input-radius');
        const inputDemand = document.getElementById('input-demand');
        const swBeaming = document.getElementById('sw-beaming');
        const swColonies = document.getElementById('sw-colonies');

        // Estrutura do Enxame de Dyson (Coletores Orbitais)
        let collectors = [];

        function buildDysonSwarm() {
            collectors = [];
            const count = state.numCollectors;
            for (let i = 0; i < count; i++) {
                // Distribuição em múltiplos anéis 3D projetados em 2D
                collectors.push({
                    angle: Math.random() * Math.PI * 2,
                    speed: (0.002 + Math.random() * 0.003) * (Math.random() < 0.5 ? 1 : -1),
                    inclination: (Math.random() - 0.5) * 0.8,
                    ringIndex: Math.floor(Math.random() * 5),
                    size: 2.5 + Math.random() * 2
                });
            }
        }

        // Atualização dos Cálculos Físicos
        function updatePhysics() {
            state.numCollectors = parseInt(inputCollectors.value);
            state.orbitRadiusFactor = parseFloat(inputRadius.value);
            
            // Reconstroi enxame se a contagem mudou substancialmente
            if (Math.abs(collectors.length - state.numCollectors) > 10) {
                buildDysonSwarm();
            }

            const captureRatio = Math.min(1.0, (state.numCollectors / 350));
            state.powerWatts = parseFloat(inputDemand.value) * 1e25 * captureRatio;

            // Fórmula de Kardashev: K = (log10(P) - 6) / 10
            state.kardashevScale = (Math.log10(state.powerWatts) - 6) / 10;

            state.energyBeaming = swBeaming.checked;
            state.coloniesActive = swColonies.checked;

            // Interface
            document.getElementById('k-score').innerText = `K ${state.kardashevScale.toFixed(3)}`;
            
            const powerScaled = (state.powerWatts / 1e25).toFixed(2);
            document.getElementById('power-display').innerText = `${powerScaled} × 10²⁵ W`;

            document.getElementById('val-collectors').innerText = state.numCollectors;
            document.getElementById('val-radius').innerText = `${state.orbitRadiusFactor.toFixed(1)} AU`;
            document.getElementById('val-demand').innerText = `${parseFloat(inputDemand.value).toFixed(1)} W`;

            // HUD
            const percentCaptured = (captureRatio * 100).toFixed(1);
            document.getElementById('hud-capture').innerText = `${percentCaptured}%`;

            const riskHUD = document.getElementById('hud-risk');
            if (state.coloniesActive && captureRatio > 0.5) {
                riskHUD.innerText = '0.0% (IMUNE)';
                riskHUD.style.color = 'var(--accent-green)';
            } else {
                riskHUD.innerText = '24.5% (VULNERÁVEL)';
                riskHUD.style.color = 'var(--accent-gold)';
            }

            document.getElementById('hud-habitats').innerText = state.coloniesActive 
                ? '8 PLANETAS + 120 LUAS' 
                : '1 PLANETA BASE';
        }

        // Eventos
        inputCollectors.addEventListener('input', updatePhysics);
        inputRadius.addEventListener('input', updatePhysics);
        inputDemand.addEventListener('input', updatePhysics);
        swBeaming.addEventListener('change', updatePhysics);
        swColonies.addEventListener('change', updatePhysics);

        buildDysonSwarm();

        // Loop de Renderização no Canvas
        function render() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            const cx = canvas.width / 2;
            const cy = canvas.height / 2;
            const starRadius = 55;
            const baseOrbitRadius = 140 * state.orbitRadiusFactor;

            state.starPulse += 0.02;

            // 1. Poeira Cósmica / Fundo Estelar
            ctx.fillStyle = 'rgba(255, 255, 255, 0.2)';
            for (let i = 0; i < 50; i++) {
                const sx = (Math.sin(i * 99) * 0.5 + 0.5) * canvas.width;
                const sy = (Math.cos(i * 33) * 0.5 + 0.5) * canvas.height;
                ctx.fillRect(sx, sy, 1, 1);
            }

            // 2. Estrela Central (Sol)
            const pulseGlow = Math.sin(state.starPulse) * 4;
            const starGrad = ctx.createRadialGradient(cx, cy, 10, cx, cy, starRadius + 30 + pulseGlow);
            starGrad.addColorStop(0, '#ffffff');
            starGrad.addColorStop(0.2, '#fef08a');
            starGrad.addColorStop(0.5, '#f59e0b');
            starGrad.addColorStop(0.85, 'rgba(249, 115, 22, 0.4)');
            starGrad.addColorStop(1, 'rgba(0, 0, 0, 0)');

            ctx.beginPath();
            ctx.arc(cx, cy, starRadius + 35 + pulseGlow, 0, Math.PI * 2);
            ctx.fillStyle = starGrad;
            ctx.fill();

            // Núcleo denso da estrela
            ctx.beginPath();
            ctx.arc(cx, cy, starRadius, 0, Math.PI * 2);
            ctx.fillStyle = '#fef08a';
            ctx.shadowColor = '#f59e0b';
            ctx.shadowBlur = 25;
            ctx.fill();
            ctx.shadowBlur = 0;

            // 3. Trajetórias Orbitais do Enxame (Anéis Visualizadores)
            for (let r = 0.8; r <= 1.2; r += 0.2) {
                ctx.beginPath();
                ctx.ellipse(cx, cy, baseOrbitRadius * r, baseOrbitRadius * r * 0.4, 0, 0, Math.PI * 2);
                ctx.strokeStyle = 'rgba(245, 158, 11, 0.12)';
                ctx.lineWidth = 1;
                ctx.stroke();
            }

            // 4. Feixes de Transmissão de Energia Interplanetária
            if (state.energyBeaming) {
                const numBeams = 6;
                for (let b = 0; b < numBeams; b++) {
                    const angle = (b / numBeams) * Math.PI * 2 + state.starPulse * 0.2;
                    const targetX = cx + Math.cos(angle) * (baseOrbitRadius + 180);
                    const targetY = cy + Math.sin(angle) * (baseOrbitRadius * 0.4 + 90);

                    ctx.beginPath();
                    ctx.moveTo(cx, cy);
                    ctx.lineTo(targetX, targetY);
                    ctx.strokeStyle = `rgba(56, 189, 248, ${0.15 + Math.sin(state.starPulse + b) * 0.1})`;
                    ctx.lineWidth = 1.5;
                    ctx.setLineDash([4, 8]);
                    ctx.stroke();
                    ctx.setLineDash([]);
                }
            }

            // 5. Renderização do Enxame de Dyson (Coletores Solares em 3D Projetado)
            collectors.forEach(c => {
                c.angle += c.speed;

                const currentRadiusX = baseOrbitRadius * (1 + c.ringIndex * 0.08);
                const currentRadiusY = currentRadiusX * 0.4;

                const x = cx + Math.cos(c.angle) * currentRadiusX;
                const y = cy + Math.sin(c.angle) * currentRadiusY + Math.sin(c.angle) * c.inclination * 40;

                // Teste de profundidade simples para esconder parte do enxame atrás da estrela
                const isBehind = Math.sin(c.angle) < 0 && Math.abs(x - cx) < starRadius;

                if (!isBehind) {
                    ctx.beginPath();
                    ctx.arc(x, y, c.size, 0, Math.PI * 2);
                    ctx.fillStyle = '#f59e0b';
                    ctx.shadowColor = '#f97316';
                    ctx.shadowBlur = 6;
                    ctx.fill();
                    ctx.shadowBlur = 0;

                    // Conexão entre coletores próximos (Efeito Malha de Dyson)
                    if (Math.random() < 0.03) {
                        ctx.beginPath();
                        ctx.moveTo(cx, cy);
                        ctx.lineTo(x, y);
                        ctx.strokeStyle = 'rgba(245, 158, 11, 0.08)';
                        ctx.lineWidth = 0.5;
                        ctx.stroke();
                    }
                }
            });

            // 6. Colônias / Planetas Interconectados (se ativas)
            if (state.coloniesActive) {
                const planets = [
                    { r: baseOrbitRadius + 110, size: 5, color: '#38bdf8', angle: state.starPulse * 0.1 },
                    { r: baseOrbitRadius + 180, size: 7, color: '#f97316', angle: state.starPulse * 0.06 + 2 }
                ];

                planets.forEach(p => {
                    const px = cx + Math.cos(p.angle) * p.r;
                    const py = cy + Math.sin(p.angle) * (p.r * 0.4);

                    ctx.beginPath();
                    ctx.arc(px, py, p.size, 0, Math.PI * 2);
                    ctx.fillStyle = p.color;
                    ctx.fill();

                    // Anel do planeta
                    ctx.beginPath();
                    ctx.arc(px, py, p.size + 3, 0, Math.PI * 2);
                    ctx.strokeStyle = 'rgba(255,255,255,0.3)';
                    ctx.lineWidth = 0.8;
                    ctx.stroke();
                });
            }

            requestAnimationFrame(render);
        }

        // Inicialização
        updatePhysics();
        render();
    </script>
</body>
</html>
