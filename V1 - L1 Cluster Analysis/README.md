# Virgo and Livingston Cluster Analysis

## Overview

This notebook analyzes gravitational-wave trigger clusters observed in coincidence between the **Virgo (V1)** and **LIGO Livingston (L1)** detectors. While L1 is one of the most sensitive detectors in the network, Virgo often provides fewer triggers due to its lower sensitivity. By examining **clusters detected by both Virgo and Livingston during times when all three detectors were operating**, this analysis explores whether Virgo still provides useful information and how its behavior compares to a stronger detector.

The output of this analysis is both **plots** (static, per-cluster) and a **movie** that visualizes cluster evolution across the two detectors.

---

## Data Preprocessing

* The **original trigger catalogs** included a column named **`InsideH1L1V1`**, which records whether all three detectors (Hanford, Livingston, Virgo) were simultaneously online.

  * `1` → all three detectors active
  * `0` → one or more detectors offline
* Data was **filtered to retain only rows where `InsideH1L1V1 = 1`**, ensuring analysis focuses exclusively on periods of true three-detector operation.
* From these filtered catalogs, we used only the **Cluster IDs** that appeared in both V1 and L1.

---

## Code Walkthrough and Motivation

### 1. Input and Setup

The notebook begins by setting paths to:

* Modified trigger data (`singles_V1.csv`, `singles_L1.csv`)
* Template bank (`template_bank.hdf5`)
* Output folders (`Plots/`, `Frames/`) and movie file path.

👉 *Motivation*: Keeping data and outputs organized by detector pair makes the workflow reproducible and modular.

---

### 2. Reading Trigger Data

CSV files for Virgo and Livingston are loaded into Pandas DataFrames. Each trigger contains:

* `Cluster ID`
* `Mass_1_mf`, `Mass_2_mf` (binary component masses)
* `SNR_mf` (matched-filter signal-to-noise ratio)

👉 *Motivation*: These variables allow comparison of astrophysical parameter distributions between detectors.

---

### 3. Clipping for Stability

* Masses clipped below `10⁻³` → prevents issues when plotting on logarithmic axes.
* SNR capped at `60` → avoids single extreme outliers dominating the color scale.

👉 *Motivation*: Clipping ensures meaningful visualization across clusters without distortion, keeping attention on the astrophysically relevant parameter space.

---

### 4. Template Bank Overlay

The template bank is read from the HDF5 file and plotted faintly in the background of all cluster plots.

👉 *Motivation*: The template bank represents the **full search space** of possible binary systems. Overlaying it provides astrophysical context for where triggers fall relative to expectations.

---

### 5. Cluster Identification

* Virgo and Livingston `Cluster ID`s are intersected.
* Only common clusters are retained, since these represent coincident detections across detectors.

👉 *Motivation*: Coincidences are critical for confirming astrophysical events and reducing false alarms.

---

### 6. Resume Logic

Before plotting:

* If all expected frames already exist in both `Plots/` and `Frames/`, a fresh run clears outputs and regenerates everything.
* Otherwise, **resume mode** skips clusters that already have frames.

👉 *Motivation*: Enables long runs without wasting time or redoing completed work, especially helpful for large datasets or when memory errors interrupt execution.

---

### 7. Plotting Logic

For each common cluster:

* Two subplots (side-by-side): Virgo and Livingston.
* Background: template bank scatter points (light gray).
* Foreground: cluster triggers, colored by SNR (`cmap="plasma_r"`, range 0–60).
* Shared log scaling on both axes with carefully chosen tick marks.
* Figure title includes cluster ID and total trigger count.
* Shared colorbar for SNR interpretation.

👉 *Motivation*:

* Side-by-side comparison highlights **agreement and divergence** between Virgo and Livingston.
* Shared scales and colorbar ensure **visual consistency** across plots and clusters.

---

### 8. Output Generation

* Each cluster produces two PNGs:

  * One in `Plots/` (presentation-quality still).
  * One in `Frames/` (numbered sequence for movie generation).
* After all frames are generated, `ffmpeg` compiles them into:
  **`Virgo - Livingston Cluster Evolution.mp4`**

👉 *Motivation*:

* Still plots are useful for deep dives into specific clusters.
* Movies enable rapid, qualitative assessment of detector behavior across many clusters — ideal for talks, posters, and teaching.

---

## Outputs

* **Plots/** → high-resolution static images per cluster
* **Frames/** → sequential PNGs used to build the movie
* **Virgo – Livingston Cluster Evolution.mp4** → **main deliverable** movie

---

## Conclusion

The V1–L1 cluster analysis demonstrates how Virgo (less sensitive) compares with Livingston (more sensitive) across coincident triggers. While Virgo often reports fewer triggers, the visualization pipeline reveals where it aligns with Livingston clusters and where it fails to detect. The resulting movies and plots serve as **intuitive tools** for understanding Virgo’s contribution in a three-detector network and for communicating findings in academic settings.

---