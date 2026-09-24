<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>SROS - Módulo de Suporte à Vida</title>
    <style>
        :root {
            --bg-color: #050a0e;
            --panel-bg: rgba(10, 25, 41, 0.7);
            --neon-cyan: #00f0ff;
            --neon-green: #39ff14;
            --text-color: #ffffff;
        }
        body {
            background-color: var(--bg-color);
            color: var(--text-color);
            font-family: 'Courier New', Courier, monospace;
            margin: 0;
            padding: 20px;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            overflow: hidden;
        }
        .hud-panel {
            background: var(--panel-bg);
            border: 2px solid var(--neon-cyan);
            border-radius: 10px;
            padding: 30px;
            width: 450px;
            box-shadow: 0 0 20px rgba(0, 240, 255, 0.2);
            backdrop-filter: blur(10px);
        }
        h2 {
            text-align: center;
            color: var(--neon-cyan);
            text-transform: uppercase;
            letter-spacing: 2px;
            margin-top: 0;
            border-bottom: 1px solid var(--neon-cyan);
            padding-bottom: 10px;
        }
        .metric {
            margin: 20px 0;
            font-size: 1.1em;
        }
        .progress-bar {
            background: #112233;
            border: 1px solid var(--neon-cyan);
            height: 20px;
            border-radius: 5px;
            overflow: hidden;
            margin-top: 5px;
        }
        .progress-fill {
            height: 100%;
            width: 0%;
            transition: width 0.5s ease-in-out;
        }
        #o2-fill { background: var(--neon-cyan); box-shadow: 0 0 10px var(--neon-cyan); }
        #co2-fill { background: #ff3333; box-shadow: 0 0 10px #ff3333; }
        .log-box {
            background: #020508;
            border: 1px solid #112233;
            height: 120px;
            overflow-y: auto;
            padding: 10px;
            font-size: 0.85em;
            color: var(--neon-green);
            border-radius: 5px;
        }
        .btn-action {
            width: 100%;
            background: transparent;
            border: 2px solid var(--neon-green);
            color: var(--neon-green);
            padding: 10px;
            font-family: inherit;
            font-weight: bold;
            cursor: pointer;
            text-transform: uppercase;
            transition: all 0.3s;
            margin-top: 15px;
        }
        .btn-action:hover {
            background: var(--neon-green);
            color: #000;
            box-shadow: 0 0 15px var(--neon-green);
        }
    </style>
</head>
<body>
    <div class="hud-panel">
        <h2>SROS // LIFE_SUPPORT</h2>
        <div class="metric">
            Nível de O₂ Purificado: <span id="o2-val">98%</span>
            <div class="progress-bar"><div id="o2-fill" class="progress-fill"></div></div>
        </div>
        <div class="metric">
            Concentração de CO₂ Expirado: <span id="co2-val">0.04%</span>
            <div class="progress-bar"><div id="co2-fill" class="progress-fill"></div></div>
        </div>
        <div class="metric" style="font-size: 0.9em; color: #aaa;">
            Potência de Termólise: <span id="power-val">98.6 W</span>
        </div>
        <div class="log-box" id="log">--- SISTEMA SROS INICIALIZADO ---</div>
        <button class="btn-action" onclick="forcarCiclo()">Forçar Ciclo de Purificação</button>
    </div>

    <script>
        let o2 = 98;
        let co2 = 0.04;
        const logBox = document.getElementById('log');

        function updateUI() {
            document.getElementById('o2-val').innerText = o2.toFixed(2) + "%";
            document.getElementById('co2-val').innerText = co2.toFixed(2) + "%";
            document.getElementById('o2-fill').style.width = o2 + "%";
            document.getElementById('co2-fill').style.width = (co2 * 20) + "%"; 
        }

        function addLog(text) {
            const time = new Date().toLocaleTimeString();
            logBox.innerHTML += `<br>[${time}] ${text}`;
            logBox.scrollTop = logBox.scrollHeight;
        }

        function simularMetabolismo() {
            co2 += Math.random() * 0.1;
            o2 -= Math.random() * 0.08;
            if(co2 > 1.5) {
                addLog("Alerta: CO2 Elevado. Ativando quebra quântica...");
                co2 = 0.04;
                o2 += 1.2;
                addLog("Sucesso: Carbono extraído e enviado para nano-grelhas.");
            }
            updateUI();
        }

        function forcarCiclo() {
            addLog("Executando termólise assistida manual...");
            co2 = 0.01;
            o2 = 100;
            updateUI();
            addLog("Ciclo completo. O2 purificado a 100%.");
        }

        updateUI();
        setInterval(simularMetabolismo, 2000);
    </script>
</body>
</html>
