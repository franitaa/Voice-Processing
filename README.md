# Voice Processing: Phoneme Analysis and Noise Reduction

This project focuses on the comprehensive processing of audio signals, particularly aimed at conditioning and characterizing phonemes for further analysis.

## Overview

The primary goals of this project are:

- To estimate and reduce background noise from audio signals.
- To isolate phonemes from speech recordings.
- To analyze the spectral characteristics of each phoneme.
- To enhance signal quality using various filtering techniques.

All code used for these tasks is included in this repository.

## Noise Estimation

Each audio file begins with one second of silence (i.e., no phonemes), which is used as a reference for background noise estimation. The `make_noise` function leverages this segment to extrapolate the background noise throughout the entire recording.

### Wiener-Hopf Filter

The Wiener-Hopf adaptive filter is used to condition the signal in situations where the desired sound overlaps with background noise. Being adaptive, it dynamically adjusts based on selected features of the input signal.

The `wh` function uses the original signal and the output from `make_noise` to generate an estimated noise signal. This estimate is used to filter the original audio. Filter parameters were selected based on graphical analysis.

Although the algorithm reasonably estimates background noise, it does not completely eliminate it in all cases.

## Phoneme Segmentation

The `recorte_while` function is used to segment phonemes from the signal. The segmentation process follows these steps:

1. Determine the maximum noise level from the silent portion.
2. Define a threshold as three times the maximum noise.
3. Traverse the audio signal using a `while` loop.
4. If a sample exceeds the threshold and the mean of the next 8000 samples exceeds twice the maximum noise:
   - Enter a second `while` loop to capture the phoneme.
   - Continue capturing samples while either the absolute value exceeds three times the noise maximum, or the mean of the next 4000 samples exceeds the noise maximum.
5. Each phoneme is stored as a separate array and returned as a list of phoneme segments.

## Spectral Analysis

The spectral density of each phoneme is analyzed using the Autoregressive (AR) method. This is a parametric approach that models the power spectral density based on certain assumptions and estimates the parameters from the data samples. It offers more precision compared to the Welch method.

## Band-Pass FIR Filtering

A windowed FIR band-pass filter is used to isolate the frequency range of interest, particularly focusing on female voice frequencies. The `BP` function:

- Computes filter coefficients using a Hamming window.
- Applies the filter to the signal.
- Selects the filter order and cutoff frequencies based on visual analysis and relevant literature.

FIR filters are chosen over IIR for their stability, despite requiring a higher order.

## Cross-Correlation Analysis

Cross-correlation is used to assess the similarity between phonemes and full words. The correlation is highest when the signals are aligned at zero lag, explaining why the aligned signals sound nearly identical. It's expected that a word will have a higher correlation with its constituent phonemes than with unrelated phonemes, though spectral similarities between different phonemes may still produce some degree of correlation.

## Alignment for Averaging

To correctly average the spectral densities of multiple phonemes, they must be aligned in frequency space. A dedicated function is used to perform this alignment before averaging.

## Conclusions

- The Wiener-Hopf adaptive filter, combined with a band-pass FIR filter, effectively conditions audio signals and reduces background noise.
- The AR method provides a more accurate power spectral density estimation than the Welch method.
- Cross-correlation supports the hypothesis that phonemes within the same word are more closely related, although some similarity exists across different phonemes due to spectral shape.
- Proper alignment is crucial for meaningful phoneme averaging.
