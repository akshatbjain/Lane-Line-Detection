**Advanced Lane Finding Project**

[//]: # (Image References)

[image1]: ./output_images/camera_calibration.jpg "Camera Calibration"
[image2]: ./output_images/undistorted_image.jpg "Distortion Correction - Test Image"
[image3]: ./output_images/color_and_gradient_thresholding.jpg "Binary Example"
[image4]: ./output_images/region_of_interest.jpg "Region of Interest"
[image5]: ./output_images/warped_image_birds_eye.jpg "Warped image - Birds eye"
[image6]: ./output_images/warped_detected_lane.jpg "Warped detected line"
[image7]: ./output_images/final_output.jpg "Final output"

### Camera Calibration

The code for this step is contained in the fourth code cell of the IPython notebook located in "run.ipynb".

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:

![alt text][image2]

I used a combination of color and gradient thresholds to generate a binary image.  Here's an example of my output for this step:

![alt text][image3]

The code for my perspective transform includes a function called `birds_eye_transform()`, which appears in the 6th code cell of the IPython notebook.  The `birds_eye_transform()` function takes as inputs an image (`img`), as well as camera matrix (`mtx`) and distortion coefficients (`dist`).  I chose the source and destination points in the following manner:

```python
src_points = np.float32([[(w/8), h], [(w/8)*7, h], [(w/8)*3.7, h/1.6], [(w/8)*4.3, h/1.6]])
dst_points = np.float32([[(w/5), h], [(w/5)*4, h], [(w/5), 0], [(w/5)*4, 0]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 160, 720      | 256, 720      | 
| 1120, 720     | 1024, 720     |
| 592, 450      | 256, 0        |
| 688, 450      | 1024, 0       |

I verified that my perspective transform was working as expected by drawing the `src` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]
![alt text][image5]

Then I took a histogram of the bottom half of the image. Then I found the peak of the left and right halves of the histogram which would be the starting point for the left and right lines. Next step is to identify the x and y positions of all nonzero pixels in the image. I then use a sliding window algorithm to detect both the lane lines by using a minimum pixel threshold of 50 pixels. Next I extract left and right line pixel positions and fit a second order polynomial to each. The output can be seen below:

![alt text][image6]

I calculate the radius of curvature and the relative vehicle position with respect to center of the lane in code cells 8th and 9th of the IPython notebook located in "run.ipynb".

The last step is to plot back the result onto the road image such that the lane area is identified clearly.  Here is an example of my result on a test image:

![alt text][image7]

---

### Pipeline (video)

Here's a [link to my video result](https://www.youtube.com/watch?v=N89aGyOPqdk)

---

### Discussion

Overall the approach works pretty well as seen in the video. However, I it might fail when the lighting conditions change dramatically. I plan to improve the pipeline further and making it work with all kinds of lightings.
