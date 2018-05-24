# Perfect Maze Solver using OpenCV

This project aims at finding the solution of perfect mazes, which is defined as a maze that has only one path from any point in the maze to any other point. Also, maze has no in-accessible sections, no circular paths and no open areas. Maze images should have dark walls on light background. To generate the mazes, an online maze generator tool has been used. 

## Tools Used

  - OpenCV 3.4
  - Python 3.6

## Task Analysis

### OpenCV
OpenCV (Open Source Computer Vision Library) is an open source computer vision and machine learning software library. OpenCV was built to provide a common infrastructure for computer vision applications and to accelerate the use of machine perception in the commercial products. The library has more than 2500 optimized algorithms, which includes a comprehensive set of both classic and state-of-the-art computer vision and machine learning algorithms.
OpenCV's application areas include:
  - Facial recognition system
  - Gesture recognition
  - Human–computer interaction (HCI)
  - Mobile robotics
  - Motion understanding
  - Object identification

### Contours
Contours can be explained simply as a curve joining all the continuous points (along the boundary), having same color or intensity. The contours are a useful tool for shape analysis and object detection and recognition.

### Dilation
Dilation is one of the two basic operators in the area of mathematical morphology, the other being erosion. It is typically applied to binary images, but there are versions that work on grayscale images. The basic effect of the operator on a binary image is to gradually enlarge the boundaries of regions of foreground pixels (i.e. white pixels, typically). Thus areas of foreground pixels grow in size while holes within those regions become smaller.

### Erosion
Erosion is the second morphological operator. It is also applied to binary images. The basic effect of the operator on a binary image is to erode away the boundaries of regions of foreground pixels (i.e. white pixels, typically). Thus areas of foreground pixels shrink in size, and holes within those areas become larger.


### Perfect Mazes
Perfect mazes have a characteristic that there is only one possible path from start to end. This means that this image can be divided into two parts and this boundary of division will be the solution itself. Therefore, morphological transformations like erosion and dilation are just enough to find the solution of the maze.

## Steps Involved

* Generate Maze using [Maze Generator][mg]
* Load and convert the source image to binary image
``` 
filename = '50x50'
img = cv2.imread(filename+'.png')
gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
ret, thresh = cv2.threshold(gray, 127, 255, cv2.THRESH_BINARY_INV) 
```
* Find Contours
```
image, contours, hierarchy = cv2.findContours(thresh, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_NONE)
dc = cv2.drawContours(thresh, contours, 0, (255, 255, 255), 5)
dc = cv2.drawContours(dc, contours, 1, (0,0,0) , 5)
```
* Dilate Contour
``` 
kernel = np.ones((19, 19), np.uint8)
dilation = cv2.dilate(thresh, kernel, iterations=1)
```
* Erode the Dilated image by same amount
```
erosion = cv2.erode(dilation, kernel, iterations=1)
```
* Find difference of two images
```
diff = cv2.absdiff(dilation, erosion)
```
* Draw the obtained path on source images
```
b, g, r = cv2.split(img)
mask_inv = cv2.bitwise_not(diff)
r = cv2.bitwise_and(r, r, mask=mask_inv)
b = cv2.bitwise_and(b, b, mask=mask_inv)
res = cv2.merge((b, g, r))
```
