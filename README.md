# AAE6102-Assignment1

## Task 1 – Acquisition

The GPS signal acquisition process is structured into three distinct phases: initialization, initial acquisition, and precision refinement.

**Phase 1: Initialization**

This preparatory phase involves setting up the necessary parameters for signal processing. The number of samples corresponding to a complete C/A code period (samplesPerCode) is determined, taking into account the sampling rate, the C/A code's inherent frequency, and its length. Concurrently, the sampling interval (ts) is calculated. From the raw input signal, two short (1 millisecond) segments are extracted to facilitate the initial acquisition stage, while a longer segment, with the DC component removed (signal0DC), is prepared for the subsequent refinement stage. A pre-computed lookup table containing the C/A codes for all potential satellites (caCodesTable) is generated. The range for frequency searching is defined, typically in 500 Hz increments and bounded by settings.acqSearchBand, yielding a set of frequency bins (frqBins). Arrays to store intermediate and final acquisition results are also initialized.

**Phase 2: Initial Acquisition (Coarse Search)**

This phase aims to obtain preliminary estimates of the code phase and carrier frequency for each satellite's PRN code. The process iterates through each potential PRN. First, a frequency-domain representation of the C/A code is obtained by computing its Fast Fourier Transform (FFT) and then its complex conjugate (caCodeFreqDom). The algorithm then proceeds to test each frequency bin within the defined search range. For each bin, a local carrier signal (sigCarr) is synthesized. This carrier is then removed from the two short signal segments (extracted in Phase 1), yielding the in-phase (I) and quadrature (Q) components. These components are transformed to the frequency domain via FFT (IQfreqDom1, IQfreqDom2). Spectral domain cross-correlation is performed by multiplying these frequency-domain representations with the conjugate of the C/A code (caCodeFreqDom). The inverse FFT (IFFT) of the result yields the correlation power for each segment (acqRes1, acqRes2). The segment exhibiting the higher correlation power is selected. Within this segment, the peak magnitude and its corresponding code phase and frequency are identified. A peak-to-average ratio is computed; if this ratio exceeds a predefined threshold, the signal is deemed potentially acquired, and the parameters are passed to the precision refinement stage.

**Phase 3: Precision Refinement (Fine Acquisition)**

This final phase refines the frequency estimate obtained during initial acquisition. Using the coarse code phase estimate from the previous phase, a 10-millisecond C/A code sequence (longCaCode) is generated. Code modulation is removed from the DC-removed signal segment (signal0DC) by correlating it with this locally generated code sequence, isolating the carrier component (xCarrier). A high-resolution FFT, with a length equal to the next power of 2 (at the least, 8 times larger than the length of longCaCode), is applied to xCarrier. Precise frequency bins (fftFreqBins) are calculated based on this FFT. The carrier frequency (acqResults.carrFreq) is then refined by locating the frequency bin corresponding to the peak magnitude in the high-resolution spectrum. The coarse code phase obtained in the initial search (acqResults.codePhase) is retained as the definitive code phase estimate.

The result figures are shown as follows,
### Figure1
![image](https://github.com/shanzewang/AAE6102-Assignment1/blob/main/Task1-fig/acquisition%20result.png)
### Figure2
![image](https://github.com/shanzewang/AAE6102-Assignment1/blob/main/Task1-fig/skyplot.png)

Figure 1 is the result of acquisition results of opensky and urban dataset. Figure2 is the result of sky plot of opensky and urban dataset. More specifically, in the open sky dataset, satellite 16,22,26,27,31 can be acquired; while in the urban dataset, satellite 1,3,11,18 can be acquired. 

## Task 2 – Tracking

### Analysis of Tracking Performance Based on C/N₀

![image](https://github.com/shanzewang/AAE6102-Assignment1/blob/main/Task2-fig/CNo%20performance.png)

Urban Environment (Top Row)
The urban environment shows data for satellites PRN1, PRN3, PRN11, and PRN18:

PRN1: C/N₀ values range approximately from 49-56 dB-Hz, with most measurements clustering around 52-53 dB-Hz. The signal shows considerable variation but maintains relatively strong values.
PRN3: C/N₀ values range approximately from 41-50 dB-Hz, with most readings around 45-47 dB-Hz. The signal shows similar variability patterns to PRN1 but at slightly lower strength.
PRN11: C/N₀ values range approximately from 25-45 dB-Hz, with a noticeable downward trend toward the end of the measurement period. This indicates significant signal degradation over time in the urban environment.
PRN18: C/N₀ values range approximately from 20-40 dB-Hz, with a visible dip in the middle section of the measurements. This satellite shows the lowest overall signal strength in the urban setting.

Open Sky Environment (Bottom Row)
The open sky environment displays data for satellites PRN16, PRN22, PRN26, PRN27, and PRN31:

PRN16: C/N₀ values stabilize around 35-40 dB-Hz after an initial higher reading, showing consistent but moderate signal strength.
PRN22: C/N₀ values range approximately from 30-35 dB-Hz, following an initial higher reading. The signal maintains a relatively consistent pattern.
PRN26: C/N₀ values remain stable around 35-38 dB-Hz, with some fluctuations but generally consistent performance.
PRN27: C/N₀ values hover around 35-38 dB-Hz, showing similar characteristics to PRN26.
PRN31: C/N₀ values range approximately from 34-38 dB-Hz, with frequent variations but maintaining a relatively consistent overall level.

Comparative Analysis

Urban satellites generally show higher maximum C/N₀ values but with greater variability compared to open sky satellites.
PRN1 and PRN3 demonstrate the strongest signal performance overall, with consistently higher C/N₀ values.
PRN11 shows concerning signal degradation over time, potentially indicating increasing obstruction or other environmental factors affecting reception.
Open sky satellites demonstrate more consistent signal strength, albeit at generally lower levels than the peak urban measurements.
The significant variations in the urban environment likely reflect signal multipath effects, signal blockage by buildings, and changing satellite geometries typical in urban canyons.
