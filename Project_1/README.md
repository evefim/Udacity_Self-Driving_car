# Project 1. Object detection in an Urban Environment
## Project overview
This project is devoted to study object detection techniques applicable to self-driving cars industry.
Namely it deals with detection of three type of objects: cars, pedestrians and cyclists in different environments: roads, highways, crossroads etc.
This task is very important for self-driving cars, because images taken by cameras (along with information from lidars and radars) are the key means that provide automotive AI with the information needed to correctly identify the car position relative to other road objects. 

The goal of this project is to train 3-class Object detection model which is able to identify position of a car, a pedestrian or a cyclist based on Waymo Open Dataset using Tensorflow Object Detection API.

## Dataset
The dataset consists of 100 tfrecord files from the [Waymo Open dataset](https://waymo.com/open/). The files can be downloaded directly from the website as tar files or from the [Google Cloud Bucket](https://console.cloud.google.com/storage/browser/waymo_open_dataset_v_1_2_0_individual_files/) as individual tf records. 

### Dataset analysis
The dataset analysis is given in the Jupyter notebook [Exploratory Data Analysis.ipynb](Exploratory%20Data%20Analysis.ipynb)

Some conclusions are summarized below:
- There are different weather, daytime and road conditions. This is good from the model generalization ability point of view.  
- Three classes (a car, a pedestrian and a cyclist) are very imbalanced. In 1937 images there are 34855 cars, 10423 pedestrians and 270 cyclists. This fact makes cyclists very difficult to detect (or in other words to teach the model to identify them).
- One image may contain a lot of detections, in the given subset there are up to 75 bounding boxes in a single image. Some images are empty, which is also valid event
- The scale of objects bounding boxes is also very different from very huge (for example, very long bus in a foreground) to tiny pedestrians in the distance.
- There are also some errors in dataset (not many though): wrong bounding boxes, objects without bounding box

### Cross validation
Dataset should be split into 3 subsets: for training (train), validation (val) and testing (test).
Initially, 97 of these files are in the folder train_and_val and 3 files were put aside for testing.
Remaining 97 files should be splitted into two parts: training and validation. The ratio 85/15 was chosen to have a reasonable number of files in both sets.
As was shown above, the classes are very imbalanced, so instead of random split of train_and_val files into two categories, the total number of every kind of objects is maintained close to 85/15 split.

The procedure implemented in the [Exploratory Data Analysis.ipynb](Exploratory%20Data%20Analysis.ipynb) is following:

0. Count number of cars, pedestrians and cyclists in each file.
1. Split the whole dataset into two groups: containing a cyclist type of object and not. 
2. Count the number of files containing cyclists, in this case it is 27.
3. Split this number also in proportion 85/15, so for validation we need to take 4 (0.15 * 27) files with cyclists and 11 (0.15 * 70) files without cyclists.
This is done in order to have different files with cyclists for better generalization.
4. Randomly permute lists of files with and without cyclists and take from this lists 4 and 11 files, respectively, for validation and remaining files for training.
5. Continue step 4 until the total number of all types of objects is be split in proportion 85/15 for training and validation sets.
6. Save filenames for validation set in validation.txt. 
7. Run [create_splits.py](create_splits.py) script to perform real dataset split.

This procedure is quite straightforward and it does not take into account another important factors, for example, diversity of weather and/or daytime conditions, but it tries to deal with imbalanced dataset. Another idea is that different ration between training and validation can be used (for example, 90/10), to have more data for training.

## Training
The training was performed using Udacity so there were some limitations on available disk space and total train time. 

### Reference experiment
The reference experiment was performed using the config for a SSD Resnet 50 640x640 model from Tensorflow Object Detection API with the default .

| Precision/Recall | area | maxDets | Score |
|-------|--------|--------|-------|
 Average Precision  (AP) @[ IoU=0.50:0.95] |    all | 100 | 0.000|
 Average Precision  (AP) @[ IoU=0.50 ]     |    all | 100 | 0.001|
 Average Precision  (AP) @[ IoU=0.75 ]     |    all | 100 | 0.000
 Average Precision  (AP) @[ IoU=0.50:0.95 ]| small | 100 | 0.000
 Average Precision  (AP) @[ IoU=0.50:0.95 ]| medium | 100 | 0.002
 Average Precision  (AP) @[ IoU=0.50:0.95 ]| large | 100 | 0.002
 Average Recall     (AR) @[ IoU=0.50:0.95 ]|  all |  1 | 0.000
 Average Recall     (AR) @[ IoU=0.50:0.95 ]|  all |  10 | 0.002
 Average Recall     (AR) @[ IoU=0.50:0.95 ]|  all | 100 | 0.007
 Average Recall     (AR) @[ IoU=0.50:0.95 ]| small | 100 | 0.000
 Average Recall     (AR) @[ IoU=0.50:0.95 ]| medium | 100 | 0.001
 Average Recall     (AR) @[ IoU=0.50:0.95 ]| large | 100 | 0.113

### Improve on the reference
This section should highlight the different strategies you adopted to improve your model. It should contain relevant figures and details of your findings.