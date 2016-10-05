## cascademan
A manager for training cascade classifiers in OpenCV. Also has utilities for cropping and sorting images.

# Help text
To display the help text, type
```
./cascademan.py help
```

# Add a root dir
Before you can use cascademan, you have to set up a root dir where all the categories will be created.
```
./cascademan.py set root <directory>
```
For example:
```
./cascademan.py set root ~/cascademan/
```

# Create an empty category
To create a category of images called trees:
```
./cascademan.py create trees
```

You can create multiple empty categories at a time:
```
./cascademan.py create trees grass flowers
```
These categories will hold images that you choose.

# List the categories
```
cascademan list
```
OR
```
cascademan ls
```

# Add images to a category
If you want to add images from the Downloads/trees/ directory into a category called "trees":
```
./cascademan.py add trees Downloads/trees
```
You can add from multiple source directories:
```
./cascademan.py add trees Downloads/trees Pictures/trees
```

# Rename a category
To rename a category called trees to forests:
```
./cascademan.py rename trees forests
```
OR
```
./cascademan.py move trees forests
```
# Copy a category
To copy a category called trees to trees_copy:
```
./cascademan.py copy trees trees_copy
```
You will get a warning if the destination category already exists.

# Get info about the categories
```
./cascademan.py info
```
To see info on a subset of the categories:
```
./cascademan.py info trees flowers
```

# View a slideshow of the images in a category
```
./cascademan.py view trees
```


# Sort images in a category to multiple other categories
To sort trees to oak, birch aspen, and pine:
```
./cascademan.py sort trees oak birch aspen pine
```

To sort trees to all other categories, leave off the destinations:
```
./cascademan.py sort trees
```

# Crop images
To crop trees to branches:
```
./cascademan.py crop trees branches
```
To crop trees to trees_crop:
```
./cascademan.py crop trees
```
If the desitination is omitted, \<source>_crop is assumed

# Training a cascade classifier from the images in a category
```
./cascademan.py train <category> <numStages> <width> <height> <negativeCategory1> <negativeCategory2> ...
```
For example, to train a classifier for trees:
```
./cascademan.py train trees 4 300 300 flowers shrubs bushes grass people
```
