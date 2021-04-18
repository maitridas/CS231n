#### This is just my notes according to my understanding

## Colab Code
```from google.colab import drive
drive.mount('/content/drive', force_remount=True)```

1. Mounts google drive to colab VM

```FOLDERNAME = 'cs231n/assignments/assignment1/'
assert FOLDERNAME is not None, "[!] Enter the foldername."```

1. stores folder path in a variable
2. checks whether the foldername is none if none asserts to add a folder name

```import sys
sys.path.append('/content/drive/My Drive/{}'.format(FOLDERNAME))```

1. python sys - The python sys module provides functions and variables which are used to manipulate different parts of the Python Runtime Environment. It lets us access system-specific parameters and functions. import sys. First, we have to import the sys module in our program before running any functions. sys.modules.
2. this ensures that the Python interpreter of the Colab VM can load python files from within it.

```%cd drive/My\ Drive/$FOLDERNAME/cs231n/datasets/
!bash get_datasets.sh
%cd /content/drive/My\ Drive/$FOLDERNAME```

1. Downloads CIFAR-10 if doesn't exists . How??

## k-Nearest Neighbor (kNN)
