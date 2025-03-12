# AAE6102-Assignment1

## Task 1 – Acquisition

The GPS signal acquisition process is structured into three distinct phases: initialization, initial acquisition, and precision refinement.

Phase 1: Initialization

This preparatory phase involves setting up the necessary parameters for signal processing. The number of samples corresponding to a complete C/A code period (samplesPerCode) is determined, taking into account the sampling rate, the C/A code's inherent frequency, and its length. Concurrently, the sampling interval (ts) is calculated. From the raw input signal, two short (1 millisecond) segments are extracted to facilitate the initial acquisition stage, while a longer segment, with the DC component removed (signal0DC), is prepared for the subsequent refinement stage. A pre-computed lookup table containing the C/A codes for all potential satellites (caCodesTable) is generated. The range for frequency searching is defined, typically in 500 Hz increments and bounded by settings.acqSearchBand, yielding a set of frequency bins (frqBins). Arrays to store intermediate and final acquisition results are also initialized.

Phase 2: Initial Acquisition (Coarse Search)

This phase aims to obtain preliminary estimates of the code phase and carrier frequency for each satellite's PRN code. The process iterates through each potential PRN. First, a frequency-domain representation of the C/A code is obtained by computing its Fast Fourier Transform (FFT) and then its complex conjugate (caCodeFreqDom). The algorithm then proceeds to test each frequency bin within the defined search range. For each bin, a local carrier signal (sigCarr) is synthesized. This carrier is then removed from the two short signal segments (extracted in Phase 1), yielding the in-phase (I) and quadrature (Q) components. These components are transformed to the frequency domain via FFT (IQfreqDom1, IQfreqDom2). Spectral domain cross-correlation is performed by multiplying these frequency-domain representations with the conjugate of the C/A code (caCodeFreqDom). The inverse FFT (IFFT) of the result yields the correlation power for each segment (acqRes1, acqRes2). The segment exhibiting the higher correlation power is selected. Within this segment, the peak magnitude and its corresponding code phase and frequency are identified. A peak-to-average ratio is computed; if this ratio exceeds a predefined threshold, the signal is deemed potentially acquired, and the parameters are passed to the precision refinement stage.

Phase 3: Precision Refinement (Fine Acquisition)

This final phase refines the frequency estimate obtained during initial acquisition. Using the coarse code phase estimate from the previous phase, a 10-millisecond C/A code sequence (longCaCode) is generated. Code modulation is removed from the DC-removed signal segment (signal0DC) by correlating it with this locally generated code sequence, isolating the carrier component (xCarrier). A high-resolution FFT, with a length equal to the next power of 2 (at the least, 8 times larger than the length of longCaCode), is applied to xCarrier. Precise frequency bins (fftFreqBins) are calculated based on this FFT. The carrier frequency (acqResults.carrFreq) is then refined by locating the frequency bin corresponding to the peak magnitude in the high-resolution spectrum. The coarse code phase obtained in the initial search (acqResults.codePhase) is retained as the definitive code phase estimate.

The result figures are shown as follows,
### Figure1
![image](https://github.com/shanzewang/AAE6102-Assignment1/blob/main/Task1-fig/acquisition%20result.png)
### Figure2
![image](https://github.com/shanzewang/AAE6102-Assignment1/blob/main/Task1-fig/skyplot.png)

Figure 1 is the result of acquisition results of opensky and urban dataset. Figure2 is the result of sky plot of opensky and urban dataset. More specifically, in the open sky dataset, satellite 16,22,26,27,31 can be acquired; while in the urban dataset, satellite 1,3,11,18 can be acquired. 



