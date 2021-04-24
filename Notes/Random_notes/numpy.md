Source - freeCodeCamp
         https://www.youtube.com/watch?v=QUT1VHiLmmI

Code from the video - https://github.com/KeithGalli/NumPy

## What is Numpy?
Can use numpy to store data in multi-dimensional array
<!--|![sucess](./images/numpy/Capture.JPG)| this is commented this is how to add images-->

![Failed to upload](./images/numpy/img1.JPG)

## How are Lists different from Numpy?
Lists are very slow whereas numpy is very fast

So Why are lists slow and numpy fast?
Because Numpy uses fixed type

![Failed to upload](./images/numpy/img2.JPG)
![Failed to upload](./images/numpy/img3.JPG)
Comparing the two images above we can notice that a single integer in numpy takes a lot less memory than a single integer in a list , so numpy is faster because numpy takes less memory.

Another reason is there is no type checking when iterating through objects i.e...., in a python list we can have a int , float, string , char, double etc so we have to typecheck but in numpy we don't.

Third reason is, numpy uses contiguous memory.
![Failed to upload](./images/numpy/img4.JPG)

Computers is SIMD Vector processing
SIMP - single instruction multiple data
benefit of this is that if we have contiguous memory we can do multiple operations in the block rather than ust doing one.

Effective cache utilization
-Though both uses cache in list we have to do longer memory look ups numpy its faster 

## How are Lists different from numpy?
![Failed to upload](./images/numpy/img5.JPG)

## Application of NumPy?
- Mathematics (MATPLAB Replacement)
- Plotting (Matplotlib)
- Backend (Pandas....etc)
- Machine Learning

Code from the video - https://github.com/KeithGalli/NumPy

## install numpy
1. Go to terminal and type
```
pip install numpy
```

## import numpy
```
import numpy as np
```
Can use any name instead np but by convention use np

## The Basics

### Initialization
```
a = np.array([1,2,3])
```
output - array([1,2,3])
```
print(a)
```
output - [1 2 3]

![Failed to upload](./images/numpy/img6.JPG)