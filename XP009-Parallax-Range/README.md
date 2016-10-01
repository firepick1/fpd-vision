### Parallax Near/Far Rangefinder
The simple geometry of [parallax](https://en.wikipedia.org/wiki/Parallax) has been used
in [coincidence rangefinders](https://en.wikipedia.org/wiki/Coincidence_rangefinder) that
compare two lateral images of the same object to triangulate the range. 
Each of us also relies on the parallax introduced by the lateral separation of our eyes for binocular rangefinding.

However, in this experiment, we explore a different parallax method for rangefinding.
In particular, we use a single camera to compare images of the same object taken at different
distances. Let's call this _Far/Near Rangefinding (FNR)_. Since an object appears to shrink as it moves
away from the observer, parallax allows us to calculate the range based on the change in images.
FNR is especially suited to pick-and-place, where the objects to be manipulated
all lie more or less in the same plane orthogonal to the camera (see discussion below about "sufficiently coplanar").

<a href="https://github.com/firepick1/fpd-vision/blob/master/XP009-Parallax-Range/img/parallax.png">
    <img src="https://github.com/firepick1/fpd-vision/blob/master/XP009-Parallax-Range/img/parallax.png" height=300px></a>

In the above diagram, the orange triangle represents the _near field of view_, while the blue triangle represents
the _far field of view_. These triangles are similar because they both have the same angular field of view
provided by a fixed focus camera. This similarity
allows us to use simple ratios to find the range. In particular, the near-height *h2* is related
to the _far/near_ distance *dh* as follows:

`dH = h2 * (l1/l2 - 1)`

In other words, if we know the _far-near_ change in height as well as the ratio of far/near images *l1/l2*, we
can calculate the near-height *h2*.

### Method
To illustrate the FNR method, we use two pictures of the floor taken at different
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
full width of the image to give us the maximum baseline. We refer to the targeted regions used for matching
as _FNR regions_:

![](https://github.com/firepick1/fpd-vision/blob/master/XP009-Parallax-Range/img/matchnear.jpg)

We will [crop](https://github.com/firepick1/FireSight/wiki/op-crop)
these rectangles for use as FNR region OpenCV matching templates:

Left FNR region (red)-> ![](https://github.com/firepick1/fpd-vision/blob/master/XP009-Parallax-Range/img/l-tmplt.jpg)
Right FNR region (blue)-> ![](https://github.com/firepick1/fpd-vision/blob/master/XP009-Parallax-Range/img/r-tmplt.jpg)

We will then use `firesight` [matchTemplate](https://github.com/firepick1/FireSight/wiki/op-matchTemplate)
to locate these templates in the far image (see [process...](https://github.com/firepick1/fpd-vision/blob/master/XP009-Parallax-Range/process)):

![](https://github.com/firepick1/fpd-vision/blob/master/XP009-Parallax-Range/img/matchfar.jpg)

In the near image, `firesight` matches our templates with a 360 pixel separation.
In the far image, `firesight` matches our templates with a 306 pixel separation.
This yields a far/near ratio of 1.17847, which tell us that our camera was 34" from the floor
when taking the near image.

### Discussion
The primary advantage of the FNR method is that it does not require
a specific calibration target. The method just requires that the image have "distinguishable
features" for OpenCV template matching. In that regard, a featureless surface would not
work with this method. However, a typical printed circuit board should do quite nicely as a target.

Another requirement of FNR is that the imaged target regions be "sufficiently coplanar".
This requirement can be easily met if, for example, the image consists entirely of a blank printed circuit board or
a subsection thereof. If the imaged surface has an irregular distance profile from the camera, then FNR
degrades to the point of failure as the targeted FNR regions fail to match uniquely.

