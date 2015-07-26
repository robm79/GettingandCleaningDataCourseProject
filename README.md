# GettingandCleaningDataCourseProject

      # Read files 
        # Subject/Activity/Data for each of Training and Test sets
        # features.txt for variable descriptions, rename columns for readability
        # activity_labels.txt for activity labels, rename columns for readability
        
        # STEP ONE  
        # Merges the training and the test sets to create one data set.
        # For Subject set, bind training and test rows, rename column "V1" to "subject"
        # For Activity set, bind training and test rows, rename column "V1" to "activityNumber"
        # For Data Set, bind Subject and Activity rows, rename columns to hold features
                
        # STEP TWO
        # Extracts only the measurements on the mean and standard deviation for each measurement.
        # Subset dataClean using only subject/activity variables and columns with "mean" or "std" in title
        
        # STEP THREE
        # Uses descriptive activity names to name the activities in the data set.
        # Replace activityNumber with descriptive values in labelActivities, convert to character
        # Summarize dataClean by aggregating mean of each column for every subject and activityName

        # STEP FOUR
        # Appropriately labels the data set with descriptive variable names.
        # Capitalize MEAN and STDDEV for readability
        # Separate time vs frequency tags
        # Expand abbreviated words
        
        # STEP FIVE
        # From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

