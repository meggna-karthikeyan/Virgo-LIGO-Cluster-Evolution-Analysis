# Virgo Livingston Hanford Triple-Detector Cluster Analysis

## Overview

This notebook investigates clusters that are **simultaneously observed in all three detectors** — Virgo (V1), Livingston (L1), and Hanford (H1). While Hanford and Livingston share similar sensitivity, Virgo is less sensitive and often fails to detect weaker signals. By restricting to clusters present in **all three detectors during periods of coincident operation**, this analysis provides a controlled comparison of how the network behaves as a whole.

The outputs include **plots, frames, and a movie** visualizing the evolution of triple-detector clusters across mass space.

---

## Data Preprocessing

* The **original catalogs** contained a column **`InsideH1L1V1`**, which indicates whether all three detectors were online.

  * `1` → all detectors active
  * `0` → one or more offline
* Data was **filtered to rows where `InsideH1L1V1 = 1`**, ensuring the analysis reflects true three-detector operation.
* Only **Cluster IDs** that appeared in **all three catalogs (V1, L1, H1)** were retained for visualization.

👉 *Motivation*: This guarantees that results represent genuine triple coincidences, not pairwise overlaps or single-detector events.

---

## Code Walkthrough and Motivation

### 1. Input and Setup

Paths are defined for:

* Detector data (`singles_V1.csv`, `singles_L1.csv`, `singles_H1.csv`)
* Template bank (`template_bank.hdf5`)
* Output directories (`Plots/`, `Frames/`) and movie file path.

👉 *Motivation*: Keeping data and outputs organized by analysis type makes results reproducible and modular.

---

### 2. Reading and Cleaning Data

Trigger catalogs are read into DataFrames. Each includes:

* `Cluster ID`
* `Mass_1_mf`, `Mass_2_mf` (component masses)
* `SNR_mf` (signal-to-noise ratio)

Preprocessing:

* Masses clipped at `10⁻³` for log-scale stability.
* SNR capped at `60` to prevent color saturation by outliers.

👉 *Motivation*: These steps ensure that the visualizations highlight astrophysical structures, not numerical extremes.

---

### 3. Template Bank Overlay

The template bank is loaded and plotted as a faint gray background on all axes.

👉 *Motivation*: Provides astrophysical context, showing where clusters fall relative to the expected parameter space of binary systems.

---

### 4. Identifying Triple-Detector Clusters

Clusters are selected where the `Cluster ID` is present in all three catalogs. These represent **true triple coincidences**.

👉 *Motivation*: Coincidence across three detectors is the gold standard for gravitational-wave detection confidence.

---

### 5. Resume Logic

* If **all expected frames** are already present in both `Plots/` and `Frames/`, a **fresh run** clears them and regenerates outputs.
* Otherwise, **resume mode** skips clusters with existing frames.

👉 *Motivation*: Ensures efficiency and robustness against interruptions or long runtimes.

---

### 6. Plotting Logic

For each triple-detected cluster:

* A **three-panel plot** is generated (V1, L1, H1).
* Each subplot shows triggers colored by SNR (`cmap="plasma_r"`, 0–60).
* Template bank faintly overlaid in the background.
* Shared log-scale axes and synchronized ticks.
* Titles indicate detector and trigger count.
* A shared colorbar maps SNR across all detectors.

👉 *Motivation*:

* **Side-by-side comparison** highlights detector-specific differences.
* **Shared colorbar and axes** ensure direct comparability.
* **Triple layout** reflects the structure of the gravitational-wave detector network.

---

### 7. Output Generation

* Per-cluster PNGs in `Plots/`.
* Sequentially numbered frames in `Frames/`.
* Movie generated with `ffmpeg`:
  **`Virgo - Livingston - Hanford Cluster Evolution.mp4`**

👉 *Motivation*:

* Still images aid detailed inspection of individual clusters.
* The movie provides a rapid, intuitive overview of triple-detector consistency.

---

## Outputs

* **Plots/** → high-resolution per-cluster static images.
* **Frames/** → sequential PNGs used to generate the movie.
* **Virgo – Livingston – Hanford Cluster Evolution.mp4** → **main deliverable**, summarizing all clusters in motion.

---

## Conclusion

This analysis demonstrates the behavior of clusters simultaneously observed in Virgo, Livingston, and Hanford. Despite Virgo’s lower sensitivity, requiring its presence ensures that only the **strongest, most robust clusters** are visualized. The resulting plots and movie serve as a compelling demonstration of **network-level consistency** and are well-suited for presentations and further comparative studies.

---