### Parallax Near/Far Rangefinder
The simple geometry of [parallax](https://en.wikipedia.org/wiki/Parallax) has been used
in [coincidence rangefinders](https://en.wikipedia.org/wiki/Coincidence_rangefinder) that
compare two lateral images of the same object to triangulate the range. 
All of us also relies on the parallax introduced by the lateral separation of our eyes for binocular rangefinding.

However, in this experiment, we explore a different parallax method for rangefinding.
In particular, we use a single camera to compare images of the same object taken at different
distances. Let's call this _near/far rangefinding_. Since an object appears to shrink as it moves
away from the observer, parallax allows us to calculate the range based on the change in images.
Near/far rangefinding is especially suited to pick-and-place, where the objects to be manipulated
all lie more or less in the same plane orthogonal to the camera.

<a href="https://github.com/firepick1/fpd-vision/blob/master/XP009-Parallax-Range/img/parallax.png">
    <img src="https://github.com/firepick1/fpd-vision/blob/master/XP009-Parallax-Range/img/parallax.png" height=300px></a>


### Homing Error Categorization
FPD has three axes and therefore three sources of homing errors:

* theta1_error
* theta2_error
* theta3_error

It is useful to categorize these errors in more detail and
distinguish between *system homing error* as opposed to
*axis homing error*:

* theta1_error = systemHomingError + axis1_error
* theta2_error = systemHomingError + axis2_error
* theta3_error = systemHomingError + axis3_error

A variance in the FPD middle plate thickness affects
all axes and contributes to system homing error. A variance in optolimiter
assembly for an axis results in axis homing error for
that axis.

### System Homing Error
To understand the impact of rotational delta homing error, we will
ignore individual axis homing error for now and consider the effect
of system homing error.

Consider a system homing error of +/- 2 degrees. This small
variation is quite possible. Indeed, the FPD beta kit and
LooseCanon designs most probably differ in their homing angles by 
this amount, perhaps more.

Let's consider a straight path from (-100,0,-70) to (100,0,-70).
This is a 100mm line along the X-axis in the Z plane at -70. A system homing error
of +/- 2 degrees affects all three XYZ axes. The error impacts
Z severely and leads to the commonly reported "bowl-shaped error".

![](https://github.com/firepick1/fpd-vision/blob/master/XP008-Homing-Error/img/ZHomingError.png)

The chart shows that a single degree of system homing error introduces
up to ~1.5mm of Z error between the intended line and the actual path.
A two degree error has even
greater effect on Z at X=+/-100mm from the origin. The error is visibly
non-linear. Indeed, it is "bowl shaped".

The error also affects the X and Y axes. The effect on the X-axis
has an interesting shape (horizontal "S") but is subtle in nature,
making it slightly difficult to measure:

![](https://github.com/firepick1/fpd-vision/blob/master/XP008-Homing-Error/img/XHomingError.png)

Notably, the effect of system homing error on the Y-axis
is quite startling and leads to an easily recognizable non-linear curve:

![](https://github.com/firepick1/fpd-vision/blob/master/XP008-Homing-Error/img/YHomingError.png)

### Calibration
Although disturbing by its magnitude, rotational delta system homing error
is actually easy to correct. All we need to do is introduce a homing
angle error parameter for each FPD motor axis.

Homing error calibration should proceed in two steps

1. Measure system homing error
2. Measure axis homing error

For now we'll assume that the axis homing error is neglible
and consider the system homing error alone. Future study
will be needed to address individual axis homing error.

To measure system homing error, we need a homing error deviation that can be measured
by a downward looking effector camera. We have three possibilites.
X homing error is perhaps too subtle. The Z homing error is larger
and could be
used to detect homing error if the bed is perfectly level. 
But we can get the best accuracy by considering the Y homing error 
displacement at the horizontal line endpoints. 
If the line bends up, we have 
negative homing error. If the line bends down, we have positive
homing error. Using this simple metric, a genetic algorithm
can easily determine the actual system homing error.

