# EconBalancer 🪙

> An interactive macroeconomics game in a Jupyter notebook. Keep an economy in
> equilibrium by balancing **Aggregate Supply** and **Aggregate Demand** against
> random economic shocks.

EconBalancer started as a small classroom prototype for understanding the
supply/demand equilibrium model. This repo contains both the **original
prototype** and a **refactored, playable version** with cleaner code, real game
mechanics, scoring, and a visualization — so you can see the before/after.

---

## 🎮 What the game is

You play an economic decision-maker. Each round a random **shock** hits the
economy (a tech boom, a spending cut, a natural disaster…) and pushes Aggregate
Supply (AS) and Aggregate Demand (AD) apart. You spend **budget points** on
**policy levers** to nudge them back together. The economy is *balanced* when
`|AS − AD|` stays within a small tolerance band. Stay balanced to score; run out
of budget and the game ends.

Three **roles** give you different strengths:

| Role            | Supply lever | Demand lever | Style                              |
|-----------------|:------------:|:------------:|------------------------------------|
| Economist       | medium       | medium       | balanced, flexible                 |
| Policy Maker    | weak         | strong       | demand-side (fiscal/monetary)      |
| Business Leader | strong       | weak         | supply-side (production/investment)|

You also get **technology tokens** that double a lever's effect when you need a
big correction.

---

## 📁 What's in this repo

| File                      | Description                                                            |
|---------------------------|------------------------------------------------------------------------|
| `EconBalancer.ipynb`      | The refactored game: engine class, scripted demo, chart, interactive UI |
| `POME_EL_original.ipynb`  | The original four-stage prototype, kept for comparison                  |
| `requirements.txt`        | Dependencies                                                           |

---

## 🚀 Quick start

```bash
git clone https://github.com/<your-username>/econbalancer.git
cd econbalancer
pip install -r requirements.txt
jupyter lab   # or: jupyter notebook
```

Open `EconBalancer.ipynb` and run the cells top to bottom. The final cell is the
interactive widget — pick a role, hit **New Round**, and start balancing.

> GitHub renders notebooks but won't run the interactive widgets. Clone and run
> locally for the full experience.

---

## 🏗️ How it's built

The refactored version is organized around a single engine class so the rules
live in one place and nothing depends on hidden global state:

```
SCENARIOS / ROLES (config)
        │
        ▼
   EconBalancer  ← all game state (AS, AD, budget, tokens, score, history)
        │
        ├── new_round()      → applies a random shock
        ├── apply_policy()   → spends budget to move a lever (role-weighted)
        ├── is_balanced / gap / status / summary
        │
        ├──► scripted auto-player  (reproducible demo)
        ├──► matplotlib chart      (AS vs AD + gap over time)
        └──► ipywidgets UI         (play it interactively)
```

Because the engine takes a `seed` and exposes its state, the exact same class
drives the console demo, the chart, and the interactive UI — and would be
straightforward to unit-test.

---

## 🔧 What changed from the prototype

The original notebook was a useful first pass but had several issues. The
refactor addresses each one.

### Bugs fixed

- **The game was effectively unwinnable.** Equilibrium was checked with exact
  equality (`AS == AD`). Since shocks and actions moved values in fixed steps,
  hitting the exact number was nearly impossible. Now equilibrium is a
  **tolerance band** (`|AS − AD| ≤ tolerance`), which is both more realistic and
  actually achievable.
- **Roles did nothing.** In the original, choosing Economist / Policy Maker /
  Business Leader had no effect on gameplay. Roles now change the **strength of
  each lever**, giving real strategic differences.
- **Technology tokens were dead code.** Declared but never used. They now
  **double a lever's effect** when spent.
- **The budget could softlock the game.** Spending bottomed out with no
  resolution. The budget now defines a clear **end condition**, and a final
  **score** rewards how long you stayed balanced.
- **Tangled global state.** Every version leaned on module-level globals
  (`global AS`, `global AD`, …), which made the logic fragile and untestable.
  Replaced with a self-contained `EconBalancer` dataclass.
- **Tkinter cell crashed in notebooks** (`TclError: no display`). Replaced with
  ipywidgets, which runs inside Jupyter.
- Removed unused imports (`clear_output`) and dead helpers.

### Features added

- A **scripted, reproducible playthrough** (seeded RNG) so the notebook produces
  output even without interaction — handy for GitHub previews.
- A **matplotlib visualization** of AS vs AD with the equilibrium band and a
  per-round gap bar chart.
- **Scoring** and a clear win/lose loop.
- A richer scenario list (export boom, credit crunch) and asymmetric shocks for
  more variety.

---

## 💡 Ideas for going further

- Add an **AI opponent** or compare strategies (greedy vs. planning-ahead).
- Track stats across many simulated games and chart **win rate by role**.
- Add **inflation / unemployment** as derived indicators for more depth.
- Wrap the engine in a small **Streamlit** app for a shareable web version.
- Add a **`tests/` suite** (pytest) over the engine — the design already supports it.

---

## 📚 Background

Aggregate Supply and Aggregate Demand are core to the **AS–AD model** in
macroeconomics: the economy moves toward equilibrium where the two meet, and
shocks (policy, technology, shocks to confidence) shift the curves. EconBalancer
turns that idea into a hands-on loop.

---

## 📝 License

Released under the MIT License — feel free to use and adapt.
