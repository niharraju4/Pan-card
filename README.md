Certainly! Below is the detailed documentation with the author's name included.

# Table of Contents
1. [Introduction](#introduction)
2. [Libraries](#libraries)
3. [Fetching and Displaying Images](#fetching-and-displaying-images)
4. [Image Preprocessing](#image-preprocessing)
5. [Image Comparison](#image-comparison)
6. [Results](#results)

## Introduction
This documentation provides a detailed guide to compare two images (original and tampered) using the Structural Similarity Index (SSIM) and visualize the differences. The process involves fetching images, preprocessing them, comparing them, and displaying the results.

**Author: Nihar Raju**

## Libraries
The following libraries are used in this code:
- `skimage.metrics`: For structural similarity comparison.
- `imutils`: For image processing utilities.
- `cv2`: OpenCV library for computer vision tasks.
- `PIL`: Python Imaging Library for handling images.
- `requests`: For making HTTP requests to fetch images.

```python
from skimage.metrics import structural_similarity
import imutils
import cv2
from PIL import Image
import requests
```

## Fetching and Displaying Images
Images are fetched from URLs and displayed using the Python Imaging Library (PIL).

```python
# Create a directory to save images
!mkdir pan_card_tampering/image

# Open and display images
original = Image.open(requests.get('https://www.thestatesman.com/wp-content/uploads/2019/07/pan-card.jpg', stream=True).raw)
tampered = Image.open(requests.get('https://assets1.cleartax-cdn.com/s/img/20170526124335/Pan4.png', stream=True).raw)

# Print image formats and sizes
print("Original image format: ", original.format)
print("Tampered image format: ", tampered.format)
print("Original image size: ", original.size)
print("Tampered image size: ", tampered.size)
```

## Image Preprocessing
Images are resized and saved in a specified format.

```python
# Resize images to a common size
original = original.resize((250, 160))
tampered = tampered.resize((250, 160))

# Save resized images
original.save('pan_card_tampering/image/original.png')
tampered.save('pan_card_tampering/image/tampered.png')

# Display the original image
original

# Display the tampered image
tampered
```

## Image Comparison
Images are converted to grayscale and compared using SSIM. Differences are highlighted using contours.

```python
# Load images using OpenCV
original = cv2.imread('pan_card_tampering/image/original.png')
tampered = cv2.imread('pan_card_tampering/image/tampered.png')

# Convert images to grayscale
original_gray = cv2.cvtColor(original, cv2.COLOR_BGR2GRAY)
tampered_gray = cv2.cvtColor(tampered, cv2.COLOR_BGR2GRAY)

# Ensure dimensions are the same
if original_gray.shape != tampered_gray.shape:
    original_gray = cv2.resize(original_gray, (tampered_gray.shape[1], tampered_gray.shape[0]))

# Compute the structural similarity index
(score, diff) = structural_similarity(original_gray, tampered_gray, full=True)
diff = (diff * 255).astype("uint8")
print("SSIM: {}".format(score))

# Calculate threshold and contours
thresh = cv2.threshold(diff, 0, 255, cv2.THRESH_BINARY_INV | cv2.THRESH_OTSU)[1]
cnts = cv2.findContours(thresh.copy(), cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
cnts = imutils.grab_contours(cnts)

# Loop over the contours and draw bounding rectangles
for c in cnts:
    (x, y, w, h) = cv2.boundingRect(c)
    cv2.rectangle(original, (x, y), (x + w, y + h), (0, 0, 255), 2)
    cv2.rectangle(tampered, (x, y), (x + w, y + h), (0, 0, 255), 2)
```

## Results
The results include displaying the original and tampered images with contours, the difference image, and the threshold image.

```python
# Display original image with contours
print('Original Format image')
Image.fromarray(original)

# Display tampered image with contours
print('Tampered Image')
Image.fromarray(tampered)

# Display difference image
print('Different Image')
Image.fromarray(diff)

# Display threshold image
print('Threshold Image')
Image.fromarray(thresh)
```

### Results Summary
- **Original Image Format**: PNG
- **Tampered Image Format**: PNG
- **Original Image Size**: (250, 160)
- **Tampered Image Size**: (250, 160)
- **SSIM Score**: [Displayed in the output]

### Visual Results
1. **Original Image with Contours**: Highlights differences with red rectangles.
2. **Tampered Image with Contours**: Highlights differences with red rectangles.
3. **Difference Image**: Shows the pixel-wise differences between the images.
4. **Threshold Image**: Binary image highlighting the significant differences.

### Detailed Steps

#### Step 1: Fetching and Displaying Images
- **Fetching Images**: Images are fetched from the provided URLs using the `requests` library.
- **Displaying Images**: The fetched images are opened and displayed using the `PIL` library.
- **Image Information**: The format and size of the images are printed.

#### Step 2: Image Preprocessing
- **Resizing Images**: Both the original and tampered images are resized to a common size (250x160 pixels) to ensure they can be compared accurately.
- **Saving Images**: The resized images are saved to the specified directory.

#### Step 3: Image Comparison
- **Loading Images**: The images are loaded using OpenCV for further processing.
- **Converting to Grayscale**: The images are converted to grayscale to simplify the comparison process.
- **Ensuring Dimensions**: The dimensions of the images are checked and adjusted if necessary.
- **Computing SSIM**: The Structural Similarity Index (SSIM) is computed to measure the similarity between the images.
- **Calculating Threshold and Contours**: The difference image is thresholded to create a binary image, and contours are found to highlight the differences.
- **Drawing Contours**: Bounding rectangles are drawn around the contours to visualize the differences.

#### Step 4: Displaying Results
- **Original Image with Contours**: The original image with highlighted differences is displayed.
- **Tampered Image with Contours**: The tampered image with highlighted differences is displayed.
- **Difference Image**: The difference image showing pixel-wise differences is displayed.
- **Threshold Image**: The threshold image highlighting significant differences is displayed.


