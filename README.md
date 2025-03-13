# AAE6102-Assignment1

## Task 1 – Acquisition

The GPS signal acquisition process is structured into three distinct phases: initialization, initial acquisition, and precision refinement.

**1.1 Initialization**

This preparatory phase involves setting up the necessary parameters for signal processing. The number of samples corresponding to a complete C/A code period (samplesPerCode) is determined, taking into account the sampling rate, the C/A code's inherent frequency, and its length. Concurrently, the sampling interval (ts) is calculated. From the raw input signal, two short (1 millisecond) segments are extracted to facilitate the initial acquisition stage, while a longer segment, with the DC component removed (signal0DC), is prepared for the subsequent refinement stage. A pre-computed lookup table containing the C/A codes for all potential satellites (caCodesTable) is generated. The range for frequency searching is defined, typically in 500 Hz increments and bounded by settings.acqSearchBand, yielding a set of frequency bins (frqBins). Arrays to store intermediate and final acquisition results are also initialized.

**1.2 Initial Acquisition (Coarse Search)**

This phase aims to obtain preliminary estimates of the code phase and carrier frequency for each satellite's PRN code. The process iterates through each potential PRN. First, a frequency-domain representation of the C/A code is obtained by computing its Fast Fourier Transform (FFT) and then its complex conjugate (caCodeFreqDom). The algorithm then proceeds to test each frequency bin within the defined search range. For each bin, a local carrier signal (sigCarr) is synthesized. This carrier is then removed from the two short signal segments (extracted in Phase 1), yielding the in-phase (I) and quadrature (Q) components. These components are transformed to the frequency domain via FFT (IQfreqDom1, IQfreqDom2). Spectral domain cross-correlation is performed by multiplying these frequency-domain representations with the conjugate of the C/A code (caCodeFreqDom). The inverse FFT (IFFT) of the result yields the correlation power for each segment (acqRes1, acqRes2). The segment exhibiting the higher correlation power is selected. Within this segment, the peak magnitude and its corresponding code phase and frequency are identified. A peak-to-average ratio is computed; if this ratio exceeds a predefined threshold, the signal is deemed potentially acquired, and the parameters are passed to the precision refinement stage.

**1.3 Precision Refinement (Fine Acquisition)**

This final phase refines the frequency estimate obtained during initial acquisition. Using the coarse code phase estimate from the previous phase, a 10-millisecond C/A code sequence (longCaCode) is generated. Code modulation is removed from the DC-removed signal segment (signal0DC) by correlating it with this locally generated code sequence, isolating the carrier component (xCarrier). A high-resolution FFT, with a length equal to the next power of 2 (at the least, 8 times larger than the length of longCaCode), is applied to xCarrier. Precise frequency bins (fftFreqBins) are calculated based on this FFT. The carrier frequency (acqResults.carrFreq) is then refined by locating the frequency bin corresponding to the peak magnitude in the high-resolution spectrum. The coarse code phase obtained in the initial search (acqResults.codePhase) is retained as the definitive code phase estimate.

