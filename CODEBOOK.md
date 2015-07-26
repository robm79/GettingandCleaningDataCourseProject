# GettingandCleaningDataCourseProject

The purpose of this project is to demonstrate your ability to collect, work with, and clean a data set. The goal is to prepare tidy data that can be used for later analysis. You will be graded by your peers on a series of yes/no questions related to the project. You will be required to submit: 1) a tidy data set as described below, 2) a link to a Github repository with your script for performing the analysis, and 3) a code book that describes the variables, the data, and any transformations or work that you performed to clean up the data called CodeBook.md. You should also include a README.md in the repo with your scripts. This repo explains how all of the scripts work and how they are connected.  

One of the most exciting areas in all of data science right now is wearable computing - see for example this article . Companies like Fitbit, Nike, and Jawbone Up are racing to develop the most advanced algorithms to attract new users. The data linked to from the course website represent data collected from the accelerometers from the Samsung Galaxy S smartphone. A full description is available at the site where the data was obtained: 

http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones 

Here are the data for the project: 

https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip 

 You should create one R script called run_analysis.R that does the following. 
Merges the training and the test sets to create one data set.
Extracts only the measurements on the mean and standard deviation for each measurement. 
Uses descriptive activity names to name the activities in the data set
Appropriately labels the data set with descriptive variable names. 
From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

run_analysis <- function(){
        library(dplyr)
        library(data.table)
        filesPath <- "/Users/robmurray/Desktop/Coursera JHU - 3 - Getting and Cleaning Data/UCI HAR Dataset"
        
        # Read files 
        # Subject/Activity/Data for each of Training and Test sets
        # features.txt for variable descriptions, rename columns for readability
        # activity_labels.txt for activity labels, rename columns for readability
        dataSubjectTraining <- tbl_df(read.table(file.path(filesPath, "train", "subject_train.txt")))
        dataActivityTraining <- tbl_df(read.table(file.path(filesPath, "train", "Y_train.txt")))
        dataDataTraining <- tbl_df(read.table(file.path(filesPath, "train", "X_train.txt" )))
        
        dataSubjectTest  <- tbl_df(read.table(file.path(filesPath, "test" , "subject_test.txt" )))
        dataActivityTest  <- tbl_df(read.table(file.path(filesPath, "test" , "Y_test.txt" )))
        dataDataTest  <- tbl_df(read.table(file.path(filesPath, "test" , "X_test.txt" )))
        
        labelFeatures <- tbl_df(read.table(file.path(filesPath, "features.txt")))
        setnames(labelFeatures, names(labelFeatures), c("featureNumber", "featureName"))
        
        labelActivities <- tbl_df(read.table(file.path(filesPath, "activity_labels.txt")))
        setnames(labelActivities, names(labelActivities), c("activityNumber","activityName"))
        
        # STEP ONE  
        # Merges the training and the test sets to create one data set.
        # For Subject set, bind training and test rows, rename column "V1" to "subject"
        # For Activity set, bind training and test rows, rename column "V1" to "activityNumber"
        # For Data Set, bind Subject and Activity rows, rename columns to hold features
                
        dataSubject <- rbind(dataSubjectTraining, dataSubjectTest)
        setnames(dataSubject, "V1", "subject")
        dataActivity <- rbind(dataActivityTraining, dataActivityTest)
        setnames(dataActivity, "V1", "activityNumber")
        
        dataClean <- rbind(dataDataTraining, dataDataTest)
        colnames(dataClean) <- labelFeatures$featureName
        
        dataSubjectActivity <- cbind(dataSubject, dataActivity)
        dataClean <- cbind(dataSubjectActivity, dataClean)
        
        # STEP TWO
        # Extracts only the measurements on the mean and standard deviation for each measurement.
        # Subset dataClean using only subject/activity variables and columns with "mean" or "std" in title
        dataClean<- subset(dataClean,select = union(c("subject","activityNumber"), grep("mean\\(\\)|std\\(\\)",labelFeatures$featureName,value=TRUE))) 
        
        # STEP THREE
        # Uses descriptive activity names to name the activities in the data set.
        # Replace activityNumber with descriptive values in labelActivities, convert to character
        # Summarize dataClean by aggregating mean of each column for every subject and activityName
        dataClean <- merge(labelActivities, dataClean , by="activityNumber", all.x=TRUE)
        dataClean$activityName <- as.character(dataClean$activityName)
        dataClean<- tbl_df(arrange(dataClean %>% group_by(subject, activityName) %>% summarise_each(funs(mean)),subject,activityName))
        
        # STEP FOUR
        # Appropriately labels the data set with descriptive variable names.
        # Capitalize MEAN and STDDEV for readability
        # Separate time vs frequency tags
        # Expand abbreviated words
        names(dataClean)<-gsub("std()", "STDDEV", names(dataClean))
        names(dataClean)<-gsub("mean()", "MEAN", names(dataClean))
        names(dataClean)<-gsub("^t", "time-", names(dataClean))
        names(dataClean)<-gsub("^f", "frequency-", names(dataClean))
        names(dataClean)<-gsub("Acc", "Accelerometer", names(dataClean))
        names(dataClean)<-gsub("Gyro", "Gyroscope", names(dataClean))
        names(dataClean)<-gsub("Mag", "Magnitude", names(dataClean))
        
        # STEP FIVE
        # From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.
        write.table(dataClean, "TidyDataSet.txt", row.name=FALSE)
}