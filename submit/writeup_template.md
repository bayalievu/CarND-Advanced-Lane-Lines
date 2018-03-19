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

[image1]: ./output_images/undistorted.png "Undistorted"
[image2]: ./output_images/edgeAndColorDetectedImage.png "Edges and Colors"
[image3]: ./output_images/warped.png "Warped Example"
[image4]: ./output_images/lineFit.png "Lane Fit"
[image5]: ./output_images/curvatureOriginal.png "Fit Visual"
[video1]: ./output_images/project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the second code cell of the IPython notebook located in "example.ipynb".  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result shown in output of cell #3. 

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

The distortion correction applied to one of the test images is shown in output of cell #3 in the IPython notebook 'example.ipynb'.

![alt text][image1]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image. Basically i use 'S' channel in the image and combine 'X' and 'Y' sobel gradients. To select color i used yellow HSV selector and white RGB selector in cell#4. The Sobel gradients are applied in cell#5. The overall output of color and Sobel gradients is in the cell#6 in IPython notebook.

![alt text][image2]
#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform is in the 7th code cell of the IPython notebook. The idea takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

```python
bottomY = 720
topY = 455
src = np.float32([ 
    [585, topY],
    [705, topY],
    [1130, bottomY],
    [190, bottomY]
])

nX = testImage.shape[1]
nY = testImage.shape[0]
offset = 200

dst = np.float32([
    [offset, 0],
    [nX-offset, 0],
    [nX-offset, nY], 
    [offset, nY]
])
```

![alt text][image3]

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 585, 455      | 320, 0        | 
| 190, 720      | 320, 720      |
| 1130, 720     | 960, 720      |
| 705, 455      | 960, 0        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

In cell #8 the horizontal place of the lanes identified using histogram. To find exact place and curve of the lane lines, window sliding algorithm was used which fit the lane lines using second order polynomial. The output images can be found in corresponding output images.

![alt text][image4]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in cell# 11. Firstly horizontal and vertical meters/pixel values were calculated. Then according to new values new real-world polynomials were fit in the lane lines and radius of the curvature was calculated for both left and right lanes.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in cell#12 and the output image is attached.

![alt text][image5]

### Pipeline (video)

Here's a [link to my video result](./output_images/project_video.mp4)

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.
The first important fallacy of this pipeline is the warping technique used, because if the camera is mounted elsewhere or the vehicle changes lanes our warping and fitting lines method will fail. 
Second point of failure is how we detect lanes using colors and sobel gradients, because if the lanes are of different colors and marked not boldly as in video pipeline is likely to fail. To improve this some kind of progressive lane detection algorithm must be used which remembers previous lanes and steering angle to follow the lanes.
Sometimes on the construction roads there maybe old and new lane lines which has the same frequency but different intensities so maybe in the future i should change the algorithm to accomodate this case.


---

