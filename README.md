# Hand-Signal-recognition-using-Jetson-Nano-Developer-Kit

![](graphic/github_demo.gif)<br>
Hand gesture allows precise and instinctive ways to maneuver the complicated
machine. We can develop a method with the use of computer vision algorithm to develop a
spontaneous way of agent-less communication between a drone and its operator.In this project we propose
the use of hand gestures as a method to control drones. Computer vision-based methods
rely on the ability of a drone's camera to capture surrounding images and use pattern
recognition to translate images to meaningful and/or actionable information. They are:
image separation from the video streams of the front camera, creating a robust and reliable
image recognition based on segregated images, and finally conversion of classified
gestures into actionable drone movement, such as takeoff, landing, hovering and so forth.
The 4 gestures we are considering in this project are:

1) Target
2) Forward
3) Descend
4) Stop
## Techinque I:Using OpenCV and Keras
Computers 'look' at images as multidimensional arrays or matricies.
Note: When images are loaded in OpenCV, they return BGR (blue, green, red) channels, where as matplotlib expects RGB (red, green, blue). Therefore, we need  to convert the loaded image matrix from BGR to RGB.

Using a background image to find differences (can be used for images and video)

This technique requires a background image to find the difference between the background and the current frame to find what as changed. This difference creates a 'mask' that represents where in the image the foreground is. A draw back of this algorithm is that any movement of the camera, change of lighting, change in focus, etc. will make the current frame totally different from the background image.

The algorithm:
* load in the background image and the current frame
* find the absolute difference between the images
* create a mask that contains a 'map' of pixels that should be 'on or off'
* apply the mask to the current frame to extract the foreground by iterating over each pixel and copying all pixels from the current frame that should be part of the foreground
Using motion based background subtraction algorithms (mainly video)

These algorithms are most used for video. The algorithm looks at a series of frames and computes which pixels are most static and identifies the foreground by the pixels that are moving. The MOG2 and KNN background subtractors are two different algorithms.
Tracking is a very complex topic and we will simply use OpenCV's tracking algorithms to track objects. For this project, we will use the Kernelized Correlation Filters (KCF) tracking as it performs well and provides built in tracking error detection.

Available tracking algorithms:
* MIL
* BOOSTING
* MEDIANFLOW
* TLD
* KCF

The next objective is to recognize which gesture a hand is posed in. We will train our neural network on 4 gestures: fist(Stop), point(Target), palm(Forward) and yo sign(Descend) You can check the dataset folder to get the idea of gesture. For this network we will train the network on the mask to reduce dimensionality. Doing this makes it a more simple problem for the network to model, while sacrificing information stored in the colours of an image.Here, we track our hand with the background subtracted and thresholded. Everytime you hit 's' a screen capture of your cropped hand is saved.
Building the Neural Network

Here we assemble the neural network with keras and compile it for training.



This is a very simple convolutional neural network containing three convolutional and max pooling layers. After a tensor is passed through the convolutional layers, it is flatted into a vector and passed through the dense layers.

Preparing Data for Training

Here we use the keras data generator to augment data. This loads data and applies certain transformations to it in order to improve generalization of the model.

The ImageDataGenerator.flow_from_directory() is a convenience method that prepares classification data according to file directories.

Training data is used to train the model to recognize gestures and validation data is used to verify that the model is not over fitting to the training data and the network is converging.

Training the Network

Now we can train the model on the augmented data.

## Techinque II: Image Classification using Fastai
Before any work can be done a dataset needs to be converted into a DataBunch object, and in the case of the computer vision data - specifically into an ImageDataBunch subclass.
Since our images are placed in folders whose names correspond to the image labels, we will use the ImageDataBunch.from_folder() function to create an object that contains our image data. This is super useful and makes it incredibly easy to read the data into our model
Fastai can automatically split our data into train and validation sets, so we don't even need to create these on our own.
The only hyperparameters we need are the path-variable pointing to our data set, the size of the inputs and the batch size for each gradient descent iteration.
The function argument called get_transforms() which returns a list of available image transformations upon call.
The parameter valid_pct which controls the percentage of images that will be randomly chosen to be in the validation set.
Fastai has a function called create_cnn() from its vision module.
 This function creates what is called a learner object, which we'll put into a named variable.
 We are using the ResNet34 architecture as our base model for transfer learning. 
Upon call, the trained architecture will be downloaded via the Fastai API and stored locally.
The learner object we create comes with a build-in function to find the optimal learning rate, or range of learning rates, for training.
 It achieves this by fitting the model for a few epochs and saving for which learning rates the loss decreases the most.
Lastly, we can use Fastai’s  ClassificationInterpretation class to interpret our results. 
To create an interpretation object we need to call the from_learner method and pass it our learner/model. 

