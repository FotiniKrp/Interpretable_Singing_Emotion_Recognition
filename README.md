# Interpretable Singing Emotion Recognition 

## Overview

We propose an interpretable Singing Emotion Recognition (SiER) framework combining domain-specific descriptors spanning prosodic, spectral, phonetic, and learned vocal technique features. Using a nested Leave-One-Speaker-Out (LOSO) feature selection strategy paired with SHAP and Grad-CAM analyses, we demonstrate that incorporating explicit singing descriptors (such as vibrato) improves cross-speaker stability and provides acoustically coherent explanations for emotional expression in singing performances.

## Repository Contents
```
├── notebooks_&scripts/
│   ├── Feature_Extraction.ipynb     # Pipeline for preprocessing and feature extraction
│   ├── run_mfa.bash                 # Automated script for Montreal Forced Aligner (MFA)
│   └── Results_and_Visuals.ipynb    # Nested LOSO feature selection, SHAP and Grad-CAM explainability, and performance evaluation
├── models/
│   └── model_pitch_leaky (1).h5     # Pre-trained Dual-Input CNN for vocal technique extraction
├── supplementary/
│   ├── Feature_Set.pdf              # Documentation for extracted feature set
│   ├── cmudict.dict                 # CMU Pronouncing Dictionary for phoneme-alignment
|   ├── mfa_results.zip              # RAVDESS phonetic alignment TextGrid files (MFA output)
│   └── SiER_pipeline.png            # Framework overview
└── README.md
```

### Notebooks & Scripts

#### 1. `Feature_Extraction.ipynb`
Comprehensive feature extraction pipeline for singing audio:

**Acoustic Features Extracted:**
- **Pitch Analysis**: pitch statistics, variability
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
- Cross-corpus performance comparisons
- Grad-cam analysis to validate learned vocal technique features

#### 3. `run_mfa.bash`
- Montreal Forced Aligner (MFA) automation script
- Provides phonetic timing information
- Enables detailed phonetic feature extraction

### Access to Data & Models

To reproduce the feature extraction and experiments, download the datasets directly from their official sources:

- **RAVDESS (Singing Subset):** [Zenodo](https://zenodo.org/records/1188976)
- **GTSinger:** [GitHub repository](https://github.com/AaronZ345/GTSinger)
- **VocalSet:** Annotations from [Zenodo](https://zenodo.org/records/7061507), audio files from [Zenodo](https://zenodo.org/records/1442513?preview_file=VocalSet1-2.zip)
  
- **`model_pitch_leaky (1).h5`** - Pre-trained CNN model
  - Architecture: Dual-input architecture with late fusion
  - Purpose: Output presence of vocal techniques (*vibrato, glissando, mix, pharyngeal, falsetto, breathy*)
  - Input: Mel-spectrograms and pitch contours from **~1.5-second audio segments**

### Selected Feature Sets (Nested LOSO)

The table below outlines the top selected feature subsets across different dataset optimizations. For more details of each feature listed, refer to [`supplementary/Feature_Definitions.pdf`](supplementary/Feature_Definitions.pdf).

| Category | Speech-Optimized (RAVDESS, 19 feats) | Singing-Optimized (RAVDESS, 18 feats) | Singing-Optimized (GTSinger, 19 feats) |
| :--- | :--- | :--- | :--- |
| **Prosodic** | 'loudness_sma3_percentile80.0', 'loudnessPeaksPerSec', 'F0semitoneFrom27.5Hz_sma3nz_stddevNorm', 'F0semitoneFrom27.5Hz_sma3nz_amean', 'F0semitoneFrom27.5Hz_sma3nz_percentile80.0', 'shimmerLocaldB_sma3nz_amean', 'shimmerLocaldB_sma3nz_stddevNorm', 'MeanVoicedSegmentLengthSec' | 'shimmerLocaldB_sma3nz_amean', 'F0semitoneFrom27.5Hz_sma3nz_stddevNorm', 'F0semitoneFrom27.5Hz_sma3nz_percentile80.0', 'loudnessPeaksPerSec', 'melodic_deviation' | 'loudnessPeaksPerSec', 'VoicedSegmentsPerSec', 'F0semitoneFrom27.5Hz_sma3nz_percentile50.0', 'F0semitoneFrom27.5Hz_sma3nz_percentile20.0', 'shimmerLocaldB_sma3nz_amean', 'jitterLocal_sma3nz_amean' |
| **Spectral** | 'HNRdBACF_sma3nz_amean', 'alphaRatioV_sma3nz_stddevNorm', 'F2amplitudeLogRelF0_sma3nz_stddevNorm', 'alphaRatioV_sma3nz_amean', 'spectralFluxUV_sma3nz_amean', 'F1bandwidth_sma3nz_stddevNorm', 'logRelF0-H1-A3_sma3nz_amean', 'slopeV0-500_sma3nz_amean', 'F2frequency_sma3nz_stddevNorm'| 'HNRdBACF_sma3nz_amean', 'F2amplitudeLogRelF0_sma3nz_stddevNorm', 'alphaRatioV_sma3nz_stddevNorm', 'spectralFlux_sma3_amean', 'alphaRatioV_sma3nz_amean' | 'alphaRatioV_sma3nz_stddevNorm', 'slopeV0-500_sma3nz_stddevNorm', 'logRelF0-H1-H2_sma3nz_stddevNorm', 'HNRdBACF_sma3nz_stddevNorm', 'logRelF0-H1-A3_sma3nz_stddevNorm' |
| **Phonetic** | 'articulation_rate', 'std_phoneme_duration' | 'articulation_rate', 'std_phoneme_duration' | 'articulation_rate', 'std_phoneme_duration' |
| **Vocal Technique** | *None (Excluded by definition)* | 'MFDR_vibrato', 'breathy_mean', 'pharyngeal_max', 'falsetto_max', 'glissando_max', 'tech_entropy' | 'pharyngeal_max', 'mix_max', 'vibrato_peak_factor', 'vibrato_max', 'falsetto_std', 'breathy_std' |

### Tools, Libraries & Feature Sets
- **OpenSMILE Toolkit:** Eyben et al. (2010) — *openSMILE -- The Munich Versatile and Fast Open-Source Audio Feature Extractor* [[Paper](https://doi.org/10.1145/1873951.1874246) | [Website](https://audeering.github.io/opensmile/)]
- **eGeMAPSv02 Feature Set:** Eyben et al. (2015) — *The Geneva Minimalistic Acoustic Parameter Set (GeMAPS) for Voice Research and Affective Computing* [[Paper](https://doi.org/10.1109/TAFFC.2015.2457417)]
- **CREPE (Pitch Tracking):** Kim et al. (2018) — *CREPE: A Monophonic Pitch Tracker Based on Deep Learning* [[Paper](https://arxiv.org/abs/1802.06182) | [GitHub](https://github.com/marl/crepe)]
- **Montreal Forced Aligner (MFA):** McAuliffe et al. (2017) [[Paper](https://www.phonetics.linguistics.northwestern.edu/mcauliffe/mfa_interspeech2017.pdf) | [GitHub](https://github.com/MontrealCorpusTools/Montreal-Forced-Aligner)]
- **CMU Pronouncing Dictionary:** Carnegie Mellon University Speech Group [[Resource](http://www.speech.cs.cmu.edu/cgi-bin/cmudict)]
- **SHAP (Explainability):** Lundberg & Lee (2017) — *A Unified Approach to Interpreting Model Predictions* [[Paper](https://arxiv.org/abs/1705.07874) | [GitHub](https://github.com/shap/shap)]
- **Core Processing Libraries:** TensorFlow, scikit-learn, Librosa
