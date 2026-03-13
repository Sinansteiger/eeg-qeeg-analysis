# 📖 Theoretical Background

## Table of Contents
1. [What is EEG?](#1-what-is-eeg)
2. [The 10-20 Electrode System](#2-the-10-20-electrode-system)
3. [Signal Characteristics](#3-signal-characteristics)
4. [EEG Frequency Bands](#4-eeg-frequency-bands)
5. [Signal Processing Concepts](#5-signal-processing-concepts)
6. [Topographic Mapping](#6-topographic-mapping)

---

## 1. What is EEG?

Electroencephalography (EEG) measures the electrical activity of the brain through electrodes placed on the scalp. When neurons fire synchronously, the summed postsynaptic potentials create measurable voltage fluctuations at the scalp surface.

**Key properties:**
- **Amplitude:** 10–100 μV (microvolts) — extremely small signals
- **Temporal resolution:** Millisecond-level — excellent for tracking rapid brain dynamics
- **Spatial resolution:** Low (centimeter-level) — difficult to pinpoint exact neural generators
- **Non-invasive** and relatively low-cost compared to fMRI or MEG

**Why it matters for BCI and neuroengineering:**  
EEG's high temporal resolution and portability make it the dominant modality for Brain-Computer Interface (BCI) systems, neurofeedback applications, and real-time cognitive state monitoring.

---

## 2. The 10-20 Electrode System

The International 10-20 System defines standardized scalp electrode positions based on proportional distances from anatomical landmarks (nasion, inion, preauricular points).

**Naming convention:**
- **Letter** = Brain region: F (Frontal), C (Central), P (Parietal), O (Occipital), T (Temporal)
- **Number** = Odd = Left hemisphere, Even = Right hemisphere
- **z suffix** = Midline (e.g., Fz, Cz, Oz)

| Region | Electrodes | Primary Functions |
|---|---|---|
| Prefrontal | Fp1, Fp2 | Attention, personality, executive planning |
| Frontal | F3, F4, Fz | Motor planning, working memory, decision making |
| Central | C3, C4, Cz | Primary motor & somatosensory cortex |
| Parietal | P3, P4, Pz | Spatial processing, attention, sensory integration |
| Occipital | O1, O2, Oz | Primary visual cortex |
| Temporal | T7, T8 | Language, auditory processing, memory |

---

## 3. Signal Characteristics

### Sampling Rate and Nyquist Theorem

The **Nyquist–Shannon sampling theorem** states that to accurately reconstruct a signal of frequency *f*, the sampling rate must be at least *2f*.

- Our dataset: 160 Hz sampling rate → accurate up to 80 Hz
- The hardware applied a 80 Hz anti-aliasing lowpass filter (visible in `raw.info['lowpass']`)

### 1/f Noise (Pink Noise)

Neural signals exhibit a characteristic *1/f* power spectrum — power decreases as a function of increasing frequency. This is why the Delta band always appears to dominate in raw power comparisons. **Log10 normalization** is applied to linearize this relationship and allow meaningful inter-band comparisons.

---

## 4. EEG Frequency Bands

| Band | Range | Cognitive/Neural Correlate |
|---|---|---|
| **Delta (δ)** | 0.5–4 Hz | Deep sleep, unconsciousness; high in infants; pathological when excessive in waking adults |
| **Theta (θ)** | 4–8 Hz | Drowsiness, meditation, memory encoding (hippocampal theta), creative ideation |
| **Alpha (α)** | 8–13 Hz | Relaxed wakefulness, cortical idling, visual suppression; strongest at occipital sites |
| **Beta (β)** | 13–30 Hz | Active thinking, motor control, anxiety, focused attention; dominant in frontocentral regions |
| **Gamma (γ)** | 30–100 Hz | High cognitive load, sensory binding, learning; often contaminated by EMG artifacts |

### Alpha Rhythm and Event-Related Desynchronization (ERD)

The **alpha rhythm** is among the most robust findings in EEG research. During resting state with eyes closed, the visual cortex (occipital lobe) exhibits strong 8–13 Hz synchronization — termed the **Berger rhythm** (named after Hans Berger, who recorded the first human EEG in 1924).

When visual input arrives (eyes open) or cognitive effort increases, this synchrony **desynchronizes** — the alpha power drops. This phenomenon is called **Event-Related Desynchronization (ERD)**:

```
Eyes Closed → High occipital Alpha (visual cortex "idling")
Eyes Open   → Low occipital Alpha (visual cortex "processing")
```

ERD is the foundational principle behind many BCI paradigms (e.g., motor imagery BCIs detect alpha/beta ERD over motor cortex during imagined movement).

---

## 5. Signal Processing Concepts

### Bandpass Filtering

A bandpass filter passes frequencies within a defined range and attenuates those outside it. In this project:

- **Lower cutoff (1 Hz):** Removes DC drift and slow electrode polarization artifacts
- **Upper cutoff (40 Hz):** Removes electromyographic (EMG) muscle artifacts

**FIR vs IIR Filters:**

| Property | FIR | IIR |
|---|---|---|
| Phase response | Linear (no phase distortion) | Non-linear |
| Stability | Always stable | Can be unstable |
| Computational cost | Higher | Lower |
| Use case | Offline EEG analysis ✅ | Real-time systems |

FIR filters are preferred for offline EEG analysis because they preserve the temporal relationships between signals — critical for event-related analyses.

### Power Spectral Density (PSD)

PSD describes how the power of a signal is distributed across frequencies. Formally:

```
PSD(f) = lim[T→∞] (1/T) |X(f)|²

where X(f) is the Fourier Transform of the signal x(t)
```

### Welch's Method

Direct FFT on a single long window is sensitive to noise. **Welch's method** improves PSD estimation by:

1. Dividing the signal into overlapping segments (window size = `n_fft`)
2. Applying a window function (Hann/Kaiser) to each segment
3. Computing FFT for each segment
4. Averaging the squared magnitudes

With `n_fft=512` and `sfreq=160 Hz`:
- Frequency resolution = 160 / 512 ≈ **0.31 Hz per bin**
- Time-bandwidth tradeoff: longer windows → better frequency resolution, worse temporal resolution

---

## 6. Topographic Mapping

### Montage and Electrode Localization

A **montage** defines the 3D Cartesian coordinates of each electrode on a standardized head model. MNE uses this to project 3D coordinates onto a 2D circular head representation via azimuthal equidistant projection.

### Interpolation

Since we only have 62 discrete electrode measurements (after excluding T9/T10), the topographic map requires **spatial interpolation** to fill the continuous scalp surface. MNE uses **CloughTocher 2D interpolation**, which fits a smooth piecewise polynomial surface through the electrode data points.

### Color Mapping

The `RdYlBu_r` colormap encodes power values:
- **Red (warm):** High power
- **Blue (cool):** Low power
- **Yellow:** Intermediate

Log10-normalized values ensure that color differences between bands reflect meaningful neural differences rather than the 1/f amplitude scaling artifact.

---

*For implementation details, see the annotated notebook: `notebooks/eeg_analysis.ipynb`*
