# Interpretable Singing Emotion Recognition 

## Overview

We propose an interpretable Singing Emotion Recognition (SER) framework combining domain-specific descriptors spanning prosodic, spectral, phonetic, and learned vocal technique features. Using a nested Leave-One-Speaker-Out (LOSO) feature selection strategy paired with SHAP and Grad-CAM analyses, we demonstrate that incorporating explicit singing descriptors (such as vibrato) improves cross-speaker stability and provides acoustically coherent explanations for emotional expression in singing performances.

## Repository Contents
```
├── notebooks_&scripts/
│   ├── Feature_Extraction.ipynb     # Pipeline for preprocessing and feature extraction
│   ├── run_mfa.bash                 # Automated script for Montreal Forced Aligner (MFA)
│   └── Results_and_Visuals.ipynb    # Nested LOSO feature selection, SHAP and Grad-CAM explainability, and performance evaluation
├── models/
│   └── model_pitch_leaky (1).h5     # Pre-trained Dual-Input CNN for vocal technique extraction
├── supplementary/
│   ├── cmudict.dict                 # CMU Pronouncing Dictionary for phoneme-alignment
|   ├── mfa_results.zip              # RAVDESS phonetic alignment TextGrid files (MFA output)
│   └── proposed_framework.png       # Framework architecture overview
└── README.md
```

### Notebooks & Scripts

#### 1. `Feature_Extraction.ipynb`
Comprehensive feature extraction pipeline for singing audio:

**Acoustic Features Extracted:**
- **Pitch Analysis**: F0 contours, pitch statistics, variability
- **Spectral Features**: Spectral centroid, spectral flatness, HNR, Formant frequencies, Alpha ratio etc.
- **Loudness & Energy**: energy dynamics, loudness variations, loudness peaks/sec
- **Vocal Technique Features:**: vocal technique presence
- **Phonetic Features**: CMU Dictionary-based phoneme analysis, articulation rates, phoneme and pause ratios.

#### 2. `Results_and_Visuals.ipynb`
Comprehensive analysis, evaluation, and interpretation of results:

**Content:**
- Leave-One-Speaker-Out (LOSO) evaluation methodology
- Feature group definitions (prosodic, spectral, phonemic, techniques)
- Model training and cross-validation
- Feature Selection pipeline (Nested LOSO)
- SHAP explainability analysis to understand feature importance
- Confusion matrices and classification reports
- Visualization of emotion distributions and feature patterns
- Cross-corpus performance comparison
- Grad-cam analysis to validate learned vocal technique features

#### 3. `run_mfa.bash`
- Montreal Forced Aligner (MFA) automation script
- Provides phonetic timing information
- Enables detailed phonetic feature extraction

### Access to Data & Models

To reproduce the feature extraction and experiments, download the datasets directly from their official sources:

- **RAVDESS (Singing Subset):** Download from [Zenodo (RAVDESS)](https://zenodo.org/records/1188976).
- **GTSinger:** access via [GTSinger GitHub Repository](https://github.com/AaronZ345/GTSinger).
- **VocalSet:** Download from [Zenodo (VocalSet)](https://zenodo.org/records/7061507)
                Download from [Zenodo (VocalSet)](https://zenodo.org/records/1442513?preview_file=VocalSet1-2.zip)

- **`model_pitch_leaky (1).h5`** - Pre-trained CNN model
  - Architecture: Dual-input architecture with late fusion
  - Purpose: Output presence of vocal techniques (*vibrato, glissando, mix, pharyngeal, falsetto, breathy*)
  - Input: Mel-spectrograms and pitch contours from **~1.5-second audio segments**

- **`cmudict.dict`** - CMU Pronouncing Dictionary
  - Contains phonetic transcriptions for 130K+ English words
  - Used for phoneme-alignment
 
### **Tools & Libraries:**
- CREPE for accurate pitch tracking
- OpenSMILE v2.6 (eGeMAPSv02) for acoustic feature extraction
- Librosa for audio processing and extracting features.
- TensorFlow (for the CNN)
- scikit-learn, SHAP, etc.

### **Summary of key findings & Figures**

### 1. Feature Selection Results
| Category | Speech-Optimized (RAVDESS, 19 feats) | Singing-Optimized (RAVDESS, 18 feats) | Singing-Optimized (GTSinger, 19 feats) |
| :--- | :--- | :--- | :--- |
| **Prosodic** | 'loudness_sma3_percentile80.0', 'loudnessPeaksPerSec', 'F0semitoneFrom27.5Hz_sma3nz_stddevNorm', 'F0semitoneFrom27.5Hz_sma3nz_amean', 'F0semitoneFrom27.5Hz_sma3nz_percentile80.0', 'shimmerLocaldB_sma3nz_amean', 'shimmerLocaldB_sma3nz_stddevNorm', 'MeanVoicedSegmentLengthSec' | 'shimmerLocaldB_sma3nz_amean', 'F0semitoneFrom27.5Hz_sma3nz_stddevNorm', 'F0semitoneFrom27.5Hz_sma3nz_percentile80.0', 'loudnessPeaksPerSec', 'melodic_deviation' | 'loudnessPeaksPerSec', 'VoicedSegmentsPerSec', 'F0semitoneFrom27.5Hz_sma3nz_percentile50.0', 'F0semitoneFrom27.5Hz_sma3nz_percentile20.0', 'shimmerLocaldB_sma3nz_amean', 'jitterLocal_sma3nz_amean' |
| **Spectral** | 'HNRdBACF_sma3nz_amean', 'alphaRatioV_sma3nz_stddevNorm', 'F2amplitudeLogRelF0_sma3nz_stddevNorm', 'alphaRatioV_sma3nz_amean', 'spectralFluxUV_sma3nz_amean', 'F1bandwidth_sma3nz_stddevNorm', 'logRelF0-H1-A3_sma3nz_amean', 'slopeV0-500_sma3nz_amean', 'F2frequency_sma3nz_stddevNorm'| 'HNRdBACF_sma3nz_amean', 'F2amplitudeLogRelF0_sma3nz_stddevNorm', 'alphaRatioV_sma3nz_stddevNorm', 'spectralFlux_sma3_amean', 'alphaRatioV_sma3nz_amean' | 'alphaRatioV_sma3nz_stddevNorm', 'slopeV0-500_sma3nz_stddevNorm', 'logRelF0-H1-H2_sma3nz_stddevNorm', 'HNRdBACF_sma3nz_stddevNorm', 'logRelF0-H1-A3_sma3nz_stddevNorm' |
| **Phonetic** | 'articulation_rate', 'std_phoneme_duration' | 'articulation_rate', 'std_phoneme_duration' | 'articulation_rate', 'std_phoneme_duration' |
| **Vocal Technique** | *None (Excluded by definition)* | 'MFDR_vibrato', 'breathy_mean', 'pharyngeal_max', 'falsetto_max', 'glissando_max', 'tech_entropy' | 'pharyngeal_max', 'mix_max', 'vibrato_peak_factor', 'vibrato_max', 'falsetto_std', 'breathy_std' |

### 2. Performance Comparison

### 3. VocalSet mappings
### 4. GTSinger SHAP analysis

## Acknowledgments & References

- **CMU Pronouncing Dictionary**: Used for phoneme-level alignment preprocessing.  
  *Carnegie Mellon University Speech Group.* Available at: [http://www.speech.cs.cmu.edu/cgi-bin/cmudict](http://www.speech.cs.cmu.edu/cgi-bin/cmudict)
