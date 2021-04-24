<p align="center" >
  <img src="images/drone_img1.jpg" width="800">
</p>

# Drone Object Detection and Classification

## Table of Contents

* [General Information](#General-Information)
    * [Building Training Set](#Building-Training-Set)
    * [Transfer Learning](#Transfer-Learning)
    * [Tools Used](#Tools-Used)
    * [Future Improvements](#Future-Improvements)


## General Information
As the worlds militaries move closer to utilizing drones for wider mission sets, with zero risk to human operators, it is logical to assume those drones will be inclinded to fly deeper into higher risk engagement zones. When this happens it will become more and more difficult to identify the drone type and country of origin. In order to create a machine learning model to assist in drone detection and classification there must be a very large dataset to train from. This highlights two issues when building a dataset:

1. Some fixed wing military drones look very similar, even to the human eye, at farther distances making it difficult to create accurate training datasets using only open source images.
2. The amount of open source images is very limited and usually only include images of the drone parked on the runway, contrary to the airborne aspect needed for training. 

In order to overcome these challenges we will utilize the power of a pretrained segmentation model, OpenCV, and Transfer Learning.

### The project files are organized as follows:

- find_box.py: Python file used to break down video into frames, augement the images, create bounding boxes, and split the verified images into subsequent folders. 
- generate_allrecords.py: Python file used to create CSV, TFRecord, and COCO files for model training.
- Model Training Files...
- images: All images used in the README file
- src: Contains a python file with all the functions used in this project
- data: Includes sample data to run the model

### Resources:

* Roboflow (https://blog.roboflow.com/pp-yolo-beats-yolov4-object-detection/)
* Raccoon Github (https://github.com/datitran/raccoon_dataset)
* Countour/Bounding Box (https://www.youtube.com/watch?v=WQeoO7MI0Bs)
* Pixellib (https://pixellib.readthedocs.io/en/latest/change_image_bg.html)
_______________________________________________
## Building Training Set:

A quick Google search highlights the first issue with building a training set. Searching for a Chinese 'CH-5 drone' provides many pictures of fixed wing drones but it is not immediately evident if all the images searched are in fact a 'CH-5 Drone'. The two results from that search, shown below, demonstrate the difficultly accurately classifying the results. The drone on the left is a Reaper drone, built by the United States, while the drone on the right is a CH-5, built by China. Without the US Military marking on the back of the drone in the picture on the left it would be very difficult to accurately classify it. Misclassifying images in the training set would certainly spell disaster for the final models precision, recall, and accuracy. 

<p align="center" >
  <img src="images/drone_img2.jpg" width="800">
</p>

The next option is to find video clips of specific drones and break the video down into frames for training, validation and testing. This method was much more reliable for label accuracy and provided a larger set of images to work with. The following number of frames were taken from open source Youtube Videos:

* Global Hawk (USA) -  1396 Frames
* Reaper (USA) - 1087 Frames
* Wing Loong (CHA) - 606 Frames

One advantage of object detecting when it comes to flying drones is that most of the time the drone will be the only object within the frame, making it ideal for using a segmentation model (Pixellib: https://pixellib.readthedocs.io/en/latest/change_image_bg.html) to identify the aircraft and then drawing a contour around the image. This will allow for a bounding box to be drawn around the largest contour in the image, which most of the time is the drone. Below shows the original image on the left followed by a segmented image of the drone in the middle and a final image on the left which includes the contour line and bounding box. 

<p align="center" >
  <img src="images/drone_img3.jpg" width="800">
</p>

We will then use image augmentation to rotate, shear, flip, and zoom the images so we are able to get over 15,000 images of each drone. 

* Global Hawk (USA) -  19,814 Training Frames
* Reaper (USA) - 15.244 Training Frames
* Wing Loong (CHA) - 17,564 Training Frames

Because the segmentation model does not always detect the aircraft and some of the videos do not always show the drone, we must browse the bounded images to ensure all the images contain the drone for correct classification. 

<p align="center" >
  <img src="images/drone_img4.jpg" width="800">
</p>

By implementing our own bounding boxes we are able to utilize those coordinates to create individual Pascal VOC files for each image and subsequently create CSV and TFRecord files for use in the Transfer Learning of EffecientDet. 

____________________________________________________________

## Supervised Learning Model: EfficientDet

EfficientDet is an object detection model created by the Google brain team, achieving the highest accuracy with fewest training epochs in object detection tasks. It is an advanced version of EfficientNet, which was the state of art object detection model in early 2019. This problem set requires accurate object detection using standard video cameras, which provide video feeds with approximately 30 frames per second (FPS). The diagram below shows that 'Efficientdet_D0' will provide the best Mean Average Precision (mAP) at video feeds above 30 FPS.

<p align="center" >
  <img src="images/drone_img5.jpg" width="800">
</p>

With help from the customized EfficientDet model structure built by RoboFlow (https://blog.roboflow.com/train-a-tensorflow2-object-detection-model/) we are able to feed in our own dataset and obtain impressive results. 
_________________________________________________________

## Final Results

After 30,000 training steps final results from the supervised models shows that the Gradient Boosting Model, used on full article text, demonstrates the best results when to predict the popularity of an article.

* mAP: 0.587327
* mAP@.50IOU: 0.932432
* mAP@.75IOU: 0.640563

<p align="center" >
  <img src="images/drone_img6.jpg" width="800">
</p>
_______________________________________

## Tools Used

***Python:***

Data Gathering: Python<br>
Data Analysis: Google Colab, TensorFlow, Pandas, Scikit-Learn<br>

***Visualization:***

Data Visualization: Tensorboard

_______________________________________
## Future Improvements

1. Translate the training dataset to COCO format and determine model results from Yolov5 for comparison to EfficientDet.
2. Run model on drone video to see the real time results of the model.

