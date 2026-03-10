Baseline Shift Correction in LabVIEW
This project implements a real‑time baseline‑shift correction system for analog signals using LabVIEW. Baseline drift—slow, low‑frequency movement of the signal—is common in physiological, mechanical, and environmental sensors. The goal of this project is to estimate the baseline, remove it, and output a clean, drift‑corrected waveform suitable for analysis or downstream processing.

1. Working Principle
Baseline drift is treated as a slow trend added to the true signal. To correct it, the system computes a baseline estimate and subtracts it:

corrected
(
𝑡
)
=
raw
(
𝑡
)
−
baseline
(
𝑡
)
This project provides three LabVIEW‑compatible baseline‑removal methods:

A. High‑Pass Filtering
A low‑cutoff high‑pass filter removes slow drift while preserving meaningful features.

Implemented using LabVIEW’s Highpass Filter.vi

Typical cutoff: 0.1–1 Hz depending on the application

Best for continuous signals like ECG, pressure, or motion sensors

B. Moving Average Baseline Subtraction
A long‑window moving average tracks only slow changes and serves as the baseline.

Implemented using Mean PtByPt.vi

Window length: 500–2000 samples

Baseline is subtracted from the raw signal in real time

C. Polynomial Detrending
A low‑order polynomial is fit to the waveform and removed.

Implemented using Polynomial Fit.vi

Suitable for curved or long‑duration drift

2. Rationale Behind the Design
The design focuses on robustness, interpretability, and real‑time performance:

High‑pass filtering is a standard DSP approach for drift removal and works well for most sensor data.

Moving average subtraction is intuitive, easy to visualize, and ideal for teaching environments.

Polynomial detrending handles non‑linear drift patterns without distorting higher‑frequency components.

All methods are modular, allowing users to swap techniques without redesigning the entire VI.

This flexibility makes the system suitable for labs, prototyping, and educational demonstrations.
