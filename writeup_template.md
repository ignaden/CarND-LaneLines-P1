# **Finding Lane Lines on the Road** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

1. Convert to grayscale
```python
gray = grayscale(image)
```

2. Run Gaussian blur
```python
blur_gray = gaussian_blur(gray, kernel_size)
```

3. Run Canny detection with threshold [50, 150]
```python
edges = canny(blur_gray, low_threshold, high_threshold)
```

4. Create the mask
```python
imshape = image.shape
vertices = np.array([[(0, imshape[0]), (450, 320), (490, 320), (imshape[1], imshape[0])]], dtype=np.int32)
masked_edges = region_of_interest(edges, vertices)
```

5. Run Hough on edge detected image
```python
line_image = hough_lines(masked_edges, rho, theta, threshold, min_line_length, max_line_gap)
```

6. Overlay the image with lines over the original image
```python
lines_edges = weighted_img(line_image, image, α=0.8, β=1., λ=0.)
```

![alt text][image1]


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when ... 

Another shortcoming could be ...


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to ...

Another potential improvement could be to ...
