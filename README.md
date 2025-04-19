# AGISETI Demo using GNURadio

GNURadio is an open-source toolkit that provides signal processing blocks to implement software-defined radios (SDRs) and complex communication systems. Using graphical user interface (GUI) blocks, users can design and simulate systems without writing low-level code.

## Interferometry
Interferometry involves combining the signals from multiple antennas (often spaced far apart) to simulate a telescope with a much larger aperture. This technique allows researchers to achieve much higher resolution than possible with a single antenna. In this project, we will be observing and editing an already established GNURadio file that uses interferometry to analyze a signal.

## Transmitter Section
The unmodificed circuit can be separated into two sections: the transmitter section and the receiver section. The transmitter section consists of a band-limited noise source and a signal source being multiplied together and sent into the virtual sink. The signal source can be represented as cos(2πft) as it has an amplitude of 1 and 0 DC or phase offset. If we define the filtered noise signal as m(t), it becomes apparent that this transmitter uses amplitude modulation, with the full signal being m(t)⋅cos(2πft). 

## Receiver Section
The receiver section consists of two antennas receiving the signal from the sink. The output of each antenna flows into a unique channel model block, meant to allow for noise profile differentiation(seed) and clock drift simulation(epsilon). The outputs of these model blocks are then sent into the input of a frequency sink, along with the blocks' sum. This summation is where interferometry comes into play, as we use it to determine whether the signals interfere with each other constructively or destructively. Finally, before the output of channel model 2 (antenna 2's signal) is sent into the adder and frequency sink, it goes through a delay block, meant to simulate sampling delays in receiving signals due to different positions of antennas. The initial receiver setup has no delay difference between antennas 1 and 2; the only difference is their seed (noise profile).

## Original Data
![Original Specrum](https://github.com/ethanduhdurr/ECSE-351-GNURadio-Project/blob/main/Original%20Frequency%20Analysis.png)
Above is the frequency analysis of the original circuit. The base signal is a cosine wave at 50 kHz. When visualized in the frequency domain using the frequency sink, the signal appears centered around 50 kHz and occupies a narrow band ranging from approximately 0 to 98 kHz (0 to 0.098 MHz, or roughly the established bandwidth) at a magnitude between -43dB and -40dB. This spectral range includes both the fundamental frequency and the immediate frequency components influenced by noise and processing blocks. Furthermore, the interferometry (summed) signal is greater in magnitude than both individual signals, so interference is constructive.

## Modifying Delay
Before adding additional antennas, I modified the sampling delay difference to observe changes in the frequency analysis. When introducing a delay of 10 samples between the two antennas, the frequency spectrum of the interferometric signal displayed a characteristic M-shape. This pattern indicates alternating constructive and destructive interference, with peaks at the edges of the frequency band and a dip near the center frequency. As the delay increased(delay_diff=30), the M-shape starts to take on more humps, varying betwen the different interferences under much smaller frequency intervals.
![10 Sample Delay](https://github.com/ethanduhdurr/ECSE-351-GNURadio-Project/blob/main/10%20Sample%20Delay%20Frequency%20Analysis.png)
![10 Sample Delay](https://github.com/ethanduhdurr/ECSE-351-GNURadio-Project/blob/main/30%20Sample%20Delay%20Frequency%20Analysis.png)

## Adding Antennas
Now that we've experimented with changing variables without altering the circuit, let's now add more antennas. As a control, I first added a third antenna, with a unique seed of 3 (every other variable is identical to antenna 1's model). The freqency analysis is below. 
![Third Antenna](https://github.com/ethanduhdurr/ECSE-351-GNURadio-Project/blob/main/Third%20Antenna%20Control%20Frequency%20Analysis.png)

## Final Thoughts

## Sources Used

[Original Repository](https://github.com/Jtearwicker/AGISETI/tree/main/Week4_Radio_Astronomy_II/grc_files)
