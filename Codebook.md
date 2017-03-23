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
setwd("~/Documents/datasciencecoursera/UCI HAR Dataset")

Load required packages

### Load required packages
library(dplyr)
library(data.table)
library(tidyr)

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
 
features.txt - Names of column variables in the dataTable

activity_labels.txt - Links the class labels with their activity name.

### Read the files and create data tables 

#### reading training data 
features      <- read.table("./features.txt", header = FALSE)
activityLabel <- read.table("./activity_labels.txt", header = FALSE)
subjectTrain  <- read.table("./train/subject_train.txt", header = FALSE)
xTrain        <- read.table("./train//X_train.txt", header = FALSE)
yTrain        <- read.table("./train/y_train.txt", header = FALSE)

#### Assign column names to training data
  colnames(activityLabel) <- c("activity_id","activity_type")
  colnames(subjectTrain)  <- "sub_id"
  colnames(xTrain)        <- features[,2]
  colnames(yTrain)        <- "activity_id"
  
#### Merge the training Data
  trainData <- cbind(yTrain,subjectTrain,xTrain)
  
#### Reading the test Data
  subjectTest <- read.table("./test/subject_test.txt", header = FALSE)
  xTest       <- read.table("./test/X_test.txt", header = FALSE)
  yTest       <- read.table("./test/y_test.txt", header = FALSE)
  
  # Assign column names to test data
  colnames(subjectTest) <- "sub_id"
  colnames(xTest)       <- features[,2]  
  colnames(yTest)       <- "activity_id"  
  
#### Merge test data
  testData <- cbind(yTest,subjectTest,xTest)

#### Final merge of both of the data sets
  finalData <- rbind(trainData,testData)
  
#### Make a vector for the column names
  colNames <- colnames(finalData);
  
  
### Extracts only the measurements on the mean and standard deviation for each measurement.
  Data_mean_std <- finalData[,grepl("mean|std|sub_id|activity_id",colnames(finalData))]

  library(plyr)
  
  Data_mean_std <- join(Data_mean_std, activityLabel, by = "activity_id", match = "first")  
  Data_mean_std <- Data_mean_std[,-1]

#### Appropriately labels the data set with descriptive variable names.

#### Remove parentheses 
  names(Data_mean_std) <- gsub("\\(|\\)","",names(Data_mean_std), perl = TRUE)
  
#### correct syntax in names
  names(Data_mean_std) <- make.names(names(Data_mean_std))
  
### Uses descriptive activity names to name the activities in the data set
  names(Data_mean_std) <- gsub("Acc","Acceleration", names(Data_mean_std))
  names(Data_mean_std) <- gsub("^t", "Time",names(Data_mean_std))  
  names(Data_mean_std) <- gsub("^f", "Frequency", names(Data_mean_std))
  names(Data_mean_std) <- gsub("BodyBody", "Body", names(Data_mean_std))
  names(Data_mean_std) <- gsub("mean", "Mean", names(Data_mean_std))
  names(Data_mean_std) <- gsub("std", "Std", names(Data_mean_std))
  names(Data_mean_std) <- gsub("Freq", "Frequency", names(Data_mean_std))
  names(Data_mean_std) <- gsub("Mag", "Magnitude", names(Data_mean_std))
  
### Creates a second independent tidy data set with the average of each variable for each activity and subject.
  
Tidy_dataset_average_subject <- ddply(Data_mean_std, c("sub_id", "activity_id"), numcolwise(mean))
 
write.table(Tidy_dataset_average_subject, file = "tidydata.txt")