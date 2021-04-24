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

### Get Dimension
```
a.ndim
```
output - 1

### Get Shape
```
b.shape
```
output - (2,3)

### Get type
```
a.dtype
```
output - dtype('int32')

### if we want to specify what type of dtype then -
```
a = np.array([1,2,3], dtype = 'int16')
a.dtype
```
output - dtype('int16')

### Get memory size
```
a.itemsize
```
output - for int32 - 4
         for int16 - 2 

### Get total size
```
a.size*a.itemsize
```
or
```
a.nbytes
```

## Accessing/changing specific elements, rows, columns, etc

### Get a specific element [r,c]
```
a[1,5]
```
numpy array index starts from 0
a.shape -> (2,7)
We can also do
```
a[1,-2]
```

### Get a specific row
a[0, :]

### Get a specific column
a[:, 2]

### Getting a little more fancy [startindex:endindex:stepsize]
```
a[0, 1:6:2]
```
prints all element in row 0 starting from index 1 to index 5 in a step of 2

```
a[0, 1:6:-2]
```
this does not work because the steps goes backward and no end point so gives error

```
a[0, 1:-1:2]
```
can be used as an alternative

### Change something
```
a[1,5] = 20
a[:,2] = [1,2]
```

### 3-d example
![Failed to upload](./images/numpy/img7.JPG)
eg -  b[:, 0, :]
![Failed to upload](./images/numpy/img8.JPG)

## Initializing Different Types of Arrays

### All 0s matrix
```
np.zeroes((2,3,3))
```
Forms a 3 dimensional array

### All 1s matrix
```
np.ones((4,2), dtype='int32')
```

### Any other number
```
np.full((2,2), 99, dtype= 'float32')
```
forms an 2x2 array with only 99

### Any other number (full_like)
```
np.full(a, 4)
```
makes an array of shape a with all element value equals to 4

### Random decimal numbers
```
np.random.rand(2,4)
```
or
```
np.random.random_sample(a.shape)
```

### Random Integer values
np.random.randint(7, size=(3,3))
![Failed to upload](./images/numpy/img9.JPG)