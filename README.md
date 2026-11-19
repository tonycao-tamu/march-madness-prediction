# Predicting March Madness 2025 🏀

A logistic regression model that predicts game-by-game win probabilities for the 2025 NCAA Men's Basketball Tournament, built for Kaggle's [March Machine Learning Mania 2025](https://www.kaggle.com/competitions/march-machine-learning-mania-2025) competition.

## Overview

March Madness is known for upsets and Cinderella runs, which makes it hard to predict. This project estimates the probability that one team beats another using two features:

- **Seed difference**: the gap between the two teams' tournament seeds
- **KenPom rank difference**: the gap between the teams' [KenPom](https://kenpom.com/) power rankings on Selection Sunday (day 133)

The model's probabilities were then used to simulate the full tournament 1,000 times and to build a submitted bracket.

## Data

All data comes from the [Kaggle competition](https://www.kaggle.com/competitions/march-machine-learning-mania-2025/data). Under the competition's rules the data is not included in this repo, so download it from Kaggle to run the notebook. The files used are:

| File | Contents |
|---|---|
| `MNCAATourneySeeds.csv` | Tournament seeds by season |
| `MNCAATourneyCompactResults.csv` | Historical tournament game results |
| `MMasseyOrdinals.csv` | Team rankings from many systems, including KenPom (`POM`) |
| `MMasseyOrdinals_2025_133_56S.csv` | 2025 rankings as of Selection Sunday |

The analysis covers 2003 onward, when KenPom rankings begin.

## Method

1. **Feature engineering**: each matchup is keyed as `Season_LowTeamID_HighTeamID`, and the seed and KenPom rank differences are computed.
2. **Train/test split by time**: the model trains on 2003–2019 and tests on 2021–2024. The 2020 tournament was cancelled.
3. **Model**: logistic regression on min-max scaled features (scikit-learn).
4. **Evaluation**: Brier score, the competition metric.
5. **Simulation**: the full 2025 bracket is simulated 1,000 times to estimate each team's chance of reaching each round.

## Results

| Split | Brier score |
|---|---|
| Train (2003–2019) | 0.187 |
| Test (2021–2024) | 0.211 |
| Baseline (always predict 0.5) | 0.250 |
| **2025 tournament (live, at time of writing)** | **0.126** |

Both features carry similar weight, with seed difference slightly ahead of KenPom rank difference:

![Feature importance](images/feature_importance.png)

In the simulations, the most likely champions were Florida (11.0%), Duke (9.1%) and Houston (7.4%). Florida went on to win the 2025 title.

## Reflection & next steps

- A simple two-feature model beats the baseline but can't capture much of what drives tournament upsets.
- Seeding and rankings tend to favor the higher seed more than betting markets do.
- KenPom **net rating** would probably be a better feature than ordinal rank, because it captures how far apart teams really are.
- Future ideas: an Elo- or ratings-based model, plus features for home-court advantage, "blue blood" bias and coaching effectiveness.

## Repo contents

```
├── march_madness_prediction.ipynb   # Full analysis (Google Colab)
├── march_madness_prediction.pdf     # PDF export of the notebook
├── images/                          # Figures used in this README
└── requirements.txt
```

## How to run

1. Download the competition data from Kaggle.
2. Open the notebook in Google Colab, or locally after `pip install -r requirements.txt`.
3. Point the data path in the notebook at your downloaded files. The notebook currently mounts Google Drive.
4. The simulation section uses `march_madness_simulator.py` from Ryan Armstrong's notebook (credited below).

## Credits

- Seed-to-win-probability approach: [Nick M](https://www.kaggle.com/code/menanick/simple-predictions-seeds-probability/notebook)
- Tournament simulation and visualization: [Ryan Armstrong](https://www.kaggle.com/code/rsa013/march-madness-simulation-example/notebook)
