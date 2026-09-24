<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Módulo 4: Ensaio de Fadiga por Radiação - SROS</title>
    <script src="https://jsdelivr.net"></script>
</head>
<body class="bg-slate-950 text-slate-100 min-h-screen p-8 font-sans">
    <div class="max-w-4xl mx-auto bg-slate-900 border border-amber-900/40 rounded-xl p-6 shadow-2xl">
        <header class="border-b border-amber-800/30 pb-4 mb-6">
            <h1 class="text-2xl font-bold text-amber-500 tracking-wide uppercase">CNT Fatigue & Ionizing Radiation Simulator</h1>
            <p class="text-slate-400 text-sm">Degradação estrutural de nanotubos de carbono sob bombardeio de prótons e raios gama.</p>
        </header>

        <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-6">
            <div class="bg-black p-4 rounded border border-slate-800">
                <span class="text-xs font-mono text-slate-500 block uppercase">Nível de Radiação</span>
                <div class="text-2xl font-mono font-bold text-amber-400 mt-1" id="radLevel">450 mSv/h</div>
            </div>
            <div class="bg-black p-4 rounded border border-slate-800">
                <span class="text-xs font-mono text-slate-500 block uppercase">Integridade Estrutural CNT</span>
                <div class="text-2xl font-mono font-bold text-emerald-400 mt-1" id="integLevel">100.0%</div>
            </div>
            <div class="bg-black p-4 rounded border border-slate-800">
                <span class="text-xs font-mono text-slate-500 block uppercase">Danos Acumulados</span>
                <div class="text-2xl font-mono font-bold text-red-400 mt-1" id="dislocationCount">0 eV</div>
            </div>
        </div>

        <div class="bg-black p-4 rounded border border-slate-800 flex justify-center">
            <canvas id="radiationCanvas" width="600" height="200" class="w-full bg-slate-950 block rounded"></canvas>
        </div>

        <div class="mt-4 flex gap-4">
            <button id="startRadiation" class="bg-amber-600 hover:bg-amber-500 text-black font-bold font-mono text-xs uppercase px-4 py-2 rounded transition-colors">Iniciar Bombardeio</button>
            <button id="resetRadiation" class="bg-slate-800 hover:bg-slate-700 text-slate-300 font-mono text-xs uppercase px-4 py-2 rounded transition-colors">Resetar Estrutura</button>
        </div>
    </div>

    <script>
        const canvas = document.getElementById('radiationCanvas');
        const ctx = canvas.getContext('2d');
        const startBtn = document.getElementById('startRadiation');
        const resetBtn = document.getElementById('resetRadiation');
        const integLevel = document.getElementById('integLevel');
        const dislocationCount = document.getElementById('dislocationCount');

        let particles = [];
        let integrity = 100.0;
        let totalDamageEv = 0;
        let isSimulating = false;
        let animationFrame;

        function spawnParticle() {
            if(!isSimulating) return;
            particles.push({
                x: 0,
                y: Math.random() * canvas.height,
                speed: Math.random() * 8 + 4,
                energy: Math.random() * 50 + 20
            });
        }

        function update() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Desenhar Malha CNT (linhas paralelas representando as paredes do tubo)
            ctx.strokeStyle = `rgba(16, 185, 129, ${integrity/100})`;
            ctx.lineWidth = 4;
            for(let y = 30; y < canvas.height; y += 40) {
                ctx.beginPath();
                ctx.moveTo(150, y);
                ctx.lineTo(550, y);
                ctx.stroke();
            }

            // Atualizar e desenhar partículas ionizantes
            particles.forEach((p, index) => {
                p.x += p.speed;
                ctx.fillStyle = '#f59e0b';
                ctx.beginPath();
                ctx.arc(p.x, p.y, 3, 0, Math.PI * 2);
                ctx.fill();

                // Detecção de colisão com a área da armadura (x > 150)
                if(p.x >= 150 && p.x <= 550) {
                    if(Math.random() > 0.96) { // Chance de colisão atômica
                        integrity = Math.max(0, integrity - 0.2);
                        totalDamageEv += Math.floor(p.energy);
                        integLevel.innerText = integrity.toFixed(1) + "%";
                        dislocationCount.innerText = totalDamageEv + " eV";
                        
                        // Efeito visual de quebra
                        ctx.fillStyle = '#ef4444';
                        ctx.beginPath();
                        ctx.arc(p.x, p.y, 8, 0, Math.PI * 2);
                        ctx.fill();
                        
                        particles.splice(index, 1);
                    }
                }

                if(p.x > canvas.width) particles.splice(index, 1);
            });

            if(integrity < 50) integLevel.className = "text-2xl font-mono font-bold text-amber-500 mt-1";
            if(integrity < 20) integLevel.className = "text-2xl font-mono font-bold text-red-500 mt-1";

            if(isSimulating) {
                if(Math.random() > 0.4) spawnParticle();
                animationFrame = requestAnimationFrame(update);
            }
        }

        startBtn.addEventListener('click', () => {
            isSimulating = !isSimulating;
            startBtn.innerText = isSimulating ? "Pausar Bombardeio" : "Iniciar Bombardeio";
            if(isSimulating) update();
        });

        resetBtn.addEventListener('click', () => {
            isSimulating = false;
            cancelAnimationFrame(animationFrame);
            startBtn.innerText = "Iniciar Bombardeio";
            particles = [];
            integrity = 100.0;
            totalDamageEv = 0;
            integLevel.innerText = "100.0%";
            integLevel.className = "text-2xl font-mono font-bold text-emerald-400 mt-1";
            dislocationCount.innerText = "0 eV";
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            update();
        });

        update();
    </script>
</body>
</html>
