# EEG Attention State Classification Using KNN

## Overview

This project implements a method to classify EEG signals into **"Focused"** or **"Drowsy"** attention states. The methodology is based on Slide 10 of the attached document `Attention State.pdf`. The dataset contains EEG recordings from **5 different subjects**, with each trial spanning 15 seconds across 7 channels.

The core objective is to identify the **optimal frequency band**, **channel**, and **K (for KNN)** that yield the **highest classification accuracy** per subject. Further analysis explores the effects of combining:

* All **bands** for a single **channel**
* All **channels** for a single **band**
* All **bands and channels** together

## Dataset Structure

Each subject's data is provided in two `.mat` files located in the `Data/` directory:

* `train_data_X.mat`
* `test_data_X.mat`

Where `X` = 1 to 5 (for each subject).

Each `.mat` file includes:

* `channels`: List of EEG channel names
* `data`: EEG signal, shape = (n\_trials × n\_samples × n\_channels)
* `fs`: Sampling frequency
* `labels`: Attention state labels (0 = Focused, 1 = Drowsy)

## Setup & Requirements

This project requires the following Python libraries:

```python
numpy
scipy
scikit-learn
matplotlib
```

You can install them via:

```bash
pip install numpy scipy scikit-learn matplotlib
```

## Processing Pipeline

### 1. Preprocessing

* Apply **Common Average Reference (CAR)** filtering to each EEG trial.

### 2. Feature Extraction

* Compute **FFT** of each trial per channel.
* Extract **power in five frequency bands**:

  * Delta (0.5–4 Hz)
  * Theta (4–8 Hz)
  * Alpha (8–13 Hz)
  * Beta (13–30 Hz)
  * Gamma (30–45 Hz)

### 3. Feature Strategies

Three strategies are evaluated using KNN:

1. **Bands per Channel** (per-channel band power vector)
2. **Channels per Band** (per-band channel power vector)
3. **All Channels and Bands Combined** (full feature vector)

Within each strategy, dimensionality reduction and aggregation methods are compared:

* Raw concatenation
* Mean aggregation
* Median aggregation
* PCA (2, 3, and 4 components)

### 4. Classification

* Train and test a **K-Nearest Neighbors (KNN)** classifier for values of **K = 1 to 10**
* Accuracy is computed on the test set for each configuration

## Deliverables

The project outputs:

1. **Code** to load data, preprocess signals, extract features, and train KNN classifiers.
2. **Per-subject analysis** of:

   * Best frequency band, channel, and value of K
   * Highest accuracy achieved
3. **Cross-subject comparison**:

   * How best-performing configurations differ across subjects
4. **Evaluation of feature combinations**:

   * **All bands for a single channel**
   * **All channels for a single band**
   * **All channels and all bands**

## Example Results (Structure Only)

```text
Subject 1:
- Best Channel: Pz
- Best Band: Alpha
- Best K: 3
- Accuracy: 89.3%

Subject 2:
...

Observation:
- Subject 1 performs best with Alpha band on Pz channel.
- Subject 2 shows higher accuracy with Theta band across frontal channels.

Combining all bands (per channel) improves accuracy in subjects 2 & 3.
Combining all channels (per band) benefits subjects 4 & 5.
Combining all channels and all bands generally increases robustness.
```

## Directory Structure

```
.
├── Data/
│   ├── train_data_1.mat
│   ├── test_data_1.mat
│   └── ...
├── Attention State.pdf
├── main.py
├── utils.py
└── README.md
```

## How to Run

1. Place all `.mat` files in a folder named `Data/`
2. Run the main evaluation script:

```bash
python main.py
```

3. Results will be printed in the terminal and optionally saved to a log or CSV.
