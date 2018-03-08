## Project: Follow Me
---

# Required Steps for a Passing Submission:

1. Clone the project repo here
2. Fill out the TODO's in the project code as mentioned here
3. Optimize your network and hyper-parameters.
4. Train your network and achieve an accuracy of 40% (0.40) using the Intersection over Union IoU metric which is final_grade_score at the bottom of your notebook.
5. Make a brief writeup report summarizing why you made the choices you did in building the network.

## [Rubric](https://review.udacity.com/#!/rubrics/1067/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  

You're reading it!

#### 2. The write-up conveys the an understanding of the network architecture.

The chosen network architecture can be seen it the figure.
![Fully convolutional network architecture](images/FCN.png)

The layers of the network are explained below:
##### 1. Encoder block

This is a convolutional block. It consists of 4 layers. Each of these layers uses 3d kernels (filters) of shape [3,3,input_depth] to extract features from the image. Number of those kernels is equla to the output depth of the layer. 

When kernel swipes the input image, it is shifted by 2 units (pixels) for every pixel of the output image, therefor each output image is aproximately (depends on padding) 2x smaller (hight and width). The depth of the image increases and is equal to the number of kernels used. 

In those layers, separable convolutions are used - every layer of input image is swept with its own 3x3x1 kernel resulting in image with reduced width and height but same depth, than [1x1xinput_depth] kernales are used to extract features. The number output layers is equal to the number of [1x1xinput_depth] kernels. This approach significantly reduces the number of model parameters.

##### 2. 1 to 1 block

This is also a convolutional layer, but its kernel is {1x1xinput_depth] and the strides are equal to 1. Therefore the size (depth and width) of input and output are the same. Its purpouse is similar to fully connected layer in standard convolutional layer but it is pixel-wise therefor it preserves spatial information.


##### 3. Decoder block

The pourpouse of this layer is to upsample the image back to the original size. It uses transpose of the original convolutional layer. Also, while upsampling the image, similar size images from encoding steps are concatenated. This allows better resolution of the overal respones. At the end it uses softmax functionto change the range to the probability scale (probabilitie for all the classes sums to one)

The exact architecture of the network can be seen in `model_training.ipynb` file.

#### 3. The write-up conveys the student's understanding of the parameters chosen for the the neural network.

The chosen parameters are:

learning_rate = 0.004 (Making this parameter smaller allowed to obtain better solution but it also increases number of epochs needed for the solution to converge)

batch_size = 100 (This is a number of images used for every step of training, higher numbers exceed computer ram memory)

num_epochs = 100 (How many times to go through the whole dataset. 100 allowed loss function to converge)

steps_per_epoch = 42 (Steps taken in every epoch. It was set that algorithm goes through the whole dataset in every epoch)

validation_steps = 12 (To go throgh the whole validation dataset)

workers = 2 (depends on computer architecture)

#### 4. The student has a clear understanding and is able to identify the use of various techniques and concepts in network layers indicated by the write-up.

Explained in pt. 2 (network architecture)

#### 5. The student has a clear understanding of image manipulation in the context of the project indicated by the write-up.

Encoding with fully connected layes allows for recognition of objects in the picture. If two objects, that we trained the network for are in the picture than only one of them will have the highest probability and is detected.

Fully convolutional network (with decoder) perforems identification pixel-wise and can detect multiple objects and show their spatial locations.

#### 6. The student displays a solid understanding of the limitations to the neural network with the given data chosen for various follow-me scenarios which are conveyed in the write-up.

The network can only work for the classes of objects that it was traind for. The training set should include the whole spectrum of objects in the triand class (varius distances to the object, various typs of the objects of a particular class, varius orintations and positions) as well as the data without object of this particular class. Wy can use the same same network architecture to detct dogs/cats bat we would to use extended dataset.

### Model

The file are supplied in the data/weights folder. Tha accuracy obtaind wa equal to 44%.
