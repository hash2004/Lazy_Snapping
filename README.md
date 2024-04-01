# Lazy_Snapping
Implementation of Lazy Snapping for quick image segmentation. Utilizes graph-cut techniques for easy foreground-background separation with minimal strokes. Ideal for efficient object isolation in images, catering to developers and researchers in image processing

## Work Flow 
![Lazy Snapping Workflow](https://github.com/hash2004/Lazy_Snapping/blob/main/lazyimageworkflow.png)

### Extracting Pixels for Foreground and Background

In the extract_seed_pixels function, we utilize stroke colors to distinguish between foreground and background seed pixels from an image, which is an essential step in the Lazy Snapping segmentation process.

When the user provides the stroke input, the stroke image contains distinct colors marking the intended foreground and background regions. Specifically, red strokes are used for the foreground and blue for the background. The function uses these color-coded strokes to extract the corresponding seed pixels from the original image.

NumPy's array functionality is employed to perform this extraction efficiently. The function converts both the stroke and the original images into NumPy arrays. It then creates color arrays for blue and red, representing the background and foreground stroke colors, respectively.

Using a boolean indexing technique (stroke_array == color).all(axis=-1), the function identifies pixels where the stroke color exactly matches either the foreground or background color. This comparison is done over the last axis (axis=-1) of the array, ensuring that the full color (all three RGB channels) matches the specified stroke color.

The resulting arrays for the foreground and background are slices from the original image array, containing the color values of pixels that have been marked as foreground or background seeds. These are returned as separate arrays, now ready for use in the subsequent segmentation steps.

### K- Means clustering 
After extracting the seed pixels, the next step is K-means clustering, which groups the pixels into coherent segments. In the context of Lazy Snapping, this unsupervised machine learning algorithm partitions the foreground and background seed pixels into clusters based on their color and spatial properties.

### Generating Probability Mask
The calculate_likelihood function plays a crucial role in determining whether each pixel in the original image belongs to the foreground or the background. It achieves this by calculating a likelihood score for each pixel based on its distance from the cluster centers of both the foreground and background regions.

The likelihood `L(c)` of a pixel belonging to a certain class `c` (either foreground or background) is calculated using the following formula:

\[ L(c) = \sum_{k=1}^{K} w \cdot e^{-||p - c_k||} \]

Where:
- `L(c)` is the likelihood of a pixel being classified into class `c`.
- `w` is the weight given to each cluster center's influence.
- `e` is the base of the natural logarithm.
- `p` represents the color vector of the pixel in question.
- `c_k` is the color vector of the k-th cluster center.
- `||p - c_k||` represents the Euclidean distance between the pixel and the cluster center.
- `K` is the total number of cluster centers.

The Euclidean distance is used without squaring to calculate a smoother gradient of likelihood, and the weight within the exponential function emphasizes the contribution of the nearest cluster centers more significantly.

#### Likelihood Calculation
For each pixel in the image, the function calculates its likelihood of belonging to the foreground and background. This is done using a weighted Euclidean distance, which is a measure of how 'close' the pixel's color is to each of the cluster centers. A weight is applied to the calculation, which can be adjusted to tune the influence of the cluster centers on the final likelihood.

#### Binary Mask Creation
The function then initializes a binary mask with the same spatial dimensions as the input image but without the color depth (it's a 2D array, not a 3D array).

#### Comparison and Assignment
The foreground and background likelihoods for each pixel are compared. The pixel is assigned to the foreground if the foreground likelihood is higher, and to the background otherwise. This assignment is binary: the mask will have a value of 255 for foreground pixels and 0 for background pixels.

#### Mask Generation
After all pixels are evaluated, you end up with a binary mask that segments the image into foreground (white) and background (black) based on their likelihood scores.

#### Display Segmentation 
The display_segmentation function is designed to visualize the results of the segmentation by applying the binary mask to the original image. The binary mask differentiates the foreground (which we want to keep) from the background (which we will alter).



#### Convert the Binary Mask
The single-channel binary mask is converted into a three-channel mask to be compatible with the original color image.

#### Extract the Foreground
The function uses the mask to keep the foreground pixels from the original image while setting the rest to black.

#### Create Inverse Mask
It inverts the binary mask to select the background pixels.

#### Prepare White Background: 
A white image (same size as the original) is created to serve as a new background.

#### Apply Inverse Mask
The white background image and the original image with the foreground are combined using the inverse mask. This effectively replaces the original background with white.

#### Display Results
The function plots two images side by side: the original image and the image with the segmented foreground over a white background. This visualization helps to compare the segmentation result with the original image.

## License
This project is licensed under the [MIT License](https://opensource.org/license/MIT)- see the LICENSE file for details.

