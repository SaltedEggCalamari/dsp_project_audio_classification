# dsp_project_audio_classification

## Methodology
Upon inspecting the spectrograms of the original training datasets and given the wide range of audio classication groups, it is apparent that no specific frequency range to adjust for. As such the signal processing will attempt to pick out only significant decibels on any frequencies, and reject low decibel frequencies as noise.

One case example that exemplifies this judgement is by comparing the spectrogram of the thunderstorm audio and birds chirping. Looking at these two spectrograms, the main features of the thunderstorm lie in the lower frequency values while the bird chirpring audio has a majority of its features in the higher frequency values. By filtering for specific frequency ranges, one audio will highlight its main features while the other will lose it.

## Preprocessing
The preprocessing is done in 4 steps.

Step 1: Trimming low amplitude values. This step removes values considered insignificant based on amplitude and achieves the removal of leading and trailing silences along with any small amplitude sounds in the main audio
Step 2: Pre-emphasis filter. This filter is applied to emphasise the higher amplitudes, making it more apparent for further processing down the pipeline
Step 3: RMS normalization. After the emphasising from the previous step, the entire audio is normalized to create a uniform amplitude level
Step 4: Fixed-length padding/truncation. This step processes the audio length to a uniformed length as the prior steps may have trimmed off the duration of the audio file

For these 4 steps in preprocessing, there are 4 configurable parameters: top_db, pre_emphasis_coeff, target_rms, and fixed_duration.

### top_db
This parameter configures the threshold for whether an audio value should be trimmed. This value is measure in dB.

Higher values result in more aggressive trimming which is more resistant to softer noise features but could cause softer key features to be lost
Lower values allow for softer key features to be retained but may permit noise to persist

###pre_emphasis_coeff
This parameter configures the amplification of frequencies. This value is adjusted between 0 and 1.

A higher value amplifies higher frequencies and reduces lower frequencies.
A lower value amplifies lower frequencies and reduces higher frequencies.

###target_rms
The parameter configures the target "loudness" of the audio. The typical range is between 0.1 and 2.

A higher value boosts the loudness of the audio, an amplification that benefits feature extraction. However, boosting the audio too much could result in clipping if the peak value exceeds the valid range.
A lower value quietens the audio and could negatively affect the feature extraction.

###fixed_duration
This parameter configures the duration of the audio. This value is measure in seconds.

A higher value increases the likelihood that key features do not get truncated which occurs during a reduction (e.g. 3s -> 2s) but increases the processing and computation time
A lower value increases the speed by reducing computation time but increases the possibility that key features become truncated.

## Feature Extraction
