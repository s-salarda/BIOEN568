# Baseline Shift Correction in LabVIEW
The baseline‑shift detection function identifies slow, low‑frequency drift in ECG signals, which can distort waveform interpretation and throw off analyses like R‑peak detection, heart‑rate estimation, or any metric that depends on a stable baseline. This type of drift comes from very low‑frequency components below 0.5 Hz, usually tied to respiration, electrode motion, or gradual sensor offset. By isolating that band and comparing the current window’s mean to the previous baseline, the function gives a lightweight, real‑time indicator of when the ECG baseline is no longer stable. This matters because it helps clinicians or algorithms immediately recognize when signal quality has degraded, which supports more reliable physiological measurements and prevents misinterpretation when the data starts shifting underneath the actual cardiac activity.

## 1. Working Principle
This project is centered around detecting that slow, low‑frequency drift in ECG signals by isolating the part of the waveform that actually shows the baseline movement. Baseline drift specifically shows up in the frequency range below 0.5 Hz, so the function first low‑pass filters the ECG to pull out that slow component. Once that’s isolated, the function looks at the mean of the current window defined by the number of samples inside the for loop, which iterates through the filtered ECG and builds an array of differences between the current window’s mean and the previous baseline mean. More specifically, the loop walks through the signal one window at a time, constantly updating the “current mean” and comparing it to the stored baseline to see how much the baseline is shifting. Since the baseline should stay relatively stable in clean ECG, any meaningful change in that low‑frequency mean reflects drift. That’s why the difference array and the max‑difference calculation are pulled since they quantify how far the baseline is moving and whether it crosses the threshold where drift is defined. This gives a lightweight indicator of when the ECG baseline is no longer stable.

## 2. Rationale Behind the Design
The rationale behind this design is that baseline drift is a slow, low‑frequency problem, so the simplest and most reliable way to detect it is to isolate the part of the ECG that actually contains that drift. A 0.5 Hz low‑pass filter cleanly separates the baseline movement from the real cardiac activity, which keeps the rest of the signal untouched. From there, using a windowed mean inside the for loop makes sense because it lets the function track how the baseline changes over time instead of assuming it stays constant. The loop updates the current mean every iteration and compares it to the previous baseline, which gives a direct measure of how much the baseline is shifting. Building an array of these differences also makes it easy to quantify the drift and check whether it crosses the threshold where it becomes a problem. 

## 3. Example Input-Output Pairs
### Example 1 — Clean ECG Data (No Drift)
Input:

- Patients 117, 201, 208, and 232, raw ECG.csv with a sample rate of 360 Hz.

- Duration: 10s

- Simulated noise amplitude: 0.2

Output:

- Max baseline shift magnitude:
  - Patient 117: 0.029977
  - Patient 201: 0.0242792
  - Patient 208: 0.0487872
  - Patient 232: 0.000338623

- Baseline Shift Indicator:
  - Patient 117: F
  - Patient 201: F
  - Patient 208: F
  - Patient 232: F

- Interpretation: No significant drift detected.

### Example 2 — Drifted ECG (Real Patient)
Input:

- PPatient 113 and 203, raw ECG.csv with a sample rate of 360 Hz.

- Duration: 10s

- Simulated noise amplitude: 0.2

Output:

- Max baseline shift magnitude:
  - Patient 113: 0.0229066
  - Patient 203: 0.157287


- Baseline Shift Indicator:
  - Patient 113: F
  - Patient 203: T

### Example 3 — Simulated Drift in Patients with Clean ECGs
Input:

- Run three times per patient

- Duration: 10s

- Simulated noise amplitude: 0.2

Output:

- Max baseline shift magnitude for 3 trials:
  - Patient 117: 0.00773368, 0.0579927, 0.0209488
  - Patient 201: 0.00861307, 0.0302622, 0.0090252
  - Patient 208: 0.0513655, 0.0310657, 0.0483109
  - Patient 232: 0.00335716, 9.19769E-5, 0.00152942

- Baseline Shift Indicator:
  - Patient 117: T, F, T
  - Patient 201: F, F, F
  - Patient 208: T, F, F
  - Patient 232: F, F, F

## 4. Suggestions for Future Improvements
Instead of using a fixed 0.05 threshold, the function could automatically adjust the threshold based on the noise statistics of the filtered baseline signal. Right now, the detector isn’t sensitive to small baseline changes because the threshold doesn’t scale with the actual variability of the signal. An adaptive approach would calculate something like the rolling standard deviation, RMS, or MAD of the low‑frequency component and set the threshold relative to that. This would improve the detector’s ability to respond to subtle baseline shifts, addressing the current function’s limited sensitivity to small‑amplitude drifts.

