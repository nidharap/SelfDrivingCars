**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector.
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

This project contains the following :

writeup.md : The markdown file that has all the write up (this file)
training_classifier.ipynb : This trains an Lienar SVM classifier on training data provided
search_and_classify.ipynb : this notebook builds and runs the pipeline on test images and the video
output_images: This folder contains the output of various processing steps
project_video_output.mp4: this is the output of the pipeline, with cars detected in each frame

###Histogram of Oriented Gradients (HOG)

####1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the first code cell of the IPython notebook (training_classifier.ipynb line #3).  

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes.I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).

####2. Explain how you settled on your final choice of HOG parameters.
I discarded RGB color space, for its undesirable properties under changing light conditions. I ended up using  `YCrCb` color space and HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)` because that gave me the best accuracy on test data.

####3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM. Refer to line 7 of training_classifier.ipynb, which has a pipeline that scales and then fits the model to the training data. I got a test accuracy of 0.9845. I finally stored the model for use in the video

###Sliding Window Search

####1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

A grid of sliding windows is created in such a way that only the regions that could potentially have vehicles are retained. For example, the sky etc is completely removed, the bottom that has the bonnet is removed. This increases the speed of detection and reduces false positives. For scales, different scales were used to create sliding windows, with the ones near the horizon being smaller, the ones closer to the camera car are larger. A total of four different scales have been used.

Test image : ![Alt text](./output_images/test_sliding_windows_grid.jpg?raw=true "Sliding Windows")

####2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

The test images can be seen in search_and_classify.ipynb notebook under line lines #5 and #6

The predictions from various sliding windows is fed through a headmap and eventually thresholded based on the number of predictions for the same pixel, removing false positives

---

### Video Implementation

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)

https://youtu.be/iz5M-2WBYAE


####2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

Thresholding the heatmap eliminates false positives because we get multiple predictions for actualy cars. Detections that are not (partially) covered by a minimum number of sliding windows is discarded. The heatmap also helps combine duplicate detections into a single detection. For this project a heatmap threshold of 3 was used.

### Here are six frames and their corresponding heatmaps:

Test Image 1:
![Alt text](./output_images/test1_detected.jpg?raw=true "Test Image 1")
![Alt text](./output_images/labeled_regions_test_1.jpg?raw=true "Test Image 1")

Test Image 2:
![Alt text](./output_images/test2_detected.jpg?raw=true "Test Image 2")
![Alt text](./output_images/labeled_regions_test_2.jpg?raw=true "Test Image 2")

Test Image 3:
![Alt text](./output_images/test3_detected.jpg?raw=true "Test Image 3")
![Alt text](./output_images/labeled_regions_test_3.jpg?raw=true "Test Image 3")

Test Image 4:
![Alt text](./output_images/test4_detected.jpg?raw=true "Test Image 4")
![Alt text](./output_images/labeled_regions_test_4.jpg?raw=true "Test Image 4")

Test Image 5:
![Alt text](./output_images/test5_detected.jpg?raw=true "Test Image 5")
![Alt text](./output_images/labeled_regions_test_5.jpg?raw=true "Test Image 5")

Test Image 6:
![Alt text](./output_images/test6_detected.jpg?raw=true "Test Image 6")
[Alt text](./output_images/labeled_regions_test_6.jpg?raw=true "Test Image 6")

### Here is the output of `scipy.ndimage.measurements.label()` on the integrated heatmap from all six frames:
This can be seen in the images above and also in search_and_classify.ipynb notebook under line lines #5 and #6

### Here the resulting bounding boxes are drawn onto the last frame in the series:

This can be seen in the images above and also in search_and_classify.ipynb notebook under line lines #5 and #6

---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

The primary time taking and challenging part of this is the parameter tuning, specially for the pipeline, with exploration of multiple color spaces. This project does some of this but with a thorough exploration, we could get an even accurate model and pipeline.

Some of the other challenges are data augmentation saying by image transformations and collecting more data online (provided I get access to more powerful processors), and trying to generalize the models to other types of environments (other than the project video)

Reference: A lot of the components were inspired from the project : https://github.com/GeoffBreemer/SDC-Term1-P5-Vehicle-Detection-and-Tracking
