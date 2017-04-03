##Writeup
###CarND-Advanced-Lane-Lines

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

[image1]: ./output_images/undistorted.jpg "Undistorted"
[image2]: ./output_images/proj_corrected.jpg "Road Transformed"
[image3]: ./output_images/combined.jpg "Binary Example"
[image4]: ./output_images/warp.jpg "Warp Example"
[image5]: ./output_images/fit_lines.png "Fit Visual"
[image6]: ./output_images/output.jpg "Output"
[image7]: ./test_images/test6.jpg "Original Image"
[video1]: ./project_video.mp4 "Video"


## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###Writeup / README

####1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  
This is it!

###Camera Calibration

####1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the second code cell of the IPython notebook located in "./examples/example.ipynb" (or in lines # through # of the file called `some_file.py`).  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

###Pipeline (single images)

All images following are based from this image:
![alt test][image7]

####1. Provide an example of a distortion-corrected image.
Box 6 of the notebook defines and uses the `distortionCorrection()` method. This function calls `cv2.undistort(img, mtx, dist, None, mtx)` with the necessary attributes obtained in the camera calibration step.
![alt text][image2]


####2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.
I used a combination of color and gradient thresholds to generate a binary image (thresholding steps in box 10 define the methods and box 12 utilizes them while defining the method `pipeline()` for image processing).  Here's an example of my output for this step.
![alt text][image3]

####3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `unwarp()`, which appears in box 7 and is applied in box 8.  The `unwarp()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

```
src = np.float32([(595,450),
                  (690,450), 
                  (275,670), 
                  (1034,670)])
dst = np.float32([(350,0),
                  (w-350,0),
                  (350,h),
                  (w-350,h)])

```

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

####4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Box 15 and 16 show the fitting process. Box 15 is the sliding window poly fit while 16 is the fit if lines had been found previously. Fit lane lines with a 2nd order polynomial from the sliding window will look like this:

![alt text][image5]

####5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in box 19 of the notebook. I used the conversion:

```
# Define conversions in x and y from pixels space to meters
    ym_per_pix = 3/95 # meters per pixel in y dimension
    xm_per_pix = 3.7/600 # meters per pixel in x dimension
```
to convert from pixel space to real world space and then calculated the curve according to the formula given in class.

####6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in boxes 21 - 24. The function `draw_lane()` will draw the lane lines as well as the filled in section, while the function `add_data-to-image()` added the curve and offset from center data to the image.  Here is an example of my result on a test image:

![alt text][image6]

---

###Pipeline (video)

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video.mp4)

---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I used a lot of the techniques demonstarted in class. To fine tune my pipeline, I experimented on the test images, and extracted a few of my own. I have a couple of wobbily sections (minor) that could likely be fixed by utilizing fit averaging (I was having issues implementing this feature). My pipeline will likely fail any time there are lines more defined than the lane lines, such as the challenge video. I will continue to improve this in the future with things like fit memory, better binary images, multiple processing techniques if lanes arent found the first time, etc.

