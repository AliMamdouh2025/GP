# MATLAB Control System Analysis and Design  

This document provides a comprehensive explanation of the objectives and step-by-step methodology used in the MATLAB script for analyzing and designing a control system.  

## Objective  
The primary aim of the script is to:  
1. **Analyze the system's step response** and frequency response characteristics.  
2. **Design a compensator** to meet specific requirements for steady-state error and phase margin.  
3. **Validate system performance** before and after applying the compensator using MATLAB plots, including step response and Bode plots.  

The system under consideration consists of the following components:  
- **Plant (\(Gp\)):** A transfer function in the continuous-time domain.  
- **Feedback Filter (\(Hs\)):** A low-pass filter included in the feedback path.  
- **Sampling Time (\(T\)):** The system is discretized using a sampling period of \(T = 0.1 \, s\).  

## Steps in the Script  

### 1. **Define the Continuous-Time System**  
The plant transfer function (\(Gp\)) and feedback filter (\(Hs\)) are defined as continuous-time transfer functions:  
```matlab  
Gp = tf(4,[1 2.5 1]); % Gp  
Hs = tf(1,[0.05 1]);   % Hs  
OL_s = Gp * Hs;   % Open-loop transfer function in the s-domain  
```  

### 2. **Discretize the System**  
The continuous-time plant (\(Gp\)) and the open-loop system (\(Gp*Hs\)) are discretized using the Zero-Order Hold (ZOH) method with a sampling period of \(T = 0.1 \, s\):  
```matlab  
T = 0.1;  
Gz = c2d(Gp, T, 'zoh');          % Discrete plant transfer function \(G(z)\)  
OL_z = c2d(OL_s, T, 'zoh');   % Discrete open-loop transfer function GH(z)
CL_z = Gz / (1 + OL_z);        % Closed-loop transfer function  
```  

### 3. **Analyze Initial System Response**  
#### Step Response:  
The initial closed-loop step response is visualized:  
```matlab  
figure  
step(CL_z)  
```  

#### Frequency Response (Bode Plot):  
The open-loop system is transformed back to the continuous domain using the Tustin method for frequency response analysis:  
```matlab  
figure  
margin(OL_w)  
```  

### 4. **Adjust the Steady-State Error**  
To achieve a steady-state error of 10%, the required gain (\(K\)) is computed:  
```matlab  
ess = 0.1;  
Kp = dcgain(OL_z);       % Compute DC gain (\(K_p\))  
K = ((1 / ess) - 1) / Kp;   % Calculate the required gain  
```  
The open-loop and closed-loop transfer functions are updated with the new gain (\(K\)):  
```matlab  
OL_new = K * OL_z;  
CL_new = K * Gz_new / (1 + OL_new);  
```  
Step and frequency responses are re-analyzed to ensure the steady-state error requirement is met.  

### 5. **Design the Compensator**  
The Design of the Compensator is illusturated in the following three figures:
![alt text](image.png)
![alt text](image-1.png)
![alt text](image-2.png)

A lead compensator is designed using MATLAB's **SISO Design Tool (sisotool)** to enhance the phase margin (target: \(PM > 50^\circ\)) and improve the system's dynamic performance.  
```matlab  
sisotool(GH_OL_W);  
```  
Key steps in the compensator design include:  
- Adding poles and zeros to create a lead compensator.  
- Iteratively tuning the compensator to achieve the desired phase margin.  
For instance, after tuning the compensator, a phase margin of \(PM = 50.0521^\circ\) was achieved.  

### 6. **Final System Response with the Compensator**  
The compensator is implemented as follows:  
```matlab  
C_tf = 8.8032 * tf([1,4.993],[1,19.54]);  %implemention of compensator from SISOTOOL

Cz_w = d2c(Cz, 'tustin');  % Convert the open-loop discrete transfer function back to continuous-time using Tustin's method 
```  
The compensator is integrated into the system:  
```matlab  
CL_new = Gz_new / (1 + OL_new); % Compute the new closed-loop transfer function

```  
The step response and frequency response are plotted to evaluate the system's performance after compensation:  
```matlab  
figure  
step(CL_new)  
figure  
margin(OLw_new)  
```  

## Summary of Achievements  
- The system's step and frequency responses were analyzed in both pre- and post-compensation states.  
- The steady-state error requirement (\(e_{ss} = 0.1\)) was met by adjusting the system gain.  
- A lead compensator was designed and implemented to enhance the phase margin and improve dynamic behavior.  
- Plots were used to illustrate the system's improved performance after applying the compensator.  

## Additional Notes  
- Compensator tuning must ensure that the phase margin is improved without introducing excessive overshoot or instability into the system.  
