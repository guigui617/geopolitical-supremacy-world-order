export interface NationState {
  name: string;
  regime: 'democracy' | 'monarchy' | 'dictatorship';
  gdp: number;
  treasury: number;
  popularity: number;
  stability: number;
  budgets: {
    health: number;
    education: number;
    military: number;
    research: number;
  };
}

const htmlContent = `
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Geopolitical Supremacy : World Order</title>
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-slate-950 text-slate-100 h-screen flex flex-col overflow-hidden font-sans">
    <header class="bg-slate-900 border-b border-slate-800 p-4 flex justify-between items-center shadow-lg">
        <div>
            <h1 class="text-xl font-black tracking-wider text-emerald-400">GEOPOLITICAL SUPREMACY <span class="text-xs text-slate-400 font-normal">WORLD ORDER</span></h1>
            <p class="text-xs text-slate-400">Nation active : <span id="nation-name" class="text-white font-bold">République Française</span> (<span id="nation-regime">Démocratie</span>)</p>
        </div>
        <div id="stats-bar" class="flex gap-4 text-sm font-semibold">
            <div class="bg-slate-800 px-3 py-1.5 rounded border border-slate-700">Trésorerie : <span id="val-treasury" class="text-emerald-400">120 B$</span></div>
            <div class="bg-slate-800 px-3 py-1.5 rounded border border-slate-700">PIB : <span id="val-gdp" class="text-blue-400">2,800 B$</span></div>
            <div class="bg-slate-800 px-3 py-1.5 rounded border border-slate-700">Popularité : <span id="val-pop" class="text-amber-400">65%</span></div>
            <div class="bg-slate-800 px-3 py-1.5 rounded border border-slate-700">Stabilité : <span id="val-stab" class="text-purple-400">90%</span></div>
        </div>
    </header>

    <div class="flex flex-1 overflow-hidden">
        <main class="flex-1 bg-slate-900 relative flex flex-col items-center justify-center p-6">
            <div class="absolute inset-0 opacity-20 bg-[radial-gradient(#334155_1px,transparent_1px)] [background-size:16px_16px]"></div>
            <div class="z-10 text-center max-w-xl bg-slate-900/80 p-8 rounded-2xl border border-slate-800 backdrop-blur shadow-2xl">
                <h2 class="text-2xl font-bold mb-3 text-emerald-400">Salle de Commandement Stratégique</h2>
                <p class="text-sm text-slate-300 mb-6">Le monde est instable. Ajustez vos budgets sectoriels à droite et validez vos réformes chaque mois pour maintenir votre pays au sommet.</p>
                <div id="event-box" class="bg-slate-800 border-l-4 border-emerald-500 p-4 rounded text-left text-xs text-slate-300">
                    <strong>Rapport du Cabinet :</strong> Situation stable. En attente des directives budgétaires pour le prochain exercice mensuel.
                </div>
            </div>
        </main>

        <aside class="w-96 bg-slate-900 border-l border-slate-800 p-6 flex flex-col gap-6">
            <h2 class="text-lg font-bold border-b border-slate-800 pb-2">Arbitrage Budgétaire (Mensuel)</h2>
            <div class="space-y-4 text-sm flex-1">
                <div>
                    <div class="flex justify-between mb-1">
                        <label class="text-slate-400">Budget Militaire</label>
                        <span id="label-mil" class="text-emerald-400 font-bold">25 B$</span>
                    </div>
                    <input id="input-mil" type="range" min="5" max="100" value="25" class="w-full accent-emerald-500">
                </div>
                <div>
                    <div class="flex justify-between mb-1">
                        <label class="text-slate-400">Recherche & Tech</label>
                        <span id="label-res" class="text-emerald-400 font-bold">15 B$</span>
                    </div>
                    <input id="input-res" type="range" min="5" max="100" value="15" class="w-full accent-emerald-500">
                </div>
                <div>
                    <div class="flex justify-between mb-1">
                        <label class="text-slate-400">Santé & Social</label>
                        <span id="label-soc" class="text-emerald-400 font-bold">30 B$</span>
                    </div>
                    <input id="input-soc" type="range" min="5" max="100" value="30" class="w-full accent-emerald-500">
                </div>
            </div>
            <button onclick="applyTurn()" class="w-full bg-emerald-600 hover:bg-emerald-500 text-slate-950 font-black py-4 rounded-xl transition shadow-lg shadow-emerald-900/40 text-center tracking-wide">
                VALIDER LE MOIS ➔
            </button>
        </aside>
    </div>

    <script>
        let state = {
            name: "République Française",
            regime: "democracy",
            gdp: 2800,
            treasury: 120,
            popularity: 65,
            stability: 90,
            budgets: { health: 30, education: 20, military: 25, research: 15 }
        };

        const inputMil = document.getElementById('input-mil');
        const inputRes = document.getElementById('input-res');
        const inputSoc = document.getElementById('input-soc');

        function updateUI() {
            document.getElementById('val-treasury').innerText = Math.round(state.treasury) + ' B$';
            document.getElementById('val-gdp').innerText = Math.round(state.gdp) + ' B$';
            document.getElementById('val-pop').innerText = Math.round(state.popularity) + '%';
            document.getElementById('val-stab').innerText = Math.round(state.stability) + '%';
            
            document.getElementById('label-mil').innerText = inputMil.value + ' B$';
            document.getElementById('label-res').innerText = inputRes.value + ' B$';
            document.getElementById('label-soc').innerText = inputSoc.value + ' B$';
        }

        async function applyTurn() {
            state.budgets.military = parseInt(inputMil.value);
            state.budgets.research = parseInt(inputRes.value);
            state.budgets.health = parseInt(inputSoc.value);

            try {
                const response = await fetch('/api/turn', {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(state)
                });
                const data = await response.json();
                state = data;
                updateUI();
                document.getElementById('event-box').innerHTML = '<strong>Mois validé :</strong> Le tour s\\'est déroulé avec succès. L\\'économie a évolué.';
            } catch (e) {
                alert('Erreur lors de la synchronisation avec le serveur de simulation.');
            }
        }
        updateUI();
    </script>
</body>
</html>
`;

export default {
  async fetch(request: Request): Promise<Response> {
    const url = new URL(request.url);

    if (url.pathname === '/api/turn' && request.method === 'POST') {
      const nation: NationState = await request.json();

      const taxRate = 0.26;
      const taxRevenue = nation.gdp * taxRate;
      const totalSpent = nation.budgets.health + nation.budgets.education + nation.budgets.military + nation.budgets.research;

      nation.treasury += (taxRevenue - totalSpent);

      if (nation.regime === 'democracy') {
        const socialBonus = (nation.budgets.health + nation.budgets.education) / 100;
        nation.popularity = Math.min(100, Math.max(0, nation.popularity + socialBonus - 0.5));
      } else {
        nation.stability = Math.min(100, Math.max(0, nation.stability + (nation.budgets.military / 30) - 0.6));
      }

      const gdpGrowth = 0.002 + (nation.budgets.research * 0.0001);
      nation.gdp *= (1 + gdpGrowth);

      return new Response(JSON.stringify(nation), {
        headers: { 'Content-Type': 'application/json' }
      });
    }

    return new Response(htmlContent, {
      headers: { 'Content-Type': 'text/html;charset=UTF-8' }
    });
  },
};
