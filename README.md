# AGISETI Demo using GNURadio

GNURadio is an open-source toolkit that provides signal processing blocks to implement software-defined radios (SDRs) and complex communication systems. Using graphical user interface (GUI) blocks, users can design and simulate systems without writing low-level code. In this project, we will use GNURadio to test parameters such as noise and sampling delay in the system and record and analyze its reactions to these changes.

## Interferometry
Interferometry involves combining the signals from multiple antennas (often spaced far apart) to simulate a telescope with a much larger aperture. This technique allows researchers to achieve much higher resolution than possible with a single antenna. In this project, we will observe and modify an existing GNURadio file that uses interferometry to analyze a signal.

## Transmitter Section
The unmodified circuit can be separated into two sections: the transmitter section and the receiver section. The transmitter section consists of a band-limited noise source and a signal source that are multiplied together and sent into the virtual sink. The signal source can be represented as cos(2πft), having an amplitude of 1 and no DC or phase offset. If we define the filtered noise signal as m(t), it becomes apparent that this transmitter uses amplitude modulation, with the full signal being m(t)⋅cos(2πft).

## Receiver Section
The receiver section consists of two antennas receiving the signal from the sink. The output of each antenna flows into a unique channel model block, designed to allow for noise profile differentiation (seed) and clock drift simulation (epsilon). The outputs of these channel models are sent into the frequency sink, along with their sum. This summation is where interferometry comes into play, as it determines whether the signals interfere constructively or destructively. Finally, before the output of channel model 2 (antenna 2’s signal) is sent into the adder and frequency sink, it goes through a delay block to simulate sampling delays in receiving signals due to the different positions of antennas. The initial receiver setup has no delay difference between antennas 1 and 2, with only a difference in their seed (noise profile).

## Original Data
Below is the frequency analysis of the original circuit. The base signal is a cosine wave at 50 kHz. When visualized in the frequency domain using the frequency sink, the signal appears centered around 50 kHz and occupies a narrow band ranging from approximately 0 to 98 kHz (0 to 0.098 MHz, or roughly the established bandwidth) at a magnitude between -43 dB and -40 dB. This spectral range includes both the fundamental frequency and the immediate frequency components influenced by noise and processing blocks. Furthermore, the interferometry (summed) signal is greater in magnitude than both individual signals, so interference is constructive.

![Original Specrum](https://github.com/ethanduhdurr/ECSE-351-GNURadio-Project/blob/main/Original%20Frequency%20Analysis.png)

## Modifying Delay
Before adding additional antennas, I modified the sampling delay difference to observe changes in the frequency analysis. When introducing a delay of 10 samples between the two antennas, the frequency spectrum of the interferometric signal displayed a characteristic M-shape. This pattern indicates alternating constructive and destructive interference, with peaks at the edges of the frequency band and a dip near the center frequency. As the delay increased (delay_diff=30), the M-shape took on more humps, varying between the different interferences under much smaller frequency intervals.
![10 Sample Delay](https://github.com/ethanduhdurr/ECSE-351-GNURadio-Project/blob/main/10%20Sample%20Delay%20Frequency%20Analysis.png)
![10 Sample Delay](https://github.com/ethanduhdurr/ECSE-351-GNURadio-Project/blob/main/30%20Sample%20Delay%20Frequency%20Analysis.png)

## Adding Antennas
Now that we've experimented with changing variables without altering the circuit, let's add more antennas. As a control, I first added a third antenna, with a unique seed of 3 (all other variables identical to antenna 1's model). The frequency analysis is shown below. 

![Third Antenna](https://github.com/ethanduhdurr/ECSE-351-GNURadio-Project/blob/main/Third%20Antenna%20Control%20Frequency%20Analysis.png)

Upon analyzing the frequency plot, the individual signal from the third antenna showed similar characteristics to the existing ones, occupying the same frequency band and exhibiting the same constructive interference pattern shape. The summed interferometry signal (now a combination of three signals) displayed a slightly higher magnitude overall (-35 dB to -32 dB), but no significant changes were observed in the shape or structure of the interference pattern. This suggests that the addition of a third antenna reinforces the combined signal’s strength without significantly altering its spectral behavior, likely due to all antennas receiving fundamentally similar signals with modest phase offsets.

## Taps Variation in Antenna 3
To simulate more realistic signal environments, such as reflections and scattering, I introduced tap variations in the channel models of antenna 3. Upon applying multiple taps to the third antenna’s channel model, I observed that increasing the number of taps led to a noticeable increase in the signal magnitude within the passband for both the third antenna and the resulting interferometry signal (shown below, with taps=2). Interestingly, this increase in magnitude was largely confined to the signal’s active frequency range — outside the passband, antenna 3’s output remained consistent with antennas 1 and 2, showing no significant gain or deviation. This suggests that the multipath components introduced by additional taps are effectively reinforcing the desired signal frequencies, likely through constructive interference at those specific frequencies, while having minimal influence on noise or frequencies outside the transmission band. As a result, tap variation proved to be a meaningful way to simulate environmental complexity and selectively enhance the signal within its intended spectral region.

![Two Taps](https://github.com/ethanduhdurr/ECSE-351-GNURadio-Project/blob/main/Antenna%203_2Taps.png)

The rest of the frequency spectra for taps variation can be accessed from the repository, but for the sake of reducing bloat, a graph is below with the significant data found from varying antenna 3's taps.

![Taps Variation Chart](https://github.com/ethanduhdurr/ECSE-351-GNURadio-Project/blob/main/Taps%20Variation%20Chart.png)

## Antenna 4 & Noise Voltage
Before testing noise voltage and its effect on the interferometric signal, I added a fourth antenna. When adding antenna 4, the control frequency spectra were nearly identical to the control with three antennas and the control with two antennas. (Its interference is constructive.) When altering noise voltage, my goal was to understand how uncorrelated noise influences the overall interference pattern and whether it disrupts the coherent signals from the other antennas. When antenna 4 was configured with an excessively high noise voltage, its spectrum became dominated by randomness and lost any distinguishable signal structure. However, its effect on the interferometry signal was surprisingly minimal. While it introduced a vertical offset in the dB scale (indicative of an increase in total received power), the characteristic interference shape remained intact (with no M-like or sinusoidal variations in the interferometric signal’s passband). This suggests that uncorrelated noise from a single antenna does not disrupt coherent interference patterns, and instead contributes primarily as a background power offset. It highlights the robustness of interferometry arrays to isolated signal degradation.

## Findings
Sampling delay introduced distinct interference patterns, with variations in delay producing a slightly sinusoidal frequency spectrum and shifting interference characteristics. Adding antennas increased the overall strength of the interferometric signal, but individual antenna signals remained largely consistent. Tap variations simulated environmental factors, enhancing the signal within the desired frequency band while leaving the overall signal structure intact. Finally, noise voltage added an offset to the interferometric signal, but its impact on the overall interference pattern was minimal, showcasing the system's resilience to isolated noise degradation. 

## Final Thoughts
This project demonstrated the use of GNURadio for simulating interferometry with multiple antennas to analyze the effects of sampling delay, noise, and antenna configurations on signal processing. By adjusting parameters like sampling delay, antenna count, and noise voltage, we explored how these factors influence the resulting frequency spectra and interferometric signals. Despite encountering some challenges in regard to adding these findings reinforce the robustness of interferometry in maintaining signal coherence across multiple antennas. The results suggest that while noise and antenna configurations can alter individual signals, they do not significantly disrupt the interferometric pattern.
## Sources Used

[Original Repository](https://github.com/Jtearwicker/AGISETI/tree/main/Week4_Radio_Astronomy_II/grc_files)
