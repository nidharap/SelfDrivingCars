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

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function. This can be found in cell #4 of Pipeline_Buildup_On_Test_Image.ipynb

### Pipeline (single images)
**The pipeline has been tested on each test image in the test folder. The output can be found in cell #10 of the notebook Running_Pipeline_Run2.ipynb**

#### 1. Provide an example of a distortion-corrected image.

Please refer to cell #5 of Initial_Analysis_With_Test_Images_Run2.ipynb for an example from the test images

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

Refer to cell #6 to #8 of Initial_Analysis_With_Test_Images_Run2.ipynb

I applied a sobel transform for gradient in x an y direction, followed by thresholding magnitude and direction. This was finally followed by color thresholding, which was applied by looking at the saturation aspect (converting the image first to HSL color space). Finally, I applied a mask to get a smaller region of interest, so that all the noise outside that region was eliminated.

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

Refer to cell #9 of Initial_Analysis_With_Test_Images_Run2.ipynb

The source and destination points, where the src points describe a polygon in the original image which will be transformed to a polygon in the dst points.

The hardcoded points were -

```python
src = np.float32([[255, 690], [550, 475], [759, 475], [1135, 690]])
# define 4 destination points for perspective transformation
dst = np.float32([[310, img_shape[1]], [310, 0], [950, 0], [950, img_shape[1]]])
```

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

For fitting a polynomial to the perspective transformed and thresholded image we use a sliding histogram approach. We start at the maximum peaks in the bottom half of the imageand move our way up. We then subsequently search for the line with the same approach and finally fit a polynomial (see #13-#16 of Initial_Analysis_With_Test_Images_Run2.ipynb)

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in lines #17 and #18 of Initial_Analysis_With_Test_Images_Run2.ipynb

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

Please refer to line #19-#21
---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

This is part of the zip file I attached and was run in a separate notebook - Running_Pipeline_Run2.ipynb

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

1. The first possible time this could fail is when there is no lane marking. This happens in real life a number of times, when the lanes are extremely old. We need to start with more test cases when there are no markings on one of the sides, and then extrapolate our lanes

2. Another possible time when this could fail is when the weather is bad (say snowing) and the visibility is low or the road is covered with snow
