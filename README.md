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


<img src="./radar_signal_formula.png" width="779" height="414" />
<img src="./images/radar_signal_fft1.png" width="779" height="414" />
Clearly the peak shows that there is a target at range = 100m.

# FP.1: Implementing the 2D CFAR
Once the signal is passed through the 2D FFT we have the range doppler map as shown below.
<img src="./images/radar_signal_fft2.png" width="779" height="414" />

```Matlab
%design a loop such that it slides the CUT across range doppler map by
%giving margins at the edges for Training and Guard Cells.
%For every iteration sum the signal level within all the training
%cells. To sum convert the value from logarithmic to linear using db2pow
%function. Average the summed values for all of the training
%cells used. After averaging convert it back to logarithimic using pow2db.
%Further add the offset to it to determine the threshold. Next, compare the
%signal under CUT with this threshold. If the CUT level > threshold assign
%it a value of 1, else equate it to 0.


   % Use RDM[x,y] as the matrix from the output of 2D FFT for implementing
   % CFAR
RDM = RDM/max(max(RDM)); % Normalizing
for i = Tr+Gr+1:(Nr/2)-(Tr+Gr)
    for j = Td+Gd+1:(Nd)-(Td+Gd)
        %Create a vector to store noise_level for each iteration on training cells
        noise_level = zeros(1,1);
        %Step through each of bins and the surroundings of the CUT
        for p = i-(Tr+Gr) : i+(Tr+Gr)
            for q = j-(Td+Gd) : j+(Td+Gd)
                %Exclude the Guard cells and CUT cells
                if (abs(i-p) > Gr || abs(j-q) > Gd)
                    %Convert db to power
                    noise_level = noise_level + db2pow(RDM(p,q));
                end
            end
        end

        %Calculate threshould from noise average then add the offset
        threshold = pow2db(noise_level/(2*(Td+Gd+1)*2*(Tr+Gr+1)-(Gr*Gd)-1));
        %Add the SNR to the threshold
        threshold = threshold + off_set;
        %Measure the signal in Cell Under Test(CUT) and compare against
        CUT = RDM(i,j);

        if (CUT < threshold)
            RDM(i,j) = 0;
        else
            RDM(i,j) = 1;
        end

    end
end

```
# FP.2: Selecting Training, Guard Cells and offset
The window used for the cell under test (CUT) with training and guard cells for both the range and doppler axis as shown below.

```Matlab
%Select the number of Training Cells in both the dimensions.

Tr = 10;
Td = 8;

% *%TODO* :
%Select the number of Guard Cells in both dimensions around the Cell under
%test (CUT) for accurate estimation

Gr = 4;
Gd = 4;

% *%TODO* :
% offset the threshold by SNR value in dB
off_set = 1.4;

% *%TODO* :
%Create a vector to store noise_level for each iteration on training cells
noise_level = zeros(1,1);

```

# FP.3: Thresholding at the edges
The edges of the RDM which was not processed by the previous CFAR algorithm is now set to 0 as shown below.

```Matlab
RDM(RDM~=0 & RDM~=1) = 0;

```
The result is the clean target with a range at 100m and 25-35m/sec speed.

<img src="./images/radar_doppler_filtered.png" width="779" height="414" />
