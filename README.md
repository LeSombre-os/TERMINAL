# ⬢ TERMINAL — Simulateur de Trading Temps Réel & Bot Studio

Simulateur de trading crypto temps réel + studio d'entraînement et de backtesting de bots,
dans **un seul fichier HTML** (aucun build, aucune dépendance à installer).

> **Prix réels, argent fictif.** Les cours, le carnet d'ordres et le ruban viennent de Binance en direct.
> Le capital, les positions et les PnL sont une simulation (paper trading) : aucun fonds réel,
> aucun ordre réel.

---

## Fonctionnalités

### 📈 Marché temps réel
- Chandeliers japonais OHLCV (10 paires : BTC, ETH, SOL, BNB, XRP, DOGE, ADA, AVAX, LINK, ARB).
- Timeframes 1m · 5m · 15m · 1h · 4h · 1d.
- Indicateurs : EMA, SMA, Bandes de Bollinger + sous-graphique RSI / MACD (+ ligne de signal) / Volume.
- Carnet d'ordres, ruban des transactions récentes, variation 24h.
- Vue **◉ Direct** (120 dernières bougies, micro-mouvements visibles) / **Fit** (tout l'historique).
- Marqueurs ▲▼ d'achats/ventes sur le graphique.
- Chaîne de repli automatique : **WebSocket Binance → REST → simulation locale** (pastille de statut + badge `BINANCE · PRIX RÉELS` / `SIMULATION LOCALE`).

### 💰 Paper trading
- Capital de départ ajustable (défaut 10 000 $).
- Ordres **marché / limite / stop**, avec **Stop-Loss / Take-Profit attachés (OCO)**.
- **Slippage** (bps) + frais **maker/taker** configurables, levier ×1 à ×10, long/short.
- PnL latent et réalisé **par trade**, marge utilisée, alerte liquidation simplifiée.
- Tableau de bord : win rate, profit factor, Sharpe, Max Drawdown, courbe d'équité.
- Export CSV de l'historique.
- Sécurité : une position est **liée à son symbole** (ordres bloqués avec explication si on change de paire).

### 🤖 Studio de bots
| Stratégie | Logique | Paramètres |
|---|---|---|
| MA Crossover | Croisement EMA rapide/lente | fast 9, slow 21 |
| RSI extrêmes | Achat survente / vente surachat (mean-reversion) | période 14, seuils 30/70 |
| Grid Trading | Accumule sous le prix de base, distribue au-dessus | 5 niveaux, pas 0,5 % |
| Neuro-heuristique | Score MACD+RSI+momentum + **Q-table apprise** (Q-learning tabulaire) | poids MACD/RSI, seuil |

- **Backtest accéléré** sur l'historique live ou sur CSV importé (glisser-déposer, format `time,open,high,low,close,volume`).
- Rapport : net, rendement, trades, win rate, profit factor, MaxDD, Sharpe, gains/pertes moyens + courbe d'équité.
- **Grid search** (≤ 120 combinaisons) avec bouton *Appliquer* vers le bot live.
- **Bot live (paper)** : évalue le prix en continu, trade sur le portefeuille virtuel, heartbeat et état visible (`Dernière analyse / Dernier signal`), pause auto si position manuelle sur une autre paire.

---

## Démarrage rapide

**Option A — double-clic** : ouvrez `index.html` dans un navigateur récent (Chrome/Edge/Firefox).
Internet requis pour les prix live et la librairie graphique ; sinon la simulation locale prend le relais.

**Option B — serveur local** (recommandé) :
```bash
cd Trading
python3 -m http.server 8080
# → http://localhost:8080
```

Aucune installation, aucun build, aucune clé API.

## Guide d'utilisation

1. **Observer** : choisissez une paire à gauche, un timeframe en haut du graphique, activez EMA/BB/SMA.
   Si la courbe semble plate, cliquez **◉ Direct** (l'autoscale sur 500 bougies écrase les micro-mouvements).
2. **Trader à la main** (panneau *Ordre*) : sens, quantité en coins, levier, SL/TP optionnels → *Exécuter*.
   Suivez la position dans l'onglet *Positions*, les ordres en attente dans *Ordres*, le détail dans *Historique*.
3. **Faire trader un bot** (panneau *Bot live*) : choisissez la stratégie, réglez la quantité et
   l'intervalle d'évaluation, ▶ *Activer*. Pour le voir agir vite : **Grid + short autorisé**, évaluation toutes les 2–3 s.
4. **Backtester** (onglet bas *Backtest & Optimisation*) : *Utiliser l'historique live* ou importez un CSV,
   réglez capital/quantité/short → *Lancer le backtest*, puis *Grid search* et *Appliquer* les meilleurs paramètres.

## Modèle de simulation

- Contrats synthétiques style CFD : `balance = collatéral + PnL réalisé`, `équité = balance + PnL latent`.
- Marché : exécution au prix ± slippage (frais taker). Limite exécutée : prix exact (frais maker).
- Défauts : taker 0,075 %, maker 0,02 %, slippage 2 bps — modifiables dans l'onglet *Risque*.

## Architecture

```
Trading/
├── index.html   # application complète (~980 lignes, ~73 Ko)
└── README.md
```

`index.html` est découpé en blocs commentés : utils · état global · indicateurs (SMA/EMA/RSI/MACD/Bollinger,
pur JS) · datafeed (WS/REST/simu) · graphiques (Lightweight-Charts via CDN) · broker paper · stats ·
stratégies + backtest + grid search · bot live · UI · boot. Mises à jour prix via `requestAnimationFrame`
(60 FPS, rendus limités à ~12 lignes pour le carnet/tape).

## Vérification

Logique des 4 stratégies, backtest, grid search et métriques testés en Node ;
parcours complet des ~25 contrôles testé en jsdom (0 erreur). Pour rejouer ce type de contrôle,
un navigateur suffit : chaque bouton a un effet visible immédiat (log, tableau ou métrique).

## Limites connues

- Position nette **unique** (pas de multi-positions simultanées).
- Pas de persistance : un rechargement réinitialise le portefeuille (pensez à l'export CSV).
- Timeframe minimum 1m (limite du spot Binance) ; backtest sans slippage intra-bougie.
- Q-learning tabulaire de démonstration, pas un modèle ML profond.

## Captures

> Ajoutez vos captures ici, ex. `docs/screenshot.png`, puis référencez-les :
> `![terminal](docs/screenshot.png)`

## Licence

Aucune licence définie pour le moment (tous droits réservés par défaut).
Recommandation : ajouter une `LICENSE` (ex. MIT) avant toute publication.
