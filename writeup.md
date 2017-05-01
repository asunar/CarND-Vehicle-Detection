**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./examples/car_not_car.png
[image2]: ./examples/HOG_example.jpg
[image3]: ./examples/sliding_windows.jpg
[image4]: ./examples/sliding_window.jpg
[image5]: ./examples/bboxes_and_heat.png
[image6]: ./examples/labels_map.png
[image7]: ./examples/output_bboxes.png
[video1]: ./project_video.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points 

---
###Writeup / README

Used `Vehicle Detection` notebook as the sandbox to test out ideas, parameters and etc. Project video is processed in `VehicleDetectionSubmission` notebook.


###Histogram of Oriented Gradients (HOG)

####1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the fourth code cell of the `Vehicle Detection` notebook 

I started by reading in all the `vehicle` and `non-vehicle` images.  I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed a random image from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Please see the output of the fourth cell of the `Vehicle Detection` for a random car/not car and the corresponding `hog_features` images

####2. Explain how you settled on your final choice of HOG parameters.
These are the parameters I ended up with:
* color_space = 'YCrCb' #RGB, HSV, LUV, HLS, YUV, YCrCb
* orient = 9
* pix_per_cell = 8
* cell_per_block = 2
* hog_channel = 'ALL' #0,1,2 or 'ALL'
* spatial_size=(32,32) #Spatial binning dimensions
* hist_bins=32 # Number of histogram bins
* spatial_feat = True
* hist_feat = True
* hog_feat = True

I tried various combinations of parameters and got the best results with YCrCb color space and using all for the hog channel.
Spatial size of 32x32 provided a nice balance of performance and accuracy.

####3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM using spatial, color histogram and hog features; transformed the training features with the StandardScaler.

Here is some diagnostic info from the `Vehicle Detection` notebook's fifth cell in which I trained the classifier.
Using: 9  orientations, 8  pixels per cell, 2  cells per block, 32  histogram bins and  (32, 32)  spatial sampling

* Feature vector length: 8460
* 29.71  Seconds to train SVC
* Test accuracy of SVC:  0.9899

###Sliding Window Search

####1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

Used the `slide_window` function in `Vehicle Detection` notebook's sixth cell to implement window search. Started by providing reasonable ystart/stop values to do the window search in the lower portion of the image. Picked window size and overlap values after a few trials.


####2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on two scales using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result. 

I processed the test images using the pipeline in `Vehicle Detection` notebook's sixth cell. In the output you can see the bounded boxes drawn on each image.

---

### Video Implementation

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./processed_project_video.mp4)


####2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

In `Vehicle Detection` notebook's eighth cell, you can see the six test images with bounded boxes with their corresponding heatmaps. Applied a threshold to filter out the false positives in the eleventh cell.


In `VehicleDetectionSubmission` notebook's last cell processed the video by processing each frame with `vehicle_detector`, used a frame queue to smooth out boxes between frames.



---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  

