# Virgo and Hanford Cluster Analysis

## Overview

This notebook compares gravitational-wave trigger clusters observed by **Virgo (V1)** and **LIGO Hanford (H1)** during periods of coincident operation across the full three-detector network (V1, L1, H1). While Hanford is among the most sensitive detectors, Virgo often struggles to detect weaker signals due to its lower sensitivity. By restricting to **clusters present in both Virgo and Hanford during triple-detector operation**, this analysis investigates Virgo’s contribution when paired with a stronger detector.

The outputs include **plots, frames, and a movie** that visualizes the evolution of coincident V1–H1 clusters in the mass–mass parameter space

## Data Preprocessing

* The **original catalogs** contained a column **`InsideH1L1V1`**, indicating whether all three detectors were online.

  * `1` → all three detectors active
  * `0` → one or more offline
* Data was **filtered to retain only rows with `InsideH1L1V1 = 1`**, ensuring that only true three-detector operation is considered.
* From these filtered data, only **Cluster IDs** appearing in **both Virgo and Hanford** were retained.

👉 *Motivation*: This guarantees that comparisons reflect coincident operation, not detector downtime or spurious single-detector clusters.

## Code Walkthrough and Motivation

### 1. Input and Setup

Paths defined for:

* Virgo and Hanford trigger catalogs (`singles_V1.csv`, `singles_H1.csv`)
* Template bank (`template_bank.hdf5`)
* Output folders (`Plots/`, `Frames/`) and the MP4 output path.

👉 *Motivation*: Keeping code and outputs detector-specific ensures modularity and reproducibility.

### 2. Reading and Cleaning Data

Trigger catalogs are read with key columns:

* `Cluster ID`
* `Mass_1_mf`, `Mass_2_mf` (component masses)
* `SNR_mf` (signal-to-noise ratio)

Preprocessing:

* Masses clipped at `10⁻³` → stabilizes logarithmic axes.
* SNR capped at `60` → prevents color saturation by extreme outliers.

👉 *Motivation*: This preserves visualization clarity and ensures comparisons are astrophysically meaningful.

### 3. Template Bank Overlay

The template bank is extracted from HDF5 and plotted faintly in the background of all figures.

👉 *Motivation*: Provides astrophysical context, showing where clusters fall relative to the theoretical binary parameter space.

### 4. Identifying Common Clusters

Clusters are selected where `Cluster ID` is shared between Virgo and Hanford.

👉 *Motivation*: Coincident clusters across detectors are more likely to correspond to true astrophysical signals rather than noise.

### 5. Resume Logic

* If all expected frames exist in both `Plots/` and `Frames/`, a fresh run regenerates everything.
* Otherwise, **resume mode** skips already completed frames.

👉 *Motivation*: This saves time on large datasets and ensures robustness against runtime interruptions.

### 6. Plotting Logic

For each V1–H1 common cluster:

* A **two-panel figure** is created (Virgo left, Hanford right).
* Both subplots:

  * Scatter triggers, colored by SNR (`plasma_r`, capped at 60).
  * Template bank faintly in the background.
  * Shared log–log axes with synchronized tick marks.
* Titles: detector name + trigger count.
* Shared colorbar for SNR across both panels.
* Figure title includes **Cluster ID** and total trigger count.

👉 *Motivation*:

* Side-by-side plots reveal Virgo’s behavior relative to Hanford’s.
* Shared scales and colorbar ensure fair, interpretable comparison.

### 7. Output Generation

* Per-cluster plots saved to `Plots/`.
* Sequential frames saved to `Frames/`.
* Once all frames exist, an MP4 movie is built with `ffmpeg`:
  **`Virgo - Hanford Cluster Evolution.mp4`**

👉 *Motivation*:

* Plots enable detailed analysis of individual clusters.
* Movies provide a **dynamic overview**, making it easy to present patterns across many clusters quickly.

## Outputs

* **Plots/** → static PNGs per cluster
* **Frames/** → sequential PNGs for video generation
* **Virgo – Hanford Cluster Evolution.mp4** → **main deliverable movie**

## Conclusion

The V1–H1 analysis reveals how Virgo (less sensitive) compares with Hanford (more sensitive) during coincident operation. By focusing only on clusters observed when all three detectors were online, the study ensures that results reflect **true network-level performance**. The produced plots and movie provide clear visualizations of Virgo’s role, highlighting both its alignments and its limitations compared to Hanford.
