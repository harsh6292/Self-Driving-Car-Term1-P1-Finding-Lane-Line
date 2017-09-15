# **Finding Lane Lines on the Road**

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

[image_original]: ./test_images/solidWhiteCurve.jpg "Color Image"
[image_grayscale]: ./test_images_intermediate_output/graysolidWhiteCurve.jpg "Grayscale Image"
[image_gaussian]: ./test_images_intermediate_output/gaussiansolidWhiteCurve.jpg "Guassian Blur Image"
[image_canny]: ./test_images_intermediate_output/cannysolidWhiteCurve.jpg "Canny Edge Image"
[image_masked]: ./test_images_intermediate_output/maskedsolidWhiteCurve.jpg "Masked Image"
[image_hough]: ./test_images_intermediate_output/houghsolidWhiteCurve.jpg "Hough Transform Image"
[image_weighted]: ./test_images_output/solidWhiteCurve.jpg "Final Image"

### Pipeline description
In the lane lines project, I followed the example presented in tutorials and created my pipeline consisting of 8 steps, which are as follows:

1. The first step was to load all the images from 'test_images' directory as RGB images and then applying steps 2-8 to all the images inside this directory.

    ![original][image_original]

2. The next step was to convert this RGB image into grayscale image. This is done to get accurate boundaries of lane lines. The lane lines are little brighter than their surroundings (black road) and hence grayscale image gives better gradient to separate lane lines from road.

    ![original][image_grayscale]

3. After converting to grayscale, next step was to apply smoothing to the image, aka Gaussian blurring. The Gaussian blurring step helps to reduce noise in the image, that is areas which are exceptionally bright or dark are averaged out so as to make better sense of where lane lines are.

    ![original][image_gaussian]

4. The next major step is to apply Canny Edge detection to the grayscale smoothed image. The canny edge detection is used to get an image containing only lines which have a strong gradient, that is areas where pixel values are changing rapidly when compared to other neighboring pixels. The optimal value of low threshold and high threshold which are passed into canny function were determined after running several tests to get best edges in image.

    ![original][image_canny]

5. Next step was to mask the image in such a way that only the area containing lane lines is considered when filtering out which lines to choose from canny output. This region of interest is found by creating a polygon with four vertices defining an area with lane lines.

    ![original][image_masked]

6. Once a masked image is obtained, I used Hough transform to get all the lines in the image where there is more probability of lane lines. This is achieved by considering each pixel or point in masked image space as a line in Hough space. In Hough space, we can then give weights (threshold) to each of the lines which intersect and consider only those lines which have high number of intersection. This will give us points in image space that correctly represents the lane lines.

    ![original][image_hough]

7. The Hough transform gives us all the lines which it thinks matches the criteria. The draw_lines() function is now used to again filter out lines from these set of lines which actually represent lane lines. The following steps describe how I approached and modified the draw_lines() function to obtain lane lines:

    I. Since we are interested in lines which form either left part of lane or right part, it is easy to separate them using the slope of line. A negative slope indicates the line belongs to left side and vice versa.

    II. Next, I ignored all lines which are horizontal, that is their slope is either 0 or infinity.

    III. Then I collected all points belonging to left lane and right lane respectively and use them to find mean values of coefficient of line (m, b).

    IV. Using the mean coefficients and fixed extreme values of y points in image (Lower part of road and middle part of road), I found the x co-ordinates. In the end, I draw two lines (left and right) using these co-ordinates to finally represent the actual lane lines in red.

8. The resulting image obtained from Step 7 is then superimposed on original image to correctly map the actual lanes with what algorithm found to help car keep in lane.

    ![original][image_weighted]




### 2. Identify potential shortcomings with your current pipeline

A potential shortcoming for the algorithm would be that it relies on hard-coded values for region of mask. This region of mask would fail or perform poorly in cases where road curvature radius is large.

Another shortcoming resulting from this would be that I try to draw lane line from bottom point to top point as a single straight line. Once road curves, algorithm would fail in sense that only straight line will be observed while actually road is curving. This can result in serious consequences of crashing with another car.



### 3. Suggest possible improvements to your pipeline

A possible improvement would be to instead fit a line, the algorithm tries to fit a 2-degree polynomial. This will ensure when road curves, the algorithm would also give a curved lane guide segment instead of single line.

The region of interest can be learned using machine learning algorithms so as to learn by error instead of hard-coding the region of interest bounding values.
