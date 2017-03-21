# **Finding Lane Lines on the Road** 
# Author: Alexander Chrusciel

---

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./test_images_output/out2solidWhiteRight.jpg "Solid White Right - Pipeline"
[image2]: ./test_images_output/outsolidYellowCurve.jpg "Solid Yellow Curve - Wrong calculation of right line"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.


My pipeline consists of five steps:

1) Parameter region: parameters required for the different algorithms to be used throughout the image processing.
2) Image preprocessing: Conversion to gray, gaussian smoothing, identify edges with canny
3) Selection of the region of interest: definition of a polygon which can be roughly described as a trapezoid
4) Detection of lines using HoughLinesP-algorithm
5) Combine the original image with the detected lines

Below is an illustration of the output of the major steps in the pipeline:
![alt text][image1]

### 2. Improving the draw_lines()-method
When the lines are continuous there was no need for improvement. But when the lines are dashed than in the lower part of the image no line could be detected by the algorithm. Therefore I implemented the following algorithm:
1) Calculate the slope of a line
2) Decide based on sign of slope whether it is a line which belongs to a left line or to a right line
3) For all left_lines (right_lines) compute x1, x2, y1, y3
	For the left lines x1 and y1 needed to be computed (left bottom). Calculating the slope and using the delta values when set y1 to img.shape[0] the value for x1 could be inferred. Analogous for x2 and y2 for the right lines.
4) In case no line information is available for a given image, no line will be drawn


###3. Identify potential shortcomings with your current pipeline

1) My extrapolation logic in the draw_lines()-method requires an adjustment parameter, which increases the calculated slope for the final lines (current value = 2.0). This is required at this moment as the calculation of the lower left (lower right) x-values is too high (too small).
2) The pipeline works at this moment only with the provided test data. Different kind of streets or different colors (e.g. at night) need to be testet as well.
3) In case an image does not provide a line information, the drawing of lines is omitted. A heuristic might be necessary to use historical information and assume a line as long as the incoming images do not provide new line information.

###4. Suggest possible improvements to your pipeline

1) The calculation of the extrapolation could be improved in order to remove the introduced adjustment parameter. Maybe some APIs in cv2 exist which could support.
See image below. On the right lower side of the image the calculated line does not fill well the whole line. In the videos this leads sometimes to jumping lines.
![alt text][image2]

2) The definition of the polygon which defines the region of interest was created with trial and error and is heavily dependend on the cameras properties (resolution, amount of space recorded, angle of the mount). At this moment hard coded values for the shape of the polygon might lead to false calculations of lines, when other hardware is used or if e.g. the car drives down/up a hill. This area of improvements holds to all hard coded parameters.