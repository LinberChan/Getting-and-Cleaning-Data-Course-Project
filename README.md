# Getting-and-Cleaning-Data-Course-Project

# 1.Merge the training and the test sets to create one data set.
## set the road
getwd()
setwd("C:\\Users\\Linbo\\Desktop\\工作\\R\\Coursera\\getting and cleanning\\assignment\\UCI HAR Dataset")

## load data into R
library(data.table)

## train set
X_train <- fread("train\\X_train.txt")
y_train <- fread("train\\y_train.txt")
subject_train <- fread("train\\subject_train.txt")

## test set
X_test <- fread("test\\X_test.txt")
y_test <- fread("test\\y_test.txt")
subject_test <- fread("test\\subject_test.txt")

## merge train set and test set into a full data
train <- cbind(subject_train, y_train, X_train)
test <- cbind(subject_test, y_test, X_test)
full <- rbind(train, test)
full <- data.frame(full)
dim(full)

# 2.Extracts only the measurements on the mean and standard deviation for each measurement.

## load feature names
featureName <- fread("features.txt", stringsAsFactors = FALSE)
featureName <- data.frame(featureName)[,2]

## extract mean and standard deviation of each measurements
featureIndex <- grep(("mean\\(\\)|std\\(\\)"), featureName)
MSdata <- full[, c(1, 2, featureIndex+2)]
colnames(MSdata) <- c("subject", "activity", featureName[featureIndex])

# 3.Uses descriptive activity names to name the activities in the data set.

## load activity names 
activityName <- data.frame(fread("activity_labels.txt"))

## replace 1 to 6 with activity names
MSdata$activity <- factor(MSdata$activity, levels = activityName[,1], labels = activityName[,2])

# 4.Appropriately labels the data set with descriptive variable names.

## alter the labels with proper words
names(MSdata) <- gsub("\\()", "", names(MSdata))
names(MSdata) <- gsub("^t", "Time", names(MSdata))
names(MSdata) <- gsub("^f", "Freq", names(MSdata))
names(MSdata) <- gsub("-mean", "Mean", names(MSdata))
names(MSdata) <- gsub("-std", "Std", names(MSdata))

# 5.From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

library(dplyr)

FinalData <- MSdata %>%
    group_by(subject, activity) %>%
        summarise_each(funs(mean))

# 6.write txt file
write.table(FinalData, "MeanData.txt", row.names = FALSE)

