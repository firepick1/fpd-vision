### Parallax Near/Far Rangefinder
The simple geometry of [parallax](https://en.wikipedia.org/wiki/Parallax) has been used
in [coincidence rangefinders](https://en.wikipedia.org/wiki/Coincidence_rangefinder) that
compare two lateral images of the same object to triangulate the range. 
All of us also relies on the parallax introduced by the lateral separation of our eyes for binocular rangefinding.

However, in this experiment, we explore a different parallax method for rangefinding.
In particular, we use a single camera to compare images of the same object taken at different
distances. Let's call this _far/near rangefinding_. Since an object appears to shrink as it moves
away from the observer, parallax allows us to calculate the range based on the change in images.
far/near rangefinding is especially suited to pick-and-place, where the objects to be manipulated
all lie more or less in the same plane orthogonal to the camera.

<a href="https://github.com/firepick1/fpd-vision/blob/master/XP009-Parallax-Range/img/parallax.png">
    <img src="https://github.com/firepick1/fpd-vision/blob/master/XP009-Parallax-Range/img/parallax.png" height=300px></a>

In the above diagram, the orange triangle represents the _near field of view_, while the blue triangle represents
the _far field of view_. These triangles are similar because they both have the same angular field of view,
which allows us to use simple ratios to find the range. In particular, the near-height *h2* is related
to the _far/near_ distance *dh* as follows:

`dH = h2 * (l1/l2 - 1)`

In other words, if we know the _far-near_ change in height as well as the ratio of far/near images *l1/l2*, we
can calculate the near-height *h2*.

### Method
To illustrate the far/near range finding technique, we use two pictures of the floor taken at different
heights.

Near the floor:

![](https://github.com/firepick1/fpd-vision/blob/master/XP009-Parallax-Range/img/FloorNear.jpg)

Far from the floor:

![](https://github.com/firepick1/fpd-vision/blob/master/XP009-Parallax-Range/img/FloorFar.jpg)

The distance between these images is 6", which gives us one required piece of data.
Determining the image far/near ratio is trickier.

### Image far/near ratio
For best accuracy, we want to make full use of our two images. We could use the ratio of the 
the foot separation or the ratio of the iPad sizes, but these occupy only a small part of the
image and will give us less accuracy. Instead, we will use two rectangles separated by the
full width of the image to give us the maximum baseline:

![](https://github.com/firepick1/fpd-vision/blob/master/XP009-Parallax-Range/img/matchnear.jpg)

We will crop each of these rectangles separately for use as OpenCV image templates:

![](https://github.com/firepick1/fpd-vision/blob/master/XP009-Parallax-Range/img/l-tmplt.jpg)
![](https://github.com/firepick1/fpd-vision/blob/master/XP009-Parallax-Range/img/r-tmplt.jpg)

And we will use `firesight` _matchTemplate_ to locate these templates in the far image:

![](https://github.com/firepick1/fpd-vision/blob/master/XP009-Parallax-Range/img/matchfar.jpg)

