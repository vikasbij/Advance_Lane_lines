## Self Driving Car Program
### Advanced Lane line Finding Project


---

**Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

![Chessboard_distorted_and_undistorted_images][/output_images/Chessboard_undistorted_image.png]
![Undistorted_image][/output_images/UNDISTORTED_IMAGE.png]
![HLS_thresholded_image][/output_images/hls_thresholding.png]
![histogram_of_test_images][/output_images/histogram.png]
![Final_output_images][/output_images/final_output_image.png]


## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located in "./advanced_lane.ipynb"

I used cv2.findChessboardCorners() to find the corners in some sample Chessboard images and with the help of them, i used  cv2.drawChessboardCorners() to draw line between corners of the image. Then i used 

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function. Now these camera callibration and distortion coeficients are used to find distortion in images all over the video. Below are the distorted Chessboard image and Undistorted Chessboard image.

![Chessboard_undistorted_image][/output_images/Chessboard_undistorted_image.png]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![Undistorted_image][/output_images/UNDISTORTED_IMAGE.png]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I tried using several combinations like combination of clor threshold, direction/magnitude thresholds. But finally i got perfect results when I used a combination of sobel_x and hls thresholds. Here's an example of hls thresholding and then warping the image:

![thresholded_image][/output_images/hls_thresholding.png]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform is under title "Warping the image". For this i have made function warp_image_fn() which takes image as an input.

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 577, 460      | 400, 0        | 
| 705, 460      | 860, 0        |
| 260, 684      | 400, 720      |
| 1050, 684     | 860, 720      |

Also I verified them with some of the test images and they were looking fine. Below is one of the example of how warped image looks like:

![warped_image][/output_images/warped_image_1.png]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

I took histogram of the thresholded image along all the columns and on the basis of that,  I identified those pixels using euqation f(y) = A.x** + B*y + c. I have used this equation for left and right  lane lines. After that i got the lane lines, i filled those lane lines with green color. These all calculations are done under the function pipeline().  Below are the histograms of some of the test images:

![histogram_of_images][/output_images/histogram.png]



#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I took help of classroom materials to calculate radius of curvature of the lane. Below is the formula which i used for this:
```
curveradius = ((1 + (2*fit_cr[0]*y_eval*ym_per_pix + fit_cr[1])**2)**1.5) / np.absolute(2*fit_cr[0])
```

Also i calculated curve radius for both left and right lane lines and calculated average of them. Below is how i did that:
```
    left_curve_rad = return_radius_of_curvature(left_fitx)
    right_curve_rad = return_radius_of_curvature(right_fitx)
    average_curve_rad = (left_curve_rad + right_curve_rad)/2
```

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

Below is the image after i performed the required steps in my code:


![final_output_image][/output_images/final_output_image.png]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [Output Video][/test_videos_output/.mp4]

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Earlier I was facing problem as my code was failing while differentiating between car and lane lines. Then i tried using color, magnitude and direction thresholding. Still the result was not upto the expectations. Also i tried different values of R, G and B to check how the i am getting my output images. But those values were not enough for me to get perfect results. So, I applied Sobel_x and region masking. 

But still my code was failing when there were shadows in the image. For that i used HLS image threhsholding. I used Light and Saturation thresholding with values more than 120 and 150 respectively. This helped me to overcome the issue and get a good result
