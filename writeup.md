

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./test_images_output/solidWhiteCurve.jpggray.jpg "Grayscale"
[image2]: ./test_images_output/solidWhiteCurve.jpgblur.jpg "Blurred"
[image3]: ./test_images_output/solidWhiteCurve.jpgedges.jpg "Edges"
[image4]: ./test_images_output/solidWhiteCurve.jpgmasked.jpg "masked"
[image5]: ./test_images_output/solidWhiteRight.jpgfinal_image.jpg "final"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps:
* Step 1 : Convert to grayscale
  >First the image is converted to grayscale by retrieving the green channel of the image.
   I've used the green channel because its normal for camera to encode images with more bits to either red and green channels.
   Thus using a single green channel is normally more efficient than using RGB conversion to grayscale.
  ![alt text][image1]

* Step 2 : Image blurring
 > By blurring the image, we can have a more robust edge detection.
 It removes noise and small artifacts that may influence the quality of edge detection.

  ![alt text][image2]

* Step 3 :  Edge detection
> This step does edge detection using the Canny algorithm.
  The implementation of the Canny edge detection in opencv does not include the low pass filter used at the first stage. This is the reason we do it  (image blurring) in step 2.
    ![alt text][image3]

* Step 4 : Mask Edges with ROI
 >  We define a trapezoid region of interest in which we mask the important edges to be further processed.
 ![alt text][image4]

* Step 5 : Use Hough Transform to find line candidates.
> Now that we have the edge we transform the edges  parametric space using the so calle Hough Transform.
With it we can find candidates lines.

In order to draw a single line on the left and right lanes, I modified the draw_lines() function:

> Using the lines candidate of our pipeline (output of step 5) we define two group of lines:
 * We first group all the lines with a slope > 0.45 to be used as detection for detection of the right lane.
This threshold is not very restrictive but eliminates horizontal lines and other false negatives.
Using this group of lines we compute the mean line parameters so we could draw a single line for the right lane.

>* We then repeat the process for another groups of line with slope < - 0.45.
The mean line of second group is the line of the left lane.

>The final result is
 ![alt text][image5]


### 2. Identify potential shortcomings with your current pipeline

Some of the potentail shortcomings:
* When the car is in a intersection, it would potentially mix up the lanes.
* When the car is doing a sharp curve a line is not an ideal lane detector.
* When you have a car changing lanes in front of the camera the detector can loose track.
* We rely on lanes marking to find the lanes. But these marks are not available or not visible under snow or heavy rain.



### 3. Suggest possible improvements to your pipeline


* We can use RANSAC algorithm to find consensus and outliers in the lane detection. This approach is more robust; but for the examples videos shown here was not really needed. The filtering of the slope was enough to tackle even the "challenge video".

* The current approach is not taking into account the current orientation of the car wheels when doing lane detection. This can potentially help our lane detection.

* Have a data driven approach solution to detect lanes: Assuming we can collect a large set of training data we can train a deep convolutional neural network to do lane prediction/segmentation for us. If the training data is large enough it is likely that this solution would out-perform hand engineering features and solutions.

* We are not predicting what are the lanes of the next frame. It would be beneficial to have a prediction system in case the lane detection fails. This way we could rely, with a degree of uncertainty, on the prediction.
