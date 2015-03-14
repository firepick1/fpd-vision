### Experiment XP006 Ambient vs. Effector Lighting

### Q: How does effector lighting affect the calculation of camera focus?

FirePick Delta has an effector-mounted camera and light. Since the light is
mounted on the effector, images taken closer to the bed will be brighter than 
images taken higher up. This difference in brightness may affect computer vision
computation based on pixel intensity. One such calculation is 
<a href="https://github.com/firepick1/FireSight/wiki/op-Sharpness">image sharpness detection</a>,
which can be used to determine the focal length of FPDs fixed focus camera.

### Hypothesis
Both effector and external frame lighting will be needed to provide sufficient 
controlled illumination to minimize the impact of ambient light changes on
computer vision calculations.

### Considerations

**Effector lighting** will be 16-LED NeoPixel ring light. 
The <a href="http://www.adafruit.com/product/1463">NeoPixel ring</a>
allows us to experiment with different RGB light levels.

**Raspberry Pi <a href="http://www.adafruit.com/product/1567">Noir camera</a>** 
for its sensitivity in computer vision applications.

**External frame lighting (EXT)** will be 
<a href="http://www.amazon.com/Goal-Zero-14101-Stick-Light/dp/B0045XRK06/ref=sr_1_3?ie=UTF8&qid=1426303833&sr=8-3&keywords=usb+led">
LED strip lighting</a> mounted on the lower
part of the FPD vertical extrusions. Although it's possible to mount four such lights,
two should be fine for assessing the hypothesis.

<a href="https://github.com/firepick1/fpd-vision/blob/master/XP006-lighting/img/FPD_EXT.jpg"><img
src="https://github.com/firepick1/fpd-vision/blob/master/XP006-lighting/img/FPD_EXT.jpg" height=200px></a>

**Ambient residential lighting** fluctuates dramatically throughout the day. Unlike
commercial factory lighting, residential lighting is affected by passing clouds
and varying insolation throughout the day. Another difference in residential lighting is
that the average home encompasses multiple kinds of light sources (fluorescent, LED,
incandescent, halogen, etc.). Since FPD will be expected to operate in a residence, 
it is important to understand the requirements for proper FPD lighting.

**A box** or other light shield will be used to understand the illumination hypothesis
without the influence of ambient light. A box will be used to minimize inconvenience
to home inhabitants. 

<a href="https://github.com/firepick1/fpd-vision/blob/master/XP006-lighting/img/FPD_BOX.JPG"><img
src="https://github.com/firepick1/fpd-vision/blob/master/XP006-lighting/img/FPD_BOX.JPG" 
style="
	-webkit-transform: rotate(90deg);
	-moz-transform: rotate(90deg);
	-ms-transform: rotate(90deg);
	-o-transform: rotate(90deg);
	transform: rotate(90deg);
"
height=200px></a>

**FireSight <a href="https://github.com/firepick1/FireSight/wiki/op-Sharpness">op-sharpness</a>** (thank you, Simon!) will be used to measure the camera
focal length.

### Method
<a href="https://www.youtube.com/watch?v=ZUBUSP92gG8">Video</a>

1. We will probe at X0Y0 to various Z-depths, in increments of 1mm from Z30 to Z-110
1. At each Z-depth, we will take three images (IMG1, IMG2, IMG3)
1. We will repeat the probing of Z-depths three times (REP1, REP2, REP3)
1. Simon will figure out something to determine the focal range

### Results

TBD

# Experiments to determine a 'sharpness' of an image using various methods.
The input is the series of images as described above.
A MATLAB <a href="http://www.mathworks.com/matlabcentral/fileexchange/27314-focus-measure">script</a> is used to compare several methods for measuring relative degree of focus of an image.
The output of some of the methods is shown in the following image. The x axis shows z coordinate of the end effector, y axis shows 'sharpness', which is normalized to span the [0,1] interval for each method separately (based on the input data).

<img src="img/allmethods.png">

The methods mostly agree, that the camera was focused to about 50 mm.
Selecting such methods, which have reasonable processing time (in MATLAB) and which show a peak around z=50mm, we get the following methods: 'GRAS', 'LAPE', 'LAPM', 'LAPV', 'LAPD', 'TENG', 'TENV', 'WAVS', and 'WAVV'.

<img src="img/best9_sharpness.png">

The mean processing time per 400x400px input image on a Intel i5 laptop is show in the following table.

