# Real time objects counting

Object counting excercise. The aim is to use a low-capability camera to perform an efficient real time objects counting. It counts static objects placed in the camera FOV. This kind of problem could be solved on a variety of different devices.

Summary here: [Presentation](https://goo.gl/UJddld)

## Usage

```
python countobj.py [mode][fidelity][fidelityValue]
```

* _mode:_ video (default) | image | h-help | info
* _fidelity:_ activate fidelity range
* _fidelityValue:_ [0.0, 1.0] default 0.7
* Info for authors and disclaimer


## Approach

The main idea is to count contours in captured frames:

* Pro:
    * Probably a contour represents an object;
	* Efficiency;
	* A lot of known edge detectors;

* Cons:
	* Contours could be noise;
	* Complex objects can have multiple and/or inner contours;

## Methodology

### Frame preprocessing

* A 15x15 median filter [[1]](#1) is applied to reduce noise, smoothing the color reducing light/shadow anomaly and similar-color differences. This technique preserves edges so the result is not altered;
* Frame is converted in a grayscale image and Otsu thresholding is applied;
* Morphological OPEN is used to reduce noise, remove little objects and strengthen the others;


### Detect static scene

* To avoid false counting, algorithm detects movements applying a background subtraction based on Mixture of Gaussians [[2]](#2). If movement is recognized as a high pixel density in the MOG result, counting is stopped at the moment because it means that we are placing an object in the scene;


### Edge detection

* Edges are detected using a Topological Structure Analysis by Border Following [[3]](#3). This technique finds a hierarchy of edges for an object;
* We used hierarchy information to get only root contours (contours with no parents);
* Each root element is an object that increases the counter;
* At the end we display the contour bounding box;


## Results

It is a fast object counting algorithm suitable for real time applications. For its prototype nature it doesn’t handle light problems;

For preprocessing we tried 4 kinds of filters:

1. Normalized Box Filter: doesn’t preserve edges;
1. **Median filter: the winner**;
1. Bilateral filter [[4]](#4): better than median but too slow;
1. Adaptive bilateral filter [[5]](#5): as above;


### Demo video

[![Alt text](https://img.youtube.com/vi/eXYA7o1Lbik/0.jpg)](https://www.youtube.com/watch?v=eXYA7o1Lbik)


## Usage on Images

It is also provided an implementation of the algorithm for images. In this case there are two choices:

1. _Fidelity range:_ if objects have a similar size/area it is helpful to prevent false detection;
1. _Normal:_ no area thresholding is applied;


## References

<a name="1">[1]</a> Median Filter: [https://en.wikipedia.org/wiki/Median_filter](https://en.wikipedia.org/wiki/Median_filter);

<a name="2">[2]</a> Mixture of Gaussians: http://www.ai.mit.edu/projects/vsam/Publications/stauffer_cvpr98_track.pdf;

<a name="3">[3]</a> Suzuki, S. and Abe, K., Topological Structural Analysis of Digitized Binary Images by Border Following. CVGIP 30 1, pp 32-46 (1985);

<a name="4">[4]</a> C. Tomasi and R. Manduchi, "Bilateral Filtering for Gray and Color Images", Proceedings of the 1998 IEEE International Conference on Computer Vision, Bombay, India;

<a name="5">[5]</a> Buyue Zhang; Allebach, J.P., "Adaptive Bilateral Filter for Sharpness Enhancement and Noise Removal," Image Processing, IEEE Transactions on , vol.17, no.5, pp.664,678, May 2008;
