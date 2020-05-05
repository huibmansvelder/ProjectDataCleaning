---
title: "CodeBook.md"
author: "Huib"
date: "5/5/2020"
output: word_document
---
```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

Getting and Cleaning Data Course Project assignment
This code book describes the variables, the data, and  transformations/work performed to clean up the data

Goal of the assignment is to make a script that:
1.	Merges the training and the test data sets to create one data set.
2.	Extracts only the measurements on the mean and standard deviation for each measurement. 
3.	Uses descriptive activity names to name the activities in the data set
4.	Appropriately labels the data set with descriptive variable names. 
5.	From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

first set a single working directory that contains all relevant files
the code 'run_analysis.R' uses dplyr package

Variables: 
features: contains variable names of all measurements, will be used as column names
colselect(1&2): hold the column names that contain words 'mean' and 'std', will be used to select       the columns from the dataset
actlabels: contains names of activities 'walking', 'standing' etc
activity: applied names of activities
sessiontype: variable used to create factor column in dataset with 'test'/'train'

Data:
testdatatmp: temporary data frame with all data from 'test' sessions
testdata: data frame with selected columns containing data from 'test' sessions
activitiestmp: temporary variable used to match activities to measurements
activities: names and numbers of activities matched
testsubjects: data frame with numbers of the test subjects per measurement
traindatatmp: temporary data frame with all data from 'train' sessions
traindata: data frame with selected columns containing data from 'train' sessions
compldataset: merged dataset containing data of both 'test' and 'train' sessions
bysubj_byact: new dataset with the average of each variable for each activity and each subject (and       each sessiontype, test or train).

Transformations:
- names of columns were simplified
- columns with 'mean' or 'std' were selected from datasets 'test' and 'train'
- columns containing subjectnumber, activities ('standing', 'walking' etc) and sessiontypes ('test', 'train') were added
- 'test' and 'train' data tables were joined
- complete data set was grouped by subjectnumber, activity and sessiontype
- average of each variable was calculated for each activity and each subject (and each sessiontype, test or train).


## R Markdown
