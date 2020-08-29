Step 1, Importing the data, naming columns
I created an R Project titled peer 4, and selected to create a Github 
repository. I then saved all of the downloaded files into that peer4 R Folder
I used the Import Dataset function to get the code for reading and importing
the files.
I named the columns as part of the import code

library(readr)

library(dplyr)

library(tidyr)

features <- read_table2("UCI HAR Dataset/features.txt", 
                        col_names = c("feature_number", "feature"))

activities <- read_table2("UCI HAR Dataset/activity_labels.txt", 
                          col_names = c("activity_number", "activity"))

subject_test <- read_csv("UCI HAR Dataset/subject_test.txt", 
                         col_names = "subject")

subject_train <- read_csv("UCI HAR Dataset/subject_train.txt", 
                          col_names = "subject")

x_test <- read_table2("UCI HAR Dataset/X_test.txt", 
                      col_names = features$feature)

y_test <- read_table2("UCI HAR Dataset/y_test.txt",
                        col_names = "code")

x_train <- read_table2("UCI HAR Dataset/X_train.txt", 
                      col_names = features$feature)

y_train <- read_table2("UCI HAR Dataset/y_train.txt",
                      col_names = "code")

Step 2, Merge the training and the test sets to create one data set.
Using rbind, I first combined the x data tables, 
followed by the y data table, and the the subject train and test data tables.
I then used cbind to merge all three of the newly created factors.

xtt <- rbind(x_test, x_train)
View(xtt)

ytt <- rbind(y_test, y_train)
View(ytt)

test_subject <- rbind(subject_train, subject_test)
View(test_subject)

merged_table <- cbind(test_subject, xtt, ytt)

Step 3: Extract only the measurements on the mean and standard deviation 
for each measurement.

subsetmeanstd <- merged_table %>% 
        select(subject, code, contains("mean"), contains("std"))
View(subsetmeanstd)


Step 4: Use descriptive activity names to name the activities in the data set

subsetmeanstd$activity <- activities[subsetmeanstd$activity, 2]
View(subsetmeanstd$activity)

Step 5: Assigns appropriate/descriptive variable names. 
names(subsetmeanstd)[2] = "activity"
names(subsetmeanstd)<-gsub("Acc", "Accelerometer", names(subsetmeanstd))
names(subsetmeanstd)<-gsub("Gyro", "Gyroscope", names(subsetmeanstd))
names(subsetmeanstd)<-gsub("BodyBody", "Body", names(subsetmeanstd))
names(subsetmeanstd)<-gsub("Mag", "Magnitude", names(subsetmeanstd))
names(subsetmeanstd)<-gsub("^t", "Time", names(subsetmeanstd))
names(subsetmeanstd)<-gsub("^f", "Frequency", names(subsetmeanstd))
names(subsetmeanstd)<-gsub("tBody", "TimeBody", names(subsetmeanstd))
names(subsetmeanstd)<-gsub("-mean()", "Mean", names(subsetmeanstd), ignore.case = TRUE)
names(subsetmeanstd)<-gsub("-std()", "STD", names(subsetmeanstd), ignore.case = TRUE)
names(subsetmeanstd)<-gsub("-freq()", "Frequency", names(subsetmeanstd), ignore.case = TRUE)
names(subsetmeanstd)<-gsub("angle", "Angle", names(subsetmeanstd))
names(subsetmeanstd)<-gsub("gravity", "Gravity", names(subsetmeanstd))

Step 6: Uses the data created from step 4 (and renamed in Step 5), 
to create a second, independent 
tidy data set with the average of each variable for each activity and each 
subject.

FinalData <- subsetmeanstd %>%
        group_by(subject, activity) %>%
        summarise_all(funs(mean))
write.table(FinalData, "FinalData.txt", row.name=FALSE)

str(FinalData)
FinalData
write.table(FinalData, file = "FinalData.txt", sep = "\t", row.names = FALSE,
            col.names = TRUE)
