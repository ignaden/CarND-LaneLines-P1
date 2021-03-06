# Project writeup for Finding Lanes

### 1. Describe the pipeline

The pipeline follows closely the course materials.

#### 1. Convert to grayscale
Convert the RGB image to grayscale.
![First image](https://github.com/ignaden/CarND-LaneLines-P1/raw/master/writeup_images/step_1.jpg "Convert to grayscale")

#### 2. Run Gaussian blur
Smooth out the image for better performance.
![After adding Gaussian blur](https://github.com/ignaden/CarND-LaneLines-P1/raw/master/writeup_images/step_2.jpg "Run Gaussian blur")

#### 3. Run Canny detection with threshold [50, 150]
Run the Canny detection on the image.
![Canny detection](https://github.com/ignaden/CarND-LaneLines-P1/raw/master/writeup_images/step_3.jpg "Canny detection")

#### 4. Create the mask
Leave out only the part of the image that can contain the road.
![Mask](https://github.com/ignaden/CarND-LaneLines-P1/raw/master/writeup_images/step_4.jpg "Mask")

#### 5. Run Hough on edge detected image
Run the Hough detection on the resulting image and obtain the two lines - left and right.
![Run Hough on edge detected image](https://github.com/ignaden/CarND-LaneLines-P1/raw/master/writeup_images/step_5.jpg "Run Hough on edge detected image")

#### 6. Overlay the image with lines over the original image
Overlay the resulting lines from step 5 onto the original image.
![Original image overlaid with lines](https://github.com/ignaden/CarND-LaneLines-P1/raw/master/writeup_images/step_6.jpg "Original image overlaid with lines")

The code for this pipeline is listed below:

```python
def process_image(image):
    # NOTE: The output you return should be a color image (3 channel) for processing video below
    # TODO: put your pipeline here,
    # you should return the final output (image where lines are drawn on lanes)

    ########################################################################################
    # Define our parameters for Canny and apply
    low_threshold  = 50
    high_threshold = 150
    kernel_size    = 5


    # Define the Hough transform parameters
    # Make a blank the same size as our image to draw on
    rho = 2               # distance resolution in pixels of the Hough grid
    theta = np.pi / 180   # angular resolution in radians of the Hough grid
    threshold = 20        # minimum number of votes (intersections in Hough grid cell)
    min_line_length = 40  # minimum number of pixels making up a line
    max_line_gap = 20     # maximum gap in pixels between connectable line segments
    ########################################################################################

    # The actual pipeline
    gray      = grayscale(image)
    blur_gray = gaussian_blur(gray, kernel_size)
    edges     = canny(blur_gray, low_threshold, high_threshold)

    # Create the mask
    imshape = image.shape
    vertices = np.array([[(0,imshape[0]),(450, 320), (490, 320), (imshape[1],imshape[0])]], dtype=np.int32)
    masked_edges = region_of_interest(edges, vertices)

    # Run Hough on edge detected image
    line_image = hough_lines(masked_edges, rho, theta, threshold, min_line_length, max_line_gap)

    # Create a "color" binary image to combine with line image
    color_edges = np.dstack((edges, edges, edges)) 

    lines_edges = weighted_img(line_image, image, α=0.8, β=1., λ=0.)
    
    return lines_edges
```

#### Test videos

Here're the resulting videos (actual videos are in the repo, the links below will take you to YouTube videos).
##### White lines
[![Whilte lines](https://img.youtube.com/vi/4ANCYahKC5c/0.jpg)](https://www.youtube.com/watch?v=Q4ANCYahKC5c)

##### Yellow line present
[![Yellow line](https://img.youtube.com/vi/Q9WYJ_0Ic28/0.jpg)](https://www.youtube.com/watch?v=Q9WYJ_0Ic28)

### 2. Identify any shortcomings

There are many shortcomings of this approach:
- The camera view is not calibrated - the image might be a distorted representation of the perspective
- We're trying to fit straight lines - this is a very sensitive to any noise present
- Cannot fit road with any curvature

### 3. Suggest possible improvements
- Change the perspective - this is the approach taken in the later course
- Split the screen into multiple frames and fit the lines separately