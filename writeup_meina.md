#**Finding Lane Lines on the Road** 



---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:

* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"
[image2]: ./test_images_output/processed_image.png "processed"

---

### Reflection

###1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of following steps:

1. Convert the images to grayscale, using the cv2.cvtColor() function. 

2. Apply Guassian smoothing to the grayscale images, using the cv2.GuassianBlur() function.

3. Apply the Canny algorithm to detect all edges in the smoothed images, using the cv2.Canny() function.

4. Define a four sided polygon region of interest as mask, and extract the edges only in the masked region. In defining the mask, cv2.fillPoly() function is used.

5. Run Hough transformation on the masked Canny edges to find all lines present in the image, using the cv2.HoughLinesP() function. 



The lines can be separated into left and right lines, making the left and right lane edges. In order to draw a single line on the left and right lanes, I modified the draw_lines() function to include more steps:

1. Separate the lines to left and right parts by their slopes. With slope > 0, the line is considered to be the left line and the end points are appended to left_points list. For slope <= 0 the line is considered to be the right line and the end points are appended to right_point list.

2. Run a 1-degree polynomial regression on the left_points array. Fit and get the equation (y=a*x+b) for the left line. Store the coefficients a and b as left_coefficients, and the line as line_left.

3. Read the four corners of the mask. Use the horizontal coordinates of the two left corners to calculate the corresponding y coordinates according to the equation of the line_left. The resultant pair of points are the end points of the final left line.

4. Draw the left line using cv2.line() function on top of the original image.
5. Repeat steps 2-4 for the right line.

6. Four corners of the masked are also drawn as blue circle for reference.

The modified function is in draw_lines_single() function.

Example of a processed image is shown below, with two solid red lines drawn for left and right lane edges:

![alt text][image2]


###2. Identify potential shortcomings with your current pipeline


1. For the moment the determination of masked region is pretty rigid. This could cause problem if the masked region does not overlap well with the area that contains the actual lanes. 

2. The current pipeline worked most of the time, but for several frames (less than 0.5%) the detected lines deviated from actual lanes. 

3. The detected lines flickers quite a lot. 




###3. Suggest possible improvements to your pipeline

1. Implement a smarter way to determine the masked region. 

2. Implement multiple stages of filters before feeding into Canny function. This could improve the accuracy of the program.

3. Implement a time-averaged algorithm - i.e. the line detected also depend on previous frames. So the lines detected will be much smoother and not flickering.