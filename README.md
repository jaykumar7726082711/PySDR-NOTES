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

# 4. Digital Modulation
The main goal of modulation is to squeeze as much data into the least amount of spectrum possible. Technically speaking we want to maximize “spectral efficiency” in units bits/sec/Hz. Transmitting 1’s and 0’s faster will increase the bandwidth of our signal (recall Fourier properties), which means more spectrum is used. We will also examine other techniques besides transmitting faster. There will be many trade-offs when deciding how to modulate, but there will also be room for creativity.
# 5. PlutoSDR in Python
The PlutoSDR (a.k.a. ADALM-PLUTO) is a low-cost SDR (just over $200) that is capable of transmitting and receiving signals from 70 MHz to 6 GHz. It is a great SDR for those who have outgrown the $20 RTL-SDR. The Pluto uses a USB 2.0 interface, which limits the sample rate to around 5 MHz if you want to receive 100% of samples over time. That being said, it can sample up to 61 MHz and you can capture contiguous bursts up to around 10M samples long at a time, allowing the Pluto to capture an enormous amount of spectrum at once. It is technically a 2x2 devices, but the second transmit and receive channels are only accessible through U.FL connectors inside the case, and they share the same oscillators so you can’t receive on two different frequencies at once. Below shows the block diagram of the Pluto, as well as the AD936x which is the RF integrated circuit (RFIC) inside the Pluto.
# 6. USRP in Python
we learn how to use the UHD Python API to control and receive/transmit signals with a USRP which is a series of SDRs made by Ettus Research (now part of NI). We will discuss transmitting and receiving on the USRP in Python, and dive into USRP-specific topics including stream arguments, subdevices, channels, 10 MHz and PPS synchronization.
While the Python code provided in this textbook should work under Windows, Mac, and Linux, we will only be providing driver/API install instructions specific to Ubuntu 22 (although the instructions below should work on most Debian-based distributions). We will start by creating an Ubuntu 22 VirtualBox VM; feel free to skip the VM portion if you already have your OS ready to go. Alternatively, if you’re on Windows 11, Windows Subsystem for Linux (WSL) using Ubuntu 22 tends to run fairly well and supports graphics out-of-the-box.
# 7. BladeRF in Python
The bladeRF 2.0 (a.k.a. bladeRF 2.0 micro) from the company Nuand is a USB 3.0-based SDR with two receive channels, two transmit channels, a tunable range of 47 MHz to 6 GHz, and the ability to sample up to 61 MHz or as high as 122 MHz when hacked. It uses the AD9361 RF integrated circuit (RFIC) just like the USRP B210 and PlutoSDR, so RF performance will be similar. The bladeRF 2.0 was released in 2021, maintains a small form factor at 2.5” x 4.5”, and comes in two different FPGA sizes (xA4 and xA9). While this chapter focuses on the bladeRF 2.0, a lot of the code will also apply to the original bladeRF which came out in 2013.
# 8. RTL-SDR in Python
The RTL-SDR is by far the cheapest SDR, at around $30, and a great SDR to start with. While it is receive-only and can only tune up to ~1.75 GHz, there are numerous applications it can be used for. In this chapter, we learn how to set up the RTL-SDR software and use its Python API.

# 9. HackRF One in Python
The HackRF One from Great Scott Gadgets is a USB 2.0 SDR that can transmit or receive from 1 MHz to 6 GHz and has a sample rate from 2 to 20 MHz. It was released in 2014 and has had several minor refinements over the years. It is one of the only low-cost transmit-capable SDRs that goes down to 1 MHz, making it great for HF applications (e.g., ham radio) in addition to higher frequency fun. The max transmit power of 15 dBm is also higher than most other SDRs, see this page for full transmit power specs. It uses half-duplex operation, meaning it is either in transmit or receive mode at any given time, and it uses 8-bit ADC/DAC.
# 10. Noise and dB
In this chapter we will discuss noise, including how it is modeled and handled in a wireless communications system. Concepts include AWGN, complex noise, and SNR/SINR. We will also introduce decibels (dB) along the way, as it is widely within wireless communications and SDR.
The Gaussian distribution has two parameters: mean and variance. We already discussed how the mean can be considered zero because you can always remove the mean, or bias, if it’s not zero. The variance changes how “strong” the noise is. A higher variance will result in larger numbers. It is for this reason that variance defines the noise power.

Variance equals standard deviation squared (
).

Decibels (dB)
We are going to take a quick tangent to formally introduce dB. You may have heard of dB, and if you are already familiar with it feel free to skip this section.

