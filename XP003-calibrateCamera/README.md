### Experiment XP003 calibrateCamera

### Q: How many images does it take to calibrate a pick-and-place camera?

If you search Google for "camera calibration opencv", you will eventually get
to a video of someone holding a very large chessboard moving all around a room
in front of the camera. Basically, the chessboard provides calibration data for
a view of a large 3D space. You will also find many people asking how to calibrate
their camera. To these forlorn queries, the answer often given is, "take more pictures".
In this experiment we explore camera calibration for pick-and-place and
will look very carefully at the advice to "take more pictures".

* [OpenCV camera calibration](http://docs.opencv.org/doc/tutorials/calib3d/camera_calibration/camera_calibration.html)

But first, let's look at pick-and-place computer vision requirements.

The pick-and-place image space is much more 2D than it is 3D.
Pick-and-place use cases work with images of thin objects lying on a flat surface.
The foreshortened image
of a chessboard angled away from the camera just doesn't happen in pick-and-place. 
It might be necessary to recognize a DIP listing sadly on its side pins, but that's 
not a mainstream use case for SMD chips.
SMD chips are almost always either right-side up or upside-down. 
Additionally, it is very common in pick-and-place
use cases to use the image in determining an XY vector for motion planning, and 
the images must therefore be mapped to an XY cartesian grid such that objects are
the same size regardless of where they appear in the image.
For pick and place camera calibration, we therefore need to compute
camera coefficients that result in a dimensionally accurate
2D cartesian map of the objects presented to the camera.

How do the pick-and-place vision requirements affect camera calibration?

### Hypothesis
It is possible to calibrate the FirePick Delta downward-looking camera with
a single image.

* **<1mm** using [grid detection](https://github.com/firepick1/FireSight/wiki/op-matchGrid)

### Considerations
See [XP001-resolution](../XP001-resolution)

**Maximize feature separation**. The accuracy of Z-distance calculation is very much influenced
by the size of the measured object. Changes in perpective are scaled with the size of the object.
Larger objects provide more accuracy. A 5mm grid lets us treat three grid vertices in a row
as a single 15mm object, which is about 4x larger than the 4mm hole separation measured in 
[XP001-resolution](../XP001-resolution).

**Maximize recognized features**. Measuring more objects provides greater accuracy.
By using a full grid that covers the image, we can easily get more data than measuring
a single row of holes as in 
[XP001-resolution](../XP001-resolution).

### Method
1. Our measured object will be the standard FirePick 5mm calibration grid.
1. We will use `image` to take a series of 3 images at each point to determine vision accuracy
1. We will use nine Z planes (A,B,C,D,E) spanning Z=0 to Z=-4mm in 0.5mm increments
1. We will CNC reposition the camera at each point three times to determine movement accuracy
1. After each set of point measurements, we will return to a known position (arbitrarily Z=-10)
1. We will use `process-grid` to measure the object size using [matchGrid](https://github.com/firepick1/FireSight/wiki/op-matchGrid), which was developed for FireSight camera calibration.

### Results
Using the [images](img) created by `image`, the `process-grid` script 
generated [raw data](process-grid.out) summarized in the table below:

<img src="img/sample-match.png"/>

<img src="img/process-grid.png"/>

Of interest are the two far right columns of the table:

* **IMGVAR** shows the image variance over three images taken without repositioning the head
* **REPVAR** shows the positioning variance over three separate "random access" movements to a particular Z-position.

The image variance is remarkably, 10-100x smaller than the positioning variance, which suggests that
the camera is quite accurate in measuring Z-distance and that the differences in IMGAVG 
are mostly due to positioning.
The results indicate that the FirePick Delta camera can indeed be used to coordinate movement 
with a Z-accuracy of 0.5mm or better. Indeed, it would appear that optical bed levelling is 
quite feasible using a calibration grid as the camera objective.

