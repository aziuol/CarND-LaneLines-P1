# **Finding Lane Lines on the Road** 

## Name: Louiza Polydorou

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./output1.jpg "White lane lines"
[image2]: ./output2.jpg "Yellow lane lines"
---

### Reflection 

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

The pipeline function is to be applied on the vehicle vision system to identify lane markings on the road.
It's implementation is described step-by-step below.


## 1.0 Pre-processing steps
(1.1) Grayscale conversion of image -> simplify the image data from 3 scales to 1.
(1.2) Gaussian Blur - Used for smoothing out noise by averaging the single bright/dark pixels from the image,  leaving us with a "cleaner" image to work with.

## 2.0 Detecting Lines 
(2.1) Define Threshold parameters on the grayscale image to apply Canny Edge Detector function to find the lines in the image. The parameters I used were 48 for the low threshold and 180 for the high threshold. ( Note the recommended ratio was 1:3 - 1:2, but with this i was able to pick up more of the segmented white lines).  
Note that the output is of all   edges detected in the image, inlcuding other non-road features like trees.

## 3.0 Defining the road as the Region of interest
Assuming the vehicle is on a road, line markings should be within it's trajectory which can be defined as a ROI. We will ignore "lines" from other aspects of the image for now as a simplification.

(3.1)  The ROI is defined with vertices whereby the apex should sit at the horizon -> in the footage provided the horizon sits approx at 270 however for different vehicle installations this may be lower or higher.  
This is implemented using the vertices of the triangle, we apply the ROI mask "masked_edges" to the canny detected lines.  These are our plausible lane markings

(3.2) Use Hough Transform to solve for lines that intersect (which pixels make line) the output from the function is intersecting points in image space. That is, the raw intersecting lines.  

The clasification between left and right line is determined by the draw_lines function.  This was modified so that gradient of the raw line markings was evaluated to determine if the raw line segment belonged to the left lane or right lane.

The lanes are assumed to be linear polynomials, we used the least squares polynomial fit (polyfit function) and poly1d function to output the closest line according to the raw lane markings.   


Note the ROI could probably be explored more, I opted to include the depth of the lines to the horizon, however I think by doing so I'm adding some jitter to the overal lane line.



# Pipeline extracts  

![alt text][image1]
![alt text][image2]


### 2. Identify potential shortcomings with your current pipeline

I haven't really tested with other videos other than the ones provided. From the challenge video it's clear the pipeline is not robust with strong features in the horizon, non-uniform roads and variable road lighting conditions.


### 3. Improvements / Future implementation for current pipeline
Potential improvements are described with reference to a step.


(3.0)  I thought of applying individual thresholds to each RGB value. E.g to filter out "blue" or "green" features that would not be typical road features. However, the first step is to implement a robust "line detecting" method with minimum processing. 

(3.1)  Exploration of other filters to be looked at in future.   

(3.2) I am applying the Canny Edge detection to the entire image, however doing this step now means that I am extracting all the edges of the image.  This was more for my understanding, however as a consequence other "lines" interfere with the lane detection. 

(3.3)  The ROI is large -> I can narrow this down and make it smaller, however I will only do this once the function is robust enough with different video footage and variable roads.  The example footage provided is similar.  Currently the horizon is hardcode as 270 however optimally this should be self adjusting or have some window bounds associated with it.

(3.4)  Confirm interference with other lines (eg. trees, other lines in the horizone.  Again quick solution to reduce the ROI.


Possibly also add a weight to the line detection, the ones "closer to the camera" could have more weight, and lines further e.g in the horizion could have less weight..  

Also, line segments at a different distances from the

- Adaptive "background" filter for the "road", with the intent of weighting down variation in pixels from shadows etc or uneveness in the road colour.  This would potentially include some normalisation.

