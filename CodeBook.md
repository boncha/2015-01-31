Code Book of Getting and Cleaning Data Course Project

The following document describes  the variables, the data,  the transformations and work performed to clean up the data in the Getting and Cleaning Data Course Project.

The data was downloaded from:
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


I wrote a script in R (“run_analysis.R”) to perform 5 tasks. Below I describe the code and I explain the transformations:

Note: this code requires that the working directory in R has been already set to “UCI HAR Dataset”, and that the user has reading and writing priviliges in this directory and the conatined files/directories. Also, this script requires that the “dplyr” package be already installed.

1. Merge the training and the test sets to create one data set.

## First we load the data and we give the names of the columns
features<-read.table("./features.txt",header=F,stringsAsFactors = F) ## this data frame contains the names of the columns for x_train and x_test dfs.

subject_train<-read.table("./train/subject_train.txt",header=F,stringsAsFactors = F,sep="") ## this data frame contains the IDs of the subjects mathcing the x_train measurement
 
y_train<-read.table("./train/y_train.txt",header=F,stringsAsFactors = F,sep="") ## this data frame contains the activities for the subjects mathcing the x_train measurement

x_train<-read.table("./train/X_train.txt",header=F,stringsAsFactors = F,sep="") ## this data frame cotains the measurements for the train subjects

names(x_train)<-features$V2 ## asignes the cloumn names to the x_train data

names(y_train)<-"Activity" ## asignes the cloumn name to the activity_train  data frame

names(subject_train)<-"SubjectID"  ## asignes the cloumn name to the subject_train  data frame

subject_test<-read.table("./test/subject_test.txt",header=F,stringsAsFactors = F,sep="") ## this data frame contains the IDs of the subjects mathcing the x_test measurement

y_test<-read.table("./test/y_test.txt",header=F,stringsAsFactors = F,sep="") ## this data frame contains the activities for the subjects mathcing the x_test measurement

x_test<-read.table("./test/X_test.txt",header=F,stringsAsFactors = F,sep="") ## this data frame cotains the measurements for the test subjects

names(x_test)<-features$V2 ## asignes the cloumn names to the x_test data

names(y_test)<-"Activity" ## asignes the cloumn name to the activity_test  data frame

names(subject_test)<-"SubjectID"  ## asignes the cloumn name to the subject_test data frame

## Then we combine the subject ID, Activity, and measurements for the train data, into a new data frame: train_data

train_data<-cbind(subject_train,y_train,x_train)

## Then we combine the subject ID, Activity, and measurements for the test data, into a new data frame: test_data
test_data<-cbind(subject_test,y_test,x_test)

## Then we merge the test_data and the train_data into merged_data
merged_data<-rbind(test_data,train_data)

2. Extracts only the measurements on the mean and standard deviation for each measurement.

## Then we extract only the measurements on the mean and standard deviation for each measurement into a new df: merged_data02. Note that per later requirements we need to keep the SubjectID and Activities columns, too.

merged_data02<-merged_data[,c(1,2, grep("mean\\(|std\\(",names(merged_data)))]

3. Use descriptive activity names to name the activities in the data set

## Then we import the table containing the codes for the Activities:
Activity_labels<-read.table("./activity_labels.txt",header=F,stringsAsFactors = F,sep="")

## And we rename the columns in the Activity_labels data frame:
names(Activity_labels)<-c("Code","Activity")

## Then we use descriptive activity names to name the activities in the data set
merged_data03<-merge(Activity_labels,merged_data02,by.y="Activity",by.x="Code")

merged_data03<-merged_data03[,-1] ## this line removes the column "Code", which is no longer needed.

4. Appropriately labels the data set with descriptive variable names.

## Then, we appropriately label the data set (merged_data03) with descriptive variable names.
## where it reads:	it will read:
## prefix "t" 		Time
## prefix "f"  		Frequency
## Acc 			Acceleration
## Gyro 		Gyroscopic
## -X 			-Xaxis
## -Y 			-Yaxis
## -Z 			-Zaxis
## Mag 			Magnitude
## std 			StandardDeviation

names_merged_data03<-names(merged_data03)
names_merged_data03<-gsub("^t","Time",names_merged_data03)
names_merged_data03<-gsub("^f","Frequency",names_merged_data03)
names_merged_data03<-gsub("Acc","Acceleration",names_merged_data03)
names_merged_data03<-gsub("Gyro","Gyroscopic",names_merged_data03)
names_merged_data03<-gsub("Mag","Magnitude",names_merged_data03)
names_merged_data03<-gsub("([XYZ])$","\\1Axis",names_merged_data03)
names_merged_data03<-gsub("std","StandardDeviation",names_merged_data03)

names(merged_data03)<-names_merged_data03

## Now we save this  tidy data set:
write.table(merged_data03,"./TidyDataSet.csv",sep=",",row.names = F)

5. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

## To summarize data we'll use the dplyr package:
library(dplyr)

## Let's import the data set
Tidy_data_set<-read.table("./TidyDataSet.csv",sep=",",header=T,stringsAsFactors = F)

##Then we group by SubjectID and Activity
grouped_Tidy_data_set<-group_by(Tidy_data_set,SubjectID,Activity)

 Then we use summarize_each to create the summary.
summary_grouped_Tidy_data_set<-summarize_each(grouped_Tidy_data_set,funs(mean))

## Then we save this summary:
write.table(summary_grouped_Tidy_data_set,"./SummaryGroupedTidyDataset.csv",sep=",",row.names = F)

