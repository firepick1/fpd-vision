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
<a href="https://github.com/firepick1/fpd-vision/blob/master/XP006-lighting/img/FPD_EXT.jpg"><img
src="https://github.com/firepick1/fpd-vision/blob/master/XP006-lighting/img/FPD_EXT.jpg" height=200px></a>
<a href="https://github.com/firepick1/fpd-vision/blob/master/XP006-lighting/img/FPD_BOX.JPG"><img
src="https://github.com/firepick1/fpd-vision/blob/master/XP006-lighting/img/FPD_BOX.JPG" 
height=200px></a>

**Effector lighting** will be 16-LED NeoPixel ring light. 
The <a href="http://www.adafruit.com/product/1463">NeoPixel ring</a>
allows us to experiment with different RGB light levels.

**THe Raspberry Pi <a href="http://www.adafruit.com/product/1567">Noir camera</a>** 
will be used for its sensitivity in computer vision applications.

**External frame lighting (EXT)** will be 
<a href="http://www.amazon.com/Goal-Zero-14101-Stick-Light/dp/B0045XRK06/ref=sr_1_3?ie=UTF8&qid=1426303833&sr=8-3&keywords=usb+led">
LED strip lighting</a> mounted on the lower
part of the FPD vertical extrusions. Although it's possible to mount four such lights,
two should be fine for assessing the hypothesis.

**Ambient residential lighting** fluctuates dramatically throughout the day. Unlike
commercial factory lighting, residential lighting is affected by passing clouds
and varying insolation throughout the day. Another difference in residential lighting is
that the average home encompasses multiple kinds of light sources (fluorescent, LED,
incandescent, halogen, etc.). Since FPD will be expected to operate in a residence, 
it is important to understand the requirements for proper FPD lighting.

**A box** or other light shield will be used to evaluate the illumination hypothesis
without the influence of ambient light. A box will be used to minimize inconvenience
to home inhabitants. 

**FireSight <a href="https://github.com/firepick1/FireSight/wiki/op-Sharpness">op-sharpness</a>** (thank you, Simon!) will be used to measure the camera
focal length.

**Brighter images** may be considered "sharper". Since focus calculations often rely on detecting 
pixel intensity changes, brighter images may be "sharper" than dim images.

**Distant images** may be considered "sharper" than up-close images. 
Since we will be imaging a grid, distant images will include more grid lines.
Each grid line triggers a contrast detection--close-up images will have
fewer gridlines and hypothetically less detected sharpness.

**FireREST Focus.js** will be used to generate 
<a href="https://github.com/firepick1/fpd-vision/tree/master/XP006-lighting/data">test data.</a>
Focus.js converges 
on a value for maximum focus Z coordinate (i.e., "sharpest Z") within 15-20 images.
We will measure the focus using the **zBest** and **zPolyFit** values returned by 
<a href="https://github.com/firepick1/FireREST/blob/dev/server/firepick/Focus.js">Focus.solve()</a>.

**Effector illumination** will be tested with the following variations:

1. **RGB255** full RGB 
1. **RGB127** half-intensity RGB
1. **R127GB63** half-intensity R, quarter-intensity GB
1. **RGB0** no effector lighting

### Method

We will calculate the FPD focal length in the following circumsances:

1. **without FPD lighting** to establish an ambient baseline at two different times of day. (RGB0,AMB)
1. **within a box** to establish ambient-independent behavior (BOX)
1. **with FPD lighting** using different LED light levels (R127GB63, RGB127, RGB255)


### Results

#### Ambient light strongly affects focus calculation. 
<a href="https://github.com/firepick1/fpd-vision/blob/master/XP006-lighting/img/ambient-38_9AM_10AM.gif">
<img src="https://github.com/firepick1/fpd-vision/blob/master/XP006-lighting/img/ambient-38_9AM_10AM.gif" 
height=200px></a>
<a href="https://github.com/firepick1/fpd-vision/blob/master/XP006-lighting/img/ambient_data.png">
<img src="https://github.com/firepick1/fpd-vision/blob/master/XP006-lighting/img/ambient_data.png" 
height=200px></a>

The picture above shows two images, taken at 9 and 10AM. Notice that:

* The "sharpest Z" changes by over 15mm, all within about an hour as the day got brighter.
* Brighter ambient images are seen as "sharper". Increased ambient light increases the contrast of more distant images, resulting in a greater z value for sharpness.
* Brighter ambient light also fluctuates more (higher standard deviation), because the higher intensity affects the relative change in intensity used to calculate sharpness
