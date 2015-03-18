### Experiment XP006 Ambient vs. Effector Lighting

### Q: How does effector lighting affect the calculation of camera focus?

FirePick Delta has an effector-mounted camera and light. Since the light is
mounted on the effector, images taken closer to the bed will be brighter than 
images taken higher up. This difference in brightness may affect computer vision
computation based on pixel intensity. One such calculation is 
<a href="https://github.com/firepick1/FireSight/wiki/op-Sharpness">FireSight op-sharpness</a>,
which can be used to determine the focal length of FPDs fixed focus camera by measuring
image sharpness at various points along the z-axis.

### Hypothesis
Effector lighting will provide sufficient 
controlled illumination to minimize the impact of ambient light changes on
computer vision calculations.

### Considerations
<a href="https://github.com/firepick1/fpd-vision/blob/master/XP006-lighting/img/ambient.gif"><img
src="https://github.com/firepick1/fpd-vision/blob/master/XP006-lighting/img/ambient.gif" height=200px></a>

**Effector lighting** will be 16-LED NeoPixel ring light. 
The <a href="http://www.adafruit.com/product/1463">NeoPixel ring</a>
allows us to experiment with different RGB light levels. For the experiment,
we will arbitrarily choose R128G63B63 as "pleasing to the eye and having not too much glare."
The NeoPixel at RGB255 is quite bright and a bit painful to look at.

**The Raspberry Pi <a href="http://www.adafruit.com/product/1567">Noir camera</a>** 
will be used for its sensitivity in computer vision applications.

**Ambient residential lighting** fluctuates dramatically throughout the day. Unlike
commercial factory lighting, residential lighting is affected by passing clouds
and varying insolation throughout the day. Another difference in residential lighting is
that the average home encompasses multiple kinds of light sources (fluorescent, LED,
incandescent, halogen, etc.). Since FPD will be expected to operate in a residence, 
it is important to understand the requirements for proper FPD lighting. For this experiment,
all ambient light will be natural. We will start the experiment before dawn and
continue it through the brightest hours of the day, sampling the focus range
every 30 minutes in sets of ten samples. <a href="https://www.youtube.com/watch?v=H8_O2updL7o">See video</a>

**No box or other light shield** will be used to block out ambient light. Boxes introduce
internal reflection which would affect the experiment in non-reproduceable ways. 
Instead, we simply start the experiment earlier in a dark room, 
since a dark room is, by definition, a black box.

**FireSight <a href="https://github.com/firepick1/FireSight/wiki/op-Sharpness">op-sharpness</a>** (thank you, Simon!) will be used to measure the camera
focal length. Specifically, we will use 
<a href="https://github.com/firepick1/FireREST/blob/dev/server/firepick/Focus.js">Focus.focalRange()</a>
to determine:

* **sharpness.max** greatest sharpness returned by op-sharpness
* **sharpness.min** 0.7 * greatest sharpness returned by op-sharpness
* **zBest** is highest FPD z-axis coordinate having **sharpness.max**
* **zHigh** is highest FPD z-axis coordinate having **sharpness.min**
* **zLow** is lowest FPD z-axis coordinate having **sharpness.min**

### Method

We will calculate the FPD focal range in the following circumsances by sampling the focal range
from 0509 to 1243, with sunrise occurring at 0716. This time range encompasses the full range
of ambient illumination in a residential setting. The sampling will be conducted at 30 minute
intervals using sets of 10 samples to give us data about ambient illumination change at that
time of day. We therefore expect a data set of about 1329 images taken over the course of the experiment.

### Results

#### 1. op-sharpness is very sensitive to ambient light changes
The following graph shows that the maximum sharpness is very sensitive to ambient light changes and
is unreliable for precise measurement. The minimum sharpness is merely 0.7 the maximum
sharpness.

<a href="https://github.com/firepick1/fpd-vision/blob/master/XP006-lighting/img/SharpAVG.png">
<img src="https://github.com/firepick1/fpd-vision/blob/master/XP006-lighting/img/SharpAVG.png" 
height=400px></a>

More precisely, the standard deviation of the maximum sharpness shows exactly how bad the
variation is:

<a href="https://github.com/firepick1/fpd-vision/blob/master/XP006-lighting/img/SharpSTDDEV.png">
<img src="https://github.com/firepick1/fpd-vision/blob/master/XP006-lighting/img/SharpSTDDEV.png" 
height=400px></a>

#### 2. The focal range minimum (lowest point of minimum acceptable sharpness) is stable

Although the ZBEST and ZHIGH focal range values fluctuate with ambient light, we do notice
a very surprising and useful result.
Looking carefully at the ZLOW value for the bottom of the focal range, we notice
that ZLOW is quite stable through all the ambient light changes from full dark to full light.

<a href="https://github.com/firepick1/fpd-vision/blob/master/XP006-lighting/img/ZAVG.png">
<img src="https://github.com/firepick1/fpd-vision/blob/master/XP006-lighting/img/ZAVG.png" 
height=400px></a>

Amazingly, ZLOW shows much less standard deviation than either ZBEST or ZHIGH. 
This actually makes sense when considering that the effector light illuminates
the calibration grid better at ZLOW than at ZHIGH. In other words, the effector
light is doing a great job of cancelling out ambient light changes. At ZBEST and
ZHIGH, the effector light is more distant and less effective, making the image
more susceptible to changes in ambient light.

<a href="https://github.com/firepick1/fpd-vision/blob/master/XP006-lighting/img/ZSTDDEV.png">
<img src="https://github.com/firepick1/fpd-vision/blob/master/XP006-lighting/img/ZSTDDEV.png" 
height=400px></a>

#### 3. The FPD has a warmup phase of 30 minutes

An unexpected result was the change in focal range that happened from power on at 0521 till
about 30 minutes later. During this time the ambient light is fully dark, but we
see a gradual shift when looking at the raw sample data:

<a href="https://github.com/firepick1/fpd-vision/blob/master/XP006-lighting/img/warmup.png">
<img src="https://github.com/firepick1/fpd-vision/blob/master/XP006-lighting/img/warmup.png" 
height=400px></a>

The NeoPixel does warm up with use, which suggests that the warmup variance might be entirely
due to camera/light as opposed to FPD itself.