<table>
<tr><td>GRAS</td><td>0.0080 s</td></tr>
<tr><td>LAPE</td><td>0.0374 s</td></tr>
<tr><td>LAPM</td><td>0.0411 s</td></tr>
<tr><td>LAPV</td><td>0.0412 s</td></tr>
<tr><td>LAPD</td><td>0.0824 s</td></tr>
<tr><td>TENG</td><td>0.0550 s</td></tr>
<tr><td>TENV</td><td>0.0620 s</td></tr>
<tr><td>WAVS</td><td>0.1776 s</td></tr>
<tr><td>WAVV</td><td>0.2062 s</td></tr>
</table>

The methods acronyms are explained in the following list, MATLAB script attached. `Image` is input image and `FM` the focus measure (i.e. sharpness).

## Methods
### GRAS - Absolute squared gradient
A.M. Eskicioglu and P. S. Fisher. Image quality measures and their performance.
Communications, IEEE Transactions on, 43(12):2959–2965, 1995

<pre>
        Ix = diff(Image, 1, 2);
        FM = Ix.^2;
        FM = mean2(FM);
</pre>

Implemented in FireSight. Results on the not-illuminated input data shown in the following image.
The results are different, as there is a slight difference in the OpenCV and MATLAB conversion from colour to grayscale images.
<img src="img/GRAS.png">


### LAPE - Energy of laplacian [Subbarao92a]
<pre>
        LAP = fspecial('laplacian');
        FM = imfilter(Image, LAP, 'replicate', 'conv');
        FM = mean2(FM.^2);
</pre>

Implemented in FireSight. Results on the not-illuminated input data shown in the following image.
<img src="img/LAPE.png">


### LAPM -  Modified Laplacian [Nayar89]
<pre>
        M = [-1 2 -1];        
        Lx = imfilter(Image, M, 'replicate', 'conv');
        Ly = imfilter(Image, M', 'replicate', 'conv');
        FM = abs(Lx) + abs(Ly);
        FM = mean2(FM);
</pre>


### LAPV - Variance of laplacian (Pech2000)
<pre>
        LAP = fspecial('laplacian');
        ILAP = imfilter(Image, LAP, 'replicate', 'conv');
        FM = std2(ILAP)^2
</pre>

### LAPD - Diagonal laplacian (Thelen2009)
<pre>
        M1 = [-1 2 -1];
        M2 = [0 0 -1;0 2 0;-1 0 0]/sqrt(2);
        M3 = [-1 0 0;0 2 0;0 0 -1]/sqrt(2);
        F1 = imfilter(Image, M1, 'replicate', 'conv');
        F2 = imfilter(Image, M2, 'replicate', 'conv');
        F3 = imfilter(Image, M3, 'replicate', 'conv');
        F4 = imfilter(Image, M1', 'replicate', 'conv');
        FM = abs(F1) + abs(F2) + abs(F3) + abs(F4);
        FM = mean2(FM);
</pre>

### TENG - Tenengrad (Krotkov86)
<pre>
        Sx = fspecial('sobel');
        Gx = imfilter(double(Image), Sx, 'replicate', 'conv');
        Gy = imfilter(double(Image), Sx', 'replicate', 'conv');
        FM = Gx.^2 + Gy.^2;
        FM = mean2(FM);
</pre>

### TENV - Tenengrad variance (Pech2000)
<pre>
        Sx = fspecial('sobel');
        Gx = imfilter(double(Image), Sx, 'replicate', 'conv');
        Gy = imfilter(double(Image), Sx', 'replicate', 'conv');
        G = Gx.^2 + Gy.^2;
        FM = std2(G)^2;
</pre>

### WAVS - Sum of Wavelet coeffs (Yang2003)
<pre>
        [C,S] = wavedec2(Image, 1, 'db6');
        H = wrcoef2('h', C, S, 'db6', 1);   
        V = wrcoef2('v', C, S, 'db6', 1);   
        D = wrcoef2('d', C, S, 'db6', 1);   
        FM = abs(H) + abs(V) + abs(D);
        FM = mean2(FM);
</pre>

### WAVV - Variance of  Wav...(Yang2003)
<pre>
        [C,S] = wavedec2(Image, 1, 'db6');
        H = abs(wrcoef2('h', C, S, 'db6', 1));
        V = abs(wrcoef2('v', C, S, 'db6', 1));
        D = abs(wrcoef2('d', C, S, 'db6', 1));
        FM = std2(H)^2+std2(V)+std2(D);
</pre>


## Neopixel illuminated dataset

With the images taken under Neopixel illumination, we get a shift in the peak sharpness (unless Karl readjusted the focal length?), as seen in the following figure.
<img src="img/nolight_vs_neopixel.png">

