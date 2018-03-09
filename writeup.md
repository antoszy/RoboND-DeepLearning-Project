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
##### a) Convolutional encoder layer

This is a convolutional block. It consists of 4 layers. Each of these layers uses 3d kernels (filters) of shape [3,3,input_depth] to extract features from the image. Number of those kernels is equla to the output depth of the layer. 

When kernel swipes the input image, it is shifted by 2 units (pixels) for every pixel of the output image, therefor each output image is aproximately (depends on padding) 2x smaller (hight and width). The depth of the image increases and is equal to the number of kernels used. 

In those layers, separable convolutions are used - every layer of input image is swept with its own 3x3x1 kernel resulting in image with reduced width and height but same depth, than [1x1xinput_depth] kernales are used to extract features. The number output layers is equal to the number of [1x1xinput_depth] kernels. This approach significantly reduces the number of model parameters.

##### b) 1 to 1 convolutional layer

This is also a convolutional layer, but its kernel is {1x1xinput_depth] and the strides are equal to 1. Therefore the size (depth and width) of input and output are the same. Its purpouse is similar to fully connected layer in standard convolutional layer but it is pixel-wise therefor it preserves spatial information.


##### c) Bilinear upsampling decoder layer

The pourpouse of this layer is to upsample the image back to the original size. It uses bilinear upsampling to increase resolution 2x. This funcion estimates value of an unknown pixels by taking linear combination of 4 closest known pixels.

Now similar size images from encoding steps are concatenated to the output of upsampling and the next step of processing is using 1 to 1 convolutional layer. This allows better resolution of the overal respones. 

##### d) Convolutional layer with softmax

At the end one more 1 to 1 convolutional layer is used with output depth of 3 (number of classes). It also uses softmax functionton to change the range to the probability scale (probabilitie for all the classes sums to one for each pixel)

Layer 1, 2, 3 use batch normalization - every batch of data is normalized not only at the input to the network but also between convolutional layers. This ensures the right distribution of data at the input of each layer, improves statistical properties and allows for higher learning rates. 


#### 3. The write-up conveys the student's understanding of the parameters chosen for the the neural network.

The chosen parameters are:

learning_rate = 0.004 When we obtaing gradient of loss function (direction in wchich changing weights vector makes the loss function decrease fastest) wy multiply it by learning rate. Higher learning rate makes loss function decrease faster. Making this parameter smaller allowed to obtain better solution becouse local minimum can be achived with higer precision. It also increases number of epochs needed for the solution to converge.

batch_size = 100 This is a number of images used for every step of training, higher numbers exceed computer ram memory. We do not have to use the whole data set for every step of learning proces. We can learn on small batches. The direction of one step can even decrease the performance of the network for the whole dataset but overal the direction of weight vector changing is right.

num_epochs = 100 How many times to go through the whole dataset. 100 allowed loss function to converge. I starterd with low number (10) and than increased it until no improv in the loss function could be seen from one epoch to another.

steps_per_epoch = 42 Steps taken in every epoch (how many batches of data are used during epoch. It was set in that way, that algorithm goes through the whole dataset in every epoch.

validation_steps = 12 (To go throgh the whole validation dataset)

workers = 2 (depends on computer architecture)

#### 4. The student has a clear understanding and is able to identify the use of various techniques and concepts in network layers indicated by the write-up.

##### Fully connected layer:
Fully connected layers connects values of every pixel it the output of encoding block to every nuron. Each nuron in this layer detects one class of object and givs yes/no information if the objects is in the picture. Encoding with fully connected layes allows for recognition of objects in the picture. If two objects, that we trained the network for, are in the picture than only one of them will have the highest probability and is detected. We get no information on spatial localization of the object.

##### 1 by 1 convolutional layer
This is a convolutional layer, but its kernel is [1x1xinput_depth] and the strides are equal to 1. This means that kernel is fedd with values of only one pixel from whole depth. The kernel moves by 1 pixel every time and therefore the size (depth and width) of input and output are the same. The number of weights in the kernel is equal to the depth of the input image. The number of kernels is equal to the depth of the output of the image that is needed. Its purpouse is similar to fully connected layer in standard convolutional layer but it is pixel-wise, therefor it preserves spatial information and provides statial class recognition. It can detect all the classes that are in the picture and provide their spatial localizations.


#### 5. The student has a clear understanding of image manipulation in the context of the project indicated by the write-up.

Fully convolutional network (with decoder) perforems identification pixel-wise and can detect multiple objects and show their spatial locations. This is needed to obtain location of chosen person in the project. The network is fed with the data from drone camera and its object is to show which pixels contain the target object. For this purpouse a fully convolutional network is built which is fed with normalized images. 

The architecture of the network is shown in the figure in section 2. of this writeup. The network consists of 3 blocks: encoder block, 1 to 1 convolutional layer and decoder block. The exact structures and working priciples of every layer in the encoder, decoder and 1 to 1 blocks are given and explained in section 2. The encoder block convolutes kernel in each layer to detect class specific features in the image. The number of filters is higher in every layer so the depth of the image increases. As the stride equals 2 (kernel is shifted by 2 pixels each time) the resolution of the image decreases at avery layer. 1 to 1 convolutional block has detects highest level structures in the picture. It uses kernel of size [1x1xinput_depth]. Next step is using a decoder block to upsample detected image features to the original size. Biliner upsampling is used with 1 to 1 convolutional layers fallowed with concatenation of images from encoder and 1 to 1 convolutional block. At the end a convolutional layer with softmax function is used to differenciate classes in the picture based on features detected by previous layes.

To make the network work properly one needs to find weights for every kernel in the network. In order to do that one needs a training data with pictures of objects of interest which are labeld appropiately. Than a cost function is defined as cross-entropy of one hot encoded labels and logits, which are the output of the network. Than the weights vector is optimized by gradient descent method to minimize loss function. To measure the performance of the network we calculate cross-entropy using data that was not used during training. This proces is called validation.

#### 6. The student displays a solid understanding of the limitations to the neural network with the given data chosen for various follow-me scenarios which are conveyed in the write-up.

The network can only work for the classes of objects that it was traind for. The training set should include the whole spectrum of objects in the triand class (varius distances to the object, various typs of the objects of a particular class, varius orintations and positions) as well as the data without object of this particular class. Wy can use the same same network architecture to detct dogs/cats bat we would to use extended dataset.

### Model

The file are supplied in the data/weights folder. Tha accuracy obtaind wa equal to 44%.
