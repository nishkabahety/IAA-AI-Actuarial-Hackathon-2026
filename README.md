# IAA AI-Assisted Actuarial Hackathon 2026

My solo entry to the International Actuarial Association's AI-Assisted Actuarial Hackathon, a two-week Kaggle competition (16–30 October 2026).

The brief: act as an insurer's pricing team and predict the expected 2025 claim cost of every policy across private motor, home buildings and home contents. That meant a developed market in Week 1, then an emerging market with only two years of claims history in Week 2.

## Results

Metric: Tweedie deviance D² (power 1.5). Higher is better.

| Notebook | Stage | Language | Holdout D² | Private score |
|---|---|---|---|---|
| `week-1-model.ipynb` | Part 1: developed market | R | 0.097 | 0.12912 |
| `part-2-comp.ipynb` | Part 2: emerging market | Python | 0.100 | 0.131 |

Final leaderboard: **25th of 40**, within 0.007 of first place.

## Approach

**Part 1: Developed market (R)**
- **Data preparation:** joined the claims, policy, customer, address and weather data. Calculated exposure, matched claims to policy periods, and cleaned undocumented category codes and impossible values.
- **Target:** claim cost net of excess and recoveries, with declined claims excluded. Developed 2024 to ultimate with chain-ladder, trended to 2025, and capped large claims.
- **Model:** a Tweedie GLM for each line of business, validated on a 2024 holdout.
- **Guardrails:** found and removed a leaking feature (customer service contacts, which claims cause rather than predict). It had inflated validation D² to an implausible 0.47. Also dropped near-duplicate features that made the model coefficients unstable.

**Part 2: Emerging market (Python)**
- **Credibility approach:** used the developed-market GLM as a baseline. The emerging-market adjustments shrink back toward the developed data according to how much data supports them.
- **Boosting correction:** a bounded LightGBM model on top picks up patterns the GLM misses, and the bounds stop it drifting.
- **Market differences:** borrowed only the inflation trend from the developed market, not its year-specific swings. Also aligned region codes that didn't match between the two markets.
- **Testing:** one pipeline runs both markets. I ran a separate stress test (trend, large-claim cap, new business) that doesn't change the submission.

## Use of AI
I used Claude as a coding and reasoning assistant throughout. Every modelling decision was checked against printed diagnostics before it went into a submission, and that's how the data leakage and region-code issues above were caught.

## What I'd improve next
- Tune the credibility weights and LightGBM bounds more systematically
- Build more features from the weather and address data
- Model claim frequency and severity separately and compare with the single Tweedie model

## Data
Competition data isn't included. See https://www.kaggle.com/competitions/iaa-ai-assisted-hackathon-2026-part-2/data.

## Author
**Nishka Bahety**, Bachelor of Commerce (Actuarial Studies & Finance), University of Melbourne
https://www.linkedin.com/in/nishka-bahety-5906621b9

