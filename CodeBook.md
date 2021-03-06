run_analysisR
=============

Peer assessment assignment for Data Science Getting and Cleaning Data

The comments in the script sufficiently explains how the script works. However, here's the general summary:

1. The script reads all necessary data from various files given and stores them in relevant variables.
2. The script then adds labels by transposing the rows in the features.txt and using appropriate values.
3. It then adds columns for the subject and activity and assigns appropriate headers
4. It combines both train and test tables into one and finally replaces data in activity column with descriptive activity. 
5. Finally, it converts itself in data.table and uses lapply to create independent tidy data set with the average of each variable for each activity and each subject. 

Note:
The working directory is UCI_HAR_Dataset as downloaded directly from 
https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip



Below is the code with comments:


# Reading test data
test_data <- read.table("test/X_test.txt")
y_test <- read.table("test/y_test.txt")
subject_test <- read.table("test/subject_test.txt")

# Reading train data
train_data <- read.table("train/X_train.txt")
y_train <- read.table("train/y_train.txt")
subject_train <- read.table("train/subject_train.txt")

# Reading labels and features
activity_labels <- read.table("activity_labels.txt")
features <- read.table("features.txt")

# Transposing rows of features to columns and assigning second row to header_names
header_names<-t(features)
header_names<-header_names[2,]

# Assiging header_names to both tables 
names(test_data)<-c(header_names)
names(train_data)<-c(header_names)

# Assigning activity and subject_name as headers to the proper tables
names(y_test)<-"activity"
names(y_train)<-"activity"
names(subject_test)<-"subject_name"
names(subject_train)<-"subject_name"

# Combining all test and train related tables separately
test_data<-cbind(subject_test, y_test, test_data)
train_data<-cbind(subject_train, y_train, train_data)

# Data merge
merged_data<-rbind(test_data, train_data)

# Replacing activity codes with descriptive activity, and thus the final merge
from <- c(1,2,3,4,5,6)
to <- c("WALKING", "WALKING_UPSTAIRS", "WALKING_DOWNSTAIRS", "SITTING", "STANDING", "LAYING")

gsub2 <- function(pattern, replacement, x, ...) {
  for(i in 1:length(pattern))
    x <- gsub(pattern[i], replacement[i], x, ...)
  x
}

merged_data$activity <- gsub2(from, to, merged_data$activity)


# Finding headers with -mean() or -std() and selecting dataframe with only those headers. Also includes headers such as xxx-meanFreqxxx
headernames<-names(merged_data)
selected<-union(grep("-mean()", c(headernames)), grep("-std()", c(headernames)))
selectedData<-merged_data[,selected]

# Independent tidy data set with the average of each variable for each activity and each subject
library(data.table)
merged_data<-data.table(merged_data)
variables <- tail( names(merged_data), -2)
dataset_with_average<-merged_data[, lapply(.SD, mean), .SDcols=variables, by=list(subject_name, activity)]







