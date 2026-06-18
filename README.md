# Video Object Tracking & Regression Mechanics

An end-to-end computer vision and mathematical modeling pipeline that tracks a moving yellow ball in a slow-motion video, builds a custom spatial-temporal dataset, and performs rigorous algebraic regression analysis (Standard Least Squares, Total Least Squares, and RANSAC).

---

## 📌 Project Overview
This project extracts structural trends from raw physical motion data captured via video. By applying custom HSV color masking, it isolates a dynamic target, computes state centroids across sequential frames, and tests the predictive limits of linear and non-linear parameter-estimation techniques.

### Key Objectives
* **Target Isolation:** Construct dynamic color masks to filter high-speed artifacts.
* **Mathematical Line Fitting:** Contrast standard vertical-offset minimizing systems against orthogonal-distance formulations.
* **Robust Curve Fitting:** Model ballistic parabolic arcs using global matrix inversions alongside iterative, noise-tolerant algorithms (RANSAC).

---

## 📂 Repository Structure
```
├── yellow_ball_up_and_down.mp4   # Source tracking video (Frames 191 to 292 analyzed)
├── ball_tracking.ipynb           # Main Python/Jupyter processing pipeline
└── README.md                     # Documentation
```
---

## 🛠️ Pipeline Architecture

### 1. Frame Extraction & HSV Localization
Using OpenCV, the source asset is parsed frame-by-frame. Pixels identifying the target object are segmented out within a deterministic HSV bound:
* **Lower Bound:** `[20, 100, 100]`
* **Upper Bound:** `[30, 255, 255]`

### 2. Coordinate Transformation & Centroid Extraction
For each operational timestamp, the multi-pixel object mask is collapsed to an explicit spatial matrix point via mass-moment calculations:

$$\bar{x} = \frac{1}{N}\sum_{i=1}^{N} x_i, \quad \bar{y} = h - \frac{1}{N}\sum_{i=1}^{N} y_i$$

*Note: Coordinates are systematically inverted along the y-axis to baseline $(x=0, y=0)$ directly at the bottom-left edge of the video canopy.*

---

## 📈 Analytical Modeling & Performance Insights

### Linear Optimization (SLS vs. TLS)
The pipeline maps architectural tracking data through standard $y = mx + b$ variations alongside Total Least Squares ($ax + by = d$) structures derived using Singular Value Decomposition (SVD).

* **Standard Least Squares (SLS):** Best suited for this dataset. Because the ball travels along a nearly vertical axis, variation (noise) is heavily focused on the $y$-axis while the $x$-axis stays relatively constant. SLS effectively handles this asymmetric vertical noise.
* **Total Least Squares (TLS) Failure Mode:** TLS accounts for noise in both the $x$ and $y$ variables by minimizing the perpendicular distance to the line. Because the variation in $y$ is drastically higher than the variation in $x$ in this dataset, TLS treats the tiny changes in $x$ as structural stability, forcing an incorrect, nearly horizontal line.

### Quadratic Trajectory Optimization (SLS vs. RANSAC)
The temporal mapping path ($y = at^2 + bt + c$) calculates the parabolic arc of physical gravity over time ($t$).

* **Total Least Squares for Time Parameters:** We do not use TLS here because time ($t$) is a perfectly controlled, independent index with zero measurement error. TLS is mathematically unnecessary when one of the variables is completely free of noise.
* **RANSAC vs. Standard LS Advantage:** Because the generated tracking dataset is clean and free of tracking errors, SLS and RANSAC produce almost identical parabolic models. RANSAC's true advantage only shows when dealing with messy datasets containing chaotic tracking anomalies or temporary obstructions.

---

## 📊 Evaluation Metrics & Projections

When predicting a boundary trajectory point at $x = 59.2$:

* **Standard Least Squares Projection:** $y \approx 49.7409$
* **Total Least Squares Projection:** $y \approx -45676.3285$ *(Demonstrates the extreme error caused by SVD coordinate-collapse on data with highly skewed variance).*

---

## 🚀 Quick Start & Environment Requirements

### Dependencies
Ensure you have Python 3.8+ installed along with the following packages:
```bash
pip install opencv-python numpy matplotlib
```

## Execution
Clone this repository:

```bash
git clone [https://github.com/pranavkolii/video-object-tracking.git](https://github.com/pranavkolii/video-object-tracking.git)
```

Place the video file (yellow_ball_up_and_down.mp4) into the workspace directory.


Launch the runtime notebook environment:

```bash
jupyter notebook ball_tracking.ipynb
```
