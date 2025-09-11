# PySDR-NOTES
## Introduction
Software-Defined Radio (SDR):
Software Defined Radio (SDR) is commonly defined as a “Radio in which some or all of the physical layer functions are software defined”. SDR technology uses software, instead of conventional hardware, to perform radio-signal processing functions.

### Digital Signal Processing (DSP):
(DSP) is the field that uses computers and digital techniques to modify, enhance, and analyze signals, which are typically real-world analog signals converted into a numerical sequence. This process involves converting an analog signal to digital using an analog-to-digital converter, performing mathematical operations on the resulting digital data, and then, if needed, converting it back to an analog signal with a digital-to-analog converter. DSP enables accurate and flexible signal processing, finding applications in areas like audio, image, video, wireless communication, and radar systems.  
### How Digital Signal Processing Works
1. Analog-to-Digital Conversion:
Real-world signals, like sound waves or images, are first converted from their continuous analog form into a discrete sequence of numbers. 
2. Digital Signal Processing:
Specialized processors, like digital signal processors (DSPs) or general-purpose computers, use advanced mathematical algorithms to manipulate and extract information from these digital signals. 
3. Digital-to-Analog Conversion:
For signals that need to be output in the real world, the processed digital data is converted back into an analog signal using a digital-to-analog converter. 

