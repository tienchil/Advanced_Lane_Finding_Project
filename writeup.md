
# **Advanced Lane Finding Project**

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

[image1]: ./output_images/chess.png "Undistorted"
[image2]: ./output_images/transform.png "Road Transformed"
[image3]: ./output_images/thresholding.png "Binary Example"
[image4]: ./output_images/out2.jpg "Fit Visual"
[image5]: ./output_images/final_out.jpg "Output"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first cell in the notebook. I used 9x6 chess board for the camera calibration in this project.  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (9, 6) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

The code for this step is contained in the second cell of the notebook. I firstly performed un-distortion on the image and then transformed the undistorted image into birds-eye view with corredsponding area of interests.

The following is an example output:
![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used several color thresholds and a gradient along x-direction for this step.
The image was converted to HLS color space. I used L and S channels for binary thresholding. In addition, the image was converted to gray space. I used Sobel gradients along the x-direction. I then combined all thresholdings into one binary image.

Then, I set all pixels x < 200 to zero to get a better result. 

This is the result for all test images:
![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `birdseye_transform()`, which appears in the 2nd code cell of the IPython notebook. I chose to hardcode the source and destination points in the following manner:

```python
src_tl = [593, 450]      # top left
src_tr = [689, 450]      # top right
src_br = [1120, 720]     # bottom right
src_bl = [190, 720]      # bottom left

src = np.float32([src_tl, src_tr, src_br, src_bl])


dst_tl = [320, 0]      # top left
dst_tr = [960, 0]      # top right
dst_br = [960, 720]     # bottom right
dst_bl = [320, 720]      # bottom left

dst = np.float32([dst_tl, dst_tr, dst_br, dst_bl])
    
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 593, 450      | 320, 0        | 
| 689, 450      | 960, 0        |
| 1120, 720     | 960, 720      |
| 190, 720      | 320, 720      |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image2]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

I used the histogram with sliding windows method to find lane-line pixels. There are 18 windos with a margin of 60 pixels.

This is an example of output:
![alt text][image4]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

The code for this step is contained in the cell under `Finding Lanes`.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in the cell under `Pipeline`.

![alt text][image5]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](https://youtu.be/mc6EkthxXJo)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

In order to get a good result of lane lines, I used high range of thresholds on gradients along x-direction. This has some unexpected result when the pavement has two different colors. Also, the pipeline is likely to fail when there is shadow on the road. This is because I used S-channel and L-channel for thresholding. Both are influenced by brightness.

One possible improvement would be noise reduction. After binary thresholding, the resulting images contained lots of noise that sometimes affected the result of detection. The other possible improvement would be finding a way to noremalize pavement colors and brightness, and to remove shawdhos completely, or at least reduce the shadow effect. 
