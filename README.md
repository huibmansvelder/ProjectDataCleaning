---
title: "Readme"
author: "Huib"
date: "5/5/2020"
output: html_document
---

full code of the script 'run_analysis.R'

## Goals of this script:
##  1.	Merges the training and the test data sets to create one data set.
##  2.	Extracts only the measurements on the mean and standard deviation for each measurement. 
##  3.	Uses descriptive activity names to name the activities in the data set
##  4.	Appropriately labels the data set with descriptive variable names. 
##  5.	From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

# set single working directory with all relevant files
# "/Users/huib/Documents/Huib/Coursera/DataScience/3-Getting and Cleaning Data/Assignment/test"
library(dplyr)


## read feature names and simplify names (Goal 4)
features <- read.table("features.txt", header = FALSE, colClasses = "character")
features <- gsub("-","", features$V2)
features <- gsub("\\()","", features)
features <- tolower(features)

# read testdata and assign features as column names (Goal 4)
testdatatmp <- read.table("X_test.txt")
colnames(testdatatmp) <- features

# to select mean and std data, determine which columnnames contain 'mean' or 'std' (Goal 2)
colselect1 <- grep("mean", features)
colselect2 <- grep("std", features)
colselect <- sort(c(colselect1, colselect2))

# subset testdata table to keep only columnns with mean and std data (Goal 2)
testdata <- testdatatmp[,colselect]

# read activity labels file and simplify names (Goal 3)
actlabels <- read.table("activity_labels.txt", header = FALSE, colClasses = "character")
actlabels <- gsub("ING_","", actlabels$V2)
actlabels <- tolower(actlabels)

# assign activities to test data (Goal 3)
activitiestmp <- read.table("y_test.txt")
activitiestmp <- tbl_df(activitiestmp)
activities <- activitiestmp %>%
  mutate(
    activity = as.factor(case_when(
      activitiestmp == 1 ~ actlabels[1],
      activitiestmp == 2 ~ actlabels[2],
      activitiestmp == 3 ~ actlabels[3],
      activitiestmp == 4 ~ actlabels[4],
      activitiestmp == 5 ~ actlabels[5],
      activitiestmp == 6 ~ actlabels[6],
    ))
  )

# add factor activity column to testdata (Goal 3)
activity <- activities$activity
testdata <- cbind(activity,testdata)

# add data type (test or train) column to testdata (Goal 3)
sessiontype <- rep("test", nrow(activities))
testdata <- cbind(sessiontype,testdata)

# read subject number file
testsubjects <- read.table("subject_test.txt")
colnames(testsubjects) <- "subjects"

# add testsubjects column to testdata
testdata <- cbind(testsubjects,testdata)
testdata <- tbl_df(testdata)


# read traindata and assign features as column names (Goal 4)
traindatatmp <- read.table("X_train.txt")
colnames(traindatatmp) <- features

# subset traindata table to keep only columnns with mean and std data (Goal 2)
traindata <- traindatatmp[,colselect]

# assign activities to train data
activitiestmp <- read.table("y_train.txt")
activitiestmp <- tbl_df(activitiestmp)
activities <- activitiestmp %>%
  mutate(
    activity = as.factor(case_when(
      activitiestmp == 1 ~ actlabels[1],
      activitiestmp == 2 ~ actlabels[2],
      activitiestmp == 3 ~ actlabels[3],
      activitiestmp == 4 ~ actlabels[4],
      activitiestmp == 5 ~ actlabels[5],
      activitiestmp == 6 ~ actlabels[6],
    ))
  )

# add factor activity column to traindata (Goal 3)
activity <- activities$activity
traindata <- cbind(activity,traindata)

# add data type (test or train) column to testdata (Goal 3)
sessiontype <- rep("train", nrow(activities))
traindata <- cbind(sessiontype,traindata)

# read subject number file
testsubjects <- read.table("subject_train.txt")
colnames(testsubjects) <- "subjects"

# add testsubjects column to testdata
traindata <- cbind(testsubjects,traindata)
traindata <- tbl_df(traindata)

# join test and train datasets into one complete dataset (Goal 1) and do some cleaning 
compldataset <- rbind(testdata, traindata)
rm(testdatatmp)
rm(traindatatmp)
rm(activitiestmp)

# group the dataset first by subject and then by activity and
# make a new dataset 'bysubj_byact' with the average of each variable for 
# each activity and each subject (and each sessiontype, test or train).(Goal 5)
bysubj_byact <- compldataset %>%                                  
  group_by(subjects, activity, sessiontype) %>%    
    summarise_if(is.numeric, mean, na.rm = TRUE)
            
View(bysubj_byact)

