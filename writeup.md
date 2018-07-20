## Writeup - Vehicle Detection

---

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
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it! A notebook containing code named project.ipynb is available, as well as an HTML export of the notebook.

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The notebook is fairly well organized, with sections to clearly identify steps.

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`). 

#### 2. Explain how you settled on your final choice of HOG parameters.

After a bit of trial and error, and after reading a couple messages from the Slack group for the course, I found that the YUV colorspace with 9 orientations, 8x8 px per cell, and a 2x2 cell per block configuration yielded high accuracies on the test set.


#### 3. Describe how you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM the LinearSVC classifier and a StandardScaler to normalize data.

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

I took a different approach and defined a step counter to move the window instead of a overlay apporach. Scales were just a basic 0.75 to 2.0 in 0.25-0.5 intervals (whatever numbers seemed right).

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

I went heavily for performance optimization, choosing in many cases to sacrifice accuracy, but I did add in quite a few sliding window searches. Especially with the lane line detection addition, I knew the pipeline had to be not only robust, but very quick.

The biggest optimization, as written in the notebook, was dropping all color processing and using only the gradients for feature extraction in the classfier.

Example images are shown, clearly labeled, in the notebook.

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_video_out.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  


### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

The pipeline, although fairly slow, performs quite well, with few false positives (that are all fixed within a frame or two). 

Although I made the scale so that it wouldn't pick up the small cars (sacrifice for performance) it would be a way to be more "robust," although I would have to question how much really knowing information about vehicles that far away will help, and more importantly, is it worth the performance sacrifice?

