#**Finding Lane Lines on the Road** 

##Writeup Template

###You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

###1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consist of the following steps:
    - convert the image to a gray image
    - apply Gaussian smoothing on the grayscale
    - run Canny on the gray image to detect ane lines
    - select the region of interest by defining a quadrilateral region, By doing this I can exclude lines that were detected by Canny, but are not part of the lane line to be detected
    - Run Hough on edge detected image to connect dots and lookinf gor lines in Hough space. In order to get an optimal result a fine-tuned the parameters of the Hough function
    - another function is called to draw the lines on a blank image (line image). the coordinates of the lines are the output of Hough transformation.
    - by combining the color image with the line image we get the final result
    



In order to draw a single line on the left and right lanes, I created a new function, called draw_solidlines, that does the following:
    - separating line segments into 2 groups (left and right side)
        - to determine to which group each line segment belongs I use (y2-y1)/(x2-x1). if the value is negative then it is the left line, if positive then right line
    - I ended up with 2 matrixes that contain the coordintes (x,y) of the line segments. I do not consider them as end-points of the lines anymore, only as dots.
    - then I use the linear least square method to determine a single line that best fits to the left and right line segments
    - based on the result of the least square method I know the coefficient of y = mx + c, then I use (m,c) to draw a line on the left any right side.
    
    


If you'd like to include images to show how the pipeline works, here is how to include an image: 

![alt text][image1]


###2. Identify potential shortcomings with your current pipeline


Issue I am still facing is that during the separation of the line segments into right and left lines based on (y2 - y1)/(x2 - x1) sometimes gives wrong "result". Means that I assume that like if the result of the division is negative then it is the left line, but there are really short line segments on the right side where y2-y1/x2-x1 gives a positive result, so I put them to the "wrong" group.
I tried to avoid the detection of those really small lines buy adjusting the parameters of the Hough transformation (threshold, min_line_length). If I set them too high, there are images where I could not detect any lines on the left or right side. If I have lower values, I have (x,y) values that go to the wrong group (as described above) that causes that my solid line is not on the lane line. With the current parameters it seems ok, but on the side where the road line is dotted, the drawn line is shorter.



###3. Suggest possible improvements to your pipeline

I tried to use other methods to draw the lines. Like calculated avarage then extrapolated. Probably this is another method I will try to fully implement.

In addition to that I can further play with the parameters (Canny, Hough tranformation) to get a better result.
I need to investigate how I can better separate line segments into 2 parts.