Working in dB is extremely useful when we need to deal with small numbers and big numbers at the same time, or just a bunch of really big numbers. Consider how cumbersome it would be to work with numbers of the scale in Example 1 and Example 2.

Example 1: Signal 1 is received at 2 watts and the noise floor is at 0.0000002 watts.

Example 2: A garbage disposal is 100,000 times louder than a quiet rural area, and a chain saw is 10,000 times louder than a garbage disposal (in terms of power of sound waves).

Without dB, meaning working in normal “linear” terms, we need to use a lot of 0’s to represent the values in Examples 1 and 2. Frankly, if we were to plot something like Signal 1 over time, we wouldn’t even see the noise floor. If the scale of the y-axis went from 0 to 3 watts, for example, the noise would be too small to show up in the plot. To represent these scales simultaneously, we work in a log-scale.

To further illustrate the problems of scale we encounter in signal processing, consider the below waterfalls of three of the same signals. The left-hand side is the original signal in linear scale, and the right-hand side shows the signals converted to a logarithmic scale (dB). Both representations use the exact same colormap, where blue is lowest value and yellow is highest. You can barely see the signal on the left in the linear scale.
# 11. Filters
Filters are used in many disciplines. For example, image processing makes heavy use of 2D filters, where the input and output are images. You might use a filter every morning to make your coffee, which filters out solids from liquid. In DSP, filters are primarily used for:

1. Separation of signals that have been combined (e.g., extracting the signal you want)
2. Removal of excess noise after receiving a signal
3. Restoration of signals that have been distorted in some way (e.g., an audio equalizer is a filter)

# 12. Link Budgets
A link budget is an accounting of all of the gains and losses from the transmitter to the receiver in a communication system. Link budgets describe one direction of the wireless link. Most communications systems are bidirectional, so there must be a separate uplink and downlink budget. The “result” of the link budget will tell you roughly how much signal-to-noise ratio (abbreviated as SNR, which this textbook uses, or S/N) you should expect to have at your receiver. Further analysis would be needed to check if that SNR is high enough for your application.
# 13. Channel Coding
As we learned in the Noise and dB chapter, wireless channels are noisy, and our digital symbols won’t reach the receiver perfectly. If you have taken a networking course, you may already know about cyclic redundancy checks (CRCs), which detect errors at the receiving end. The purpose of channel coding is to detect and correct errors at the receiver. If we allow some room for error, then we can transmit at a higher order modulation scheme, for example, without having a broken link. As a visual example, consider the following constellations showing QPSK (left) and 16QAM (right) under the same amount of noise. QPSK provides 2 bits per symbol, while 16QAM is twice the data rate at 4 bits per symbol. But note how in the QPSK constellation that the symbols tend to not pass the symbol decision boundary, or the x-axis and y-axis, which means the symbols will be received correctly. Meanwhile in the 16QAM plot, there is overlap in the clusters, and, as a result, there will be many incorrectly received symbols.
# 14. IQ Files and SigMF
In all our previous Python examples we stored signals as 1D NumPy arrays of type “complex float”. In this chapter we learn how signals can be stored to a file and then read back into Python, as well as introduce the SigMF standard. Storing signal data in a file is extremely useful; you may want to record a signal to a file in order to manually analyze it offline, or share it with a colleague, or build a whole dataset.
# 15. Multipath Fading
we introduce multipath, a propagation phenomenon that results in signals reaching the receiver by two or more paths, which we experience in real-world wireless systems. So far we have only discussed the “AWGN Channel”, i.e., a model for a wireless channel where the signal is simply added to noise, which really only applies to signals over a cable and some satellite communications systems.

Multipath
All realistic wireless channels include many “reflectors”, given that RF signals bounce. Any object between or near the transmitter (Tx) or receiver (Rx) can cause additional paths the signal travels along. Each path experiences a different phase shift (delay) and attenuation (amplitude scaling). At the receiver, all of the paths add up. They can add up constructively, destructively, or a mix of both. We call this concept of multiple signal paths “multipath”. There is the Line-of-Sight (LOS) path, and then all other paths. In the example below, we show the LOS path and a single non-LOS path:
# 16. Pulse Shaping
In the Filters chapter we learned that block-shaped symbols/pulses use an excess amount of spectrum, and we can greatly reduce the amount of spectrum used by “shaping” our pulses. However, you can’t use just any low-pass filter or you might get inter-symbol-interference (ISI), where symbols bleed into and interfere with each other.

