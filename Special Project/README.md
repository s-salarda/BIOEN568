# Baseline Shift Correction in LabVIEW

## 1. Working Principle
This project detects low‑frequency baseline drift in ECG signals — a common artifact caused by respiration, electrode motion, or gradual sensor offset. Baseline drift is dominated by frequencies below 0.5 Hz, so the system isolates this component using a 0.5 Hz low‑pass filter.

After filtering, the system computes:

- The current window mean

- The baseline mean (previous window)

- The difference array between the two

- The maximum baseline shift magnitude

If the maximum difference exceeds a threshold (0.05 mV), the system flags a Baseline Shift Indicator = TRUE.

This allows real‑time detection of slow drift without affecting the higher‑frequency ECG morphology.

## 2. Rationale Behind the Design
Baseline drift is a slow, smooth artifact, so a low‑pass filter is the most direct way to isolate it. Using a 0.5 Hz cutoff ensures that only drift‑related components remain while preserving the true ECG waveform for downstream analysis.

A dynamic mean‑difference method was chosen because:

It adapts to patient‑specific baseline levels

It avoids fixed thresholds that fail across subjects

It is computationally lightweight and suitable for real‑time LabVIEW execution

This design balances simplicity, robustness, and clinical interpretability.

## 3. Example Input-Output Pairs
Example 1 — Clean ECG (No Drift)
Input:

- Patient ECG with stable baseline

- Simulated noise amplitude: 0.2

Output:

- Max baseline shift magnitude: 0.018

- Threshold: 0.05

- Baseline Shift Indicator: FALSE

- Interpretation: No significant drift detected.

Example 2 — Drifted ECG (Real Patient)
Input:

- Patient with known baseline drift

- Low‑frequency drift visible in raw signal

Output:

- Max baseline shift magnitude: 0.091

- Threshold: 0.05

- Baseline Shift Indicator: TRUE

- Interpretation: Drift successfully detected.

Example 3 — Simulated Drift Injection
Input:

- Clean patient ECG

- Added synthetic drift with amplitude = 2

Output:

- Patients 117 & 201: drift detected

- Patients 208 & 232: drift detected at amplitude 1.2

- Baseline Shift Indicator: TRUE

This demonstrates the system’s sensitivity across multiple subjects.

## 4. Suggestions for Future Improvements
- Adaptive thresholding: Automatically adjust the 0.05 threshold based on noise statistics.

- Hysteresis logic: Reduce false positives by requiring sustained drift before triggering.

- Polynomial detrending: Compare performance against higher‑order drift removal.

- Visualization panel: Plot drift vs. corrected signal for easier debugging.

- Window‑size optimization: Tune sample size for different sampling rates or patient conditions.
