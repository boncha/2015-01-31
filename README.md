README.md

Getting and Cleaning Data Course Project
Prepared by: Gustavo Kijak
Date: 2015-01-31

As per instructions, “The purpose of this project is to demonstrate your ability to collect, work with, and clean a data set. The goal is to prepare tidy data that can be used for later analysis”.

The analyzed data was downloaded from:
https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

The zip file was extracted, resulting in the “UCI HAR Dataset”  folder, which contians all original the dataset. The original dataset is described by its original authors in the README.txt file. The files used in the current excesise are:

./features.txt: this file  contains the original names of the variables represented in the columns from datasets x_train and x_test.

./activity_labels.txt: this file containes the codes for the Activities

./train/subject_train.txt: this file contains the IDs of the subjects mathcing the x_train measurement.

./train/y_train.txt: this file  contains the activities for the subjects mathcing the x_train measurement.

./train/X_train.txt:  this file cotains the measurements for the train subjects.

./test/subject_test.txt:  this file contains the IDs of the subjects mathcing the x_test measurement.

./test/y_test.txt:  this file contains the activities for the subjects mathcing the x_test measurement.

 ./test/X_test.txt: this file cotains the measurements for the test subjects.

The following document explains how the script “run_analysis.R” works.

Note: this code requires that the working directory in R has been already set to “./UCI HAR Dataset”, and that the user has reading and writing priviliges in this directory and the conatined files/directories. Also, this script requires that the “dplyr” package be already installed.

I wrote a single script in R (“run_analysis.R”) to sequentially perform 5 tasks:

1. Merges the training and the test sets to create one data set.
This is achieved by:
a) reading the data and storing it in respective data frames.
b) assigning column names to the data frames.
c) merging the data frames from the SubjectID, Coded Activities, and measurements. This is performed separately for the “train” and “test” groups.
d) merging the train and test data frames into a single data frame.

2. Extracts only the measurements on the mean and standard deviation for each measurement.
This is achieved by:
a) creating a new data frame that extracts the columns whose names contain the strings “mean(“ or “std(“ using the grep function. Note that per later requirements we need to keep the SubjectID and Activities columns, too.

3. Uses descriptive activity names to name the activities in the data set
This is achieved by:
a) importing the table containing the codes for the Activities.
b) merging the data frame from task 2.a with the data frame from task 3.a, using the merge function.

4. Appropriately labels the data set with descriptive variable names.
This is achieved by:
a) substituting the abbreviated terms for descriptive terms using the gsub function. 
 where it reads:	it will read:
prefix "t" 		Time
 prefix "f"  		Frequency
 Acc 			Acceleration
Gyro 		Gyroscopic
-X 			-Xaxis
-Y 			-Yaxis
-Z 			-Zaxis
 Mag 			Magnitude
std 			StandardDeviation

5. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.
This is achieved by:
a)grouping the SubjectID and Activity columns, using the group_by function from the dplyr package.
b)summarizing the data using the summarize_each function rom the dplyr package.

The application of the script results in the generation of two new files:
./TidyDataSet.csv : product of tasks 1 through 4

./SummaryGroupedTidyDataset.csv : product of task 5.
