## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

**Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./examples/undistort_output.png "Undistorted"
[image2]: ./test_images/test1.jpg "Road Transformed"
[image3]: ./examples/binary_combo_example.jpg "Binary Example"
[image4]: ./examples/warped_straight_lines.jpg "Warp Example"
[image5]: ./examples/color_fit_lines.jpg "Fit Visual"
[image6]: ./examples/example_output.jpg "Output"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located in "./examples/example.ipynb" (or in lines # through # of the file called `some_file.py`).  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained the result as given in the python book 



### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
Distortion corrected image has been provided in output image folder named "undistorted_chessboard.png" and "undistorted_image.png"

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image. Used saturation, value and R Colour for generating binary image.
Example of this is combined_binary.png.

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform is presented in the python notebook.   It has source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

src = np.float32([[200,700],
                [570,470],
                [780,470],
                [1180,700]])


dst = np.float32([[325,700],
                [325,0],
                [900,0],
                [900,700]])
```


I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image. Example is warped.png



#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

The entire logic of the project is in function process_image_2().The way I identified lane pixels was

a) Plot a histogram. 
b) The position of x pixels where there is the maximum number of pixels in the binary image would be the lanes. 
c) Now dividing the warped image into 9 sliding windows vertically, we search the each window in the region of interest. The region of interest is defined as the area of 100 pixels around the histogram. 
d) For each sliding window, we identify all non zero pixels for left and right lanes
e) We collect all left and right lane points to an array
f) We fit a polynomial of degree 2 to fit the left and right lane points. An example image is given slidingwindow.png



#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

The entire logic of the project is in function process_image_2().Convert the polyfit of left and right lanes to real world co-ordinates. This is done by multiplying the polyfit across y dimension by 30/720 and x dimension by 3.7/700 as given in the lesson.  Radius of curvature is found by following formula given a polyfit

R​curve ​​ = ​ ​(1+(2Ay+B) ^​2))^1.5 /|2A|
​​
You can assume the camera is mounted at the center of the car, such that the lane center is the midpoint at the bottom of the image between the two lines you've detected. The offset of the lane center from the center of the image (converted from pixels to meters) is your distance from the center of the lane.​​ 

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

The entire logic of the project is in function process_image_2()..ow taking the polyfit of left and right lanes, poly fill is used to color the region of interest as green. Now we rewarp the image to re orient itself to the original perspective. Now on this image we overlap the polyfill to get the final image. Example is final_image.png

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

My output video is white.mp4

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

a) Does not use the determiniation from sliding window to select region of interest for further images. This makes the algorithm ineffient
b) Things to improve
    a) Use sliding window coefficients to determines areas of interest for further processing of images. Only if the new detected lane is offset by more large deviation, then should we re-run the sliding windows.
