# HSD-data: Curated Benchmarks for Lithography Hotspot Detection

This repository contains the **curated** HSD-data benchmarks, designed for training and evaluating computer vision and machine learning models for lithography hotspot detection (HSD).

These benchmarks have been rigorously standardized and cleaned to address issues found in prior datasets, such as inconsistent evaluation protocols and data leakage (duplicates between training and testing sets). This dataset supports the research presented in the paper: **"From Contrastive to Generative Alignment: Large-Scale Hierarchical Multi-Modal Pre-training for Hotspot Detection"**.

## Introduction

Hotspot detection is critical for optimizing mask designs in semiconductor manufacturing. While benchmarks like ICCAD 2012 and Via have been widely used, prior versions suffered from inconsistencies in clip resolution and significant data redundancy.

**This repository provides a standardized and curated version of these benchmarks.**

Key Curation Steps (as detailed in the associated paper):
1.  **Standardization**: 
    *   **ICCAD2012**: Layouts from designs with the same technology node (Designs 2, 3, 4, and 5) were selected, clipped into consistent resolutions (4.8µm × 4.8µm), and merged.
    *   **Via**: Standardized to 2.0µm × 2.0µm resolution.
2.  **De-duplication**: Duplicate clips within the training and testing sets were removed to ensure data integrity.
3.  **De-contamination**: Cross-dataset de-duplication was performed to remove any training cases that overlapped with the testing set, preventing data leakage and ensuring fair evaluation.

## Benchmark Statistics

This section summarizes the key statistics for each curated data split. The values are derived from the number of hotspots (`HS#`) and non-hotspots (`NHS#`) samples in each set.

*   **HS#**: The number of samples containing hotspots (Hotspots).
*   **NHS#**: The number of non-hotspot samples (Non-Hotspots).

| Benchmarks   | Training Set HS# | Training Set NHS# | Testing Set HS# | Testing Set NHS# |
| :----------- | :--------------: | :---------------: | :-------------: | :--------------: |
| `ICCAD2012`  |       712        |      16,151       |      2,524      |      13,179      |
| `Via-1`      |      3,429       |      10,279       |      2,267      |      6,877       |
| `Via-2`      |      1,023       |      11,231       |       723       |      7,465       |
| `Via-3`      |       586        |      18,418       |       422       |      12,483      |
| `Via-4`      |        33        |      13,797       |       25        |      12,445      |
| `Via-Merge`  |      5,071       |      53,725       |      3,437      |      39,270      |

## Download

You can download the full curated dataset from the link below:

[**Download HSD-data Benchmarks (Google Drive)**](https://drive.google.com/file/d/1_qXpDluYfuG4JDyJAPED7QJagS6UYUGx/view?usp=drive_link)

After downloading, extract the contents to your local project directory.

## Dataset Structure

The dataset is organized to support three experimental settings: **Full Fine-tuning**, **Low-Resource Fine-tuning**, and **Cross-Design Generalization**.

### Directory Layout

```text
release/
├── iccad-2012/
│   ├── test/           # Test images
│   ├── train/          # Train images
│   ├── test.json       # Labels for test set
│   └── train.json      # Labels for train set
├── via-1/
│   └── ... (Standard structure)
├── via-2/
│   └── ... (Standard structure)
├── via-3/
│   └── ... (Standard structure)
├── via-4/
│   └── ... (Standard structure)
└── via-merge/
    ├── test/           # Full Via-Merge test images
    ├── train/          # Full Via-Merge train images
    ├── test.json       # Full test set labels
    ├── train.json      # Full training set labels (100% data)
    ├── train_001.json  # Low-resource: 1% of training data
    ├── train_002.json  # Low-resource: 2% of training data
    ├── train_005.json  # Low-resource: 5% of training data
    └── train_01.json   # Low-resource: 10% of training data
```

### File Contents

*   **`train/` & `test/`**: Directories containing the layout image clips.
*   **`train.json` & `test.json`**: JSON files containing the annotations. Each entry maps a filename to its label (Hotspot vs. Non-Hotspot).
*   **`via-merge/train_XXX.json`**: These files are specific to the **Low-Resource Fine-tuning** experiments described in the paper. They contain subsets of the full `via-merge` training labels to simulate data-scarce environments.

## Usage

This section details how to utilize the dataset files to reproduce the three experimental settings discussed in the paper.

### 1. Full Fine-tuning
This is the standard supervised learning setting where the model is trained on the full training set and evaluated on the standard test set for each benchmark.

*   **Training Data**: Use the `train.json` file located in the specific benchmark folder (e.g., `iccad-2012/train.json`).
*   **Testing Data**: Use the `test.json` file located in the same benchmark folder (e.g., `iccad-2012/test.json`).

### 2. Low-Resource Fine-tuning
This setting evaluates the model's performance when training data is scarce. Experiments are conducted using the `Via-Merge` dataset with varying percentages of training data (1%, 2%, 5%, and 10%).

*   **Training Data**: Select one of the subset JSON files in the `via-merge` folder:
    *   `via-merge/train_001.json` (1% Data)
    *   `via-merge/train_002.json` (2% Data)
    *   `via-merge/train_005.json` (5% Data)
    *   `via-merge/train_01.json` (10% Data)
*   **Testing Data**: Always use the full test set: `via-merge/test.json`.

### 3. Cross-Design Generalization
This setting tests the model's ability to generalize to unseen designs. The model is trained on one design (Source) and evaluated on a completely different design (Target).

*   **Training Data**: Use the `train.json` and `test.json` of the **Source Design** as the training set.
    *   *Example*: If training on Via-1, combine `via-1/train.json` and `via-1/test.json` (or just use the standard train split depending on your specific protocol, though typically the source domain is fully utilized).
*   **Testing Data**: Use the combined `train.json` and `test.json` of the **Target Design** as the testing set.
    *   *Example*: To test on Via-2, merge `via-2/train.json` and `via-2/test.json` to form the complete evaluation set.
