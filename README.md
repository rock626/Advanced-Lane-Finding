# Advanced-Lane-Finding

Use advanced computer vision techniques to identify left and right lanes from dash-mounted camera video.

# Pipeline:
1. Distortion correction

2. Apply color and binay thresholds

3. Use OpenCV perspective transform

4. Apply moving average filter to remove any markings or features that could be due to other artifacts in the image.

5. Apply polynomial regression to compute the left and right lanes.

6. Apply frame to frame smoothing to discard noise between images, and draw lines back on the image.

7. Compute curvature and lane deviation and write them on the image.

