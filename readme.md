# OTFS Anti-Interference Receiver: Frequency Domain Detection and Suppression Techniques in-depth Explanation with MATLAB Code

OTFS Anti-Interference Receiver: Frequency Domain Detection and Suppression Techniques
However, the OTFS waveform lacks inherent anti-interference characteristics: after single-tone continuous wave interference and narrow-band partial band blocking interference intrude into the receiving link, they spread pollution throughout the entire delay - Doppler grid through ISFFT/SFFT two-dimensional transformation, directly elevating the system bit error floor. Traditional time domain adaptive notch filtering and linear filtering anti-interference schemes have shortcomings such as complex parameter tuning, poor variable bandwidth interference adaptation, and excessive computational complexity.
In 2025, the Li team from the Electronics journal proposed a modular frequency domain front-end interference architecture that does not require modifications to the existing OTFS modulation and demodulation core. It relies on short-term frequency domain processing to achieve interference blind detection and adaptive suppression, becoming the preferred solution for low-cost implementation of OTFS interference resistance. This paper will analyze in four dimensions: signal model, algorithm principles, simulation performance, and engineering complexity.
II. Mathematical Modeling of OTFS Baseband and Interference
1  
Transmitter: Doppler - Delay-domain information symbol in the time-frequency domain.
Sen
Fort
Change
Change
Moment
Form
Vein
Pulse
Time-domain emission baseband signal;
2  
Receiver: Time-domain receiver
G
Nar
Change
Change
Time-frequency domain restoration of Doppler - Delayed domain symbols.
After superposition of interference and Gaussian white noise, the input time domain signal of receiver is: for RF interference, it is time-domain sampling; for complex Gaussian noise. Interference can penetrate the entire OTFS two-dimensional transformation chain, ultimately distorting Doppler - Doppler domain demodulation symbols, thus interference elimination must be completed at the OTFS demodulation front end.
2.2 In the standardization modeling project of two types of typical interference, OTFS is most frequently encountered with continuous wave interference and partial band blocking interference. The interference intensity is quantified by the dry signal-to-interference ratio (I/S).
(1) Monosonic interference continuous wave: Monosonic interference is the most common source of radar adjacent frequency leakage and malicious active suppression. The continuous time domain expression is: Normalized frequency points after discrete sampling; if it's an integer multiple, the interference energy concentrates on a single FFT frequency point. If it's not an integer multiple, frequency deviation will cause spectrum leakage due to window truncation, sidelobe pollution affecting multiple neighboring frequencies. Simulations uniformly select non-rational normalized frequency points to simulate real measurement scenarios.
(2) Simulates broadband blocking interference with partial blockage and narrowband communication blocking interference by adjacent stations, in the frequency domain: band-limited complex Gaussian noise.
Its
He
Interference Duty Cycle (Total Bandwidth of OTFS): Full Bandwidth Blocking, Equated to Narrowband Interference; Implementation Method: Gaussian White Noise Managerial Bandpass Filter Generation of Time-Domain Interference Waveform.
3.1 Time-domain overlapping window + segmented FFT full frame direct FFT drawbacks: large frame length causes overly dense frequency resolution, and point-to-point interference energy dispersion; frame-to-frame time-domain jumps induce spectral leakage, and sidelobes of interference spread. The use of Hamming window + 50% overlap segmented short-time Fourier transform processing:  
Set the single frame window length (commonly 512/1024 points), Hamming window weighting suppresses truncation leakage;  
50% temporal overlap between adjacent subframes, a single original sampling point is reweighted by both frames;  
Subframes are independent in sequence, resulting in segmented spectrum.  
The overlapping window not only suppresses low-frequency spectral leakage and improves the interference's frequency domain characteristics but also lays the foundation for subsequent perfect time-domain reconstruction.  
3.2 Frequency domain neighborhood smoothing preprocessing useful signal + noise power spectrum changes locally within continuous frequency bands, interference manifests as sharp peaks/continuous elevations in the frequency domain. By exploiting this characteristic, frequency domain smoothing and noise reduction are performed to lower random fluctuations in noise and reduce detection false alarms.  
Set the single frame amplitude spectrum, smooth half-window (selected for the paper based on smoothing coefficients), and after smoothing, statistics:  
Smoothed noise spikes are smoothed by the neighborhood mean, while the interference frequency points still have significantly higher amplitude than the surrounding background, optimizing the stability of subsequent CFAR detection thresholds.  
3.3 Segmented initialization improvement of FCME CFAR detection FCME belongs to a CFAR blind detection algorithm with no prior knowledge of interference power, overcoming the defects of traditional CME thresholds that are raised by strong interference across the entire band and increase the miss rate dramatically. It further proposes a segmented basis initialization improvement of FCME for broadband partial-band interference.  
Classic CME drawbacks: calculating the overall mean threshold across all frequencies, strong interference raises the overall power, and after threshold elevation, interference frequency points cannot pass the threshold, leading to extensive missed detections.
Standard FCME principle: Take the frequency points with the minimum 10% power spectrum as the initial clean sample set, iteratively expand the clean frequency points below the threshold, and after convergence, use the mean of the clean set to generate the detection threshold. The shortcoming is that in broadband interference, the initial minimum 10% samples are easily mixed into the interference edge frequency points.
Improved segmentation FCME steps
1  
The entire spectrum is divided into sub-bands.
2  
Calculate the average power of each frequency band, select the sub-segment with the smallest mean as the initial pure sample set.
3  
Generate the initial thresholds from the mean and standard deviation of the initial set.
4  
Adopting the standard FCME iterative convergence, final threshold.
Relying on the characteristic of interference frequency domain distributed in a block, as long as the proportion of interference bandwidth is less than, there will always be complete and uninterrupted sub-bands. This approach effectively avoids initial sample contamination at the source, significantly enhancing the probability of detecting interference in broadband parts.
3.4 Dual-mode Frequency Domain Interference Reduction: After setting zeros VS amplitude clipping mark interference frequency points, two suppression schemes are provided to adapt to different signal-to-noise ratio scenarios.
Solution 1: Directly set zero for the frequency points.
The interference components are eliminated to the greatest extent, but the synchronization loss of the useful signal energy carried by this frequency point leads to signal spectrum indentation distortion.
Scheme 2: Phase-Amplitude Clipping
For full-band average amplitude, fixed scaling factor; retain original frequency point phases, only clamp the interference magnitude to the background average level, maximize retention of embedded useful signals, high JSR suppression scenario performance is superior to zeroing, low interference intensity can easily leave minor interference noise.
Optimal balance between complexity and suppression performance is achieved through the combination of frequency domain smoothing and zero placement.
3.5 When weighted overlapping is used for time domain reconstruction, suppressing the post-frequency domain segmentation, use a previous 50% overlap Hamming window for weighted combination to restore the complete OTFS time domain sampling:
The denominator is the sum of all window functions covering the sampling point, strictly meeting perfect reconstruction without interference. After local zeroing of the interfered frequency points, weighted average smooths the frame boundary distortions, and clean time-domain signals are fed into the standard OTFS demodulation link.
IV. Detector Simulation Performance
4.1 Single-tone Interference (JSR: 0~20dB) CME detects the probability of interference at low JSR <6dB, where strong interference raises the mean value across all frequencies causing a false threshold and missed detection; JSR >10dB causes intermodulation distortion in the spectrum, leading to an increasing false alarm probability. At JSR=6dB, the improved FCME slightly outperforms the standard FCME; the false alarm probability stabilizes around 0.05 over the full JSR range, significantly surpassing traditional CME algorithms in detection stability.
4.2 Under partial-band interference with continuous blocks, the initial sampling of the standard FCME mixed in with interference edges at JSR=10dB, resulting in only a detection probability of 1; segmented initialization of the improved FCME effectively avoids interference blocks, breaking through a detection probability of 0.9 at JSR=8dB, strictly controlled false alarms, making it the optimal detection algorithm for broadband blocking interference.
V. System Bit Error Rate (BER) Simulation Results
SNR scanning range from 0~16dB, divided into single-tone (JSR=10dB) and narrow-band partial-band (JSR=10dB) interference scenarios, comparing four schemes: no anti-interference, pure zeroing, pure clipping, and smooth + zeroing.
5.1 In the single-tone interference scenario without suppression, system bit error rate plateaus, and there is no optimization in SNR; zeroing/clipping drops to at SNR=12dB; frequency domain smoothing beforehand brings about approximately 3dB gain in SNR, significantly improving demodulation performance.
5.2 In the partial-band interference scenario, spectral leakage spreads by 10% bandwidth, and the residual interference remains even with simple zeroing; adding frequency domain smoothing precisely masks the interference, bringing the BER curve close to the ideal uninterfered AWGN baseline; clipping slightly outperforms zeroing due to the embedded signal retained within the frequency points.
Engineering Selection Conclusion: The smooth + frequency point zeroing approach has the best overall robustness across all scenarios, serving as the receiver's standard configuration.
6. Analysis of Algorithm Engineering Complexity
The total computational load is concentrated in segmented FFT/IFFT and frequency domain statistical computations, with a 50% overlap between frames, significantly outperforming the complexity of time-domain adaptive filtering. The improved FCME segmented traversal only requires one full spectrum scan, with threshold iterations converging to 2-3 times, resulting in minimal additional overhead. The entire anti-interference frontend can be seamlessly integrated with USRP and self-developed FPGA receivers without modifying OTFS waveform parameters or baseband core, fully meeting the real-time engineering implementation needs.
7. Summary and Future Research Directions
7.1 Key Conclusive Findings: The frequency domain preprocessing anti-jamming architecture relies on repetitive windowing, frequency domain smoothing and noise reduction, segmented improvement of FCME blind detection, dual mode interference suppression, and weighted time domain reconstruction across the entire link, achieving single tone/partial band jamming blind suppression, possessing three core advantages:
Passive blind processing: Without prior information on interference bandwidth, frequency points, or power; CFAR adapts dynamically to varying interference intensity;
Low computational overhead: Based on FFT linear operations, meets SDR real-time engineering implementation requirements;
Modular plug-and-play: Frontend preprocessing architecture compatible with any parameter configuration OTFS transceivers.
7.2 Future Expansion Directions: Expand to multipath Doppler time-varying channels, coupling delay-Doppler domain two-dimensional joint interference suppression;
Introducing lightweight CNN/Transformer for complex sweeping and frequency modulation smart interference detection;
USRP hardware real-world testing validation, completing air interface real-world data iterative optimization algorithm parameter adjustments.
References: Li et al., "Jamming Detection and Suppression Technique for OTFS Systems in an AWGN Channel," Electronics, 2025, 14(7):1286.

## Images

![OTFS Anti-Interference Receiver: Frequency Domain Detection and Suppression Technology in Detail [with MATLAB Code]](01.jpg)
![OTFS Anti-Interference Receiver: Frequency Domain Detection and Suppression Technology in Detail [with MATLAB Code]](02.jpg)

<!-- PAYMENT_BLOCK -->

## Get this dataset

- Payment link (Stripe): [Buy now](https://buy.stripe.com/3cs8yP7sY87d0vu9AB)
- Payment QR code: ![Payment QR code](pay.png)

<!-- /PAYMENT_BLOCK -->
