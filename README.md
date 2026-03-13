# 🧠 Quantitative EEG Analysis & Topographic Brain Mapping

![Python](https://img.shields.io/badge/Python-3.10+-blue?style=flat-square&logo=python)
![MNE](https://img.shields.io/badge/MNE--Python-1.6+-orange?style=flat-square)
![NumPy](https://img.shields.io/badge/NumPy-1.24+-013243?style=flat-square&logo=numpy)
![License](https://img.shields.io/badge/License-MIT-green?style=flat-square)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen?style=flat-square)

> A computational neuroengineering project implementing quantitative EEG (qEEG) signal processing, spectral analysis, and topographic visualization of cognitive states using Python and MNE-Python.

---

## 📌 Overview

This project implements a full **EEG signal processing pipeline** on the publicly available [PhysioNet EEG Motor Movement/Imagery Dataset](https://physionet.org/content/eegmmidb/1.0.0/). The pipeline covers raw data ingestion, artifact filtering, power spectral density (PSD) estimation via Welch's method, and topographic brain mapping across five canonical EEG frequency bands.

The project bridges **neuroscience theory** and **signal processing engineering**, making it relevant for roles in BCI (Brain-Computer Interface), clinical neuroengineering, and computational neuroscience.

---

## 🎯 Key Results

| Analysis | Finding |
|---|---|
| Frequency Bands | Delta, Theta, Alpha, Beta, Gamma characterized via Welch PSD |
| Topographic Maps | 5-band spatial power distribution across 62 EEG channels |
| Alpha Dominance | Confirmed posterior (occipital) dominance during eyes-open rest |
| 1/f Noise | Addressed via log10 normalization for meaningful inter-band comparison |

---

## 🗂️ Project Structure

```
eeg-qeeg-analysis/
│
├── notebooks/
│   └── eeg_analysis.ipynb       # Full analysis pipeline (annotated)
│
├── outputs/
│   ├── 01_filtering.png         # Raw vs filtered signal comparison
│   ├── 02_psd_analysis.png      # Power Spectral Density (linear)
│   ├── 02b_psd_normalized.png   # PSD (log-normalized)
│   └── 03_topomap.png           # 5-band topographic brain maps
│
├── THEORY.md                    # Neuroscience & signal processing background
├── requirements.txt             # Python dependencies
├── .gitignore
└── README.md
```

---

## ⚙️ Pipeline

```
Raw EEG (.edf)
      │
      ▼
[1] Load & Inspect          mne.io.read_raw_edf()
      │                     → 64 channels, 160 Hz, 60s
      ▼
[2] Electrode Mapping       set_montage('biosemi64')
      │                     → 3D spatial coordinates assigned
      ▼
[3] Bandpass Filtering      raw.filter(1.0, 40.0, method='fir')
      │                     → Removes DC drift & EMG artifacts
      ▼
[4] PSD Estimation          compute_psd(method='welch', n_fft=512)
      │                     → Frequency resolution: ~0.31 Hz
      ▼
[5] Band Power Extraction   δ(0.5-4) θ(4-8) α(8-13) β(13-30) γ(30-40)
      │                     → Log10 normalization applied
      ▼
[6] Topographic Mapping     mne.viz.plot_topomap()
                            → Spatial power distribution per band
```

---

## 📊 Visualizations

### Raw vs Filtered Signal
Comparison of raw EEG signal against 1–40 Hz bandpass filtered output, demonstrating removal of DC drift and high-frequency muscle artifacts.

### Power Spectral Density
Welch-method PSD showing the characteristic 1/f (pink noise) slope of neural signals, with annotated frequency band regions.

### Topographic Brain Maps
Five-band topographic maps reveal spatially distinct power distributions:
- **Alpha (8–13 Hz):** Strong posterior (occipital) dominance — consistent with visual cortex idling rhythm during resting state
- **Beta (13–30 Hz):** Frontocentral distribution — motor and cognitive engagement regions
- **Delta (0.5–4 Hz):** Broadly distributed, highest absolute power (1/f artifact, log-normalized)

---

## 🧬 Dataset

| Property | Value |
|---|---|
| Source | [PhysioNet EEG Motor Movement/Imagery Dataset](https://physionet.org/content/eegmmidb/1.0.0/) |
| Subject | S001 (baseline resting state, eyes open) |
| File | S001R01.edf |
| Channels | 64 EEG (BioSemi ActiveTwo, 10-20 system) |
| Sampling Rate | 160 Hz |
| Duration | ~60 seconds |
| Reference | Schalk et al., 2004. *BCI2000: A general-purpose brain-computer interface system.* IEEE TBME. |

---

## 🚀 Getting Started

### 1. Clone the repository
```bash
git clone https://github.com/YOUR_USERNAME/eeg-qeeg-analysis.git
cd eeg-qeeg-analysis
```

### 2. Install dependencies
```bash
pip install -r requirements.txt
```

### 3. Download the dataset
```bash
# Download Subject 1 resting state recording
wget https://physionet.org/files/eegmmidb/1.0.0/S001/S001R01.edf
mkdir -p data/S001
mv S001R01.edf data/S001/
```

### 4. Run the notebook
```bash
jupyter notebook notebooks/eeg_analysis.ipynb
```

> **Note:** Update the `data_path` variable in the notebook to match your local directory.

---

## 🧰 Dependencies

| Package | Version | Purpose |
|---|---|---|
| `mne` | ≥ 1.6 | EEG I/O, filtering, PSD, topomap |
| `numpy` | ≥ 1.24 | Numerical computation |
| `scipy` | ≥ 1.10 | Signal processing utilities |
| `matplotlib` | ≥ 3.7 | Visualization |
| `seaborn` | ≥ 0.12 | Statistical plotting |
| `jupyter` | ≥ 1.0 | Interactive notebook environment |

---

## 📚 References

1. Schalk, G., McFarland, D.J., Hinterberger, T., Birbaumer, N., Wolpaw, J.R. (2004). BCI2000: A general-purpose brain-computer interface system. *IEEE Transactions on Biomedical Engineering*, 51(6), 1034–1043.
2. Goldberger, A., et al. (2000). PhysioBank, PhysioToolkit, and PhysioNet. *Circulation*, 101(23).
3. Gramfort, A., et al. (2013). MEG and EEG data analysis with MNE-Python. *Frontiers in Neuroscience*, 7, 267.
4. Welch, P.D. (1967). The use of fast Fourier transform for the estimation of power spectra. *IEEE Transactions on Audio and Electroacoustics*, 15(2), 70–73.

---

## 📄 License

This project is licensed under the MIT License. The dataset is publicly available under the PhysioNet Credentialed Health Data License.

---

## 🙋 Author

**[Your Name]**
- GitHub: [@YOUR_USERNAME](https://github.com/YOUR_USERNAME)
- LinkedIn: [linkedin.com/in/YOUR_PROFILE](https://linkedin.com/in/YOUR_PROFILE)

---

*If you find this project useful, please consider giving it a ⭐*
