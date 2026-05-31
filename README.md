# Personalized Adaptive VR Experience Driven by EEG-Based Pain Recognition
---
## Overview

Modern pain management in clinical settings (anesthesiology, oncology, palliative care, ICU) relies heavily on pharmaceuticals that carry risks of side effects and dependency. This project offers a non-invasive alternative: an AI-powered system that detects a patient's pain level in real time from EEG brain signals, then responds with a personalized, avatar-guided VR therapy experience to help alleviate that pain.

The system was built in collaboration with **RIT Dubai**, **Mohammed Bin Rashid University of Medicine & Health Sciences (MBRU)**, and **Healthy Mind** (a CE-certified medical VR company).
---

## System Architecture

The system is composed of three main subsystems:

**1. Machine Learning Subsystem (EE Team)**
- Preprocesses EEG data (artifact removal, band-pass filtering, ICA)
- Extracts features using FFT-based and Wavelet domain techniques
- Trains XGBoost and LightGBM classifiers to categorize pain levels
- Exports trained models in ONNX format for Unreal Engine integration

**2. VR Environment Subsystem (CIT Team)**
- Built in Unreal Engine 5.3 with the PICO SDK for PICO 4 headset
- Receives pain level predictions from the ML subsystem via a local REST API / WebSocket (UWS plugin)
- Dynamically adjusts the virtual environment (lighting, ambient sound, visuals) based on predicted pain

**3. Avatar Subsystem (CIT Team)**
- MetaHuman avatar created in Unreal Engine
- Responds to pain categories with distinct facial expressions, body language, and dialogue
- Dialogue powered by ConvAI plugin for empathetic, context-aware therapeutic responses
- Supports English and Arabic

---
## Datasets

### Dataset 1 — Open-Source EEG Pain Dataset (Primary)
- **Participants:** 51 subjects (ages 22–32)
- **Setup:** 69-channel EEG recorded via BrainVision Recorder; laser pain stimuli applied to the left hand
- **Conditions:** Perception, Motor, Autonomic, and Combined
- **Pain Rating:** Verbal scale 0–100 per stimulus
- **Files per session:** `.vhdr` (header), `.vmrk` (markers/triggers), `.eeg` (raw data)
---

## Preprocessing Pipeline

1. Load raw EEG files using the MNE Python library
2. Check and interpolate bad channels
3. Apply notch filter at 50 Hz (power line noise removal)
4. Apply band-pass filter (1–40 Hz)
5. Re-reference to average across channels
6. Run AutoReject to identify and fix noisy epochs
7. Apply ICA to remove ocular (eye movement) artifacts
8. Segment data into epochs; extract pain-level labels from trigger markers
9. Export cleaned data as `.fif` and `.csv` for model training

---
## Models

### XGBoost
- Wavelet feature extraction pipeline ((Daubechies-4, 5 levels)
- 80/20 train/test split with 5-fold cross-validation
- **Classification accuracy:** 97.25% (test) (cross-validation: 96.09% ± 0.70%)
- Pain categories: 0-11

### LightGBM
- Wavelet feature extraction pipeline (Daubechies-4, 5 levels)
- MinMax scaling applied to normalize EEG signals
- Hyperparameter tuning via RandomizedSearchCV
- **Classification accuracy:** 97.89% (test) (cross-validation: 95.78% ± 0.82%)
- Pain categories: 0-11

Both models outperform comparable published benchmarks on EEG-based pain detection.

---
## VR Integration

- **Headset:** PICO 4 (6DOF tracking, Android-based)
- **Engine:** Unreal Engine 5.3 with PICO SDK and ONNX Runtime plugin
- **Communication:** Python prediction script → HTTP POST → UWS WebSocket server → Unreal Blueprint logic
- **Avatar Response Mapping:**

| Pain Level | Avatar Behavior |
|---|---|
| 0–3 (Slight) | Calm, gentle reassurance; peaceful forest environment |
| 4–6 (Moderate) | Empathetic tone; guided breathing exercises |
| 7–10 (Severe) | Urgent compassion; grounding techniques; visual dimming |

---
## Tech Stack

| Category | Tools |
|---|---|
| EEG Processing | Python, MNE, AutoReject, PyWavelets |
| Machine Learning | XGBoost, LightGBM, PyTorch, scikit-learn |
| VR Development | Unreal Engine 5.3, PICO SDK, MetaHuman |
| Avatar AI | ConvAI Plugin |
| Communication | Flask API, UWS WebSocket Plugin |
| Prototyping | Unity, ML-Agents Toolkit |

---
## Future Work

- Real-time integration with live EEG hardware (32-channel MBRU system)
- Deep learning models (CNN, RNN/LSTM) for improved temporal pattern recognition
- Additional VR environment designs for different therapeutic contexts
- Multi-gender avatar options
- Multilingual support expansion beyond English and Arabic
- Publication of a journal paper documenting the full project lifecycle
