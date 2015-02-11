### Experiment XP005 Focal Range Determination

### Q: What's the best way to automatically determine the focal range of a fixed focus camera?

FirePick Delta currently relies on fixed focus camera, which are affordable and ubiquitous.
For a consistent user experience, we need a way to calibrate the fixed focus so that
we can tell the operator how to adjust the focus to a recommended norm.

### Hypothesis
A series of images taken along the Z-axis at X0Y0 provides information sufficient to 
determine the focal range in real world coordinates (vs. raw machine coordinates)

### Considerations

**Raw machine coordinates** are uncorrected machine coordinates. There is no guarantee
that a 1mm move in raw machine coordinates will actually move the effector 1mm or even
move the effector in the desired direction. 

**Measure at X0Y0** to reduce raw machine coordinate error. Moves along X0Y0 require identical
delta motor translations at each step.

**Use the calibration grid** to correlate actual Z-height with raw Z-height.

**Home before each measurement.**
For consistency, each measurement is preceded by G28 G0Z0M400 to ensure that the
effector starts at a known position. This is time-consuming, but eliminates uncertainty 
from the procedure.

**Take lots of samples.**
The FPD is accurate, but not reproduceably accurate to 0.1mm. However, multiple samples
provide more information and therefore greater accuracy. We will take 3 images at each
Z coordinate and repeat the entire process 3 times.

### Method
<a href="https://www.youtube.com/watch?v=ZUBUSP92gG8">Video</a>

1. We will probe at X0Y0 to various Z-depths, in increments of 1mm from Z30 to Z-110
1. At each Z-depth, we will take three images (IMG1, IMG2, IMG3)
1. We will repeat the probing of Z-depths three times (REP1, REP2, REP3)
1. Simon will figure out something to determine the focal range

### Results

TBD




