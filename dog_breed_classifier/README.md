[//]: # (Image References)

[image1]: ./images/sample_dog_output.png "Sample Output"
[image2]: ./images/vgg16_model.png "VGG-16 Model Layers"
[image3]: ./images/vgg16_model_draw.png "VGG16 Model Figure"


## Project Overview

Welcome to the Convolutional Neural Networks (CNN) project in the AI Nanodegree! In this project, you will learn how to build a pipeline that can be used within a web or mobile app to process real-world, user-supplied images.  Given an image of a dog, your algorithm will identify an estimate of the canine’s breed.  If supplied an image of a human, the code will identify the resembling dog breed.  

![Sample Output][image1]

Along with exploring state-of-the-art CNN models for classification and localization, you will make important design decisions about the user experience for your app.  Our goal is that by completing this lab, you understand the challenges involved in piecing together a series of models designed to perform various tasks in a data processing pipeline.  Each model has its strengths and weaknesses, and engineering a real-world application often involves solving many problems without a perfect answer.  Your imperfect solution will nonetheless create a fun user experience!

## Project Structure

We break the notebook into separate steps.

Step 0: Import Datasets
Step 1: Detect Humans
Step 2: Detect Dogs
Step 3: Create a CNN to Classify Dog Breeds (from Scratch)
Step 4: Create a CNN to Classify Dog Breeds (using Transfer Learning)
Step 5: Write your Algorithm
Step 6: Test Your Algorithm

## My Approach

1. Detect humans in the image using OpenCV's implementation of Haar feature-based cascade classifiers to detect human faces in images.
2. Check if a dog is present in the image using VGG16 pretrained model  and as another try using Inception-V3 model.
3. VGG16 falls short of 100% accuracy which can be attributed to the fact that VGG16 gives out only 118 out of 133 dog breed categories.
4. Creating CNN from scratch: 
    I began by trying previously used CNN architectures in this course like CIFAR 10 and the VGG16, which later was my base model as an inspiration
    Input is 224 x 224 x 3 and Output is 133 classes
    After several changes and iterations and learnings from various standard methodologies available, I stayed with the model defined above.
    Convolutional steps were pretty simple and standard where I went with the multiple of computer memory 16,32,64 ending at 128 as I thought this is closest to 133 i.e. no of our classes.
    With consecutive Max pooling Layers, I'm drying to downsample the size by a factor of 2 while increasing the depth by the same factor, input shape at each convolutional layer is defined in comments
    I went wrong with the input shape for flattening image and had error in the first try; solved this using x.view before image flattening. Working on the math behind this output greatly enhanced my knowledge of CNN's and how the layer's work.
    To avoid overfitting, I introduced drop out layer with 25% probability after each fully connected linear layer with ReLU activation.
    Resized images to 256 and then to 224 by cropping at the center. Input tensor size is 224 because VGG-16 Takes 224x224 images as input, so we resize all of them
    Augmentation was done to make sure dog face is identifiable in mirror image i.e horizontal flip and image was also rotated randomly by 45 degrees first but after many iterations settled on 15 degress, all this done randomly.
    Also, to match the normalization used when the models were trained; each color channel was normalized separately, where the means are [0.485, 0.456, 0.406] and the standard deviations are [0.229, 0.224, 0.225]
5. Custom CNN gave an abmyssal 16% accuracy, next up is to use transfer learning on pretrained RestNet50 model.
6. ResNet50 logic:
    I shifted to ResNet50 which has less error rate than VGG16 and is also one of the recommended models.
    I avoided changing anything in the features and just made change in the last classification layer as it is a good model on the ImageNet and has hence already seen similar data, so no need to train the whole model for features again.
    I obtained 71% accuracy with SGD optimizer and 25 epochs. Wanting to reduce the iterations for reaching low error rates, I tried used Adam optimizer and obtained 85% accuracy with 20 epochs.
    
    
## General Obsrvations
1. The output is good, could be better.
2. The complete app in itself is very complicated and has too many unncessary cells, which could be moved to a python module for reuse.
3. The app could return top-N class probabilities and not just the final class.
4. The algorithm is trained and evaluated on images that are very close, if the dog appears far away it may fail to detect.
5. Thus better training images in more random poses could be used. Also images where dogs and humans are in the same picture could be used for training.
6. The code can be cleaned further to make it more clean and easy to execute and the app can be served as a Flask API, where we can take ensemble output from two or more different models to get the best result.
