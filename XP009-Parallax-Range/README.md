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

In the above diagram, the orange triangle represents the _near field of view_, while the blue triangle represents
the _far field of view_. These triangles are similar because they both have the same angular field of view,
which allows us to use simple ratios to find the range. In particular, the near-height *h2* is related
to the _near-far_ distance *dh* as follows:

`dH = h2 * (l1/l2 - 1)`

In other words, if we know the _far-near_ change in height as well as the ratio of near/far images *l1/l2*, we
can calculate the near-height *h2*.

### Method
To illustrate the near-far range finding technique, we use two pictures of the floor taken at different
heights.

Near the floor:

![](https://github.com/firepick1/fpd-vision/blob/master/XP009-Parallax-Range/img/FloorNear.jpg)

Far from the floor:

![](https://github.com/firepick1/fpd-vision/blob/master/XP009-Parallax-Range/img/FloorFar.jpg)

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

