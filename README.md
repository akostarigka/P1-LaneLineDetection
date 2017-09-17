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

### 1. Auxiliary functions

Problem solution required the construction of certain auxiliary functions. Namely:

**Function name:** `color_selection()`
- Description: This function is used to convert the image from RGB color space to HLS [(RGB<->HLS)](http://opencv-python-tutroals.readthedocs.io/en/latest/py_tutorials/py_imgproc/py_colorspaces/py_colorspaces.html)
- Inputs:
    * `image`: colored input image
- Outputs:
    * `out_hls_image`: output image in HLS color-space

** Rationale: ** Even though the course prompts the use of RGB color-space, it was proved that this technique was not efficient in more challenging cases (such as challenge.mp4). This raised the need to turn into more effective approaches, such as the HLS color-space approach [(RGB<->HLS)](http://opencv-python-tutroals.readthedocs.io/en/latest/py_tutorials/py_imgproc/py_colorspaces/py_colorspaces.html)

**Function name:** `moving_average()`
- Description: This function performs a simple moving average [(SMA)](https://en.wikipedia.org/wiki/Moving_average) of the input data within a given window
- Inputs:
    * `mean_value`: current mean value to be added to the sum
    * `mov_av_accu`: accumulator that stores the mean values at each run
    * `moving_average_window`: number of samples that provide the average at each run
- Outputs:
    * `mov_av_out`: mean value of the moving window

** Rationale: ** The use of the moving average proved to be an efficient way to smoothen the lane transitions and provide a more robust solution [Ref: [Robust Extrapolation of Lines in Video Using Probabilistic Hough Transform](https://medium.com/@esmat.anis/robust-extrapolation-of-lines-in-video-using-linear-hough-transform-edd39d642ddf)]

**Function name:** `average_lines()`
- Description: This function is used to average a set of lines. It separates them according to
    their slope sign to left and right lines and outputs the mean value of their
    respective slopes and intercepts. The function offers the choice of performing a
    moving average via 'moving_average_flag'
- Inputs:
    * `lines`: input lines to be averaged
    * `moving_average_flag`: flag to denote whether the output will be a simple or a moving average
- Outputs:
    * `out_slopes_intercepts`: slopes and intercepts of both lines

** Rationale: ** This is a common practice of separating left and right lines in the image

**Function name:** `extrapolate_line()`
- Description: This function is used to extrapolate a line of a specific slope and intercept within a specified Y-range
- Inputs:
    * `y_range`: denotes the range within minimum and maximum y `[y_bottom, y_top]`
    * `slope_intercept`: slope and intercept of the extrapolated line
- Outputs:
    * Extrapolated line: `[initial_point, end_point]`

** Rationale: ** This is a common way of extrapolating a line given its slope and intercept

**Function name:** `draw_extrapolated_lines()`
- Description: This function is used to draw the extrapolated lines within the specified Y-range
- Inputs:
    * `image`: image to which the extrapolated lines will be drawn
    * `lines`: extrapolated lines to be drawn `[x1, y1, x2, y2]`
    * `y_range`: range `[y_bottom, y_top]` in which the extrapolated lines will be drawn
- Outputs:
    * `combined_image`: combination of input image and extrapolated lines  

** Rationale: ** This is a part of the pipeline that will draw the extrapolated lines in the original figure


### 2. Pipeline description.

The pipeline is organized in a main function `draw_lane_lines()`

**Function name:** `draw_lane_lines()`
- Description: This is the main pipeline used to draw lane lines into a given image.
    It involves the following steps:
    * Perform color selection
    * Perform gaussian smoothing
    * Perform canny edge detection
    * Create masked edges
    * Perform Hough transform on edge detected image
    * Draw extrapolated lines
- Inputs:
    * `image`: colored input image
- Outputs:
    * `lane_line_image`: output image with drawn lane lines

Detailed steps:
- Step 1: Define the **region of interest**. This is done by constructing a rectangular with the appropriate end points
- Step 2: Perform **color selection.** This step uses the auxiliary function `color_selection()` to convert the image from RGB color space to HLS
- Step 3: Perform **gaussian smoothing** to the HLS image using the cv2 function `cv2.GaussianBlur()`
- Step 4: Perform **Canny edge detection** to the gaussian blurred image using the cv2 function `cv2.Canny()`
- Step 5: **mask** the canny edges to the specified region of interest using the help function `region_of_interest()`
- Step 6: Perform **Hough transform** to the masked edges using the
cv2 function `cv2.HoughLinesP()`
- Step 7: **Draw** lane lines after having performed averaging/extrapolation of the hough lines using the auxiliary function `draw_extrapolated_lines()`

### 3. Potential shortcomings in pipeline

- Challenging cases: e.g low light images, curvy lanes may not be confronted with the current algorithm


### 4. Suggest possible improvements to your pipeline
- Code efficiency: The moving average process required the mean values to be stored in global accumulators. This was done with the use of 2 global lists (`left_mov_av_accu` for left moving average accumulator, `right_mov_av_accu` for right moving average accumulator) that were initialized as empty before each image or video manipulation. However this is **not** a professional and robust way of coding. Such cases should be handled with the use of classes and object oriented programming  
- Perform more advanced and robust techniques on the estimation of the lane line (e.g. effectively handle curved lines)  