# 2. Frequency Domain
frequncy domain formation characterizes a signal by decomposing it into its constituent frequencies, showing the amplitude and phase of each frequency component, rather than by how its value changes over time. This provides a different perspective on a signal's structure, revealing patterns, identifying noise (often high frequencies), and simplifying complex systems for analysis using tools like the Fourier Transform.  
#### frequency domain really means, how to convert between time and frequency (plus what happens when we do so)
![](https://pysdr.org/_images/time_and_freq_domain_example_signals.png)
### Fourier Series
Fourier series represents a periodic function as an infinite sum of sine and cosine waves (sinusoids). This mathematical technique, developed by Joseph Fourier, allows complex periodic signals to be broken down into simpler components, making them easier to analyze and understand. Fourier series are fundamental in signal processing, physics, and engineering for tasks like circuit analysis and wave pattern modeling.  
![](https://pysdr.org/_images/summing_sinusoids.svg)
To understand how we can break down a signal into sine waves, or sinusoids, we need to first review the three attributes of a sine wave:

1. Amplitude
2. Frequency
3. Phase
#### Amplitude indicates the “strength” of the wave, while frequency is the number of waves per second. Phase is used to represent how the sine wave is shifted in time, anywhere from 0 to 360 degrees
![](https://pysdr.org/_images/amplitude_phase_period.svg)
### Time-Frequency Pairs
time-frequency pair represents a signal in a two-dimensional (time, frequency) space, providing a comprehensive view of its characteristics at any given moment. Unlike one-dimensional time or frequency domain analysis, a time-frequency pair reveals how a signal's frequency content changes over time, a concept crucial for analyzing non-stationary signals like those found in telecommunications, medical diagnosis, and radar. 
![](https://pysdr.org/_images/sine-wave.png)
The time domain should look very familiar. It’s an oscillating functionThe.
### Fourier Transform
Fourier transform is a mathematical tool that converts a signal from the time domain to the frequency domain, revealing its constituent frequencies. This decomposition allows for analysis of signals by breaking them into a series of sines and cosines, making it easier to understand their spectral content. Common applications include signal processing, image enhancement, data compression, and modern communication technologies like 5G and Wi-Fi.  
<img width="325" height="79" alt="image" src="https://github.com/user-attachments/assets/685f526c-89b5-4c2e-9c0d-678e0bb33e56" />
<img width="213" height="78" alt="image" src="https://github.com/user-attachments/assets/d6b9f022-443e-4004-a76b-4dd28ad196c6" />
<img width="292" height="114" alt="image" src="https://github.com/user-attachments/assets/e0c688a9-1dec-4e9d-a68c-22e2222a6384" />
<img width="2137" height="1297" alt="image" src="https://github.com/user-attachments/assets/67363cb3-cf79-4cae-a50b-de1f10400445" />
<img width="2065" height="1332" alt="image" src="https://github.com/user-attachments/assets/55fd9df4-bcb5-41a4-a76d-13035603630a" />




### Fast Fourier Transform (FFT)
 The Fast Fourier Transform (FFT) is an efficient algorithm for computing the Discrete Fourier Transform (DFT), which converts a time-domain signal into its frequency-domain representation. It works by decomposing a signal into its constituent frequencies and their amplitudes. This process allows for detailed analysis of a signal's frequency content, making it a fundamental tool in digital signal processing, with applications ranging from audio processing and image compression to radar and telecommunications.
 ![](https://pysdr.org/_images/fft-block-diagram.svg)
 The size of the output is always the same as the size of the input. If I feed 1,024 samples into the FFT, I will get 1,024 out. The confusing part is that the output will always be in the frequency domain, and thus the “span” of the x-axis if we were to plot it doesn’t change based on the number of samples in the time domain input. Let’s visualize that by looking at the input and output arrays, along with the units of their indices:
![](https://pysdr.org/_images/fft-io.svg)
### Negative Frequencies
Negative frequency is a mathematical concept, not a physically measurable quantity, representing the direction of rotation in a complex signal. In signal processing, it's used in the Fourier Transform to model real-valued signals as a combination of two counter-rotating complex signals, simplifying analysis by allowing the frequency spectrum of a real signal to be represented with only positive frequencies and an energy-doubled symmetric counterpart. For example, a clockwise rotation can be a positive frequency, while a counter-clockwise rotation is a negative frequency. 
![](https://pysdr.org/_images/negative-frequencies2.svg)
From a mathematical perspective, negative frequencies can be seen by looking at the complex exponential function, 
. If we have a negative frequency, we can see that it will be a complex sinusoid that rotates in the opposite direction
![](https://pysdr.org/_images/negative_freq_animation.gif)

### FFT in Python
The Fast Fourier Transform (FFT) in Python is primarily implemented using the numpy.fft module and the scipy.fft module. These modules provide efficient functions for performing the Discrete Fourier Transform (DFT) and its inverse.
Key Concepts:
Fast Fourier Transform (FFT):
An efficient algorithm to compute the Discrete Fourier Transform (DFT). It transforms a signal from the time domain (or spatial domain) to the frequency domain, revealing the constituent frequencies within the signal.
Frequency Domain Analysis:
FFT allows for the analysis of the frequency components present in a signal. This is useful for tasks such as signal denoising, spectral analysis, and identifying periodic patterns.
 <img width="2133" height="1330" alt="image" src="https://github.com/user-attachments/assets/93822545-e513-4987-bbc9-421cb6f1caf9" />
![](https://pysdr.org/_images/fft-python4.svg)
![](S = np.fft.fftshift(np.fft.fft(s)))
<img width="2118" height="1288" alt="image" src="https://github.com/user-attachments/assets/c8c8ed6e-0fd6-4d9c-9ea4-a2d40a666677" />
First we need to create a signal in the time domain. Feel free to follow along with your own Python console. To keep things simple, we will make a simple sine wave at 0.15 Hz. We will also use a sample rate of 1 Hz, meaning in time we sample at 0, 1, 2, 3 seconds, etc.
### FFT Sizing
The last thing to note is FFT sizing.FFT (Fast Fourier Transform) size, which is the number of samples in a transform window, determines the trade-off between time and frequency resolution. Larger FFT sizes provide higher frequency resolution (better for static tones) and require more computation, while smaller FFT sizes offer higher temporal resolution (better for transients) but with less precise frequency information. The optimal FFT size depends on the specific signal and analysis goals, but it is commonly set to a power of two (e.g., 1024, 2048, 4096) for computational efficiency. 
![](https://pysdr.org/_images/spectrogram_diagram.svg)
Spectrogram/Waterfall
Because a spectrogram involves plotting 2D data, it’s effectively a 3D plot, so we have to use a colormap to represent the FFT magnitudes, which are the “values” we want to plot. Here is an example of a spectrogram, with frequency on the horizontal/x-axis and time on the vertical/y-axis. Blue represents the lowest energy and red is the highest. 
<img width="726" height="495" alt="image" src="https://github.com/user-attachments/assets/75560ffd-3ea8-4c2a-bd28-e0fc5108178f" />
<img width="2098" height="1330" alt="image" src="https://github.com/user-attachments/assets/c81def7d-ab39-4eed-be55-95958f71ee1e" />
### FFT Implementation
What is an FFT Implementation?
An FFT implementation refers to a practical application of the Fast Fourier Transform (FFT) algorithm in software or hardware. It's designed to take a discrete signal from the time domain and efficiently compute its frequency-domain representation, revealing its underlying spectral components (frequencies, amplitudes, and phases). 
Implementations involve algorithms like Cooley-Tukey, radix-2, or radix-4, often requiring input data to be a power of two, and sometimes using techniques like zero-padding and windowing to improve resolution and reduce interference
The basic version of this algorithm works on power-of-two size FFTs, and is intended for complex inputs but can also work on real inputs. The building block of this algorithm is known as the butterfly, which is essentially a N = 2 size FFT, consisting of two multiplies and two summations:

<img width="767" height="345" alt="image" src="https://github.com/user-attachments/assets/f90327d6-0345-4b94-841d-343ebc85a276" />

<img width="1131" height="1084" alt="image" src="https://github.com/user-attachments/assets/e541db4e-559e-455b-9050-d3d09601e498" />

<img width="1023" height="628" alt="image" src="https://github.com/user-attachments/assets/b9ee24b7-dafa-49b9-8a49-597b22876419" />

# 3. IQ Sampling
## Sampling Basics
iq sampling is the form of sampling that an SDR performs, as well as many digital receivers (and transmitters). IQ sampling, an in-phase and quadrature sampling technique, to measure the phase of incoming signals. In IQ sampling, the receiver mixes the incoming signal with the local oscillator (LO) at 0 and 90 degree offsets.
<img width="829" height="356" alt="image" src="https://github.com/user-attachments/assets/ad01ab86-8276-4a21-a453-b5c450393562" />



 

