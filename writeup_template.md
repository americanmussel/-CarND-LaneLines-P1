 # **Finding Lane Lines on the Road** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.
[image02]: ./test_images_output/start.jpg "Start"
My pipeline consisted of 5 steps. First, I converted the images to grayscale and used a gaussian blur to smooth out pixelation that might lead to artificially high gradients.
[image03]: ./test_images_output/grayscale.jpg "Grayscale"
With the image ready for edge detection, I measured the pixel brightness gradient at each point, keeping only points with a high gradient and adjoining points with a moderate gradient.
[image04]: ./test_images_output/edges.jpg "Canny Edges"
Left with the edges marked by quick changes in brightness, I next applied a filter to restrict our vision to the roadway.
[image05]: ./test_images_output/filtered_region.jpg "Restricted Region"
These X-Y coordinates could be transformed into lines in Hough Space, where the X-Y lines through each point would act as "votes" for where a line should be drawn on the image. The output of this vote is the raw mapping of lines on the road.
[image06]: ./test_images_output/raw_lines.jpg "Raw Lines"
My last step was to split the image in half - one for each lane line - and run a linear regression on the hough lines to model the location of both lane lines. 
[image07]: ./test_images_output/final.jpg "Final Lines"

In order to draw a single line on the left and right lanes, I chose not to modify the draw_lines() function directly, because seeing the edges detected in each frame is incredibly useful for debugging. Instead, I created a second function to perform the extrapolation. I split the image into two, translate the N x M x 3 image into an N x M matrix, then run a linear regression on all the points on the hough lines.


### 2. Identify potential shortcomings with your current pipeline


This pipeline drastically overfits the data provided. The linear regression is easily distracted by shadows, brightly-colored concrete roadways, markings on the roadside, and even the hood of the car. I solved this primarily by restricting the viewport, but should actually solve it by better tuning edge detection parameters - possibly through trained parameters rather than prescribed ones.
 
Another shortcoming is roadway curvature. Because I split the image in two, a sharp curve in the roadway may distract the linear regression. Again, this is due to over-fitting the viewport to the highway data provided.

### 3. Suggest possible improvements to your pipeline

A possible improvement would be to improve parameter tuning for more stable lines via machine learning - e.g. supervised learning from the example videos. This would help the linear regression to be less easily distracted by shadows and stray road markings.

Another potential improvement could be to eliminate the image splitting used for linear regression. This is a super prescriptive and not very robust method of modeling the lane lines. It would be better to make use of the existing hough lines by bucketing them into positive slope (left side) & negative slope (right side), then averaging the coefficients of these hough lines into a single model for each lane line.