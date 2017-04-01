#**Finding Lane Lines on the Road** 

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

###1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

The pipeline consists of the following steps :

1. Convert RGB to grayscale
2. Apply Gaussian blur
3. Perform Canny edge detection
4. Define a region of interest and mask undesired portions of the image
5. Find Hough lines
6. Apply lines to the original image

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by -
 1. Finding the slope (positive for right and negative for left)
 2. Finding the bottom most and top most points for each side
 3. Finding the corresponding points where the line would touch the bottom part of the image
 4. Drawing lines between highest and the bottom most point (now the bottom of the image)

###2. Identify potential shortcomings with your current pipeline
1. These do not work well when I have a curved road (not a straight line)
2. These lines also flicker when applied to a video

###3. Suggest possible improvements to your pipeline
1. Instead of detecting only straight lines, I could use a more generic curve detection that could work on both lines and curved paths
2. Try out values for Cany edge detection algorithm that can work on faint images OR first detect if the image resolution is very low, and then apply different parameters based on the clarity of the video/image. To improve the efficiency, I could check this only after certain number of frames in the video (assuming that the video might get blurred and get cleared after intervals)