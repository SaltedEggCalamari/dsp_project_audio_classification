# CEG3003 Project: Environmental Sound Classification using Machine Learning

## Methodology
Upon inspecting the spectrograms of the original training datasets and given the wide range of audio classication groups, it is apparent that no specific frequency range to adjust for. As such the signal processing will attempt to pick out only significant decibels on any frequencies, and reject low decibel frequencies as noise.

One case example that exemplifies this judgement is by comparing the spectrogram of the thunderstorm audio and birds chirping. Looking at these two spectrograms, the main features of the thunderstorm lie in the lower frequency values while the bird chirpring audio has a majority of its features in the higher frequency values. By filtering for specific frequency ranges, one audio will highlight its main features while the other will lose it.

## Preprocessing
The preprocessing is done in 4 steps.

- Step 1: Trimming low amplitude values. This step removes values considered insignificant based on amplitude and achieves the removal of leading and trailing silences along with any small amplitude sounds in the main audio

- Step 2: Pre-emphasis filter. This filter is applied to emphasise the higher amplitudes, making it more apparent for further processing down the pipeline

- Step 3: RMS normalization. After the emphasising from the previous step, the entire audio is normalized to create a uniform amplitude level

- Step 4: Fixed-length padding/truncation. This step processes the audio length to a uniformed length as the prior steps may have trimmed off the duration of the audio file

For these 4 steps in preprocessing, there are 4 configurable parameters: *top_db, pre_emphasis_coeff, target_rms, and fixed_duration*.

### top_db
This parameter configures the threshold for whether an audio value should be trimmed. This value is measure in dB.

- A higher value result in more aggressive trimming which is more resistant to softer noise features but could cause softer key features to be lost.

- A lower value allow for softer key features to be retained but may permit noise to persist.

### pre_emphasis_coeff
This parameter configures the amplification of frequencies. This value is adjusted between 0 and 1.

- A higher value amplifies higher frequencies and reduces lower frequencies.

- A lower value amplifies lower frequencies and reduces higher frequencies.

### target_rms
The parameter configures the target "loudness" of the audio. The typical range is between 0.1 and 2.

- A higher value boosts the loudness of the audio, an amplification that benefits feature extraction. However, boosting the audio too much could result in clipping if the peak value exceeds the valid range.

- A lower value quietens the audio and could negatively affect the feature extraction.

### fixed_duration
This parameter configures the duration of the audio. This value is measure in seconds.

- A higher value increases the likelihood that key features do not get truncated which occurs during a reduction (e.g. 3s -> 2s) but increases the processing and computation time.

- A lower value increases the speed by reducing computation time but increases the possibility that key features become truncated.

## Feature Extraction
Features are extracted by the four groups as annotated in the source code: MFCC + Deltas,  Log-Mel Spectrogram, Spectral Features, and Chroma. Each feature is passed through the compute_stats() function which extracts the statistics for each feature. 

There are four configurable parameters: *n_mfcc, n_mels, n_fft, hop_length*

### n_mfcc
This parameter configures the number of Mel-Frequency Cepstral Coefficients are computed per frame.

- A higher value means more coefficients per frame which translates to a higher-dimensional feature matrix. This can potentially capture finer spectral details but risks overfitting

- A lower value provides faster processing but may miss subtle details 

### n_mels
This parameter configures the number of Mel-Spaced frequency bands are used to create the Mel spectrogram.

- A higher value creates a higher resolution Mel spectrogram which captures more details but potentially captures more noise

- A lower value lowers the resolution of the Mel spectrogram which improves its resistance to noise at the cost of losing the finer details

### n_fft
This parameter configures the number of samples used for each Short-Time Fourier Transform frame.

- A higher value captures longer frequency details better (such as a vacuum cleaner where a high frequency feature is present throughout the audio)

- A lower value captures shorter frequecy details better (Such as a dog barking where the frequency features is present for short bursts)

### hop_length
This paramater configures the overlap between adjacent frames, used in conjuction with n_fft.

- A higher value results in a smaller overlap which translates to fewer total frames for a given audio.

- A lower value results in larger overlaps which could result in a overly correlated adjacent frames

## Model Selection
There were 4 models tested; LogisticRegression, SVC, HistGradientBoostingClassfier, and MLPClassifier. Each model was tested with the different versions of preprocessing and feature extraction to see which model could achieve the highest accuracy.

After 5 rounds of testing, LogisticRegression was chosen to proceed for further fine-tuning as it had achieved the overall highest accuracy across all rounds.

## Version history

### Preprocessor

#### Version 1
- top_db: 20

- pre_emphasis_coeff: 0.97

- target_rms: 0.1

- fixed_duration: 0.5

#### Version 2
- top_db: 20 --> 10

- pre_emphasis_coeff: 0.97 --> 0.50

- target_rms: 0.1 (No change)

- fixed_duration: 0.5 --> 2.5

#### Version 3
- top_db: 10 --> 20

- pre_emphasis_coeff: 0.50 (No change)

- target_rms: 0.1 (No change)

- fixed_duration: 0.5 --> 4.0

### Feature Extraction

#### Version 1
- n_mfcc: 20

- n_mels: 128

- n_fft: 2048

- hop_length: 512

#### Version 2
- n_mfcc: 20 --> 30

- n_mels: 128 --> 184

- n_fft: 2048 (No change)

- hop_length: 512 (No change)

### Machine Learning Model

#### Version 1
- max_iter: 2000

- class_weight: "balanced"
