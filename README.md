
# Design of a 5.8 GHz rectangular Patch Linear Array

Team:
- Alessandro Di Clemente
- Stefano Fantauzzi
- Lorenzo Valletti

End-course project for *Wireless Electromagnetics Technology* @ University of Rome Tor Vergata.

## Design specifications

Design an **antenna array** with **five elements** ![n_e = 5](https://render.githubusercontent.com/render/math?math=%5Cdisplaystyle+n_e+%3D+5) and a **main-lobe/side-lobe ratio** of ![R = 100](https://render.githubusercontent.com/render/math?math=%5Cdisplaystyle+R+%3D+100).
The **array factor** must be a *Chebyshev polynomial* designed in order to **minimize the beamwidth** of the radiation pattern's main lobe.
After designing the array factor, design a **rectangular patch array** fed by **coaxial cable** for **5.8 GHz** and evaluate the performance of the overall array in both **broadside** case and 30° off the boresight direction case.

## Array factor design
All the calculations needed to design the array factor were made using the MATLAB Symbolic Math Toolbox.
The Chebyshev polynomial must have a *order* equal to

![N=\frac{n_e-1}{2}=2](https://render.githubusercontent.com/render/math?math=%5Cdisplaystyle+N%3D%5Cfrac%7Bn_e-1%7D%7B2%7D%3D2)

Then the *array factor* has the form:

![T_2(x)=2x^2-1](https://render.githubusercontent.com/render/math?math=%5Cdisplaystyle+T_2%28x%29%3D2x%5E2-1)

The *visibility window* must be constrained in order to fullfill the requested **ML/SL ratio**. Moreover, in order to obtain the **minimum beamwidth** in *broadside* configuration, the visibility window must be enlarged to include the maximum number of side lobes without allowing more than one main lobe to show.
Thus the optimal solution for the **inter-element spacing** is calculated as:

![d = 34.4 \text{ mm}](https://render.githubusercontent.com/render/math?math=%5Cdisplaystyle+d+%3D+34.4+%5Ctext%7B+mm%7D)

 The obtained array factor has a wider beam but an extremely greater ML/SL ratio if compared with the *uniform array factor*: the **first-null beamwidth** is evaluated as 63.1° while the **half-power beamwidth** as 20.97° using the numerical solver of MATLAB on the synthetized polynomial.

 The polynomial is used to generate the **current coefficients** of the array elements:

 ![C=\begin{bmatrix}8.21 && 24.75 && 34.07 && 24.57 && 8.21\end{bmatrix}
](https://render.githubusercontent.com/render/math?math=%5Cdisplaystyle+C%3D%5Cbegin%7Bbmatrix%7D8.21+%26%26+24.75+%26%26+34.07+%26%26+24.57+%26%26+8.21%5Cend%7Bbmatrix%7D%0A)

with a **taper efficiency** of ![\eta_F\approx 0.79](https://render.githubusercontent.com/render/math?math=%5Cdisplaystyle+%5Ceta_F%5Capprox+0.79).

Here is a plot of the synthetized array factor:

<p align="center">
<img src="/images/arrayFactor.png" width="512">
</p>

## Rectangular coaxial-fed patch design
The rectangular patch design process is straightforward. First, we have to choose the right **dielectric substrate**, which must be, as for specifications, a **low loss-dielectric with thickness below the millimeter**. After the material has been chosen, we can make an estimate of the patch dimensions and characteristics using the known equations, and use them as a starting point for the optimization using the CAE software FEKO.
It is important to note that, except from the dielectric dimensions which are guaranteed from the constructor, all the dimensions are approximated for an implementation process with an accuracy at the tenth of millimeter.

The best choice for this project is to use the **RT/Duroid 5880**, which is a glass microfiber reinforced PTFE (Polytetrafluoroethylene) composite from Rogers Corporation.
This laminate exhibits a **low dielectric constant** ![\varepsilon_r = 2.2](https://render.githubusercontent.com/render/math?math=%5Cdisplaystyle+%5Cvarepsilon_r+%3D+2.2)
 and a **low dissipation factor** ![\tan\delta = 0.0009](https://render.githubusercontent.com/render/math?math=%5Cdisplaystyle+%5Ctan%5Cdelta+%3D+0.0009)
, so it is suitable for optimal patch radiation and overall performance.
This lot of laminates comes with standard values of heights; for this design project the chosen **thickness** is ![h=0.787\text{ mm}](https://render.githubusercontent.com/render/math?math=%5Cdisplaystyle+h%3D0.787%5Ctext%7B+mm%7D).

Copper thickness and hole size for the patch design are chosen according to **PCB manufacturer capabilities** to 18um and 0.2mm.

Design flow starts with **patch geometric parameter estimation** from **antenna theory**:

![W = \frac{\lambda_0}2\sqrt{\frac2{\varepsilon_r+1}}\approx 20.4\text{ mm}](https://render.githubusercontent.com/render/math?math=%5Cdisplaystyle+W_0+%3D+%5Cfrac%7B%5Clambda_0%7D2%5Csqrt%7B%5Cfrac2%7B%5Cvarepsilon_r%2B1%7D%7D%5Capprox+20.4%5Ctext%7B+mm%7D)

![L=\frac c{2f\sqrt{\varepsilon_{re}}}-2\Delta L \approx 17.2\text{ mm}](https://render.githubusercontent.com/render/math?math=%5Cdisplaystyle+L_0%3D%5Cfrac+c%7B2f%5Csqrt%7B%5Cvarepsilon_%7Bre%7D%7D%7D-2%5CDelta+L+%5Capprox+17.2%5Ctext%7B+mm%7D)

Another important parameter to estimate is the **feed position** which determines the **impedance matching**. To achieve an **input impedance** of 50Ω the distance from the edge of the feed along the length of the patch is estimated:

![x_0 = \frac c{2\pi f\sqrt{\varepsilon_{re}}}\acos\sqrt{\frac{R_{in}}{R_{in,0}}}\approx 5.5\text{mm}](https://render.githubusercontent.com/render/math?math=%5Cdisplaystyle+x_0+%3D+%5Cfrac+c%7B2%5Cpi+f%5Csqrt%7B%5Cvarepsilon_%7Bre%7D%7D%7D%5Cacos%5Csqrt%7B%5Cfrac%7BR_%7Bin%7D%7D%7BR_%7Bin%2C0%7D%7D%7D%5Capprox+5.5%5Ctext%7Bmm%7D)


Knowledge of a starting point in the design of the patch allows to apply an **optimization procedure** whose aim is to **minimize the magnitude of the reflection coefficient** at the working frequency and to **center the resonant frequency** at the desired 5.8GHz.

The parameters to optimize are L, W and x0 and the optimization is done with FEKO, simulating each iteration of the antenna design and tuning its parameters to achieve better performances.
The **patch length L** is first adjusted to center the resonant frequency to 5.8 GHz, minimizing the magnitude of the reflection coefficient.

<p align="center">
<img src="/images/Loptimization.png" width="512">
</p>

The chosen patch length is ![L\approx 16.6\text{mm}](https://render.githubusercontent.com/render/math?math=%5Cdisplaystyle+L%5Capprox+16.6%5Ctext%7Bmm%7D)
. After fixing this parameter, a **contour plot** of the surface ![\left|\Gamma_{f_0}\left(W,x_0\right)\right|](https://render.githubusercontent.com/render/math?math=%5Cdisplaystyle+%5Cleft%7C%5CGamma_%7Bf_0%7D%5Cleft%28W%2Cx_0%5Cright%29%5Cright%7C)
  is made to estimate the best couple of values for patch width and feed position:

  <p align="center">
  <img src="/images/contourplot.png" width="512">
  </p>

The minimum is located at the values ![W\approx 21.5\text{mm}](https://render.githubusercontent.com/render/math?math=%5Cdisplaystyle+W%5Capprox+21.5%5Ctext%7Bmm%7D)
 and ![x_0\approx 5.1\text{mm}](https://render.githubusercontent.com/render/math?math=%5Cdisplaystyle+x_0%5Capprox+5.1%5Ctext%7Bmm%7D) .

 With this configuration, the patch element is simulated.

 **Reflection coefficient** plot:


 <p align="center">
 <img src="/images/reflCoeffPlot.png" width="512">
 </p>

**Input impedance** plot:

<p align="center">
<img src="/images/inputImpedancePlot.png" width="512">
</p>

**Current distribution** plot:

<p align="center">
<img src="/images/currentDistrPlot.png" width="512">
</p>

**Gain pattern**, with 6.67dB maximum gain:

<p align="center">
<img src="/images/gainPlot.png" width="512">
</p>

## Linear array design
After creating a FEKO model of the array, the system is feeded with equal phases in order to obtain a boresight radiation as modeled in first section. Afterwards, the phase difference between each element is calculated in order to obtain a phased array with the goal to modify the direction of the main lobe 30° off the boresight direction.

### Broadside array
Using the simulation results from the single patch, an estimate of the obtained array radiation pattern is made exploiting the **array pattern multiplication principle** on MATLAB.
On the left the single patch polar pattern is shown, on the right the expected array pattern:

<p align="center">
<img src="/images/boresightPatternMult.png" width="512">
</p>

The system is then modeled in FEKO, and the radiation pattern matches the estimated one:

<p align="center">
<img src="/images/boresightAntenna.png" width="512">
</p>

The obtained gain pattern has a maximum gain on the boresight of 12.52 dBi, a HPBW = 20.42° and a ML/SL ratio of 44.313 dB.

The **near field radiation** can be evaluated on a plane just above the array plane in order to analyze non-uniformities in the fringing field produced by inter-antenna coupling.
What we would expect if there wasn’t a coupling between elements is a null of the electric field in the center of each patch along the y axis of the structure, which corresponds to the direction of the patch length.
Figure shows the magnitude of the electric field in dBV/m on a plane which is 393.5um above the array plane.

<p align="center">
<img src="/images/nearField.png" width="512">
</p>

In the midmost patches, the vicinity between the edges of two different antennas is sufficient to allow **coupling** and the **fringing field** is not as expected.

### Phased array
In order to modify the inclination of the main lobe with respect to the boresight, a **phase difference** is applied to each element of the array.
Then the current coefficients are

<p align="center">
<img src="/images/phasedCoefficients.png" width="256">
</p>

A **grating lobe** is expected to appear in the radiation pattern of the array. This is due to the fact that the array factor has been designed in order to minimize the beamwidth in the broadside radiation, which means to enlarge the visibility window to the maximum possible extension to not have a grating lobe in that configuration.
Simulating the phased array in FEKO the radiation pattern is obtained

<p align="center">
<img src="/images/phasedArray.png" width="512">
</p>

### Grating lobe suppression
The grating lobe that appears in the radiation pattern of the phased array lowers the performances of the antenna in both receiving and transmitting mode.
There are several possible ways to reduce the effect of the grating lobe in the radiation pattern of the beam- steered array. Howsoever, the grating lobe suppression **cannot coexist** with both beamwidth minimization and ML/SL ratio optimization if the number of elements stays fixed to 5 and the structure geometry is not modified, with the known array factor synthesis methods.
If the structure is not changed and d is constant, the large extension of the visibility window at the working frequency does not allow to move the center far without getting another main lobe inside; this condition holds also if we use a **uniform array factor** instead of a Chebyshev polynomial: in this case, the condition of absence of a grating lobe is almost at its limit, and we can see the start of the formation of a grating lobe in the obtained array factor; this contribute is partially filtered by the element radiation pattern, and we can estimate to obtain an overall pattern given by the polar plot

<p align="center">
<img src="/images/uniformPhased.png" width="512">
</p>

This pattern has approximately the same ML/GL ratio of the one obtained with the Chebyshev factor, but the other side lobes appear at a stronger magnitude while the prominent lobes’ beamwidth appear narrower.

By reducing the inter-element distance without violating the geometry constraints and superposition of patch elements, it is possible to  narrow the overall extension of the visibility window in order to make possible a wider translation without introducing any grating lobe; this, however, would end in a larger beamwidth also in boresight radiation, and would cost the re-design of the entire array.
Introducing a greater number of array elements also sacrifices the project constraints in terms of structure area, but opens to the possibility to use a larger order Chebyshev array factor that could have the same beamwidth and ML/SL ratio with a narrower visibility window, thus suppressing the appearance of a grating lobe.

## References
- *Wireless Electromagnetic Technology* course notes, G. Marrocco
- *Antenna Theory: Analysis and Design*, C. A. Balanis
