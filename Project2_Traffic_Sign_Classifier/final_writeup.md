#**Traffic Sign Recognition** 

**Build a Traffic Sign Recognition Project**

The goals / steps of this project are the following:
* Load the data set (see below for links to the project data set)
* Explore, summarize and visualize the data set
* Design, train and test a model architecture
* Use the model to make predictions on new images
* Analyze the softmax probabilities of the new images
* Summarize the results with a written report

---
###Data Set Summary & Exploration

####1. Provide a basic summary of the data set and identify where in your code the summary was done. In the code, the analysis should be done using python, numpy and/or pandas methods rather than hardcoding results manually.

The code for this step is contained in the third code cell of the IPython notebook.  

I used the numpy library to calculate summary statistics of the traffic
signs data set:
* The size of training set is 34799
* The size of test set is 12630
* The shape of a traffic sign image is (32, 32, 3)
* The number of unique classes/labels in the data set is 43

####2. Include an exploratory visualization of the dataset and identify where the code is in your code file.

The code for this step is contained in the 5th - 9th cells of the IPython notebook.

Here is an exploratory visualization of the data set. It is a bar chart showing how the training and testing data is spread out per class label. The EDA shows us that the distribution is not balanced across class labels. Augmenting the dataset with transformed images (or getting more labeled dataset per class) will help us with the training phase.

In code cell number 9, we analyze 5 images per class label. This shows us that we have a good amount of variation in -
 - brightness
 - contrast
 - clarity
 - darkness

 An equalization (e.g. histogram equalization) might help us clean up and standardize the dataset.

###Design and Test a Model Architecture

####1. Describe how, and identify where in your code, you preprocessed the image data. What tecniques were chosen and why did you choose these techniques? Consider including images showing the output of each preprocessing technique. Pre-processing refers to techniques such as converting to grayscale, normalization, etc.

