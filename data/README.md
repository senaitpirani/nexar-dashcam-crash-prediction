# Dataset

This project uses the **Nexar Dashcam Collision Prediction** dataset from Kaggle:
https://www.kaggle.com/competitions/nexar-collision-prediction/data

The dataset (~30 GB of video) is not stored in this repo because of its size and Kaggle's competition data rules.

## Contents

| File / folder | Description |
|---|---|
| `train.csv` | `id`, `target` (1 = collision/near-miss, 0 = normal), `time_of_alert`, `time_of_event` |
| `test.csv` | `id` of the 1,344 unlabeled test videos |
| `train/` | 1,500 training videos (`00000.mp4`, ...), 750 positive and 750 negative |
| `test/` | 1,344 test videos |

## Download

1. Join the competition on Kaggle and accept its rules.
2. With the [Kaggle API](https://github.com/Kaggle/kaggle-api) set up, run:

   ```bash
   kaggle competitions download -c nexar-collision-prediction
   unzip nexar-collision-prediction.zip -d data/
   ```

The notebooks read from `/kaggle/input/nexar-collision-prediction/`, which works as-is in a Kaggle notebook. To run them locally, change those paths to point at `data/`.
