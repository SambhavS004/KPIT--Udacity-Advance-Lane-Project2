## Writeup 

## Overview

In this Project we were asked to Identify the Lane Lines just by the Camera Images which is supposed to be mounted on the to top of the Car. It requires a Pipeline to process the Images coming from the Camera Input to run that into the video and find the Lane Lines correctly with the metrics showing on the video.


---

**Advanced Lane Finding Project**

## Below are the Steps which were used to reach out to the output i.e. Project Video Solution file

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./examples/undistort_output.png "Undistorted"
[image2]: ./test_images/test1.jpg "Road Transformed"
[image3]: ./examples/binary_combo_example.jpg "Binary Example"
[image4]: ./examples/warped_straight_lines.jpg "Warp Example"
[image5]: ./examples/color_fit_lines.jpg "Fit Visual"
[image6]: ./examples/example_output.jpg "Output"
[video1]: ./project_video.mp4 "Video"

### Step1: Camera Calibration

#### Calculating the Camera Calibration using Chessboard Images provided to us

The code for this step is contained in the first code cell of the IPython notebook "Advance_Lane_Lines_Detection_Project"

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![Corner Detection][writeup_images/Corner_detection.png]

### Step2: Undistorting an Image
#### Undistorting the Image using cv2.undistort(). The undistorted image result is attached in the Notebook.

![Undistorted Image][writeup_images/undistort.png]

### Step3: Calculated Gradient Threshold, Color Transform to create a Thresholded Binary Image
In this step we have to use a combination of below thresholds which were calculated separately in my Notebook starting from Cell# 5 to 9

#### Calculate the Directional Gradient by abs_sobel_thresh() function
![Undistorted Image][writeup_images/grad_thresh.png]

#### Calculate the Gradient Magnitude by mag_thresh()
![Undistorted Image][writeup_images/mag_thresh.png]

#### Calculate the Gradient Magnitude by dir_threshold()
![Undistorted Image][writeup_images/dir_grad.png]

#### Calculate the Color Threshold by hls_thresh()

#### Combine all the gradient and check the results
![Undistorted Image][writeup_images/comb_thresg.png]



### Step4: Apply the Perspective Transform to get a Birds-eye-view

In this Step, we have to select the coordinates in the image which will create a trapezoid and from birds view it should look like a rectangle.
A combination of Source and destination coordinates will then be created in the clockwise direction and then OpenCV function getPerspectiveTransform() is used to calculate the Perspective Tranform Matrix M and the Inverse Perspective Tranform Matrix Minv
These Matrix will the be used to warp and unwarp the images.

The code is present in the Notebook cell# 10


```python
        src_coordinates = np.float32(
            [[280,  700],  # Bottom left
             [595,  460],  # Top left
             [725,  460],  # Top right
             [1125, 700]]) # Bottom right
        
        dst_coordinates = np.float32(
            [[250,  720],  # Bottom left
             [250,    0],  # Top left
             [1065,   0],  # Top right
             [1065, 720]]) # Bottom right 
```

![Perspective Tranform][writeup_images/warp_img.png]

### Step5: Identified lane-line pixels and fit the Lane boundary

Have created a Histogram for the Lower half of the images

![Histogram][writeup_images/histo.png]

Then, the starting left and right lanes positions are selected by looking to the max value of the histogram to the left and the right of the histogram's mid position.
Have used Sliding Window mechanism to identify the most likely coordinates of the lane lines in a window, which slides vertically through the image for both the left and right line.
Finally, a second order polynomial is calculated for both the left and right lane line. 
The code is present in cells 12, 13 and 14 in the Notebook.

![Sliding Window][writeup_images/sliding_window.png]

### Step6: Calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

Have calculated the curvature, Car Offset, in the Notebook cells# 17, 18, 19 and 20

![Curvature][writeup_images/curva.png]

![Car Offset][writeup_images/offset.png]


### Step7: Result plotted back down onto the road such that the lane area is identified clearly.

Drawing lanes onto the warped blank image using fillPoly() and then blank will be warped back to original image space using inverse Perspective Matrix Minv

The code can be found in the Notebook cell# 21

![Lane Area Detection][writeup_images/lane_detected.png]

### Step8: Displaying the Lane Boundaries and Metrics Lane Curvature and Vehicle Position

Code can be found in the Notebook Cell# 23 and 24

![Lane Area Detection][writeup_images/metrics.png]

---

### Pipeline (video)

### Step9: The final solution. This is just a pipeline of the above functions. The complete Image Processing Pipeline then will transform the Camera Inputs to clearly Detect the Lanes.

Here's a [link to my video result](./project_video_solution.mp4)

---

### Discussion

It was really a knowledgable Project for me. I started liking OpenCV, I mean you just have to put your inputs to these Magic Function and wait for the results.
There is a lot of room for the improvements in my Projetc. Due to my Day Job and Paucity of time, I was not able to optimize the detections and the problem can be seen in the Sliding Window Mechanism, where the likelyhood of the coordinates are not woriking properly.
I think I just need to use the Orientation in x(orient= 'x') and have to neglect the orient= 'y' while deriving the combined threshold.

I can see that Detection is not working properly in the Tree Shadow which can be further optimized by Smoothning, but due to Paucity of Time I am not doing this time and will complete that after this Project submission.

Throughout this Project, What really impressed me is the OpenCV. 