The pre-processing is done in cell number 10.
We apply  Histogram Equilization technique to enhance and normalize the data, so that we are dealing with a more standardized dataset. Testing for a random image and its normalized version (cell #11 and #12), it is clear that the normalized version is able to enhance the image for better training.

I also tried converting to grayscale and normalizing, as covered in some of the earlier lectures, but I was losing quite a bit of information. This was evident when I tried to train and test with normalized grayscale images, which gave me poor results on the test data. These signs have colors for a reason, which will help us train and predict better.

####2. Describe how, and identify where in your code, you set up training, validation and testing data. How much data was in each set? Explain what techniques were used to split the data into these sets. (OPTIONAL: As described in the "Stand Out Suggestions" part of the rubric, if you generated additional data for training, describe why you decided to generate additional data, how you generated the data, identify where in your code, and provide example images of the additional data)

The code for splitting the data into training and validation sets is contained in the cell #13. I used a 75-25 split for training and validation sets. I think having a big enough validation set will allow us to generalize better.

After augmenting data, my final training set had 34799 number of images. My validation set and test set had 12630 and 8700 number of images respectively.

I decided to generate additional data because -
1. we have an imbalanced data set
2. rotating, shearing and shifting the images will introduce variation for each image, that will help our classifier to learn and generalize better

To add more data to the the data set, I used ImageDataGenerator from keras.preprocessing.image, which provides a handy way to rotate, shear and apply transformations to images. I decided to augment data so that all the classes have 3000 total examples each.


####3. Describe, and identify where in your code, what your final model architecture looks like including model type, layers, layer sizes, connectivity, etc.) Consider including a diagram and/or table describing the final model.

The code for my final model is located in the 21st cell of the ipython notebook. 

My final model consisted of the following layers:

| Layer         		|     Description	        					| 
|:---------------------:|:---------------------------------------------:| 
| Input         		| 32x32x3 RGB image   							| 
| Convolution 3x3     	| 1x1 stride, Valid padding, outputs 28x28x6 	|
| RELU					|												|
| Max pooling	      	| 2x2 stride,  outputs 14x14x6  				|
| Convolution 3x3	    | 1x1 stride, Valid Padding, outputs 10x10x16	|
| RELU					|												|
| Max pooling			| 2x2 stride,  outputs 5x5x16					|	
| Flatten 				| Input = 5x5x16. Output = 400					|
| Fully connected		| Input = 400 Output = 120						|
| RELU 					| 												|
| Fully connected 		| Input = 84. Output = 43						|
| Softmax				| Probability of each class						|
 

####4. Describe how, and identify where in your code, you trained your model. The discussion can include the type of optimizer, the batch size, number of epochs and any hyperparameters such as learning rate.

The code for training the model is located in the 25th cell of the ipython notebook. 

To train the model, I used the following :
1. #EPOCH : 30
2. Batch size: 128
3. AdamOptimizer with a learning rate of 0.001
4. Lenet architecture mentioned above
5. softmax probabilities with cross entropy loss function 

####5. Describe the approach taken for finding a solution. Include in the discussion the results on the training, validation and test sets and where in the code these were calculated. Your approach may have been an iterative process, in which case, outline the steps you took to get to the final solution and why you chose those steps. Perhaps your solution involved an already well known implementation or architecture. In this case, discuss why you think the architecture is suitable for the current problem.

The code for calculating the accuracy of the model is located in the ninth cell of the Ipython notebook.

My final model results were:
* validation set accuracy of 99.1 %
* test set accuracy of 94.6%
* accuracy on new images downloaded 60%

I started by using a LeNet predefined architecture on grayscale images, but that did not provide good results. I then decided to focus on colored images and keep the architecture as is. LeNet Architecture with colored images provided a pretty good accuracy on the validation set, as well as the testing set. While this was good enough, may be a drop out regularization will help generalize better , as well as downloading or augmenting a larger dataset might help get better accuracy. Also, I stuck to Lenet architecture since I did not have a GPU, which is key in some of the other architectures.
 

###Test a Model on New Images

####1. Choose five German traffic signs found on the web and provide them in the report. For each image, discuss what quality or qualities might be difficult to classify.

Cell number 27 shows all the images that I downloaded from the internet. I tried to stick to images that came up online when I searched for some of 43 classes. The images that it got wrong were 

The 4 images that it got wrong might because of the following - 
1. Some of the features like the basic shapes might be common across the predicted class and the actual class. Additional augmented data or additional training data will probably help
2. Some of the incorrectly classified images have a shear or similar transformation effects due to angles in which they images were shot. We need to augment data with more shear or such transformation, so that the model learns that these are the same classes
3. Finally, adding drop out might help generalize better
 
####2. Discuss the model's predictions on these new traffic signs and compare the results to predicting on the test set. Identify where in your code predictions were made. At a minimum, discuss what the predictions were, the accuracy on these new predictions, and compare the accuracy to the accuracy on the test set (OPTIONAL: Discuss the results in more detail as described in the "Stand Out Suggestions" part of the rubric).

The code for making predictions on my final model is located in the 27th cell of the Ipython notebook.

Here are the results of the prediction:

| Image			        |     Prediction	        					| 
|:---------------------:|:---------------------------------------------:| 
| Speed limit (20km/h)	| Speed limit (20km/h)  						| 
| Wild animals crossing	| No entry 										|
| Bicycles crossing		| Bicycles crossing								|
| General caution 		| General caution				 				|
| Keep right			| Keep right	      							|
| Children crossing		| Children crossing 							|
| Road work				| Road work 									|
| Slippery road			| Beware of ice/snow 							|
| Speed limit (30km/h) 	| Right-of-way at the next intersection 		|
| End of all speed and passing limits| Roundabout mandatory 			|


The model was able to correctly guess 6 of the 10 traffic signs, which gives an accuracy of 60%.

####3. Describe how certain the model is when predicting on each of the five new images by looking at the softmax probabilities for each prediction and identify where in your code softmax probabilities were outputted. Provide the top 5 softmax probabilities for each image along with the sign type of each probability. (OPTIONAL: as described in the "Stand Out Suggestions" part of the rubric, visualizations can also be provided such as bar charts)

The code for making predictions on my final model is located in the 31st cell of the Ipython notebook.

For most of the images, the model was very confident in making the right predictions. The rest of the 4 predictions had almost 0 probability. The only image where there were two choices with high enough probability was Speed limit (30km/h), which was the second last image in the list, and we had a wrong prediction.