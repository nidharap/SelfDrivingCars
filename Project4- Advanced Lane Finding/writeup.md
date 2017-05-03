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

#### 1. Provide an example of a distortion-corrected image.

Please refer to cell #5 of Pipeline_Buildup_On_Test_Image.ipynb for an example from the test images

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I first started with a Perspective transform (see cell #6 of Pipeline_Buildup_On_Test_Image.ipynb). Next I used a two step process to get a final binary image :

1. Since our lanes are white and yellow in color, I first applied a color transform to extract only the yellow and white lane lines (see cell #7-#9 of Pipeline_Buildup_On_Test_Image.ipynb)

2. I then combined this with sobel transformations on saturation space (see cell #11 of Pipeline_Buildup_On_Test_Image.ipynb)

I then combined the output of the two steps to get a combined binary image (see cell #12-#13 of Pipeline_Buildup_On_Test_Image.ipynb)


#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The source and destination points, where the src points describe a polygon in the original image which will be transformed to a polygon in the dst points. I hardcoded the values and used :
 (see cell #6 of Pipeline_Buildup_On_Test_Image.ipynb)

```python
src = np.float32([[220,719],[1130,719],[755,477],[580,478]])
# define 4 destination points for perspective transformation
dst = np.float32([[240,719],[1040,719],[1040,300],[240,300]])
```

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

For fitting a polynomial to the perspective transformed and thresholded image we use a sliding histogram approach. We start at the maximum peaks in the bottom half of the imageand move our way up. We then subsequently search for the line with the same approach and finally fit a polynomial (see #16-#19 of Pipeline_Buildup_On_Test_Image.ipynb)

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in lines #20-#21 of Pipeline_Buildup_On_Test_Image.ipynb

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

Please refer to line #22-#24
---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

This is part of the zip file I attached and was run in a separate notebook - Running_Pipeline_On_Video.ipynb.ipynb

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

1. The first possible time this could fail is when there is no lane marking. This happens in real life a number of times, when the lanes are extremely old. We need to start with more test cases when there are no markings on one of the sides, and then extrapolate our lanes

2. Another possible time when this could fail is when the weather is bad (say snowing) and the visibility is low or the road is covered with snow
