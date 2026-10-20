# Nexar Dashcam Crash Prediction

Group project (Spring 2025) for the [Nexar Dashcam Collision Prediction Challenge](https://www.kaggle.com/competitions/nexar-collision-prediction/overview) on Kaggle.

**Team:** Eleanor Madderra, Anastasiya Peravalava, Senait Pirani

## Goal

The goal was to build a model that watches part of a dashcam clip and gives a probability (0–1) that a collision or near-miss is about to happen, before the event itself appears in the video.

## Data

- 1,500 training videos: 750 positive (collision or near-miss) and 750 negative (normal driving)
- Each video is about 40 seconds long, 30 fps, 1280×720
- Positive clips include an **alert time**, the earliest moment action could be taken, and an **event time**. On average the gap between them is under 2 seconds.
- The test set has 1,344 unlabeled videos.

The dataset is over 30 GB, so all of the work was done in Kaggle notebooks. See [`data/README.md`](data/README.md) for how to download it.

## Approach

1. **Frame extraction (OpenCV):** sample a fixed number of evenly spaced frames from each video.
2. **Feature engineering:** reduce each video to a single feature vector:
   - **Optical flow (Farneback):** mean, std and max motion magnitude, plus mean horizontal and vertical flow. These capture hard braking, swerving and impacts.
   - **Canny edge density:** mean and std. These pick up objects suddenly entering the frame.
   - **Brightness:** mean and variance. These reflect low visibility, headlights and brake lights.
3. **Modeling:** compare Random Forest, Gradient Boosting, AdaBoost, SVC, XGBoost, LightGBM and an MLP on a balanced sample. Also apply StandardScaler, GridSearchCV tuning for the SVC, and probability calibration.
4. **Ensemble:** soft-voting ensemble with weights of 0.7 SVC and 0.3 XGBoost.

## Results (validation, 200-video balanced sample)

| Model | Accuracy | AUC |
|---|---|---|
| Random Forest (baseline) | 0.45 | 0.51 |
| SVC | 0.65 | 0.41 |
| XGBoost | 0.525 | 0.565 |
| **SVC + XGBoost ensemble** | **0.575** | **0.52** |

Performance was close to chance. Hand-crafted video-level summary features turned out to be a hard way to capture the brief moments right before a crash. Scoring the full test set took more than 6 hours and timed out, so a few test videos were also checked by hand against sampled frames.

## What we learned / next steps

- Features matter more than model choice. Optical flow was the most useful signal.
- Sampling a fixed number of frames per video can miss the critical moment. Sampling per second, or sampling densely around the alert window, would likely help.
- Processing time was the main constraint. Lower-resolution frames or precomputed features would allow more experiments.

## Repository structure

```
notebooks/
  01-exploratory-analysis.ipynb    # frame extraction, optical flow & edge detection exploration
  02-features-and-models.ipynb     # feature extraction pipeline + model comparison/tuning
  03-test-submission.ipynb         # ensemble scoring on the test set → submission.csv
report/
  Dashcam_Crash_Prediction.pptx    # final report presentation
data/
  README.md                        # dataset description and download instructions
```

**Tools:** Python, OpenCV, pandas, NumPy, scikit-learn, XGBoost, LightGBM, Kaggle Notebooks
