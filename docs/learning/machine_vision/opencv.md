# Open CV
OpenCV (Open Source Computer Vision Library) is an open-source computer vision and machine learning software library. It provides a wide range of tools for image processing, video analysis, and computer vision applications such as face detection, object tracking, and motion analysis.

This page will focus on using in a python environment, but the same functionality is available in other languages such as C++ as well.

## Key Features
- Image Processing: Filtering, transformations, edge detection, and color space conversions.
- Object Detection: Pre-trained models for face, eyes, and pedestrian detection.
- Video Analysis: Motion tracking, background subtraction, and optical flow.
- Machine Learning: Integration with models for classification and clustering.
- Cross-Platform: Works on Windows, Linux, macOS, Android, and iOS.
- Language Support: Bindings are available for C++, Python, Java, and MATLAB.

## Basic Usage
Using pip, the module can be installed with 'pip install opencv-python'. For more install info see here: https://opencv.org/get-started/

To check you're install is correct run a basic example

    import cv2 as cv
    img = cv.imread("path/to/image")
    cv.imshow("Display window", img)
    k = cv.waitKey(0) 

## Exercises
To help learn there are some exercises located in this github repo: https://github.com/incognitopikachu/opencv-workshop

You can use git to clone it locally, or just download it as a zip if you're unfamiliar with git (option under green 'Code' button)

All the exercises are in the 'exercises.py' file.

To follow along you'll need to use the opencv docs: https://docs.opencv.org/4.x/
Note that the documentation normally lists functions with their C++ syntax, but there will always be an equivilent python function. The library is well used so google, stackoverflow and LLMs will help you find the answer.

## Photogramatory
<!-- this could be an entire page -->
To make accurate measuremnets, understanding how a 3D point, X in the real world gets projected onto a 2D point, x in the image is essential.

This relationship is described by two key matrices:

1 Intrinsic Matrix (Camera Matrix) K
2 Extrinsic Matrix (Pose: Rotation + Translation), [R|t]

$$
x = K [R|t] X
$$

### Intrinsic Matrix
The intrinsic matrix encodes the internal parameters of the camera — the characteristics that describe how it forms an image from incoming light. It tells us how 3D points in camera coordinates are projected onto the 2D image plane (pixels).

$$
K = \
\begin{array}{}
fx & 0 & cx\\ 
0 & fy & cy\\ 
0 & 0 & 1
\end{array}
$$ 

| Symbol       | Meaning                                              |
| :----------- | :--------------------------------------------------- |
|  fx, fy      | Focal lengths (in pixels) along x and y axes.        |
|  cx, cy      | Coordinates of the optical center (principal point). |

Note, that the focal lengths here refer to the pixel scaled focal length.

### Camera Calibration with Open CV
These parameters can be calculated during a calibration process which uses a checkerboard pattern.



Note that changing zoom and autofocus setings can change the intirnsic matrix.


### Extrinsic Matrix 
The extrinsic matrix describes the camera’s position and orientation relative to the world (or object) coordinate system. It converts coordinates from world space → camera space.

$$
R|t = \
\begin{matrix}{} 
r_{11} & r_{12} & r_{13} & t_x\\ 
r_{21} & r_{22} & r_{23} & t_y\\ 
r_{31} & r_{32} & r_{33} & t_z
\end{matrix}
$$ 


| Symbol | Meaning                                          |
| :----- | :----------------------------------------------- |
| ( R )  | 3×3 rotation matrix (orientation of the camera). |
| ( t )  | 3×1 translation vector (position of the camera). |

On a drone, these parameters can be calculated using GPS, IMU and altimeters readings. More more precise measurements, a technique called Structure from Motion may be used to calcualte parameters from existing images.