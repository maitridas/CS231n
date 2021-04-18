#### This is just my notes according to my understanding

## Colab Code
```
from google.colab import drive
drive.mount('/content/drive', force_remount=True)
```

1. Mounts google drive to colab VM

```
FOLDERNAME = 'cs231n/assignments/assignment1/'
assert FOLDERNAME is not None, "[!] Enter the foldername."
```

1. stores folder path in a variable
2. checks whether the foldername is none if none asserts to add a folder name

```
import sys
sys.path.append('/content/drive/My Drive/{}'.format(FOLDERNAME))
```

1. python sys - The python sys module provides functions and variables which are used to manipulate different parts of the Python Runtime Environment. It lets us access system-specific parameters and functions. import sys. First, we have to import the sys module in our program before running any functions. sys.modules.
2. this ensures that the Python interpreter of the Colab VM can load python files from within it.

```
%cd drive/My\ Drive/$FOLDERNAME/cs231n/datasets/
!bash get_datasets.sh
%cd /content/drive/My\ Drive/$FOLDERNAME
```

1. Downloads CIFAR-10 if doesn't exists . How??

## k-Nearest Neighbor (kNN)

### Setup code
``` 
import random
import numpy as np
from cs231n.data_utils import load_CIFAR10
import matplotlib.pyplot as plt
```
1. imports number of files

```
%matplotlib inline
plt.rcParams['figure.figsize'] = (10.0, 8.0) # set default size of plots
plt.rcParams['image.interpolation'] = 'nearest'
plt.rcParams['image.cmap'] = 'gray'
```

1. Makes matplotlib figures inline
2. Checkout - https://matplotlib.org/stable/api/matplotlib_configuration_api.html
3. plt.rcparams - An instance of RcParams for handling default Matplotlib values.

``` 
%load_ext autoreload
%autoreload 2
```

1.the notebook will reload external python modules
2. Checkout- http://stackoverflow.com/questions/1907993/autoreload-of-modules-in-ipython

```
# Load the raw CIFAR-10 data.
cifar10_dir = 'cs231n/datasets/cifar-10-batches-py'

# Cleaning up variables to prevent loading data multiple times (which may cause memory issue)
try:
   del X_train, y_train
   del X_test, y_test
   print('Clear previously loaded data.')
except:
   pass

X_train, y_train, X_test, y_test = load_CIFAR10(cifar10_dir)

# As a sanity check, we print out the size of the training and test data.
print('Training data shape: ', X_train.shape)
print('Training labels shape: ', y_train.shape)
print('Test data shape: ', X_test.shape)
print('Test labels shape: ', y_test.shape)
```

1. Output - Training data shape:  (50000, 32, 32, 3)
            Training labels shape:  (50000,)
            Test data shape:  (10000, 32, 32, 3)
            Test labels shape:  (10000,)
2. Checkout - https://stackoverflow.com/questions/57294070/what-is-the-difference-between-a-numpy-array-of-size-100-1-and-100
for difference between (5000,1) and (5000,)
3. data is a matrix label is vector
4. Convert (5000,1) to (5000,) by b = b.reshape(-1) where b is (5000,1)
5. or a= a.reshape(5000,1) where a is (5000,) 
6. Check this too for details - https://stackoverflow.com/questions/22053050/difference-between-numpy-array-shape-r-1-and-r

```
# Visualize some examples from the dataset.
# We show a few examples of training images from each class.
classes = ['plane', 'car', 'bird', 'cat', 'deer', 'dog', 'frog', 'horse', 'ship', 'truck']
num_classes = len(classes)
samples_per_class = 7
for y, cls in enumerate(classes):
    idxs = np.flatnonzero(y_train == y)
    idxs = np.random.choice(idxs, samples_per_class, replace=False)
    for i, idx in enumerate(idxs):
        plt_idx = i * num_classes + y + 1
        plt.subplot(samples_per_class, num_classes, plt_idx)
        plt.imshow(X_train[idx].astype('uint8'))
        plt.axis('off')
        if i == 0:
            plt.title(cls)
plt.show()
```

1. Visualises data but how does it works?
2. Let's Analyse line by line
3. Some definitions-
    i. enumerate classes returns (0,'plane'),(1,'car').....etc where index like 0,1,2...gets stored in y and 'plane', 'car' gets stored in cls
    ii. np.flatnonzero - https://numpy.org/doc/stable/reference/generated/numpy.flatnonzero.html
    Return indices that are non-zero in the flattened version of a. flattened version means converts a 2d array into a single 1 d vector
    for example - [[1,2],[3,4]] -> [1,2,3,4]
    iii. np.random.choice - https://numpy.org/doc/stable/reference/random/generated/numpy.random.choice.html
    random.choice(a, size=None, replace=True, p=None)
    Generates a random sample from a given 1-D array
    a is an ndarray, a random sample is generated from its elements
    iv. X_train[idx].astype('uint8') - https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.astype.html
    v. uint8 - A uint8 data type contains all whole numbers from 0 to 255. As with all unsigned numbers, the values must be non-negative. Uint8's are mostly used in graphics (colors are always non-negative).
4. So,
```
idxs = np.flatnonzero(y_train == y)
idxs = np.random.choice(idxs, samples_per_class, replace=False)
```
flatnonzero returns all non-zero value indices from the row y that is if y=0 then row 0 , returns all pictures of plane 
Now. indices has all pictures of planes that were present in y_train
after that random.choice chooses samples_per_class number of pictures from the idxs
5. Now, for all pictures in idxs we plot
6. plt.imshow(X_train[idx].astype('uint8')) - for clarification of this line
https://stackoverflow.com/questions/24739769/matplotlib-imshow-plots-different-if-using-colormap-or-rgb-array

```
# Subsample the data for more efficient code execution in this exercise
num_training = 5000
mask = list(range(num_training)) # line 1
X_train = X_train[mask]
y_train = y_train[mask]

num_test = 500
mask = list(range(num_test))
X_test = X_test[mask]
y_test = y_test[mask]

# Reshape the image data into rows
X_train = np.reshape(X_train, (X_train.shape[0], -1))
X_test = np.reshape(X_test, (X_test.shape[0], -1))
print(X_train.shape, X_test.shape)
```

1. mask in line 1 has [0,1,2,................]
2. output- (5000, 3072) (500, 3072) 3072 = 32*32*3
3. that's means  np.reshape(X_train, (X_train.shape[0], -1)) this converts the 32*32*3 photo to a column of 3072
4. About np.reshape - https://numpy.org/doc/stable/reference/generated/numpy.reshape.html
 