When we transmit digital symbols, we transmit them back-to-back (as opposed to waiting some time between them). When you apply a pulse-shaping filter, it elongates the pulse in the time domain (in order to condense it in frequency), which causes adjacent symbols to overlap with each other. The overlap is fine, as long as your pulse-shaping filter meets this one criterion: all of the pulses must add up to zero at every multiple of our symbol period 
, except for one of the pulses. The idea is best understood through the following visualization:
# 17. Synchronization
We have discussed how to transmit digitally over the air, utilizing a digital modulation scheme like QPSK and by applying pulse shaping to limit the signal bandwidth. Channel coding can be used to deal with noisy channels, such as when you have low SNR at the receiver. Filtering out as much as possible before digitally processing the signal always helps. In this chapter we will investigate how synchronization is performed on the receiving end. Synchronization is a set of processing that occurs before demodulation and channel decoding. The overall tx-channel-rx chain is shown below, with the blocks discussed in this chapter highlighted in yellow. (This diagram is not all-encompassing–most systems also include equalization and multiplexing).
# 19. Beamforming & DOA
 phased array, a.k.a. electronically steered array, is an array/collection of antennas that can be used on the transmit or receive side in both communications and radar systems. You’ll find phased arrays on ground-based systems, airborne, and on satellites. We typically refer to the antennas that make up an array as elements, and sometimes the array is called a “sensor” instead. These array elements are most often omnidirectional antennas, equally spaced in either a line or across two dimensions.
# 20. 2D Beamforming
Rectangular arrays (a.k.a. planar arrays) involve a 2D array of elements. With an extra dimension we get some added complexity, but the same basic principles apply, and the hardest part will be visualizing the results (e.g. no more simple polar plots, now we’ll need 3D surface plots). Even though our array is now 2D, that does not mean we have to start adding a dimension to every data structure we’ve been dealing with. For example, we will keep our weights as a 1D array of complex numbers. However, we will need to represent the positions of our elements in 2D. We will keep using theta to refer to the azimuth angle, but now we will introduce a new angle, phi, which is the elevation angle.
# 21. Hands-on with Phaser
In this chapter we use the Analog Devices Phaser, (a.k.a. CN0566 or ADALM-PHASER) which is an 8-channel low-cost phased array SDR that combines a PlutoSDR, Raspberry Pi, and ADAR1000 beamformers, designed to operate around 10.25 GHz. We will cover setup and calibration steps, and then go through some beamforming examples in Python
# 23. Cyclostationary Processing
Cyclostationary signal processing (a.k.a., CSP or simply cyclostationary processing) is a set of techniques for exploiting the cyclostationary property found in many real-world communication signals. These are signals such as modulated signals like AM/FM/TV broadcast, cellular, and WiFi as well as radar signals, and other signals that exhibit periodicity in their statistics. A large swath of traditional signal processing techniques are based on the assumption that the signal is stationary, i.e., the statistics of the signal like the mean, variance and higher-order moments do not change over time. However, most real-world RF signals are cyclostationary, i.e., the statistics of the signal change periodically over time. CSP techniques exploit this cyclostationary property, and can be used to detect the presence of signals in noise, perform modulation recognition, and separate signals that are overlapping in both time and frequency.

If after reading through this chapter and playing around in Python, you want to dive deeper into CSP, check out William Gardner’s 1994 textbook Cyclostationarity in Communications and Signal Processing, his 1987 textbook Statistical Spectral Analysis, or Chad Spooner’s collection of blog posts.

One resource that you will find here and in no other textbook: at the end of the SCF chapter you will be rewarded with an interactive JavaScript app that allows you to play around with the SCF of an example signal, to see how the SCF changes with different signal and SCF parameters, all in your browser! While these interactive demos are free for everyone, they are largely made possible by the support of PySDR’s Patreon members.
# 23. Real-Time GUIs with PyQt
Qt (pronounced “cute”) is a framework for creating GUI applications that can run on Linux, Windows, macOS, and even Android. It is a very powerful framework that is used in many commercial applications, and is written in C++ for maximum performance. PyQt is the Python bindings for Qt, providing a way to create GUI applications in Python, while harnessing the performance of an efficient C++ based framework. In this chapter we will learn how to use PyQt to create a real-time spectrum analyzer that can be used with an SDR (or with a simulated signal). The spectrum analyzer will have time, frequency, and spectrogram/waterfall graphics, as well as input widgets for adjusting the various SDR parameters. We use PyQtGraph, which is a separate library built on top of PyQt, to perform plotting. On the input side, we use sliders, combo-box, and push-buttons. The example supports the PlutoSDR, USRP, or simulation-only mode. Even though the example code uses PyQt6, every single line is identical to PyQt5 (besides the import), very little changed between the two versions from an API perspective. Naturally, this chapter is extremely Python code heavy, as we explain through examples. By the end of this chapter you will have gained familiarity with the building blocks used to create your own custom interactive SDR application!
