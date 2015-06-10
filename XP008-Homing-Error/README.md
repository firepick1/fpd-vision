### Rotational Delta Homing Error
FirePick Delta (FPD) relies on rotational delta kinematics. As such, it
is subject to a source of error that does not affect cartesian
or even linear delta machines. That error is homing error, which
is the XYZ position error introduced by homing angle errors.

In a traditional cartesian machine homing error is linear.
A 0.1mm error in homing results in a 0.1mm error for the
entire axis. In addition, a homing error on any axis only
affects that axis. With optical limit switches, we can 
control that error over many repititions after a simple
one-time calibration.

For rotational delta machines such a FPD, the homing error is
non-linear and affects ALL points in XYZ cartesian space.
Worse, since the error is non-linear, it differs at each point.
The magnitude of the homing positioning error is also surprising.
A homing angle error of as little as 1 degree can lead to XYZ
cartesian space errors of over 3mm.

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
ignore axis homing error for now and consider the effect
of system homing error.

Consider a system homing error of +/- 2 degrees. This small
variation is quite possible. Indeed, the FPD beta kit and
LooseCanon designs most probably differ in their homing angles by 
this amount, perhaps more.

Let's consider a straight path from (-100,0,-70) to (100,0,-70).
This is a 100mm line along the X-axis. A system homing error
of +/- 2 degrees affects all three XYZ axes. The error impacts
Z severely and leads to the commonly reported "bowl-shaped error".

![](https://github.com/firepick1/fpd-vision/blob/master/XP008-Homing-Error/img/ZHomingError.png)

The chart shows that a single degree of system homing error introduces
~3mm of Z error between the origin endpoint and the remote endpoint
of our supposedly straight path. A two degree error has even
greater effect on Z at 100mm from the origin. The error is also
non-linear and it is indeed "bowl shaped".

The error also affects the X and Y axes. 
The effect on the Y-axis
is quite startling and leads to a surprising non-linear curve:

![](https://github.com/firepick1/fpd-vision/blob/master/XP008-Homing-Error/img/YHomingError.png)

Although disturbing by its magnitude, rotational delta system homing error
is actually easy to correct. All we need to do is introduce a homing
angle error parameter for each FPD motor axis.

Calibration is also quite robust because the system homing error
of a horizontal line on the X-axis bends the line up or down
on the Y-axis and can therefore be easily measured with a
camera mounted on the effector.

