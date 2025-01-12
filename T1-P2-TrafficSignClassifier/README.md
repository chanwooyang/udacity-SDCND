## Project: Build a Traffic Sign Recognition Program
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

Overview
---
In this project, you will use what you've learned about deep neural networks and convolutional neural networks to classify traffic signs. You will train and validate a model so it can classify traffic sign images using the [German Traffic Sign Dataset](http://benchmark.ini.rub.de/?section=gtsrb&subsection=dataset). After the model is trained, you will then try out your model on images of German traffic signs that you find on the web.

We have included an Ipython notebook that contains further instructions 
and starter code. Be sure to download the [Ipython notebook](https://github.com/udacity/CarND-Traffic-Sign-Classifier-Project/blob/master/Traffic_Sign_Classifier.ipynb). 

We also want you to create a detailed writeup of the project. Check out the [writeup template](https://github.com/udacity/CarND-Traffic-Sign-Classifier-Project/blob/master/writeup_template.md) for this project and use it as a starting point for creating your own writeup. The writeup can be either a markdown file or a pdf document.

To meet specifications, the project will require submitting three files: 
* the Ipython notebook with the code
* the code exported as an html file
* a writeup report either as a markdown or pdf file 

Creating a Great Writeup
---
A great writeup should include the [rubric points](https://review.udacity.com/#!/rubrics/481/view) as well as your description of how you addressed each point.  You should include a detailed description of the code used in each step (with line-number references and code snippets where necessary), and links to other supporting documents or external references.  You should include images in your writeup to demonstrate how your code works with examples.  

All that said, please be concise!  We're not looking for you to write a book here, just a brief description of how you passed each rubric point, and references to the relevant code :). 

You're not required to use markdown for your writeup.  If you use another method please just submit a pdf of your writeup.

The Project
---
The goals / steps of this project are the following:
* Load the data set
* Explore, summarize and visualize the data set
* Design, train and test a model architecture
* Use the model to make predictions on new images
* Analyze the softmax probabilities of the new images
* Summarize the results with a written report

### Dependencies
This lab requires:

* [CarND Term1 Starter Kit](https://github.com/udacity/CarND-Term1-Starter-Kit)

The lab environment can be created with CarND Term1 Starter Kit. Click [here](https://github.com/udacity/CarND-Term1-Starter-Kit/blob/master/README.md) for the details.

### Dataset and Repository

1. Download the data set. The classroom has a link to the data set in the "Project Instructions" content. This is a pickled dataset in which we've already resized the images to 32x32. It contains a training, validation and test set.
2. Clone the project, which contains the Ipython notebook and the writeup template.
```sh
git clone https://github.com/udacity/CarND-Traffic-Sign-Classifier-Project
cd CarND-Traffic-Sign-Classifier-Project
jupyter notebook Traffic_Sign_Classifier.ipynb
```

------------------------

# Traffic Sign Recognition: Writeup Report

**Build a Traffic Sign Recognition Project**

The goals / steps of this project are the following:
* Load the data set (see below for links to the project data set)
* Explore, summarize and visualize the data set
* Design, train and test a model architecture
* Use the model to make predictions on new images
* Analyze the softmax probabilities of the new images
* Summarize the results with a written report


[//]: # (Image References)

[chart1]: ./writeup_images/chart1.png "Chart1"
[chart2]: ./writeup_images/chart2.png "Chart2"
[test_images]: ./writeup_images/six_test_images.png "6 Test Images"
[six_test]: ./writeup_images/six_test_result.png "6 Test Images Result"
[overall_test]: ./writeup_images/overall_test_result.png "Test Dataset Result"

## Rubric Points
### Here I will consider the [rubric points](https://review.udacity.com/#!/rubrics/481/view) individually and describe how I addressed each point in my implementation.  

---
#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one. You can submit your writeup as markdown or pdf. You can use this template as a guide for writing the report. The submission includes the project code.

### Data Set Summary & Exploration

#### 1. Provide a basic summary of the data set. In the code, the analysis should be done using python, numpy and/or pandas methods rather than hardcoding results manually.

I used python built-in funciton 'len()' to get the number of each data sets:

* The size of training set: 34799
* The size of the validation set: 4410
* The size of test set: 12630
* The shape of a traffic sign image: (32,32,3)
* The number of unique classes/labels in the data set: 43

I also included assert(len(feature) == len(label)) to ensure number of features and number of labels are the same in each dataset.

#### 2. Include an exploratory visualization of the dataset.

Here is an exploratory visualization of the data set. The first bar chart shows the number of images by each dataset; The whole dataset is distributed to about 70% of training set, 10% of validation set, and 20% of test set. The dataset split is reasonable as the total number of image is ~52000 and this is considered relatively small dataset.


![Number of Dataset by Type][chart1]

Second bar chart shows the number of training images by each class/label. The chart shows that number of training images of some classes are small compared to other classes. Number of training images can be increased by introducing the data augmentation techniques.

![Number of Images by Class][chart2]

### Design and Test a Model Architecture

#### 1. Describe how you preprocessed the image data. What techniques were chosen and why did you choose these techniques? Consider including images showing the output of each preprocessing technique. Pre-processing refers to techniques such as converting to grayscale, normalization, etc. (OPTIONAL: As described in the "Stand Out Suggestions" part of the rubric, if you generated additional data for training, describe why you decided to generate additional data, how you generated the data, and provide example images of the additional data. Then describe the characteristics of the augmented training set like number of images in the set, number of images for each class, etc.)

First, I decided not to grayscale dataset, but only normalize them. Compared to the MNIST data, this traffic sign dataset can be distinguished by not only its shape and pattern, but also its color. So, grayscaling dataset could lose significant information that can be trained by a model.

I used Min-Max Scaling to normalize dataset. This normalizing input helps to make the cost function more symmetric in space and this makes an optimizer to optimize the cost function more easily with higher learning rate.

For the data augmentation, this is something that needs to be carefully processed. In this project context, data augmentation can mean the image distortion, like image rotation, change in color, introducing noises, etc. But, image rotation and change in color can introduce error in the training set. For example, if an ‘Ahead Only’ sign is rotated, then this can be looked the same with a ‘Keep Right’ or ‘Keep Left’ sign. Thus, in this project context, traffic sign images should not be rotated or changed in color, but they can only be distorted in such ways, like changing brightness, making blurry, etc.


#### 2. Describe what your final model architecture looks like including model type, layers, layer sizes, connectivity, etc.) Consider including a diagram and/or table describing the final model.

My final model consists of following layers:

| Layer         		|     Description	        					| 
|:---------------------:|:---------------------------------------------:| 
| Input         		| 32x32x3 Normalized image   					| 
| Convolution 5x5     	| 1x1 stride, VALID padding, outputs 28x28x40 	|
| Max pooling	      	| 2x2 stride, SAME padding, outputs 14x14x40	|
| Convolution 5x5	    | 1x1 stride, VALID padding, outputs 10x10x80 	|
| Max pooling	      	| 2x2 stride, SAME padding, outputs 5x5x80	 	|
| Flattened layer      	| outputs 1x2000							 	|
| Fully connected		| Dropout 0.75, output 1x1000					|
| Fully connected		| Dropout 0.75, output 1x300					|
| Fully connected		| Dropout 0.75, output 1x100					|
| Output layer			| output 1x43									|
 
I used Leaky ReLU activation with alpha = 0.15 instaed of normal ReLU on every convolutional layer and fully connected layer because all negative input values to layers are discarded by normal ReLU and this could lose some significant information.

I also implemented Batch-Normalization in every layer. Like input normalization, batch-norm normalize the value by shifting the mean and by adjusting variance before they are fed into an activation function within every layer. This helps to train a model faster and yield a model with better performance.

#### 3. Describe how you trained your model. The discussion can include the type of optimizer, the batch size, number of epochs and any hyperparameters such as learning rate.

To train the model, I used an Adam Optimizer, which is an adaptive moment estimation and it combines RMS prop and Momentum techniques. For the Batch Size, I tried 128, 256, 512, and 1025, and Batch size of 512 yielded better result than others. Learning rate value was set to 0.0005 and this was set after few trials of training. For the Epoch, I initially tried with large number like 50 ~ 80 Epochs, but it was found that the validation accuracy was not significantly improved after about 30~40 Epochs. If the model is trained longer, though the accuracy does not increase, it could overfit the model. Thus, I implemented a couple of line of code that stop the training once the validation accuracy reaches higher than 98%. This saved processing time and promised optimal network.

#### 4. Describe the approach taken for finding a solution and getting the validation set accuracy to be at least 0.93. Include in the discussion the results on the training, validation and test sets and where in the code these were calculated. Your approach may have been an iterative process, in which case, outline the steps you took to get to the final solution and why you chose those steps. Perhaps your solution involved an already well known implementation or architecture. In this case, discuss why you think the architecture is suitable for the current problem.

My final model results were:
* training set accuracy of 100.00%
* validation set accuracy of 98.12%
* test set accuracy of 97.82%

If an iterative approach was chosen:

* What was the first architecture that was tried and why was it chosen?

First, I tried out with simple LeNet structure: 2 convolution layers and 2 fully connected layers. This is simple and well performing image classifying model, so it was a good starting point.

* What were some problems with the initial architecture?

The problem with the simple LeNet architecture was its validation accuracy was too low; it was around 85%. 

* How was the architecture adjusted and why was it adjusted? Typical adjustments could include choosing a different model architecture, adding or taking away layers (pooling, dropout, convolution, etc), using an activation function or changing the activation function. One common justification for adjusting an architecture would be due to overfitting or underfitting. A high accuracy on the training set but low accuracy on the validation set indicates over fitting; a low accuracy on both sets indicates under fitting.

Since the accuracy of the first architecture was too low and this indicates the underfitting of the model, it was thought that first architecture was small model for 43 classes. So I increased the model architecture size: Number of output in each convolution layer was increased, number of output of each fully connected layer was increased, and one more fully connected layer was added to the model. And, with some hyperparameter tuning, the performance of the model increased higher than 90%.

However, a gap between training error and validation error was quite big, like around 10%,  and this indicates the overfitting of the model to the training data. This variance problem can be solved by regularizations. So, I added dropout to fully connected layers and implemented batch-normalization to convolution layers and fully connected layerrs. With some hyperparamter tuning, again, the validation error reduced down close to the training error.

* Which parameters were tuned? How were they adjusted and why?

Learning rate was tuned specifically as it is the hyperparameter that is directly related to the cost function, so it heavily affects the performance of the model training. I started with low value around 0.001 and tried training the model with more values by increasing/decreasing by 50%.

Batch size was tuned between 128 and 1024, and Epoch was tuned between 10 and 80. I repeated tuning them until the model yields better accuracy.

* What are some of the important design choices and why were they chosen? For example, why might a convolution layer work well with this problem? How might a dropout layer help with creating a successful model?

I believe implementing dropout and batch-normalization are important design choices. It is relatively easy to overcome the avoidable bias by either making the model bigger and/or training model longer, but overcoming the variance problem was bit tricky as it is required to use regularization techniques. 

Overfitting, simply saying, comes from the model learning to much detail information of training dataset, so the model needs to be 'regulated' so that the impact of hidden units is reduced. So, the dropout technique zeroes out some of nodes in hidden layers and it regulates the model to reduce the impact of hidden units.

Batch-normalizaition, as said above, works in the same concept of the input normalization. As a model gets trained, hidden layer weights and bias are changed, and outputs of an activation function of each layer are also changed. This means that the distribution of inputs to next layer, or in other words, their mean and variance are changed. This is so-called 'covariate shift'. Then, this covariate shift can lead input values to become either very small or very large, and this leads the gradient of the model to become flat or close to 0, and ultimately, it makes much harder for optimizer to train the model. Thus, the Batch-Normalization technique helps to prevent covariate shift by adjusting the mean and variance of input values before they are fed into an activation function of a layer. Many of academic papers have shown that Batch-Norm significantly increases the training speed and can yield higher accuracy of the model.

### Test a Model on New Images

#### 1. Choose five German traffic signs found on the web and provide them in the report. For each image, discuss what quality or qualities might be difficult to classify.

These are six German traffic signs that I downloaded from 'benchmakr.ini.rub.de' web:

![6 Test Images][test_images]

The second image could be difficult to classify as there is a noise in the image. And, the fourth and sixth images could be difficult to classify because the brightness is too low and they are even quite difficult for a human to classify.

#### 2. Discuss the model's predictions on these new traffic signs and compare the results to predicting on the test set. At a minimum, discuss what the predictions were, the accuracy on these new predictions, and compare the accuracy to the accuracy on the test set (OPTIONAL: Discuss the results in more detail as described in the "Stand Out Suggestions" part of the rubric).

Here are the results of the prediction:

| Image			        |     Prediction	        					| 
|:---------------------:|:---------------------------------------------:| 
| No Entry      		| No Entry   									| 
| End of no passing ... | End of no passing ... 						|
| Bicycles crossing		| Bicycles crossing								|
| No Passing      		| No Passing 					 				|
| Go straight or right	| Go straight or right							|
| Speed limit (120 km/h)| Speed limit (120 km/h)						|


The model was able to correctly guess all traffic signs, which gives an accuracy of 100%. 

#### 3. Describe how certain the model is when predicting on each of the five new images by looking at the softmax probabilities for each prediction. Provide the top 5 softmax probabilities for each image along with the sign type of each probability. (OPTIONAL: as described in the "Stand Out Suggestions" part of the rubric, visualizations can also be provided such as bar charts)

For all images, the model is 100% sure about its predictions. 

![6 Test Images Result][six_test]

However, since only 6 images are not enough to correctly evaluate the trained model, test dataset was used to get the test accuracy of the trained model. The final model yielded 97.82% of test accuracy and I randomly chose and displayed 6 images with their softmax probability results.

![Test Dataset Result][overall_test]

### (Optional) Visualizing the Neural Network (See Step 4 of the Ipython notebook for more details)
#### 1. Discuss the visual output of your trained network's feature maps. What characteristics did the neural network use to make classifications?


