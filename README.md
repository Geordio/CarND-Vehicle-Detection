# Vehicle Detection
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)


In this project, your goal is to write a software pipeline to detect vehicles in a video (start with the test_video.mp4 and later implement on full project_video.mp4), but the main output or product we want you to create is a detailed writeup of the project.  Check out the [writeup template](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) for this project and use it as a starting point for creating your own writeup.  

Creating a great writeup:
---
A great writeup should include the rubric points as well as your description of how you addressed each point.  You should include a detailed description of the code used in each step (with line-number references and code snippets where necessary), and links to other supporting documents or external references.  You should include images in your writeup to demonstrate how your code works with examples.  

All that said, please be concise!  We're not looking for you to write a book here, just a brief description of how you passed each rubric point, and references to the relevant code :). 

You can submit your writeup in markdown or use another method and submit a pdf instead.

The Project
---

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

Here are links to the labeled data for [vehicle](https://s3.amazonaws.com/udacity-sdc/Vehicle_Tracking/vehicles.zip) and [non-vehicle](https://s3.amazonaws.com/udacity-sdc/Vehicle_Tracking/non-vehicles.zip) examples to train your classifier.  These example images come from a combination of the [GTI vehicle image database](http://www.gti.ssr.upm.es/data/Vehicle_database.html), the [KITTI vision benchmark suite](http://www.cvlibs.net/datasets/kitti/), and examples extracted from the project video itself.   You are welcome and encouraged to take advantage of the recently released [Udacity labeled dataset](https://github.com/udacity/self-driving-car/tree/master/annotations) to augment your training data.  

Some example images for testing your pipeline on single frames are located in the `test_images` folder.  To help the reviewer examine your work, please save examples of the output from each stage of your pipeline in the folder called `ouput_images`, and include them in your writeup for the project by describing what each image shows.    The video called `project_video.mp4` is the video your pipeline should work well on.  

**As an optional challenge** Once you have a working pipeline for vehicle detection, add in your lane-finding algorithm from the last project to do simultaneous lane-finding and vehicle detection!

**If you're feeling ambitious** (also totally optional though), don't stop there!  We encourage you to go out and take video of your own, and show us how you would implement this project on a new video!

## How to write a README
A well written README file can enhance your project and portfolio.  Develop your abilities to create professional README files by completing [this free course](https://www.udacity.com/course/writing-readmes--ud777).


# Introduction

This document forms the write up report for my submission of the Vehicle Detection project

My project consists of the following files:

| File        | Description        |
| ------------- |:-------------:|
| dataset_explore.ipynb      | jupyter notebook used for initial dataset exploration, including, size, mix, sample images, and colourspace visualisations |
| solution.ipynb | jupyter notebook for full pipeline of my solution    |
|output.mp4 | output video of my processing of a test video |
|README.md| Project report (this file)|


# Initial exploration

Initially I explorer the dataset and familarised myself with teh HOG representation of the data

## Dataset

I used all sub directories of the dataset, i.e both the GTI and KITTI folders of the vehicle folders.
The dataset consists of:

 - Vehicles images: 8792
 - Total Non Vehicles image: 8968
 
 Hence the dataset is fairly evenly balanced, so should not favour one class over the other.
 
 It is worth noting that some vehicle images are from angles that will not be seen in the video samples, so are not actually helpful for the classification and could result in incorrect classifications.

Below are some sample of the vehicle and non vehicle images, selected at random.

I think experiemtated with the HOG function and colourspaces, inorder to try to identify which colour space and channels could potentially give teh best perfomance.
I selected a vehicle and non vehicle image at random and used the get_hog_features method form the lessons to return the HOG features and image for each of the following colourspaces, return individual channels and all channels, and finally visualising them all.

Below are the visualisations
![sample_images](https://github.com/Geordio/CarND-Vehicle-Detection/blob/master/output_images/sample_images.png)


Following the previous projects, I had doubts over the perfomance of the RGB colourspace, and anticipated that another colour would give better performance.
From a human eye perspective, it appears that a number of colour spaces produce potentially good images and HOG feature images that could potentially be good feature sources.
HLS L Channel, HSV V Channel, LAB L Channel, and the YCbCr Y  channel all produce outputs that look reasonable when visualised.
 However, in order to determine which performs best I will use each colourspace and channel (all, 0,1,2) s an input to my classifier.

![colourspaces](https://github.com/Geordio/CarND-Vehicle-Detection/blob/master/output_images/hog_veh_hls.png)
![colourspaces](https://github.com/Geordio/CarND-Vehicle-Detection/blob/master/output_images/hog_veh_hsv.png)
![colourspaces](https://github.com/Geordio/CarND-Vehicle-Detection/blob/master/output_images/hog_veh_luv.png)
![colourspaces](https://github.com/Geordio/CarND-Vehicle-Detection/blob/master/output_images/hog_veh_rgb.png)
![colourspaces](https://github.com/Geordio/CarND-Vehicle-Detection/blob/master/output_images/hog_veh_ycrcb.png)
![colourspaces](https://github.com/Geordio/CarND-Vehicle-Detection/blob/master/output_images/hog_veh_lab.png)
![colourspaces](https://github.com/Geordio/CarND-Vehicle-Detection/blob/master/output_images/hog_veh_yuv.png)
![colourspaces](https://github.com/Geordio/CarND-Vehicle-Detection/blob/master/output_images/hog_non_veh_hls.png)
![colourspaces](https://github.com/Geordio/CarND-Vehicle-Detection/blob/master/output_images/hog_non_veh_hsv.png)
![colourspaces](https://github.com/Geordio/CarND-Vehicle-Detection/blob/master/output_images/hog_non_veh_luv.png)
![colourspaces](https://github.com/Geordio/CarND-Vehicle-Detection/blob/master/output_images/hog_non_veh_rgb.png)
![colourspaces](https://github.com/Geordio/CarND-Vehicle-Detection/blob/master/output_images/hog_non_veh_ycrcb.png)
![colourspaces](https://github.com/Geordio/CarND-Vehicle-Detection/blob/master/output_images/hog_non_veh_lab.png)
![colourspaces](https://github.com/Geordio/CarND-Vehicle-Detection/blob/master/output_images/hog_non_veh_yuv.png)

    


## Classifier

I used a Linear SVC from the sklearn libraries.
Due to time constraints I didn't have time to experiment with other classifiers.

As I starting point, I used only the HOG features as the input features. I had planned to expand the feature input to use spatial binning but again de to the time constraints, and due to the fact I acheived acceptable performace with only HOG features I did not continue with this plan.

The table below shows the accuracy of each colourspace (and the channels within each colourspace), when used as the input features

|Colourspace|Channel|Accuracy|Train Time
| ------------- |:-------------:|:-------------:|:-------------:|
|RGB|ALL |0.9707|17.1
|RGB|0 |0.9538|4.55
|RGB|1 |0.9606|3.91
|RGB|2 |0.9589|4.03
|HLS|ALL|0.9837|8.51
|HLS|0|0.9336|5.3
|HLS|1|0.9654|4.17
|HLS|2|0.9074|5.89
|HSV|ALL|0.9834|7.49
|HSV|0|0.9299|5.73
|HSV|1|0.9091|5.67
|HSV|2|0.9637|4.08
|YUV|ALL|0.9825|7.86
|YUV|0|0.9699|4.2
|YUV|1|0.9412|3.99
|YUV|2|0.9271|4.82
|LUV|ALL|0.9764|8.86
|LUV|0|0.9606|4.03
|LUV|1|0.9372|4.37
|LUV|2|0.9268|5.23
|YCrCb|ALL|0.9814|6.94
|YCrCb|0|0.9662|4.13
|YCrCb|1|0.9496|4.22
|YCrCb|2|0.9268|4.42
|LAB|ALL|0.978|8.47
|LAB|0|0.9626|4.12
|LAB|1|0.9229|4.11
|LAB|2|0.9158|5.82

From the above results, using all channels of the HLS colourspace would appear to give the best results, although using all of the HSV space is within 0.03% of this.



Sliding Window Search

I started from the code provided in the lesson as the basis for this functionality.

I initially started using a single window size, and searched my region of interest with this. However, as pointed out in the lessons, the vehicles further away are obviously musch smaller, hence I implemented functionality to use a larger window in the foreground, with smaller windows in the middle distance and smallest windows at the furthest distance away.
I didn't adjust the size depending upon the horizontal distance from the centre of the vehicle but that should be considered in the future.





Heat maps


Discussion

The pipeline could be improved by the following:
- Considering the colour histogram and spacial bin features
- Exploration of different classifier types, such as implementing decision tree, Naive Bayes
- Using more data to train the classifier. This could be via additional images or through augmented data. The number of samples in the data is quite small. Even though the classified works well against the test set, the test dataset images and the cars from the videos are not too similar.

Limitations
- The classifier would need to be retrained if different vehicle types are released to market that could result in different HOG signatures
- The pipeline does not consider gradients, hence the assumptions that vehicles at a given y value are at a particular distance from the car would not always be correct.
- The majority of images in the dataset are of vehicle taken from the rear, hence on a single carriageway where vehilces are likely to be seen in both directions could have issues in identifying cars.
- As with the LaneFinding project, the pipeline does not compensate the bumps in the road surface, such as when the vehicle enters and exits what appears to be a bridge section. On a real car, the system could make use of the vehicles build in sensors such as yaw and steering angle
- The pipeline is not optimised and cannot cope with real time processing of a video stream.
- For some of the different scaled sliding windows, there is a gap at the right hand side of the image, where only a partial box can fit. This should be addressed.
