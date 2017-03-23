# CodeBook
## Getting and Cleaning Data Course Project

### Instructions
The purpose of this project is to demonstrate your ability to collect, work with, and clean a data set. The goal is to prepare tidy data that can be used for later analysis. You will be graded by your peers on a series of yes/no questions related to the project. You will be required to submit: 1) a tidy data set as described below, 2) a link to a Github repository with your script for performing the analysis, and 3) a code book that describes the variables, the data, and any transformations or work that you performed to clean up the data called CodeBook.md. You should also include a README.md in the repo with your scripts. This repo explains how all of the scripts work and how they are connected.

One of the most exciting areas in all of data science right now is wearable computing - see for example this article . Companies like Fitbit, Nike, and Jawbone Up are racing to develop the most advanced algorithms to attract new users. The data linked to from the course website represent data collected from the accelerometers from the Samsung Galaxy S smartphone. 

### A full description is available at the site where the data was obtained:
http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones

### Here are the data for the project:
https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

### You should create one R script called run_analysis.R that does the following.
1.Merges the training and the test sets to create one data set.

2.Extracts only the measurements on the mean and standard deviation for each measurement.

3.Uses descriptive activity names to name the activities in the data set

4.Appropriately labels the data set with descriptive variable names.

5.From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

### Description of abbreviations of measurements
leading t or f is based on time or frequency measurements.
Body = related to body movement.
Gravity = acceleration of gravity
Acc = accelerometer measurement
Gyro = gyroscopic measurements
Jerk = sudden movement acceleration
Mag = magnitude of movement
mean and SD are calculated for each subject for each activity for each mean and SD measurements

The units given are g’s for the accelerometer and rad/sec for the gyro and g/sec and rad/sec/sec for the corresponding jerks.

These signals were used to estimate variables of the feature vector for each pattern:
‘-XYZ’ is used to denote 3-axial signals in the X, Y and Z directions. They total 33 measurements including the 3 dimensions - the X,Y, and Z axes.

tBodyAcc-XYZ
tGravityAcc-XYZ
tBodyAccJerk-XYZ
tBodyGyro-XYZ
tBodyGyroJerk-XYZ
tBodyAccMag
tGravityAccMag
tBodyAccJerkMag
tBodyGyroMag
tBodyGyroJerkMag
fBodyAcc-XYZ
fBodyAccJerk-XYZ
fBodyGyro-XYZ
fBodyAccMag
fBodyAccJerkMag
fBodyGyroMag
fBodyGyroJerkMag

#### The set of variables that were estimated from these signals are:
mean(): Mean value
std(): Standard deviation

### Data Set Information:
The experiments have been carried out with a group of 30 volunteers within an age bracket of 19-48 years. Each person performed six activities (WALKING, WALKING_UPSTAIRS, WALKING_DOWNSTAIRS, SITTING, STANDING, LAYING) wearing a smartphone (Samsung Galaxy S II) on the waist. Using its embedded accelerometer and gyroscope, we captured 3-axial linear acceleration and 3-axial angular velocity at a constant rate of 50Hz. The experiments have been video-recorded to label the data manually. The obtained dataset has been randomly partitioned into two sets, where 70% of the volunteers was selected for generating the training data and 30% the test data.

The sensor signals (accelerometer and gyroscope) were pre-processed by applying noise filters and then sampled in fixed-width sliding windows. From each window, a vector of features was obtained by calculating variables from the time and frequency domain.

### Doing the Project
Download the data from the given url. 
#### set the working directory
setwd("~/Documents/Coursera/week 4/Getting-and-Cleaning-Data-Course-Project")

Load required packages

### Load required packages
library(dplyr)
library(data.table)
library(reshape2)

### Files in folder ‘UCI HAR Dataset’ that will be used are:
SUBJECT FILES
test/subject_test.txt
train/subject_train.txt
 
ACTIVITY FILES
test/X_test.txt 
train/X_train.txt
 
DATA FILES
test/y_test.txt
train/y_train.txt

## 1) Merges the training and the test sets to create one data set.

### reading in the activity labels
activity_labels <- read.table("./UCI HAR Dataset/activity_labels.txt")[,2]

### reading in the features data
features <- read.table("./UCI HAR Dataset/features.txt")[,2]

## 2) Extract only the measurements on the mean and standard deviation for each measurement.
extract_features <- grepl("mean|std", features)

### reading the test data : X_test & y_test data.
X_test <- read.table("./UCI HAR Dataset/test/X_test.txt")
y_test <- read.table("./UCI HAR Dataset/test/y_test.txt")
subject_test <- read.table("./UCI HAR Dataset/test/subject_test.txt")

names(X_test) = features

### Extracting the features from the test data
X_test = X_test[,extract_features]

## 3) Uses descriptive activity names to name the activities in the data set
  names(Data_mean_std) <- gsub("Acc","Acceleration", names(Data_mean_std))
  names(Data_mean_std) <- gsub("^t", "Time",names(Data_mean_std))  
  names(Data_mean_std) <- gsub("^f", "Frequency", names(Data_mean_std))
  names(Data_mean_std) <- gsub("BodyBody", "Body", names(Data_mean_std))
  names(Data_mean_std) <- gsub("mean", "Mean", names(Data_mean_std))
  names(Data_mean_std) <- gsub("std", "Std", names(Data_mean_std))
  names(Data_mean_std) <- gsub("Freq", "Frequency", names(Data_mean_std))
  names(Data_mean_std) <- gsub("Mag", "Magnitude", names(Data_mean_std))

## 4) Appropriately labels the data set with descriptive variable names.

### Load activity labels
y_test[,2] = activity_labels[y_test[,1]]
names(y_test) = c("Activity_ID", "Activity_Label")
names(subject_test) = "subject"

### Binding all the test data
test_data <- cbind(as.data.table(subject_test), y_test, X_test)

### reading the train data: X_train & y_train data.
X_train <- read.table("./UCI HAR Dataset/train/X_train.txt")
y_train <- read.table("./UCI HAR Dataset/train/y_train.txt")

subject_train <- read.table("./UCI HAR Dataset/train/subject_train.txt")

names(X_train) = features

### Extracting the features from train data.
X_train = X_train[,extract_features]

### Load activity data
y_train[,2] = activity_labels[y_train[,1]]
names(y_train) = c("Activity_ID", "Activity_Label")
names(subject_train) = "subject"

### Bind data
train_data <- cbind(as.data.table(subject_train), y_train, X_train)

### Merge test and train data
data = rbind(test_data, train_data)

# 5) From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.
id_labels   = c("subject", "Activity_ID", "Activity_Label")
data_labels = setdiff(colnames(data), id_labels)
melt_data      = melt(data, id = id_labels, measure.vars = data_labels)

### Apply mean function to dataset using dcast function
tidy_data   = dcast(melt_data, subject + Activity_Label ~ variable, mean)

write.table(tidy_data, file = "./tidy_data.txt")