The result figures are shown as follows,
### Figure1
![image](https://github.com/shanzewang/AAE6102-Assignment1/blob/main/Task1-fig/acquisition%20result.png)
### Figure2
![image](https://github.com/shanzewang/AAE6102-Assignment1/blob/main/Task1-fig/skyplot.png)

Figure 1 is the result of acquisition results of opensky and urban dataset. Figure2 is the result of sky plot of opensky and urban dataset. More specifically, in the open sky dataset, satellite 16,22,26,27,31 can be acquired; while in the urban dataset, satellite 1,3,11,18 can be acquired. 

## Task 2 – Tracking

### 2.1 Analysis of Tracking Performance Based on C/N₀

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

### 2.2 Tracking result for opensky dataset

![image](https://github.com/shanzewang/AAE6102-Assignment1/blob/main/Task2-fig/T2-Opensky.png)

The upper figure shows the tracking results for satellites PRN16, PRN22, PRN26, PRN27, and PRN31 in an open sky environment, with six different visualization types per satellite.
The discrete-time scatter plots (first column) display constellation diagrams with clear I/Q signal patterns, indicating good signal quality across all satellites. Each satellite shows consistent constellation patterns with well-defined quadrature components.
Both raw and filtered DLL (Delay-Locked Loop) discriminators (second and third columns) demonstrate effective code tracking. The filtered outputs show appropriate amplitude scaling and noise reduction compared to raw signals.
Similarly, the PLL (Phase-Locked Loop) discriminators (fourth and fifth columns) indicate stable phase tracking for all satellites. The filtered PLL outputs exhibit considerable noise reduction and amplitude normalization.
The correlation results (sixth column) display three correlation branches (Early, Prompt, Late) that maintain consistent separation and amplitude across the observation period. The sustained high correlation values confirm robust signal acquisition and tracking performance.

![image](https://github.com/shanzewang/AAE6102-Assignment1/blob/main/Task2-fig/ACF%20opensky.png)
The ACF forms a triangular-shaped correlation peak centered at zero code delay, with maximum correlation value of approximately 3400. The correlation strength decreases symmetrically on both sides of the peak, declining to around 1200 at the edges of the measured range (±0.5 code delay units).
The smooth, symmetric shape of the correlation function indicates proper signal tracking performance with optimal alignment occurring at zero delay. This triangular ACF shape is characteristic of GNSS signal processing using typical spreading codes, showing the expected correlation properties between the received signal and the locally generated replica.
The high correlation values and well-defined peak suggest strong signal quality and effective signal acquisition.

### 2.3 Tracking result for urban dataset

![image](https://github.com/shanzewang/AAE6102-Assignment1/blob/main/Task2-fig/T2-Urban.png)

Comparing the urban tracking results with the previously analyzed open sky data reveals significant differences in signal quality and tracking stability:
Urban vs. Open Sky Differences:
The discrete-time scatter plots for urban satellites show more compact and tighter constellations compared to the open sky environments, indicating potentially stronger but more constrained signal reception.
Urban DLL (both raw and filtered) signals display higher amplitude variations and more irregular patterns than their open sky counterparts, suggesting increased multipath effects in the urban environment.
The most notable difference appears in the filtered PLL outputs, where urban satellites (especially PRN1 and PRN18) show distinct downward trends, indicating phase tracking challenges likely caused by signal obstruction or reflection from buildings.
Correlation results for urban satellites exhibit greater variability between the three correlation branches and higher overall amplitude ranges (note the scale differences) compared to the more consistent open sky results.
Key Insights:
Urban environments create more challenging signal conditions with higher variability in all tracking metrics compared to the more stable open sky performance.
PRN11 and PRN18 show particularly degraded performance in the urban setting with significant PLL tracking instability.

![image](https://github.com/shanzewang/AAE6102-Assignment1/blob/main/Task2-fig/ACF%20urban.png)

The urban multi-correlator ACF shows a significantly higher overall correlation magnitude (peaking near 11700 versus 3400 in open sky) and notably different shape. While the open sky ACF exhibited a clean triangular pattern, this urban ACF features asymmetry and irregularities, particularly visible in the right side of the curve around 0.2-0.3 code delay where a distinct plateau appears.

This distortion in the correlation function suggests the presence of multipath signals in the urban environment. The plateau indicates secondary correlation peaks caused by signal reflections from buildings, which is a common challenge in urban GNSS reception. These reflections create overlapping correlation peaks that disrupt the ideal triangular shape seen in open sky conditions.


## Task 3 – Navigation data decoding

### 3.1 Processing steps

**Initial Data Acquisition Phase**

The methodology begins by collecting 1500 navigation signal samples from trackResults.I_P, encompassing five complete subframes (300 bits each, with 20 samples representing each bit). The starting point is determined by subFrameStart, with an additional 20-sample buffer for alignment purposes. This extensive sampling window (30 seconds) ensures comprehensive navigation message capture.

**Signal Processing and Enhancement**

The collected samples undergo matrix transformation into a 20-row configuration, where each row corresponds to a single bit representation. Column summation is then performed to generate composite bit values. This integration approach significantly improves signal quality by minimizing random noise interference, thereby enhancing the reliability of subsequent decoding operations.

**Signal Digitization Protocol**

A threshold-based algorithm converts the aggregated values into a binary representation. Positive sums are classified as "1" while non-positive values are designated as "0". This conversion step transforms analog-like measurements into the discrete binary format required for navigation message interpretation.
Format Standardization
The resulting binary array undergoes transformation via dec2bin functionality to create a standardized string array comprising exclusively "0" and "1" characters. This standardization ensures compatibility with the subsequent ephemeris extraction functions.

**Navigation Parameter Extraction**

The final phase employs specialized algorithms to analyze the 1500-bit sequence (five subframes) and extract critical navigation parameters including ephemeris elements and Time of Week from Subframe 1. The process uses navBitsBin(2:1501) as primary data with navBitsBin(1) serving as a validation reference, ultimately generating a comprehensive ephemeris structure and precise timing information essential for accurate positioning calculations.

### 3.2 Results analysis

**Amplitude Differences:**
The most striking difference is the amplitude scale. Urban satellites (PRN1, PRN3, PRN11, PRN18) display significantly lower amplitude ranges (approximately ±2 to ±4) compared to open sky satellites (PRN16, PRN22, PRN26, PRN27, PRN31) which consistently show amplitudes of approximately ±5000.

**Signal Consistency:**
Urban environment signals show gradual degradation in amplitude and increased noise, particularly evident in PRN11 and PRN18, where the bit transitions appear less distinct toward the end of the measurement period.

**Bit Pattern Clarity:**
Open sky satellites demonstrate remarkably consistent navigation message bit patterns with clear transitions between bits. The transitions are sharp and well-defined across the entire 40-second interval for all satellites.

**Signal Quality:**
PRN1 in the urban environment shows the strongest and most consistent bit transitions among urban satellites, while PRN11 and PRN18 show the weakest signal quality with less defined bit edges.

![image](https://github.com/shanzewang/AAE6102-Assignment1/blob/main/Task3-fig/bitsall.png)

# Comparative Ephemeris Data: Urban vs. Open-Sky Satellites

| Ephemeris Parameter | Meaning | **Urban Environment** ||||  **Open-Sky Environment** ||||
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| | | PRN-1 | PRN-3 | PRN-11 | PRN-18 | PRN-16 | PRN-22 | PRN-26 | PRN-31 |
| C_ic | Cosine-harmonic-correction-to-inclination (rad) | -7.4506e-08 | 1.1176e-08 | -3.1665e-07 | 2.5332e-07 | -1.0058e-07 | -1.0058e-07 | -2.0489e-08 | -1.1362e-07 |
| omega_0 | Right ascension at reference time (rad) | -3.1060 | -2.0642 | 2.7258 | 3.1218 | -1.6743 | 1.2727 | -1.8129 | -2.7873 |
| C_is | Sine-harmonic-correction-to-inclination (rad) | 1.6019e-07 | 5.2154e-08 | -1.3225e-07 | 3.5390e-08 | 1.3597e-07 | -9.3132e-08 | 8.9407e-08 | -5.0291e-08 |
| i_0 | Inclination at reference time (rad) | 0.9761 | 0.9629 | 0.9008 | 0.9546 | 0.9716 | 0.9365 | 0.9399 | 0.9559 |
| C_rc | Cosine-harmonic-correction-to-orbit-radius (m) | 287.4688 | 160.3125 | 324.4063 | 280.1563 | 237.6875 | 266.3438 | 234.1875 | 240.1563 |
| omega | Argument of perigee (rad) | 0.7115 | 0.5950 | 1.8915 | 1.3930 | 0.6796 | -0.8879 | 0.2957 | 0.3116 |
| omegaDot | Rate of right ascension (rad/s) | -8.1696e-09 | -7.8325e-09 | -9.3043e-09 | -8.6107e-09 | -8.0128e-09 | -8.6686e-09 | -8.3114e-09 | -7.9950e-09 |
| IODE_sf3 | Issue of Data, Ephemeris (Subframe 3) | 72 | 72 | 83 | 56 | 9 | 22 | 113 | 83 |
| IDot | Rate of inclination (rad/s) | -1.8108e-10 | 4.8109e-10 | -1.2858e-11 | -1.6179e-10 | -4.8931e-10 | -3.0358e-11 | -4.1752e-10 | -3.2144e-11 |
| WeekNumber | GPS-week number | 1032 | 1032 | 1032 | 1032 | 1155 | 1155 | 1155 | 1155 |
| T_GD | Satellite clock correction (s) | 5.5879e-09 | 1.8626e-09 | -1.2573e-08 | 5.5879e-09 | -1.0245e-08 | -1.7695e-08 | 6.9849e-09 | -1.3039e-08 |
| IODC | Issue of Data, Clock | 12 | 4 | 229 | 244 | 234 | 218 | 15 | 228 |
| t_oc | Clock reference time (s) | 453600 | 453600 | 453600 | 453600 | 396000 | 396000 | 396000 | 396000 |
| a_f1 | Clock drift linear term (s/s) | -9.4360e-12 | -1.1369e-12 | 8.5265e-12 | 3.1832e-12 | -6.3665e-12 | 9.2086e-12 | 3.9790e-12 | -1.9327e-12 |
| a_f0 | Clock bias (s) | -1.2087 | 1.8633e-04 | -5.9009e-04 | 9.8655e-05 | -4.0693e-04 | -4.8947e-04 | 1.4479e-04 | -1.4490e-04 |
| IODE_sf2 | Issue of Data, Ephemeris (Subframe 2) | 72 | 72 | 83 | 56 | 9 | 22 | 113 | 83 |
| C_rs | Sine-harmonic-correction-to-orbit-radius (m) | -120.7188 | -62.0938 | -67.1250 | -113.8750 | 23.3438 | -99.8125 | 21.2500 | 30.7188 |
| delta_n | Mean motion correction (rad/s) | N/A | N/A | N/A | N/A | 4.2466e-09 | 5.2831e-09 | 5.0513e-09 | 4.8073e-09 |
| M_0 | Mean anomaly at reference time (rad) | 0.5179 | -0.4304 | -0.1989 | 0.2598 | 0.7181 | -1.2610 | 1.7356 | 2.8245 |
| C_uc | Cosine-harmonic-correction-to-latitude (rad) | 8.6149e-06 | 3.9066e-06 | 3.6042e-06 | 6.1095e-06 | 1.3895e-06 | -5.1558e-06 | 1.1530e-06 | 1.4603e-06 |
| e | Eccentricity | 0.0089 | 0.0022 | 0.0166 | 0.0154 | 0.0123 | 0.0067 | 0.0063 | 0.0103 |
| C_us | Sine-harmonic-correction-to-latitude (rad) | 5.3031e-06 | 3.9066e-06 | 1.5123e-06 | 5.1148e-06 | 7.6871e-06 | 5.1651e-06 | 7.0408e-06 | 7.2289e-06 |
| sqrtA | Square-root-of-semi-major-axis (m^(1/2)) | 5153.7e+03 | 5153.8e+03 | 5153.7e+03 | 5153.7e+03 | 5.1538e+03 | 5.1537e+03 | 5.1536e+03 | 5.1536e+03 |
| t_oe | Ephemeris reference time (s) | 453600 | 453600 | 453600 | 453600 | 396000 | 396000 | 396000 | 396000 |

**Note:** The delta_n parameter (Mean motion correction) is not available in the urban environment dataset.


## Task 4 – Position and Velocity Estimation

### 4.1 Processing steps

**Theoretical Framework**

The Weighted Least Squares (WLS) approach represents a sophisticated mathematical technique for determining receiver coordinates and velocity vectors in Global Navigation Satellite Systems (GNSS). This methodology optimizes the solution by minimizing weighted residuals between observed and predicted measurements, thereby accounting for variable observation quality across different satellite signals.

**Position Determination Algorithm**

In the positioning component, pseudorange observations (ρ_obs) follow the mathematical model:

ρ_obs = ||r_sat - r_rec|| + c·dt + ε

Where:
- r_sat represents satellite position coordinates
- r_rec represents receiver position coordinates
- dt denotes receiver clock bias
- c is the speed of light constant
- ε encompasses measurement noise and other error sources

The state vector x = [x, y, z, dt]^T undergoes iterative refinement through the solution of:

x = (A^T·W·A)^(-1)·A^T·W·b

Implementation specifics include:
- Satellite elevation-dependent weighting using a sinusoidal model: weight(i) = sin²(el(i))
- Diagonal weight matrix construction: W = diag(weight)
- Geometric transformation: C = W'·W
- Solution computation: x = (A'·C·A)\\(A'·C·omc)

Where omc represents the observed-minus-computed measurements vector.

**Velocity Estimation Process**

The velocity determination employs a parallel WLS framework with Doppler measurements. The procedure involves:

1. Wavelength calculation using the carrier frequency: λ = c/1575.42×10^6
2. Range rate conversion: rate = -λ·doppler
3. Adjustment for satellite motion: rate = rate'
4. Residual vector construction:
   ```
   For i = 1 to numberOfSatellites
       b(i) = rate(i) - satVelocity(i,:)·(A(i,1:3))'
   End
   ```
5. Velocity solution computation: v = (A'·C·A)\\(A'·C·b')

This dual-component WLS implementation enables comprehensive receiver state determination, providing both positional coordinates and velocity vectors with appropriate weighting to maximize estimation accuracy under variable signal conditions.

### 4.2 Results Analysis

**Open Sky Environment**

The open sky positioning results show dramatically improved stability and accuracy:
The coordinate variation plot displays much smaller fluctuations, with all components (East, North, and Up) varying within approximately ±20m range. The variations exhibit a more balanced pattern across all three components, indicating consistent satellite geometry and signal quality.
The 2D and 3D position plots show a remarkably tighter clustering of measurement points around the mean position (Lat: 22°19'42.3835", Lng: 114°10'16.9629", Hgt: +5.7m). The measurement scatter is confined to approximately ±10m in horizontal components and ±30m in the vertical component, representing a precision improvement of roughly 20 times compared to the urban environment.

![image](https://github.com/shanzewang/AAE6102-Assignment1/blob/main/Task4-fig/openskyall.png)

**Urban Environment**

The urban environment positioning results show significant coordinate variations and positional scatter:
The coordinate variation plot displays substantial fluctuations, particularly in the East (E) component which varies by approximately ±250m. The Up (U) component shows variations of around ±100m, while the North (N) component exhibits more moderate variations of approximately ±50m. These large fluctuations indicate challenging signal conditions typical of urban canyons.
The 2D and 3D position plots reveal considerable scatter in the measurement points around the mean position (Lat: 22°19'10.4142", Lng: 114°12'27.3914", Hgt: -33.9m). The measurements spread across a range of approximately ±200m in both East and North directions, with even greater variation in the Up component (±400m). This wide dispersion demonstrates the degraded positioning performance in urban environments.
![image](https://github.com/shanzewang/AAE6102-Assignment1/blob/main/Task4-fig/urbanall.png)

**Key Differences**
The comparison between these environments illustrates how urban structures dramatically impact GNSS positioning performance. The urban scenario suffers from multipath effects, signal blockage, and reduced satellite visibility, resulting in positioning errors that are an order of magnitude larger than in open sky conditions. The open sky environment, with its unobstructed satellite view, produces significantly more precise and stable position estimates with a much smaller error distribution.


## Task 5 – Kalman Filter-Based Positioning

To enhance positioning accuracy, an Extended Kalman Filter (EKF) is developed using pseudorange and Doppler measurements. The EKF framework enables dynamic filtering and smoothing of the position and velocity estimates, leading to improved robustness against measurement noise and signal disturbances. The EKF implementation provides refined positioning results and demonstrates its advantages over the WLS approach in challenging environments.

**EKF results in Open Sky and Urban dataset:**

![image](https://github.com/shanzewang/AAE6102-Assignment1/blob/main/Task5-fig/all.png)
