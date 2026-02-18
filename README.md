# Blackjack-Monte-Carlo-Simulator-Hi-Lo-Card-Counting
Monte Carlo blackjack simulator in Python. Compares Hi-Lo card counting vs flat betting across 1,000 sessions. Educational project — explores bet sizing, true count, basic strategy and bankroll dynamics. Kelly Criterion not implemented.
> ** Educational purposes only.** This project is a quantitative exercise in probability, simulation design, and bankroll management theory. It is not a gambling guide, and it does not constitute financial or strategic advice for casino play.

---

## Overview

This Python simulator models a 6-deck blackjack environment with multiple players and evaluates two strategies side-by-side across thousands of simulated sessions:

- **With card counting** — Hi-Lo true count with a tiered bet spread
- **Without card counting** — flat betting at a fixed base bet

The goal is to study, in a controlled simulation, whether card counting produces a statistically meaningful edge over flat betting, how bankroll paths evolve over time, and how often ruin occurs under each strategy.

---

## How It Works

### Game Rules
- 6-deck shoe, H17 (dealer hits on soft 17)
- 4 players at the table (player 0 = you, the rest are bots playing basic strategy)
- Reshuffle triggered when the shoe drops below 25% of its original size (75% penetration)
- Blackjack pays 3:2

### Card Counting — Hi-Lo System
The Hi-Lo count assigns a value to each visible card:

| Cards     | Count |
|-----------|-------|
| 2 – 6     | +1    |
| 7 – 9     |  0    |
| 10, J, Q, K, A | −1 |

The **running count** is maintained across all visible cards (all players' cards and the dealer's up-card). The **true count** normalises by decks remaining:

```
True Count = Running Count / Decks Remaining
```

### Bet Sizing (Counting Mode)

| True Count | Bet (EUR) |
|------------|-----------|
| TC < 1     | 10 (base) |
| TC ≥ 1     | 50        |
| TC ≥ 2     | 200       |
| TC ≥ 3     | 500       |
| TC ≥ 4     | 1,200     |

### Basic Strategy (Simplified)
- Hard 17+: stand
- Hard 13–16: stand vs. dealer 2–6, otherwise hit
- Hard 12: stand vs. dealer 4–6, otherwise hit
- Hard 11: always double down
- Hard 10: double down unless dealer shows 10-value or Ace
- Soft 19+: stand; soft 18 or less: hit

---

## Monte Carlo Setup

| Parameter         | Value       |
|-------------------|-------------|
| Initial Bankroll  | EUR 10,000  |
| Base Bet          | EUR 10      |
| Max Bet           | EUR 1,200   |
| Sessions          | 1,000       |
| Hands per session | 200 – 1,000 (random) |
| Number of decks   | 6           |

---

## Output

The simulation produces:

- **Avg ROI** per strategy
- **% winning sessions** (final bankroll > starting bankroll)
- **% bankrupt** sessions
- **Global min/max** bankroll observed
- **Delta ROI** (counting edge over flat)
- Three charts: bankroll pathways (counting), bankroll pathways (flat), and a final bankroll distribution histogram

---

## Known Limitations & Why It Probably Doesn't Work as Expected

This simulator is an educational prototype. Several important elements are simplified or missing:

**1. Bet sizing is not Kelly-optimal**
The tiered bet spread used here is heuristic. In theory, optimal bet sizing under card counting follows the **Kelly Criterion**:

```
f* = edge / odds
```

where `f*` is the fraction of bankroll to bet. Without proper Kelly sizing, the bet spread is likely too aggressive at high true counts (risking ruin) and too conservative at moderate counts (leaving edge on the table). Implementing Kelly would require estimating the player's edge as a continuous function of the true count, which adds significant complexity.

**2. Hand count parameters are not calibrated**
The range of 200–1,000 hands per session is arbitrary. Real card counting profitability is highly sensitive to the number of hands played — both in terms of expected value convergence and variance. The optimal range depends on the specific bet spread, penetration depth, and bankroll size. This hasn't been tuned here.

**3. Basic strategy is simplified**
The strategy implemented covers the most common decisions but omits several important cases: pair splitting, soft doubling, surrender, and full hard/soft doubling tables. A complete basic strategy would reduce the house edge by a few additional basis points and meaningfully change outcomes over thousands of hands.

**4. No risk-of-ruin management**
There is no dynamic bankroll scaling — bet sizes are fixed thresholds regardless of how the bankroll has grown or shrunk during a session. A real counting system would resize bets proportionally to the current bankroll.

**5. Variance is high**
Even with a genuine positive expected value, blackjack has high hand-to-hand variance. The session length distribution used here may be too short for the central limit theorem to kick in reliably across all simulated paths.


## A Note on Development

The conceptual design — Hi-Lo counting, true count normalisation, basic strategy logic, Kelly criterion theory, Monte Carlo methodology — is entirely my own work, developed as part of my studies in quantitative finance. The implementation was built with the assistance of AI (Claude by Anthropic) for code structuring and debugging.

I think this is worth being transparent about. Using AI as a coding assistant in 2025 is standard practice, in the same way that using Stack Overflow or a textbook was in 2015. What matters is understanding what the code does and why — and I do. The interesting parts of this project are the mathematical and probabilistic concepts underneath, not the syntax.


## Disclaimer

This project is purely academic. Counting cards is not illegal, but it is grounds for removal from a casino. More importantly, **no simulation guarantees positive returns in practice** — real casinos introduce countermeasures (frequent shuffles, flat bets, banning) that are not modelled here. Do not use this as a basis for actual gambling decisions.
