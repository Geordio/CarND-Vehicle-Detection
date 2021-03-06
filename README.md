# Vehicle Detection
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)


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
![sample_images](https://github.com/Geordio/CarND-Vehicle-Detection/blob/master/output_images/dataset_samples.png)


Following the previous projects, I had doubts over the perfomance of the RGB colourspace, and anticipated that another colour would give better performance.
From a human eye perspective, it appears that a number of colour spaces produce potentially good images and HOG feature images that could potentially be good feature sources.
HLS L Channel, HSV V Channel, LAB L Channel, and the YCbCr Y  channel all produce outputs that look reasonable when visualised.
 However, in order to determine which performs best I will use each colourspace and channel (all, 0,1,2) s an input to my classifier.
 Below are visualisations of the channels of the candidate colour spaces, followed by the HOG feature image of each of these.

Visualisation of Vehicle, HLS Colorspace
![colourspaces](https://github.com/Geordio/CarND-Vehicle-Detection/blob/master/output_images/hog_veh_hls.png)

Visualisation of Vehicle, HSV Colorspace
![colourspaces](https://github.com/Geordio/CarND-Vehicle-Detection/blob/master/output_images/hog_veh_hsv.png)

Visualisation of Vehicle, LUV Colorspace
![colourspaces](https://github.com/Geordio/CarND-Vehicle-Detection/blob/master/output_images/hog_veh_luv.png)

Visualisation of Vehicle, RGB Colorspace
![colourspaces](https://github.com/Geordio/CarND-Vehicle-Detection/blob/master/output_images/hog_veh_rgb.png)

Visualisation of Vehicle, YCrCb Colorspace
![colourspaces](https://github.com/Geordio/CarND-Vehicle-Detection/blob/master/output_images/hog_veh_ycrcb.png)

Visualisation of Vehicle, LAB Colorspace
![colourspaces](https://github.com/Geordio/CarND-Vehicle-Detection/blob/master/output_images/hog_veh_lab.png)

Visualisation of Vehicle, YUV Colorspace
![colourspaces](https://github.com/Geordio/CarND-Vehicle-Detection/blob/master/output_images/hog_veh_yuv.png)

Visualisation of Non Vehicle, HLS Colorspace
![colourspaces](https://github.com/Geordio/CarND-Vehicle-Detection/blob/master/output_images/hog_non_veh_hls.png)

Visualisation of Non Vehicle, HSV Colorspace
![colourspaces](https://github.com/Geordio/CarND-Vehicle-Detection/blob/master/output_images/hog_non_veh_hsv.png)

Visualisation of Non Vehicle, LUV Colorspace
![colourspaces](https://github.com/Geordio/CarND-Vehicle-Detection/blob/master/output_images/hog_non_veh_luv.png)

Visualisation of Non Vehicle, RGB Colorspace
![colourspaces](https://github.com/Geordio/CarND-Vehicle-Detection/blob/master/output_images/hog_non_veh_rgb.png)

Visualisation of Non Vehicle, YCrCb Colorspace
![colourspaces](https://github.com/Geordio/CarND-Vehicle-Detection/blob/master/output_images/hog_non_veh_ycrcb.png)



Visualisation of Non Vehicle, YUV Colorspace
![colourspaces](https://github.com/Geordio/CarND-Vehicle-Detection/blob/master/output_images/hog_non_veh_yuv.png)

    


## Classifier

I used a Linear SVC from the sklearn libraries.
Due to time constraints I didn't have time to experiment with other classifiers.

As I starting point, I used only the HOG features as the input features. I had planned to expand the feature input to use spatial binning but again de to the time constraints, and due to the fact I achieved acceptable performace with only HOG features I did not continue with this plan.
I used the following parameters for the HOG function:
orient=9
pix_per_cell=8 
cell_per_block=2 
 

I found that increasing the Orient parameter resulted in signicantly increasing the training time, but without significantly improving performance.
I tried 8 and 16 for the pixels per cell parameter. Using a value of 8 had an increased training time, but slightly better performance or the test images.
I didnt experiment with the cells per block parameter.

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
After deciding upon the features and classifier, I used the StandardScaler from sklearn to scale my features

Note, that although that HLS, HSV and YCrCb all performed similarly on the test set, ultimately on the video and test_image files, the YCrCb colour space performed much better than any other colourspace, so I finally used this.


## Sliding Window Search

I started from the code provided in the lesson as the basis for this functionality. This is implemented in the 'find_cars' method.

I initially started using a single window size, and searched my region of interest with this. However, as pointed out in the lessons, the vehicles further away are obviously musch smaller, hence I implemented functionality to use a larger window in the foreground, with smaller windows in the middle distance and smallest windows at the furthest distance away.
I didn't adjust the size depending upon the horizontal distance from the centre of the vehicle but that should be considered in the future.

The overlap was specified using the number of cells to overlap by. I used 2 cells as the overlap originally, then reduced this to 1 as I found that this improved the perfomance, although this had a penalty to the processing time, which is equivalent to an overlap of 75%.
I trialed using a smaller overlap between windows, but found that it did not detect cars that were present.

I established the size of windows again using trial and error. I started with a scale of 1, which gives a window of 64 x 64.
I initially implemented additional windows with scales of 1.5 and 2, but found that this was not detecting the nearer vehicle consistently, when using the HLS or HSV colourspaces. I hence increase to include a scale of 3 and 4.
I found that this worked well. I did try scales of less than 1 but found that this created a lot of false positives. 
Keeping the number of window scales as small as possible will aid the speed performance. Ideally I would like to change teh overlap back to 2 cells from 1 as this would also improve perfomance.

Below is a visualisation of my window search. (Note that the titles have become corrupted, they are from top to bottom, input image, Scale 1, Scale 1.5, Scale 2, Scale 3, Scale 4.

![windows](https://github.com/Geordio/CarND-Vehicle-Detection/blob/master/output_images/windows_all_all.png)

The windows in Red are ones where the pipeline has detected a vehicle. Note that from this visualisation, I decided that the larger window should be higher up the image, as the Scale 3 window fails to detect a vehicle, and is very close to the boundary of the subject vehicle.
Note that this shows all the scales, but finally I only used 1, 1.5 and 2

I used the code from the lesson material as a basis from my implementation of Heatmaps in order to aggregate multiple detections of a single vehicle.
Below is an example of an output from the processing along with the associated heatmap

![heatmaps](https://github.com/Geordio/CarND-Vehicle-Detection/blob/master/output_images/heatmap.png)

When used for a single frame, the threshold for heatmap detection is set to 4, when used for video, the threshold is set to 15 over 10 frames.

Below is the output for all test images provided.

![test output](https://github.com/Geordio/CarND-Vehicle-Detection/blob/master/output_images/test_images_output.png)

Note that in the test3 image the white car is missed.



## Video Implementation

For processing of videos, my pipeline essentially remains the same. The only key difference is that previous frames and detections are used with the detections on the current frame in order to smooth the output and eliminate false positives.
In order to do this, I store the boxes detected on the previous 10 frames
The boxes from the previous frames are used to create an overall heatmap of the recent frames, with this then being used to create the bounding boxes for each detected vehicle. Then the aggregated heatmap is filtered by threshold, with a value of 15 detections over teh 10 frames required. Finally I draw a bounding box round the maximum limits of the heatmap.


The output from the 'project_video.mp3' is at the following link ![output video](https://github.com/Geordio/CarND-Vehicle-Detection/blob/master/project_output.mp4)

The output from the 'test_video.mp3' is at the following link ![output video](https://github.com/Geordio/CarND-Vehicle-Detection/blob/master/test_video_output.mp4)

The output from the 'challenge_video.mp3' from the advanced lane lines project is at the following link ![output video](https://github.com/Geordio/CarND-Vehicle-Detection/blob/master/test_video_output.mp4)
Note that this shows the detection of vehicles on the opposite carriageway
## Discussion

### Potential Improvements
The pipeline could be improved by the following:
- Considering the colour histogram and spacial bin features
- Exploration of different classifier types, such as implementing decision tree, Naive Bayes
- Using more data to train the classifier. This could be via additional images or through augmented data. The number of samples in the data is quite small. Even though the classified works well against the test set, the test dataset images and the cars from the videos are not too similar.
- I would be interested to see if a CNN would be faster if optimised for running on a GPU
- I had signifianct issues when transferring from the test set I created to the images from the test image files and the video feed. I don't know if this was purely down to the variance between the 2 datasources, or what I suspect, that it might be down the the difference of how png and jpg files are handled by mpimg and the other python libraries.

### Limitations
- The classifier would need to be retrained if different vehicle types are released to market that could result in different HOG signatures
- The pipeline does not consider gradients, hence the assumptions that vehicles at a given y value are at a particular distance from the car would not always be correct.
- The majority of images in the dataset are of vehicle taken from the rear, hence on a single carriageway where vehilces are likely to be seen in both directions could have issues in identifying cars.
- As with the LaneFinding project, the pipeline does not compensate the bumps in the road surface. On a real car, the system could make use of the vehicles build in sensors such as yaw and steering angle.
- The pipeline is not optimised and cannot cope with real time processing of a video stream.
- For some of the different scaled sliding windows, there is a gap at the right hand side of the image, where only a partial box can fit. This should be addressed.
- Only using a forward facing camera limits the field of vision and the amount of time to track vehicles. If the vehilce was fitted with more cameras, like many autonomous vehicles are then the peipleine could be updated to allow tracking of vehicles on all cameras
- The pipeline loses vehicles if they are blocked by another vehilce. This can be seen on the project video output. When hidden vehicle reappears, there is a period where the 2 vehicles are detected as a single vehicle. 
- Vehicle on teh opposite carriageway are occasionally detected. These are not that important for the path planning of our vehicle and should be classed differently than vehicles on the same carriageway.