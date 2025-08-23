# Virgo Livingston Hanford Cluster Analysis

<img width="1010" height="509" alt="image" src="https://github.com/user-attachments/assets/8863dbf9-becd-41ad-83bc-d38380799e15" />

## Abstract

Gravitational-wave astronomy relies on networks of detectors that operate in coincidence to improve both sensitivity and confidence in detections. LIGO Hanford and LIGO Livingston typically operate with near-equal sensitivities, whereas Virgo often contributes fewer or weaker triggers due to its lower sensitivity. This asymmetry creates a **data interpretation problem**: Virgo’s information may be treated as non-informative, counter-intuitive, or discarded altogether.

This repository presents a **reproducible visualization pipeline** designed to interrogate how Virgo behaves in comparison to LIGO detectors during coincident operation. By examining trigger clusters in mass–mass space, and comparing them against the astrophysical template bank, we create high-resolution plots and movies that illuminate both agreements and divergences between detectors. These visual artifacts support both research discussions and presentations at conferences.

## Problem Statement

The three-detector network (Hanford, Livingston, Virgo) enables robust gravitational-wave detections. However:

* **Hanford and Livingston**: high sensitivity, strong and consistent triggers.
* **Virgo**: lower sensitivity, with patterns that may appear inconsistent or misleading.

In practice, Virgo data are often down-weighted or discarded. This project asks:

* Can Virgo’s apparent “weak” information still reveal structure if analyzed carefully?
* How do Virgo’s trigger clusters compare in distribution to those from LIGO?
* What visualization strategies can make these differences interpretable?

## Objectives

* Obtain coincident, unclustered triggers with simulated injections across V1, L1, H1.
* Analyze detector trigger patterns by focusing on **component masses** (mass₁, mass₂).
* Construct reproducible pipelines that generate plots and movies for conference/talk settings.

## Goals

* Predict when Virgo diverges or fails to detect injections.
* Explore whether alternative detection statistics could improve the chance of achieving consistent **triple coincidences** across all detectors.
* Demonstrate the value of **visualization-driven exploration** as a tool for better utilizing less-sensitive detectors.

## Methodology

### Data Preprocessing

* The **original trigger catalogs** contained a column named **`InsideH1L1V1`**, which indicates whether all three detectors (Hanford, Livingston, Virgo) were operating at a given time.

  * Value `1` → all three detectors were on.
  * Value `0` → one or more detectors were off.
* We **filtered the data to retain only rows where `InsideH1L1V1 = 1`**, ensuring that the analysis focuses exclusively on times when **all three detectors were active together**.
* After this filtering step, we worked only with **Cluster IDs** observed during triple-detector operation. The resulting datasets are stored under `Modified Data/` and serve as the foundation for all cluster analyses in this repository.

### Analysis Workflow

1. **Data Input**

   * Load filtered CSV trigger catalogs for V1, L1, H1.
   * Load template bank (HDF5) for astrophysical comparison.

2. **Standardization**

   * Clip `Mass_1_mf`, `Mass_2_mf` at 10⁻³ to stabilize logarithmic axes.
   * Cap `SNR_mf` at 60 to prevent outliers from saturating color scales.

3. **Cluster Identification**

   * Compute overlapping cluster IDs across detectors (pairs or all three).
   * Sort clusters by size for systematic visualization.

4. **Visualization**

   * Scatter plots of trigger clusters in the mass–mass plane.
   * Template bank rendered faintly in the background for astrophysical context.
   * Points colored by SNR for interpretability.
   * Shared log scaling and synchronized ticks.

5. **Output Generation**

   * Save per-cluster plots (`Plots/`).
   * Save video frames (`Frames/`).
   * Compile MP4 movie with `ffmpeg` when frames are complete.

6. **Resilience Features**

   * **Resume Mode**: skips frames that already exist, enabling continuation after interruptions.
   * **Fresh Run Detection**: if all frames exist, clears folders and regenerates outputs.

## Deliverables

* **MP4 movies** showing cluster evolution across detectors (**main output**).
* **High-resolution plots** saved in `Plots/` (supporting output, useful for individual inspection).
* **Frame-by-frame PNGs** saved in `Frames/` (intermediate output, used to build the movies).
* **Cluster analysis code** stored in each detector-pair (or triple) folder for reproducibility.

## Results (High-Level)

* Produced consistent visualizations across V1, L1, and H1.
* Demonstrated clear regions where Virgo’s clusters align with LIGO versus regions where Virgo fails to detect.
* Delivered movies that serve as **conference-ready visual artifacts**, aiding rapid communication of results.

## Future Scope

* Extend analysis of **Virgo’s response differences** across mass space.
* Investigate **modifications to detection statistics** to enhance triple coincidences.
* Incorporate **machine learning classification** of clusters to predict detector-specific performance.

## Repository Structure

```
Modified Data/
  singles_V1.csv
  singles_L1.csv
  singles_H1.csv
  template_bank.hdf5

V1 - L1 Cluster Analysis/
  V1 - L1 Code.ipynb
  Plots/
  Frames/
  Virgo - Livingston Cluster Evolution.mp4

V1 - H1 Cluster Analysis/
  V1 - H1 Code.ipynb
  Plots/
  Frames/
  Virgo - Hanford Cluster Evolution.mp4

V1 - L1 - H1 Cluster Analysis/
  V1 - L1 - H1 Code.ipynb
  Plots/
  Frames/
  Virgo - Livingston - Hanford Cluster Evolution.mp4
```

## How to Run

1. Place the required input files under `Modified Data/`.
2. Adjust `base_dir` and `ffmpeg_path` at the top of the notebooks.
3. Run the notebook cells.
4. Find per-cluster plots under `Plots/`, frame-by-frame images under `Frames/`, and MP4 movies in the analysis folder.

## Acknowledgments

This work was carried out as part of my **Master’s final project**. I am deeply grateful to my professor, who provided the data and guidance that made this research possible. I also acknowledge the broader gravitational-wave community, particularly the **LIGO–Virgo Collaboration**, whose pioneering work in building and operating these detectors created the foundation for this analysis.
