## Writeup


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
[image_CarNotCar]: ./output_images/CarNotCar.PNG
[image_HOG]: ./output_images/HOG.PNG
[image_Windows]: ./output_images/Windows.PNG
[image_Pipeline]: ./output_images/Pipeline.PNG
[image_Test1-4]: ./output_images/Test1-4.PNG
[image_Test5-7]: ./output_images/Test5-7.PNG

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

You're reading it!

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the third code cell of the IPython notebook.  

I started by reading in all the `vehicle` and `non-vehicle` images.  
The training data set contains 8792 car images and 8968 not-car images.
So, there is almost an equal amount of car images and not-car images.
Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image_CarNotCar]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `YUV` color space and HOG parameters of `orientations=15`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:

![alt text][image_HOG]

#### 2. Explain how you settled on your final choice of HOG parameters.

I tried and tuned various combinations of parameters and color spaces to get a high test accuracy of the SVC.

My final choice of parameters are:

| Parameter        | Value   |
|:-------------:|:-------------:|
| Color space      | YUV        |
| orientations      | 15          |
| pixels per cell      | 8      |
| cells per block     | 2        |
| HOG channels | ALL |
| spatial size | (32, 32) |
| histogram bins | 32 |
| use spatial features| yes|
|use histogram features | yes |
| use HOG features | yes |


#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM using the standard options in code block 5 (Train classifier). The training set contained 14208 labels and a feature vector of length 11988.
I combined spatial features, histogram features and HOG features.
The resulting accuracy is 0.9913.


### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

The sliding window search is implemented in code block 6.
I decided to search in the image from y-position 400 to 700 (lines 235-240). I used different scaling factors (1.0, 1.3, 1.7) to identify cars in different distances.

![alt text][image_Windows]

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on three scales using YUV 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are some example images:

![alt text][image_Pipeline]
---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my test video.](./test_video_result.mp4)

Here's a [link to my video result.](./project_video_result.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

The implementation is in code block 6.
I recorded the positions of positive detections in each frame of the video.  
From the positive detections I created a heatmap and then thresholded that map with value 2 to identify vehicle positions (line 300).  
Then I evaluated the heatmaps of the previous 8 frames with `deque()`-Variable of length 8 (lines 303-307).
I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap (line 310).  
I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from 7 different test images, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last column.

### Here are seven test images and their corresponding heatmaps:

![alt text][image_Test1-4]
![alt text][image_Test5-7]




---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

It took quit long to find a suitable combination of parameters for the feature extraction by hand.
Next time I would write an automatic routine, that tries all different combinations and yields the best combination in the end.
Furthermore, the algorithm is far from running in real-time.
It needed almost an hour for the project video.
