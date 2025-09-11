# PySDR-NOTES
## Introduction
Software-Defined Radio (SDR):
As a concept it refers to using software to perform signal processing tasks that were traditionally performed by hardware, specific to radio/RF applications. This software can be run on a general-purpose computer (CPU), FPGA, or even GPU, and it can be used for real-time applications or offline processing of recorded signals. Analogous terms include “software radio” and “RF digital signal processing”.

As a thing (e.g., “an SDR”) it typically refers to a device that you can plug an antenna into and receive RF signals, with the digitized RF samples being sent to a computer for processing or recording (e.g., over USB, Ethernet, PCI). Many SDRs also have transmit capabilities, allowing the computer to send samples to the SDR which then transmits the signal at a specified RF frequency. Some embedded-style SDRs include an onboard computer.

### Digital Signal Processing (DSP):
The digital processing of signals; in our case, RF signals.It is designed for someone who is:

1. Interested in using SDRs to do cool stuff
2. Good with Python
3. Relatively new to DSP, wireless communications, and SDR
4. A visual learner, preferring animations over equations
5. Better at understanding equations after learning the concepts
6. Looking for concise explanations, not a 1,000 page textbook
# 2. Frequency Domain
One of the coolest side effects of learning about DSP and wireless communications is that you will also learn to think in the frequency domain. Most people’s experience with working in the frequency domain is limited to adjusting the bass/mid/treble knobs on a car’s audio system. Most people’s experience with viewing something in the frequency domain is limited to seeing an audio equalizer, such as this clip:
#### frequency domain really means, how to convert between time and frequency (plus what happens when we do so)
![](https://pysdr.org/_images/time_and_freq_domain_example_signals.png)
### Fourier Series
The basics of the frequency domain start with understanding that any signal can be represented by sine waves summed together. When we break a signal down into its composite sine waves, we call it a Fourier series
![](https://pysdr.org/_images/summing_sinusoids.svg)
To understand how we can break down a signal into sine waves, or sinusoids, we need to first review the three attributes of a sine wave:

1. Amplitude
2. Frequency
3. Phase
#### Amplitude indicates the “strength” of the wave, while frequency is the number of waves per second. Phase is used to represent how the sine wave is shifted in time, anywhere from 0 to 360 degrees
![](https://pysdr.org/_images/amplitude_phase_period.svg)
### Time-Frequency Pairs
We have established that signals can be represented as sine waves, which have several attributes. Now, let’s learn to plot signals in the frequency domain. While the time domain demonstrates how a signal changes over time, the frequency domain displays how much of a signal rests in which frequencies. Instead of the x-axis being time it will be frequency. 
![](https://pysdr.org/_images/sine-wave.png)
The time domain should look very familiar. It’s an oscillating functionThe.
### Fourier Transform
Mathematically, the “transform” we use to go from the time domain to the frequency domain and back is called the Fourier Transform. 
<img width="325" height="79" alt="image" src="https://github.com/user-attachments/assets/685f526c-89b5-4c2e-9c0d-678e0bb33e56" />
<img width="213" height="78" alt="image" src="https://github.com/user-attachments/assets/d6b9f022-443e-4004-a76b-4dd28ad196c6" />
<img width="292" height="114" alt="image" src="https://github.com/user-attachments/assets/e0c688a9-1dec-4e9d-a68c-22e2222a6384" />
<img width="2137" height="1297" alt="image" src="https://github.com/user-attachments/assets/67363cb3-cf79-4cae-a50b-de1f10400445" />
<img width="2065" height="1332" alt="image" src="https://github.com/user-attachments/assets/55fd9df4-bcb5-41a4-a76d-13035603630a" />




### Fast Fourier Transform (FFT)
 The Fast Fourier Transform (FFT) is simply an algorithm to compute the discrete Fourier Transform. It was developed decades ago, and even though there are variations on the implementation, it’s still the reigning leader for computing a discrete Fourier transform. 
 ![](https://pysdr.org/_images/fft-block-diagram.svg)
 The size of the output is always the same as the size of the input. If I feed 1,024 samples into the FFT, I will get 1,024 out. The confusing part is that the output will always be in the frequency domain, and thus the “span” of the x-axis if we were to plot it doesn’t change based on the number of samples in the time domain input. Let’s visualize that by looking at the input and output arrays, along with the units of their indices:
![](https://pysdr.org/_images/fft-io.svg)
Negative Frequencies
What in the world is a negative frequency? For now, just know that they have to do with using complex numbers (imaginary numbers)–there isn’t really such thing as a “negative frequency” when it comes to transmitting/receiving RF signals, it’s just a representation we use.
![](https://pysdr.org/_images/negative-frequencies2.svg)
From a mathematical perspective, negative frequencies can be seen by looking at the complex exponential function, 
. If we have a negative frequency, we can see that it will be a complex sinusoid that rotates in the opposite direction
![](https://pysdr.org/_images/negative_freq_animation.gif)
### Order in Time Doesn’t Matter
Recall that an FFT is performed on many samples at once, i.e., you can’t observe the frequency domain of a single instance in time (one sample); it needs a span of time to operate on (many samples). The FFT function essentially “mixes around” the input signal to form the output, which has a different scale and units. We are no longer in the time domain after all. A good way to internalize this difference between domains is realizing that changing the order things happen in the time domain doesn’t change the frequency components in the signal.
### FFT in Python
Now that we have learned about what an FFT is and how the output is represented, let’s actually look at some Python code and use Numpy’s FFT function, np.fft.fft(). It is recommended that you use a full Python console/IDE on your computer, but in a pinch you can use the online web-based Python console linked at the bottom of the navigation bar on the left.
 <img width="2133" height="1330" alt="image" src="https://github.com/user-attachments/assets/93822545-e513-4987-bbc9-421cb6f1caf9" />
![](https://pysdr.org/_images/fft-python4.svg)
![](S = np.fft.fftshift(np.fft.fft(s)))
<img width="2118" height="1288" alt="image" src="https://github.com/user-attachments/assets/c8c8ed6e-0fd6-4d9c-9ea4-a2d40a666677" />
First we need to create a signal in the time domain. Feel free to follow along with your own Python console. To keep things simple, we will make a simple sine wave at 0.15 Hz. We will also use a sample rate of 1 Hz, meaning in time we sample at 0, 1, 2, 3 seconds, etc.
### FFT Sizing
The last thing to note is FFT sizing. The best FFT size is always an order of 2 because of the way the FFT is implemented. You can use a size that is not an order of 2, but it will be slower. Common sizes are between 128 and 4,096, although you can certainly go larger. In practice we may have to process signals that are millions or billions of samples long, so we need to break up the signal and do many FFTs. That means we will get many outputs. 
Spectrogram/Waterfall
A spectrogram is the plot that shows frequency over time. It is simply a bunch of FFTs stacked together (vertically, if you want frequency on the horizontal axis). We can also show it in real-time, often referred to as a waterfall. A spectrum analyzer is the piece of equipment that shows this spectrogram/waterfall. The diagram below shows how an array of IQ samples can be sliced up to form a spectrogram:
![](https://pysdr.org/_images/spectrogram_diagram.svg)
Spectrogram/Waterfall
Because a spectrogram involves plotting 2D data, it’s effectively a 3D plot, so we have to use a colormap to represent the FFT magnitudes, which are the “values” we want to plot. Here is an example of a spectrogram, with frequency on the horizontal/x-axis and time on the vertical/y-axis. Blue represents the lowest energy and red is the highest. 
<img width="726" height="495" alt="image" src="https://github.com/user-attachments/assets/75560ffd-3ea8-4c2a-bd28-e0fc5108178f" />
<img width="2098" height="1330" alt="image" src="https://github.com/user-attachments/assets/c81def7d-ab39-4eed-be55-95958f71ee1e" />
### FFT Implementation
Even though NumPy has already implemented the FFT for us, it’s nice to know the basics of how it works under the hood. The most popular FFT algorithm is the Cooley-Tukey FFT algorithm, first invented around 1805 by Carl Friedrich Gauss and then later rediscovered and popularized by James Cooley and John Tukey in 1965.

The basic version of this algorithm works on power-of-two size FFTs, and is intended for complex inputs but can also work on real inputs. The building block of this algorithm is known as the butterfly, which is essentially a N = 2 size FFT, consisting of two multiplies and two summations:

<img width="767" height="345" alt="image" src="https://github.com/user-attachments/assets/f90327d6-0345-4b94-841d-343ebc85a276" />

<img width="1131" height="1084" alt="image" src="https://github.com/user-attachments/assets/e541db4e-559e-455b-9050-d3d09601e498" />

<img width="1023" height="628" alt="image" src="https://github.com/user-attachments/assets/b9ee24b7-dafa-49b9-8a49-597b22876419" />

# 3. IQ Sampling
## Sampling Basics
Before jumping into IQ sampling, let’s discuss what sampling actually means. You may have encountered sampling without realizing it by recording audio with a microphone. The microphone is a transducer that converts sound waves into an electric signal (a voltage level). That electric signal is transformed by an analog-to-digital converter (ADC), producing a digital representation of the sound wave. To simplify, the microphone captures sound waves that are converted into electricity, and that electricity in turn is converted into numbers. The ADC acts as the bridge between the analog and digital domains. SDRs are surprisingly similar. Instead of a microphone, however, they utilize an antenna, although they also use ADCs. In both cases, the voltage level is sampled with an ADC. For SDRs, think radio waves in then numbers out.
<img width="829" height="356" alt="image" src="https://github.com/user-attachments/assets/ad01ab86-8276-4a21-a453-b5c450393562" />



 

