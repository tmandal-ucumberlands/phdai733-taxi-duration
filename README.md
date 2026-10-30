# phdai733-taxi-duration
# Predicting NYC Taxi Trip Duration — Deliverable 1

PhDAI 733: Python Application for Data Analytics in AI | University of the Cumberlands

## Project Summary
This project predicts how many minutes a New York City taxi trip will take, using only information known when the trip starts (pickup time, pickup and dropoff zone, distance, passenger count, taxi type). Deliverable 1 covers data preparation and an initial baseline model.

**Business question:** Can we give riders and dispatchers an accurate trip-time estimate, and which factors matter most?

## Dataset
- **Source:** NYC Taxi and Limousine Commission trip records (March 2019 sample), distributed with the seaborn data collection: https://raw.githubusercontent.com/mwaskom/seaborn-data/master/taxis.csv
- **Size:** 6,433 trips, 14 columns. 6,350 trips remain after cleaning.
- **Target:** trip duration in minutes (dropoff time minus pickup time).

## Data Preparation
- Removed implausible trips (duration under 1 minute or distance of 0): 83 rows.
- Filled missing pickup/dropoff zone, borough and payment values with "Unknown".
- Set passenger counts below 1 to 1.
- Created features: hour, day of week, weekend flag, rush-hour flag, same-borough flag.
- Excluded fare, tip, tolls and total charge from the inputs, because they are only known after the trip (data leakage).
- Split 80/20 into train and test sets (5,080 / 1,270 trips) with `random_state=42`.
- Scaled numeric features and one-hot encoded categorical features inside a scikit-learn pipeline, fitted on the training data only.

## Initial Model
Linear regression, compared with a baseline that always predicts the mean duration.

| Model | MAE (min) | RMSE (min) | R² |
|---|---|---|---|
| Mean-duration baseline | 8.707 | 12.391 | 0.000 |
| Linear regression (initial) | 4.560 | 6.566 | 0.719 |

Linear regression explains about 72% of the variation in duration and roughly halves the typical error. It underestimates long trips and cannot capture rush-hour or zone interactions. Deliverable 2 addresses this.

## Repository Contents
| File | Description |
|---|---|
| `Taxi_Duration_Deliverable1.ipynb` | Colab notebook: data loading, quality checks, cleaning, exploration, preprocessing, initial model |
| `Deliverable1_Taxi_Duration_Report_APA.docx` | Written report |

## How to Run
1. Open `Taxi_Duration_Deliverable1.ipynb` in Google Colab (File → Upload notebook, or open from this repo).
2. Choose **Runtime → Run all**. The data loads directly from the URL above, so no uploads are needed.
3. Requirements: Python 3, pandas, NumPy, scikit-learn, Matplotlib, Seaborn (all preinstalled in Colab).

Results are reproducible because the notebook uses a fixed random seed (42) and a single data-loading cell.

## Team Workflow
Work is divided as described in section 4 of the report. Code lives in this repository and is changed through commits on branches, with a teammate reviewing each pull request before it is merged into `main`.

## References
- New York City Taxi and Limousine Commission. (2019). *TLC trip record data* [Data set]. https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page
- Pedregosa, F., et al. (2011). Scikit-learn: Machine learning in Python. *Journal of Machine Learning Research, 12*, 2825–2830.
- Waskom, M. L. (2021). seaborn: Statistical data visualization. *Journal of Open Source Software, 6*(60), Article 3021.
