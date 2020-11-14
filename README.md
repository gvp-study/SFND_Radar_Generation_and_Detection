# SFND Radar Generation and Detection
## George V. Paul
[Github link to this project is here](https://github.com/gvp-study/SFND_Radar_Generation_and_Detection.git)

Write a README outlining the following:
1. Implementation steps for the 2D CFAR process.
2. Selection of Training, Guard cells and offset.
3. Steps taken to suppress the non-thresholded cells at the edges.

<img src="./images/flowchart.png" width="779" height="414" />

Configure the FMCW waveform based on the system requirements.
Define the range and velocity of target and simulate its displacement.
For the same simulation loop process the transmit and receive signal to determine the beat signal
Perform Range FFT on the received signal to determine the Range
Towards the end, perform the CFAR processing on the output of 2nd FFT to display the target.
Radar System Requirements

System Requirements defines the design of a Radar. The sensor fusion design for different driving scenarios requires different system configurations from a Radar. In this project, you will designing a Radar based on the given system requirements (above).

Max Range and Range Resolution will be considered here for waveform design.

The sweep bandwidth can be determined according to the range resolution and the sweep slope is calculated using both sweep bandwidth and sweep time.

Bandwidth(Bsweep)=speedoflight/(2∗rangeResolution)

The sweep time can be computed based on the time needed for the signal to travel the unambiguous maximum range. In general, for an FMCW radar system, the sweep time should be at least 5 to 6 times the round trip time. This example uses a factor of 5.5.

chirp=5.5⋅2⋅Rmax/c

Giving the slope of the chirp signal

Slope=Bandwidth/Tchirp
​	 
Initial Range and velocity of the Target
You will provide the initial range and velocity of the target. Range cannot exceed the max value of 200m and velocity can be any value in the range of -70 to + 70 m/s.
<img src="./radar_signal_formula.png" width="779" height="414" />

# FP.1: Implementing the 2D CFAR

Next, you will be simulating the signal propagation and moving target scenario.

Theory :

In terms of wave equation, FMCW transmit and received signals are defined using these wave equations, where

α=Slopeofthesignal. The Transmit Signal is given by:

Tx=cos(2π(fc​	 t+2 αt 2))

The received signal is nothing but the time delayed version of the Transmit Signal. In digital signal processing the time delayed version is defined by
((t−τ), where τ
τ represents the delay time, which in radar processing is the trip time for the signal.

On mixing these two signals, we get the beat signal, which holds the values for both range as well as doppler. By implementing the 2D FFT on this beat signal, we can extract both Range and Doppler information

The beat signal can be calculated by multiplying the Transmit signal with Receive signal. This process in turn works as frequency subtraction. It is implemented by element by element multiplication of transmit and receive signal matrices.

Mixed or Beat Signal = (Tx.*Rx)

<img src="./images/radar_signal_fft1.png" width="779" height="414" />


# FP.2: Selecting Training, Guard Cells and offset
<img src="./images/radar_signal_fft2.png" width="779" height="414" />



# FP.3: Thresholding at the edges
<img src="./images/radar_doppler_filtered.png" width="779" height="414" />
