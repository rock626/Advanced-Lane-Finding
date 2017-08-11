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

[image1]: ./images/undist_calibration1.png "Undistorted"
[image2]: ./test_images/tracked/undist_test1.jpg "Road Transformed"
[image3]: ./test_images/tracked/test1.jpg "Binary Example"
[image4]: ./test_images/tracked/Warped_test1.jpg "Warp Example"
[image5]: ./test_images/tracked/laneline_test1.png "Fit Visual"
[image6]: ./test_images/tracked/Out1_test1.jpg "Output"
[video1]: ./test_videos_output/project_video.mp4 "Video"

## Camera Calibration

The code for this step is contained in the 2-3 code cells of the IPython notebook located in "./p4.ipynb".  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()`  (code cell 4) function and obtained this result: 

![alt text][image1]

** Pipeline (single images)**

** 1. Distortion correction:**

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:

![alt text][image2]

** 2. Applying color and binary thresholds.**

I used a combination of color and gradient thresholds to generate a binary image (code cell 6). For color thresholds i used a combination of s channel from hls image and v channel from hsv image.I then combined with xand y gradient threshold on undistorted image. Here's an example of my output for this step.

![alt text][image3]

** 3. Perspective transform:**

For perpective transform , i defined source and destination points and used cv2.getPerspectiveTransform to transform the image(part of the function 'process_image'). I chose the hardcode the source and destination points in the following manner:

```python
    top_left = [560, 470]
    top_right = [730, 470]
    bottom_right = [1180, 720]
    bottom_left = [210, 720]
    src = np.float32([bottom_left, bottom_right,top_right,top_left])

    top_left_dst = [220,0]
    top_right_dst = [1100,0]
    bottom_right_dst = [1100,720]
    bottom_left_dst = [220,720]

    dst = np.float32([bottom_left_dst,bottom_right_dst,top_right_dst,top_left_dst])
```
By visualizing the images manually, i verifies that lanes are parallel and distance between the lanes is same.Here is an example of warped image:

![alt text][image4]

** 4. Finding lane lines:**

I used the method 'peaks in a histogram' from the classroom to find the lane lines and used the classroom code snippets.
'findlanelines' method has the code which takes warped and undistorted images as input.Histogram is used for the 1st frame to calculate the lane coefficients and from the second frame onwards, previously calculated polynomial fit is used.

An example image which shows window sliding and the polynomial fit for left and right lanes

![alt text][image5]

** 5. Radius of curvature of the lane and the position of the vehicle with respect to center.**

Code for this is present in function 'process_image'.For radius of curvature,x and y values are converted from pixels space to meters and formula from the classroom is used.

For vehicle position, camera center is taken as mid of left and right lanes (from the polynomial coefficients).Then center of image is calculated and the difference multiplied by meters per pixel in x dimension.

** 6. An image with lane lines and radius of curvature plotted onto original image.**

'visualize_img method' is used to plot the lane line onto original image and cv2.putText is used to write the curvature.

![alt text][image6]

---

** Pipeline (video)**

Here's a [link to my video result](./test_videos_output/project_video.mp4)

---

** Discussion**

** 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?**

For applying thresholds, i experimented different approaches and failed many times.I tried s,l channels from hsl image and r,g channels from rgb and combined with gradient threshold.But once i plotted lane line , there were some issues like lanes going inward or outward.I even tried applying a region of interest to mask the unwanted areas which might effect the histogram.That too didnt help either.Then i came across this FAQ on Advanced lane finding video from udacity.After going through i tried myself at hsv color space. By changing the thresholds, i was able to detect lane line for most of the frames.

I didnt apply any averaging/smoothing techinques so i think my pipeline might fail when there are bad frames.
Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  
