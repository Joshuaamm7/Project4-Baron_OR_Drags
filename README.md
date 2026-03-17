# Dragons or Baron? Modeling Objective Control and Win Probability in League of Legends

**Author:** Joshua Huang

**Website:** [https://joshuaamm7.github.io/Project4-Baron_OR_Drags/](https://joshuaamm7.github.io/Project4-Baron_OR_Drags/)

---

## Introduction

### Background

Competitive multiplayer games like League of Legends involve teams competing to control strategic objectives throughout a match. Among these objectives, dragons provide permanent team-wide buffs that can significantly influence the outcome of the game. Different dragon types grant different advantages — such as increased damage, movement speed, or durability. In addition to dragons, Baron Nashor provides a powerful temporary buff that can shift the momentum of a match in a single skirmish.

Because professional and ranked players often prioritize these objectives, understanding how different objectives influence the probability of winning can provide insight into effective gameplay strategies.

### Research Question

**Between Baron Nashor and Elemental Dragons, which objective has a stronger relationship with a team's probability of winning in League of Legends?**

Both objectives provide powerful advantages. Dragons grant permanent stacking buffs that accumulate over the game, while Baron grants a temporary but highly impactful buff enabling teams to push lanes and close out games. By comparing the relationship between these objectives and match outcomes, this project determines which is the stronger predictor of victory.

### Dataset

The dataset comes from [Oracle's Elixir](https://oracleselixir.com/) and contains 2025 professional League of Legends esports match data. The raw dataset has **120,636 rows** and **165 columns**. Each match contributes 12 rows: 10 player-level rows and 2 team-level rows. Since this project focuses on objective control and team outcomes, only the **2 team-level rows per game** (20,106 rows total across 10,053 games) are used for analysis.

The key columns used in this analysis are:

| Column | Description |
|---|---|
| `gameid` | Unique match identifier |
| `result` | Match outcome: 1 = win, 0 = loss |
| `dragons` | Number of dragons secured |
| `barons` | Number of Baron Nashor buffs secured |
| `elders` | Number of Elder Dragons secured |
| `heralds` | Number of Rift Heralds secured |
| `void_grubs` | Number of Void Grubs secured |
| `atakhans` | Number of Atakhans secured |
| `teamkills` | Total kills by the team |
| `totalgold` | Total gold earned |
| `damagetochampions` | Total damage dealt to enemy champions |

---

## Data Cleaning and Exploratory Data Analysis

### Cleaning Steps

The raw dataset required several cleaning steps before analysis:

1. **Column selection:** Only columns relevant to team-level objective control and match outcomes were kept.
2. **Filtering to team rows:** Only rows where `position == "team"` were retained, reducing the dataset from 120,636 rows to 20,106 rows (2 rows per game).
3. **Handling missing values:** The `elders` column had 1,634 missing values and `atakhans` had 1,686 missing values. These were filled with 0 where appropriate, under the assumption that a missing value indicates the objective was never secured.
4. **Feature engineering:** Difference columns (e.g., `dragons_diff`, `barons_diff`, `totalgold_diff`) were computed by subtracting each team's count from their opponent's count within the same game. Boolean advantage columns (`dragon_adv`, `baron_adv`) were also created.

After cleaning, the dataset had **20,106 rows** with no remaining missing values in any key objective column.

**Sample of cleaned dataset (first 5 rows):**

| gameid | teamname | result | dragons | barons | heralds | totalgold |
|---|---|---|---|---|---|---|
| LOLTMNT03_179647 | IziDream | 0 | 0.0 | 0.0 | 0.0 | 42,255 |
| LOLTMNT03_179647 | Team Valiant | 1 | 2.0 | 1.0 | 1.0 | 53,936 |
| LOLTMNT06_96134 | Esprit Shōnen | 1 | 3.0 | 1.0 | 1.0 | 64,669 |
| LOLTMNT06_96134 | Skillcamp | 0 | 2.0 | 0.0 | 0.0 | 50,679 |
| LOLTMNT06_95160 | Karmine Corp Blue Stars | 0 | 0.0 | 0.0 | 0.0 | 51,389 |

### Univariate Analysis

#### Distribution of Dragons Secured

<iframe
  src="assets/dragon_distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Most teams secure between 1 and 3 dragons per game. Very few teams reach 5 or more, and securing 7 dragons is extremely rare.

#### Distribution of Barons Secured

<iframe
  src="assets/barons_distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Baron is a rarer objective — most teams secure either 0 or 1 per game. Multiple barons in a single game is uncommon and signals a prolonged match.

### Bivariate Analysis

#### Dragons Secured vs Match Outcome

<iframe
  src="assets/Dragons_Sec_vs_Match_Outcome.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Teams that win typically secure 2–4 dragons, while losing teams often secure only 1–2. However, the distributions overlap considerably, meaning dragons alone do not guarantee victory.

#### Barons Secured vs Match Outcome

<iframe
  src="assets/barons_Sec_vs_Match_Outcome.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The contrast here is much sharper. Most losing teams secure 0 barons, while winning teams almost always secure at least 1. This suggests Baron control is significantly more decisive than dragon control.

### Aggregate Table: Win Rate by Objective Advantage

The table below shows the win rate for teams that secured more of each objective than their opponent, compared to teams that did not. The win-rate difference measures how strongly each objective predicts victory.

| Objective | Win Rate (More) | Win Rate (Less/Same) | Win Rate Difference |
|---|---|---|---|
| Barons | 0.905 | 0.249 | **0.656** |
| Dragons | 0.830 | 0.254 | 0.577 |
| Atakhans | 0.796 | 0.262 | 0.534 |
| Elders | 0.870 | 0.487 | 0.383 |
| Heralds | 0.662 | 0.339 | 0.322 |
| Void Grubs | 0.600 | 0.419 | 0.181 |

Teams with Baron advantage win over 90% of the time. Baron has the largest win-rate gap of all objectives, followed by dragons.

#### Win Rate vs Dragons Secured



| Dragons Secured | Win Rate |
|---|---|
| 0 | 3.6% |
| 1 | 18.0% |
| 2 | 46.8% |
| 3 | 69.4% |
| 4 | 90.2% |
| 5 | 94.9% |
| 6 | 97.1% |
| 7 | 100.0% |

Win rate increases sharply with each additional dragon, suggesting that while individual dragons are not decisive, accumulating them strongly correlates with winning.

#### Win Rate vs Barons Secured



| Barons Secured | Win Rate |
|---|---|
| 0 | 21.9% |
| 1 | 84.1% |
| 2 | 83.4% |
| 3 | 83.3% |
| 4 | 50.0% |
| 

Securing even a single Baron pushes a team's win rate above 84%. Teams that fail to secure any Baron win less than 22% of the time.

---

## Assessment of Missingness

### MNAR Analysis

One column with non-trivial missingness is `atakhans`, which has **1,686 missing values**. The missingness likely occurs because this objective does not appear in every match — it may be absent in certain game patches or in games that end before the objective spawns. Because the probability that a value is missing may depend on factors not directly observed in the dataset (such as game patch or match duration), the missingness of `atakhans` is plausibly **Missing Not At Random (MNAR)**.

### Permutation Test 1: Atakhans Missingness Does NOT Depend on Dragons

**Null Hypothesis (H₀):** The missingness of `atakhans` does not depend on the number of dragons secured. The distribution of dragons is the same for rows where `atakhans` is missing and where it is not.

**Alternative Hypothesis (H₁):** The missingness of `atakhans` depends on the number of dragons secured.

**Test Statistic:** Difference in mean dragons between missing and non-missing `atakhans` groups.

**Result:** Observed difference = −0.039, p-value = 0.266.

<iframe
  src="assets/atakhans_missingness_permutation_dragons.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Since the p-value (0.266) is well above the 0.05 significance level, we **fail to reject the null hypothesis**. There is no strong evidence that the missingness of `atakhans` depends on dragons secured.

### Permutation Test 2: Atakhans Missingness DOES Depend on Teamkills

**Null Hypothesis (H₀):** The missingness of `atakhans` is independent of the number of team kills.

**Alternative Hypothesis (H₁):** The missingness of `atakhans` depends on the number of team kills.

**Test Statistic:** Difference in mean team kills between missing and non-missing `atakhans` groups.

**Result:** Observed difference = −1.454, p-value ≈ 0.0.

<iframe
  src="assets/atakhans_missingness_permutation_teamkills.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Teams where `atakhans` is missing have approximately 1.45 fewer kills on average than teams where it is not missing. Since the p-value is essentially 0, we **reject the null hypothesis**. The missingness of `atakhans` depends on team kills — games with more kills are more likely to have non-missing `atakhans` values, which aligns with the idea that higher-kill, more prolonged games are more likely to include this objective.

---

## Hypothesis Testing

### Question

Is Baron advantage more predictive of winning than Dragon advantage?

### Hypotheses

**Null Hypothesis (H₀):** Baron difference and Dragon difference are equally predictive of winning. Any observed difference in their win-rate gaps is due to random variation.

**Alternative Hypothesis (H₁):** Baron advantage is more predictive of winning than Dragon advantage.

### Test Statistic

The test statistic is the difference between the win-rate gaps:

> T = (Win Rate when Baron Advantage) − (Win Rate when No Baron Advantage) − [(Win Rate when Dragon Advantage) − (Win Rate when No Dragon Advantage)]

### Results

The observed test statistic was **0.079**, meaning the win-rate gap associated with Baron advantage is approximately 7.9 percentage points larger than the win-rate gap for Dragon advantage.

A permutation test with 2,000 simulations shuffled match results to generate a null distribution. The resulting p-value was approximately **0.0**, meaning none of the 2,000 simulated statistics were as large as the observed value.

<iframe
  src="assets/Hypothesis_test_permutation_of_distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Since the p-value is far below the 0.05 significance level, we **reject the null hypothesis**. This provides strong evidence that Baron advantage is more predictive of match outcomes than Dragon advantage.

This result aligns with League of Legends gameplay mechanics: Baron Nashor provides an immediate, powerful team-wide buff that dramatically increases map pressure and siege capability. While dragons offer stacking long-term advantages, securing Baron often leads to immediate strategic advantages that can decide a match.

---

## Framing a Prediction Problem

### Prediction Problem

Can we predict whether a team will win a match based on their objective control and in-game statistics?

### Response Variable

The response variable is `result` (1 = win, 0 = loss). This is a **binary classification** problem.

### Features Available for Prediction

The model uses features measurable during gameplay: `dragons`, `barons`, `heralds`, `elders`, `void_grubs`, `teamkills`, `teamdeaths`, `totalgold`, `damagetochampions`, and `monsterkills`. Engineered features such as `dragons_diff`, `barons_diff`, `totalgold_diff`, and `teamkills_diff` are also included in the final model.

### Evaluation Metric

Models are evaluated using **accuracy** — the proportion of correctly predicted match outcomes.

---

## Baseline Model

### Features Used

The baseline model uses two quantitative features: `dragons` and `barons`, representing the raw counts of each objective secured.

### Model

Logistic Regression was used as the baseline classifier due to its simplicity and interpretability for binary classification.

### Performance

**Baseline Accuracy: 0.8224**

The model correctly predicted match outcomes approximately 82% of the time using only two features. This indicates that even raw objective counts carry strong predictive signal, confirming that neutral objectives play an important role in determining match results.

---

## Final Model

### Feature Engineering

To improve on the baseline, I engineered four additional features representing each team's advantage relative to their opponent:

- `dragons_diff` — difference in dragons secured vs. opponent
- `barons_diff` — difference in barons secured vs. opponent
- `totalgold_diff` — difference in total gold earned vs. opponent
- `teamkills_diff` — difference in team kills vs. opponent

These relative features capture game dominance more directly than raw counts alone. A team with 3 dragons against an opponent with 0 is in a fundamentally different position than a team with 3 dragons against an opponent with 3.

### Hyperparameter Tuning

The regularization strength `C` in Logistic Regression was tuned using `GridSearchCV` with 5-fold cross-validation over the values `[0.01, 0.1, 1, 10, 100]`. The best performing value was `C = 100`, which applies minimal regularization and allows the model to fit the training data more closely.

### Performance

**Final Model Accuracy: 0.9887**

This is a substantial improvement over the baseline accuracy of 0.8224 — an improvement of approximately **16.6 percentage points**. The engineered difference features provide significantly more predictive information than raw objective counts, because they directly measure which team dominated objectives and resources during the game.

---

## Fairness Analysis

### Question

Does the model perform worse on short games (≤ 20 minutes) compared to long games (> 20 minutes)?

This is worth examining because short games may have fewer objectives secured and smaller gold differences, providing weaker predictive signals for the model.

### Hypotheses

**Null Hypothesis (H₀):** The model performs equally well on long games and short games. Any observed difference in accuracy is due to random chance.

**Alternative Hypothesis (H₁):** The model performs worse on short games than long games.

### Test Statistic

Difference in accuracy: (accuracy on long games) − (accuracy on short games)

### Results

The observed accuracy difference was **0.261**, meaning the model is approximately 26.1 percentage points more accurate on long games than on short games.

A permutation test with 2,000 simulations shuffled the `long_game` labels to generate a null distribution. The resulting p-value was **0.0165**.

<!-- TODO: Add fairness analysis permutation plot once exported as assets/fairness_permutation.html -->

Since the p-value (0.0165) is below the 0.05 significance level, we **reject the null hypothesis**. The model performs significantly better on long games than on short games.

This result is expected: features like objective counts and gold differences become more informative as a game progresses. In longer games, the winning team typically builds larger and more distinguishable advantages in these statistics. In shorter games, fewer objectives are secured and differences are smaller, making predictions less reliable.
