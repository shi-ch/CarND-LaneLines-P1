# **Finding Lane Lines on the Road** 


---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---


### Reflection

### 1. Pipeline Description

My pipeline consisted of 8 steps. 
First, mask color ranges(white and yellow) to filter out other colors.
Second, convert the image to gray scale
Third, use Gaussian blur to smoothen the edges
Fourth, use canny algorithm to detect edges.

[image1]: ./examples/test1.png "Some challenges in adjusting parameters"

Fifth, mask region out of interested areas to be dark, 
Sixth, use cv2.HoughLinesP to detect lines
Seventh, with the segments in selected region found from last step, group them to two groups(left lane and right lane) based on the slope and intercept of each segments. The idea is noticing left lane will intercept with bottom of the picture on the left half, the right lane will intercept with the bottom on the right half. So we could use this to separate the segments of these two lanes.
Eighth, draw the lanes we computed onto the original image. The lanes were drew on a black empty image of original size then "overrap" with original image.

    color_image = select_rgb_white_yellow(image)
    gray_image = grayscale(color_image)
    blur_image = gaussian_blur(gray_image)
    cannyed_image = canny(blur_image)
    masked_image = select_region(cannyed_image)
    lines = hough_lines(masked_image)
    lanes = get_lane_from_segements(image, lines)
    result = draw_my_lines(image, lanes)

Some challenges to overcome in the pipeline:

### When there is shadow

When there is shadow, the challenges were 'white' and 'yellow' are no more so 'white' or 'yellow', Some preprocessing could be helpful here.

![When there is shadow](https://github.com/chunhui-shi/CarND-LaneLines-P1/blob/master/test_images/shadow1.jpg)

More images with shadow to handle

![When there is shadow](https://github.com/chunhui-shi/CarND-LaneLines-P1/blob/master/test_images/shadow2.jpg)

### 2. Summary


One potential shortcoming of this approach would be the way to detect lanes relying on the colors of the lanes ("yellow and white") has to be known first before we could actual coding our pipeline manually figure out the mask to get the lanes. Ideally this should be detected automatically.

Another shortcoming could be the way to group the segments into two lanes, Right now I use 'fall into left half or right half" to detect a lane which may not always be feasible.

### 3. Suggest possible improvements to your pipeline

A possible improvement would be to detect the region of roads with cursive edges. Then identify the colors of the road, then with the knowledge of shape of lanes detect what could be a lane and automatically decide what color masks to apply on lanes.

Another potential improvement could be to group the segments into two lanes using k-mean cluster algorithm. With this approach we don't need to rely on the camera position in the middle of two lanes.

Better fault tolerance. Right now we assume can always draw two lane lines for each frame in testing videos. If there is one lane missing in one frame, the code fails. We should consider drawing lanes for current frame using the lanes we drew for previous frames. This approach is kind of like human behaviors. When human could not see lanes temporarily due to any reason, light from cars of opposite direction, blink, or anything, human will assume the lanes are still at the same place.

Smoothen lanes caculated between frames next to each other. To achive this purpose, the calculation may look ahead several frames, detect glitch, or spike frames, in which lanes being drawn might have been interferenced by some spots on ground. then smoothen the lanes(slope and intercept values)

