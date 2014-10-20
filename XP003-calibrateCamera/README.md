### Experiment XP003 calibrateCamera

### Q: How many images does it take to calibrate a pick-and-place camera?

If you search Google for "camera calibration opencv", you will eventually get
to a video of someone holding a very large chessboard moving all around a room
in front of the camera. Basically, the chessboard provides calibration data for
a view of a large 3D space. You will also find many people asking how to calibrate
their camera. To these forlorn queries, the answer often given is, "take more pictures".
In this experiment we explore camera calibration for pick-and-place. We will
also look very carefully at the brute-force advice to "take more pictures", 
which sounds suspiciously like advising a hunter to simply use a larger shotgun.

* [OpenCV camera calibration](http://docs.opencv.org/doc/tutorials/calib3d/camera_calibration/camera_calibration.html)

But first, let's examine pick-and-place computer vision requirements.

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

How do these pick-and-place vision requirements affect camera calibration?

### Hypothesis
Using [FireSight matchGrid](https://github.com/firepick1/FireSight/wiki/op-matchGrid),
it is possible to calibrate the FirePick Delta downward-looking camera with
a single image.

### Considerations

**Calibrate by matching image/object points**. 
[OpenCV cameraCalibrate](http://docs.opencv.org/modules/calib3d/doc/camera_calibration_and_3d_reconstruction.html#calibratecamera)
takes matched sets of image and object points and uses these sets to generate calibration 
matrices and coefficients. The OpenCV function does not actually inspect the image. In fact,
OpenCV just uses one or more matched sets of image/object points. It should therefore be
possible generate these matched sets from a single image.

**Calibrate one parameter at a time**
When you look at the chessboard video, you'll notice that the chessboard is small when it is far away.
That obvious statement suggests that a matched set of image/object points need not occupy the
entire image. In fact, you will see the chessboard essentially move to all the image quadrants,
occupying a small sections of the total image on each successive image. Images taken at the edge
of the frame are used to determine specific distortion coefficients (e.g., radial or tangential).
Foreshortened images taken in center of the frame are used to determine X- and Y- focal lengths and
to locate the principal (Px,Py) point. This suggests that a carefully chosen set of calibration
images (or matched sets) might work as well and even better than a massive number of random images,
especially if the matched sets chosen can be used to each determine a single calibration parameter.
Since camera calibration is generally time-consuming, a reduction in the number of required images
is quite beneficial to end users.

**Use a simple line grid**
OpenCV can calibrate to chessboards or circle paterns. A small chessboard is very recognizable in a
large 3D space since the chessboard is a grid of crash dummy symbols (i.e., black and white squares 
meeting at right angles).
Circle patterns (symmetric or asymmetric) also work for calibration. What all these three methods
have in common is that they have easily
recognized local features grouped into a recognizable large pattern. With this requirement
in mind, it becomes clear that a simple line grid will work just as well. In fact,
the simple line grid can provide just as many features as a very large chessboard, but
with a lot less ink.  A simple line grid is also more robust at different magnifications
because we don't have to specify things like minimum and maximum diameters.

**Define a calibration metric.** The OpenCV cameraCalibrate function returns the RMS reprojection error
of the identified object. The one difficulty about this measure is that if the original image is foreshortened
due to perspective, then the RMS error will be with respect to a foreshortened grid, not a flat, uniform
cartesian grid. We will therefore use the RMS error from a flat uniform cartesian grid to choose the
best calibration method.

### Method
1. Our measured object will be a single picture of the standard FirePick 5mm calibration line grid.
1. We will use [FireSight matchGrid](https://github.com/firepick1/FireSight/wiki/op-matchGrid) to identify the grid intersections and their row/column arrangement.
1. We will choose and evaluate different combinations of one or more subimages for minimum RMS error from 
an ideal cartesian grid instead of the calibrateCamera RMS error. 

### Results
Using the [images](img) created by `image`, the `process-grid` script 
generated [raw data](process-grid.out) summarized in the table below:

<img src="img/sample-match.png"/>



