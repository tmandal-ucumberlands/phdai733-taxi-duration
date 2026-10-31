# Predicting NYC Taxi Trip Duration with Deliverables 1 and 2

PhDAI 733: Python Application for Data Analytics in AI | University of the Cumberlands
Team Member: Tannishtha Mandal,Sagar Kothakonda,Shahezad Ali Mohammad,Rohith Reddy Kondakindi,Chakradhar Patnam 

## Project Summary
This project predicts how many minutes a New York City taxi trip will take using only information known when the trip starts.

- **Deliverable 1:** data preparation and an initial baseline model (linear regression).
- **Deliverable 2:** model comparison, a log of trial-and-error experiments, hyperparameter tuning, final evaluation, a fairness audit by group and prediction ranges.

**Business question:** Can we give riders and dispatchers an accurate trip-time estimate, and which factors matter most?

## Dataset
- **Source:** NYC Taxi and Limousine Commission trip records (March 2019 sample), distributed with the seaborn data collection: https://raw.githubusercontent.com/mwaskom/seaborn-data/master/taxis.csv
- **Target:** trip duration in minutes (dropoff time minus pickup time).

## Deliverable 1: Data Preparation and Initial Model
- Removed implausible trips (duration under 1 minute or distance of 0): 83 rows.
- Filled missing pickup/dropoff zone, borough and payment values with "Unknown"; set passenger counts below 1 to 1.
- Created features: hour, day of week, weekend flag, rush-hour flag, same-borough flag.
- Excluded fare, tip, tolls and total charge from the inputs because they are only known after the trip (data leakage).
- Split 80/20 into train and test sets (5,080 / 1,270 trips) with `random_state=42`. Scaling and one hot encoding are fitted on the training data only.
- Initial model: linear regression, compared with a mean duration baseline.

## Deliverable 2: Improve and Evaluate
All model selection uses 5-fold cross-validation on the **training set only**. The test set is used once, for the final model.

| Step | What was done |
|---|---|
| Model comparison  | Added pickup/dropoff zones and a log-transformed target. Compared linear regression, ridge, random forest and histogram gradient boosting. |
| Experiment log  | 26 logged trials: target transforms and loss functions, Lasso/ElasticNet/k-nearest neighbours/extra trees, dropping one feature group at a time, new features (cyclic hour, log and squared distance, distance × rush hour, late-night flag), trimming outliers, voting and stacking ensembles, and a learning curve. A repeated-cross-validation noise check (about 0.18 min RMSE) decides whether a difference is real. No trial beat the reference model by more than the noise. |
| Hyperparameter tuning  | Randomized search (25 settings), then one-at-a-time sensitivity of each setting, a comparison of random search, a grid around the best settings and successive halving, and tuning of random forest and ridge for comparison. The random search model stays final. |
| Final evaluation  | One evaluation on the held-out test set, compared with the initial model; permutation importance. |
| Error audit | Errors and bias by pickup borough, taxi type, time of day and trip length. |
| Prediction range  | An 80% range from out-of-fold errors, with its coverage on the test set. |

### Results (held-out test set, 1,270 trips)

| Model | MAE (min) | RMSE (min) | R² | Median AE (min) |
|---|---|---|---|---|
| Mean-duration baseline | 8.707 | 12.391 | 0.000 | 6.945 |
| Linear regression (initial model, Deliverable 1) | 4.560 | 6.566 | 0.719 | 3.403 |
| Gradient boosting, untuned | 2.988 | 4.812 | 0.849 | 1.794 |
| **Tuned gradient boosting (final model)** | 3.031 | 4.793 | 0.850 | 1.904 |

The final model improves on the initial model on every measure: typical error falls by about one third (4.56 to 3.03 minutes) and R² rises from 0.719 to 0.850. Tuning added almost nothing on top of the untuned boosting model.So most of the gain comes from letting the model capture non-linear patterns and location.

### Limitations
- Errors are not equal across groups. The model tends to underestimate evening trips, green-taxi trips and trips of 3–6 miles, and the Bronx group has only 16 test trips.
- The data cover one month in 2019 without weather, traffic or events and the split is random, so future weeks are not tested.
- Trip-level timestamps and zones can be privacy-sensitive.Any real use needs access limits.

## Repository Contents
| File | Description |
|---|---|
| `taxi_duration_deliverable_1.ipynb` | Deliverable 1 Colab notebook: loading, quality checks, cleaning, exploration, preprocessing, initial model |
| `taxi_duration_deliverable_2.ipynb` | Deliverable 2 Colab notebook: the Deliverable 1 work. On top of that sections 7 to 12 (comparison, experiment log, tuning, final evaluation, audit, ranges) |

## How to Run
1. Open `taxi_duration_deliverable_2.ipynb` in Google Colab 
2. Choose **Run all**. The data loads directly from the URL. The full run takes roughly 10 to 15 minutes because of the cross validation and tuning searches.
3. Requirements: Python 3, pandas, NumPy, scikit-learn, Matplotlib, Seaborn, SciPy (all preinstalled in Colab).

Results are reproducible because the notebook uses a fixed random seed (42) and a single data-loading cell.

## Team Workflow
Deliverable 2 work was divided by stage of the analysis:

| Member | Responsibility |
|---|---|
| Tannishtha | Model comparison, randomized search, final test-set evaluation,combined the work into one notebook and reportand managed this repository |
| Sagar | Experiments on target transforms, loss functions and model families with noise check |
| Shahezad | Feature ablation, new features, outlier trimming, ensembles, learning curve |
| Rohith | Additional tuning trials: setting sensitivity, search strategies, tuning other models |
| Chakradhar | Error audit by group, prediction ranges, ethics and application sections |

Each member reviewed one teammate's work against the notebook before it was pushed to the github repository.
