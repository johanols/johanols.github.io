---
layout: post
title:  "Rotate images with OpenCV"
date:   2014-07-07 20:13:00 CEST
categories: [opencv, java, android]
---

In an attempt to rotate an image using the image processing framework [OpenCV][opencv] I first came up short since there are no pure method for this operation to be found. After scouting the [documentation][doc] the closest I came a rotation method was `getRotationMatrix2D` which in conjunction with `warpAffine` are able to rotate an image but only within its bounds. This was not quite the desired behaviour since I wanted  the bounds to rotate aswell.

![Image before and after rotation with OpenCV getRotationMatrix2D and warpAffine]({{ site.url }}/assets/images/rotating-image-warpaffine.jpg)

The above picture has been rotated using the method mentioned. It's clear how the image has indeed been rotated within its bounds clipping the top and bottom in the process.

So how to achieve the behavior we want? It's worth mentioning that it is possible to achieve by translating the image into place after the image has been warped. However, this requires calculating the delta x and y values for translating the image into place. Seems like it's a lot of unnecessary work just to rotate an image. This shouldn't be that complicated at all!

Luckily there is a better solution and a quite elegant one aswell. Since all pixels are arranged in a matrix structure, like how the image is shown on the display, one can simply relocate the entries in the matrix to relocate the corresponding pixel. The trick here is to [transpose][transpose] the pixel matrix turning the rows of the image into columns and vice versa. Alone, this operation will rotate the image the 90 degrees desired. But there is one small problem, the image has been mirrored in the process. Solving this is easy though, just flip the image around its y-axis and the 90 degrees counter clockwise rotation will be completed.

{% highlight java %}
String filePath = "/images/cobblestone-road.jpg";
Mat image = Highgui.imread(filePath, Highgui.CV_LOAD_IMAGE_UNCHANGED);
Core.transpose(image, image);
Core.flip(image, image, 1);
Highgui.imwrite(filePath, image);
image.release();
{% endhighlight %}

Repeating the process will of course rotate the image another 90 degrees, ending up with a 180 degrees rotation total.

[opencv]:		http://opencv.org/
[doc]:			http://docs.opencv.org/
[transpose]:	http://en.wikipedia.org/wiki/Transpose