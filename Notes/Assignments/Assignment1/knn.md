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

```
from cs231n.classifiers import KNearestNeighbor

# Create a kNN classifier instance. 
# Remember that training a kNN classifier is a noop: 
# the Classifier simply remembers the data and does no further processing 
classifier = KNearestNeighbor()
classifier.train(X_train, y_train)
```
Self explanatory

## Distance Computation
Note :- This is knn.ipynb note so external reference is in their respective files

```
# Open cs231n/classifiers/k_nearest_neighbor.py and implement
# compute_distances_two_loops.

# Test your implementation:
dists = classifier.compute_distances_two_loops(X_test)
print(dists.shape)
```

Computes the distances using 2 loops


```
# We can visualize the distance matrix: each row is a single test example and
# its distances to training examples
plt.imshow(dists, interpolation='none')
plt.show()
```

plt.imshow - https://matplotlib.org/3.1.1/api/_as_gen/matplotlib.pyplot.imshow.html

```
# Now implement the function predict_labels and run the code below:
# We use k = 1 (which is Nearest Neighbor).
y_test_pred = classifier.predict_labels(dists, k=1)

# Compute and print the fraction of correctly predicted examples
num_correct = np.sum(y_test_pred == y_test)
accuracy = float(num_correct) / num_test
print('Got %d / %d correct => accuracy: %f' % (num_correct, num_test, accuracy))
```
num_correct = np.sum(y_test_pred == y_test) -> sums over all the value whose y_test_pred == y_test

```
y_test_pred = classifier.predict_labels(dists, k=5)
num_correct = np.sum(y_test_pred == y_test)
accuracy = float(num_correct) / num_test
print('Got %d / %d correct => accuracy: %f' % (num_correct, num_test, accuracy))
```

```
# Now lets speed up distance matrix computation by using partial vectorization
# with one loop. Implement the function compute_distances_one_loop and run the
# code below:
dists_one = classifier.compute_distances_one_loop(X_test)

# To ensure that our vectorized implementation is correct, we make sure that it
# agrees with the naive implementation. There are many ways to decide whether
# two matrices are similar; one of the simplest is the Frobenius norm. In case
# you haven't seen it before, the Frobenius norm of two matrices is the square
# root of the squared sum of differences of all elements; in other words, reshape
# the matrices into vectors and compute the Euclidean distance between them.
difference = np.linalg.norm(dists - dists_one, ord='fro')
print('One loop difference was: %f' % (difference, ))
if difference < 0.001:
    print('Good! The distance matrices are the same')
else:
    print('Uh-oh! The distance matrices are different')


# Now implement the fully vectorized version inside compute_distances_no_loops
# and run the code
dists_two = classifier.compute_distances_no_loops(X_test)

# check that the distance matrix agrees with the one we computed before:
difference = np.linalg.norm(dists - dists_two, ord='fro')
print('No loop difference was: %f' % (difference, ))
if difference < 0.001:
    print('Good! The distance matrices are the same')
else:
    print('Uh-oh! The distance matrices are different')

```

1. np.linalg.norm -> https://numpy.org/doc/stable/reference/generated/numpy.linalg.norm.html
This function is able to return one of eight different matrix norms, or one of an infinite number of vector norms (described below), depending on the value of the ord parameter.

```
# Let's compare how fast the implementations are
def time_function(f, *args):
    """
    Call a function f with args and return the time (in seconds) that it took to execute.
    """
    import time
    tic = time.time()
    f(*args)
    toc = time.time()
    return toc - tic

two_loop_time = time_function(classifier.compute_distances_two_loops, X_test)
print('Two loop version took %f seconds' % two_loop_time)

one_loop_time = time_function(classifier.compute_distances_one_loop, X_test)
print('One loop version took %f seconds' % one_loop_time)

no_loop_time = time_function(classifier.compute_distances_no_loops, X_test)
print('No loop version took %f seconds' % no_loop_time)

# You should see significantly faster performance with the fully vectorized implementation!

# NOTE: depending on what machine you're using, 
# you might not see a speedup when you go from two loops to one loop, 
# and might even see a slow-down.
```

1. If we look at the time_fuction 
inside the time func f is the fuction and *args is many argument so it calls f(*args) which is the function call for the parameter we send in time_function ugh T_T i made it more confusing , Just follow the function calls

## Cross-validation
```
num_folds = 5
k_choices = [1, 3, 5, 8, 10, 12, 15, 20, 50, 100]

X_train_folds = []
y_train_folds = []
################################################################################
# TODO:                                                                        #
# Split up the training data into folds. After splitting, X_train_folds and    #
# y_train_folds should each be lists of length num_folds, where                #
# y_train_folds[i] is the label vector for the points in X_train_folds[i].     #
# Hint: Look up the numpy array_split function.                                #
################################################################################
# *****START OF YOUR CODE (DO NOT DELETE/MODIFY THIS LINE)*****

X_train_folds = np.array_split(X_train, num_folds)
y_train_folds = np.array_split(y_train, num_folds)

# *****END OF YOUR CODE (DO NOT DELETE/MODIFY THIS LINE)*****

# A dictionary holding the accuracies for different values of k that we find
# when running cross-validation. After running cross-vali`dation,
# k_to_accuracies[k] should be a list of length num_folds giving the different
# accuracy values that we found when using that value of k.
k_to_accuracies = {}


################################################################################
# TODO:                                                                        #
# Perform k-fold cross validation to find the best value of k. For each        #
# possible value of k, run the k-nearest-neighbor algorithm num_folds times,   #
# where in each case you use all but one of the folds as training data and the #
# last fold as a validation set. Store the accuracies for all fold and all     #
# values of k in the k_to_accuracies dictionary.                               #
################################################################################
# *****START OF YOUR CODE (DO NOT DELETE/MODIFY THIS LINE)*****
```
this code is self explanatory

```
for k in k_choices:
  k_to_accuracies[k] = []
  for i in range(num_folds):
    #Prepare training data
    X_train_f = np.concatenate([fold for j,fold in enumerate(X_train_folds) if i!=j ]) 
    y_train_f = np.concatenate([fold for j,fold in enumerate(y_train_folds) if i!=j ]) 

    #Training the data
    classifier.train(X_train_f, y_train_f)
    y_test_pred = classifier.predict(X_train_folds[i], k=k)

    # Compute and print the fraction of correctly predicted examples
    num_correct = np.sum(y_test_pred == y_train_folds[i])
    #alternate for the line below
    #accuracy = float(num_correct) / X_train_folds[i].shape[0]
    accuracy = float(num_correct) / len(y_train_folds[i])
    k_to_accuracies[k].append(accuracy)

# *****END OF YOUR CODE (DO NOT DELETE/MODIFY THIS LINE)*****

# Print out the computed accuracies
for k in sorted(k_to_accuracies):
    for accuracy in k_to_accuracies[k]:
        print('k = %d, accuracy = %f' % (k, accuracy))
```
Also self explanatory if follow along the code

```
# plot the raw observations
for k in k_choices:
    accuracies = k_to_accuracies[k]
    plt.scatter([k] * len(accuracies), accuracies)
```
plt.scatter - https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.scatter.html

```
# plot the trend line with error bars that correspond to standard deviation
accuracies_mean = np.array([np.mean(v) for k,v in sorted(k_to_accuracies.items())])
accuracies_std = np.array([np.std(v) for k,v in sorted(k_to_accuracies.items())])
plt.errorbar(k_choices, accuracies_mean, yerr=accuracies_std)
plt.title('Cross-validation on k')
plt.xlabel('k')
plt.ylabel('Cross-validation accuracy')
plt.show()
```
Don't understand the above will update later

