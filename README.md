# Libraries Used
library(dplyr)
library(data.table)

# Reading the test data

x_test <- read.table("./UCI HAR Dataset/test/X_test.txt")
y_test <- read.table("./UCI HAR Dataset/test/y_test.txt")
subject_test <- read.table("./UCI HAR Dataset/test/subject_test.txt")

# Reading the training data

x_train <- read.table("./UCI HAR Dataset/train/X_train.txt")
y_train <- read.table("./UCI HAR Dataset/train/y_train.txt")
subject_train <- read.table("./UCI HAR Dataset/train/subject_train.txt")

# Reading names of variables

variables <- read.table("./UCI HAR Dataset/features.txt")

# Reading activities' names

activities <- read.table("./UCI HAR Dataset/activity_labels.txt")

# Merging test, training and subjects for final data tables

final_x <- rbind(x_test, x_train)
final_y <- rbind(y_test, y_train)
final_subj <- rbind(subject_test, subject_train)

# Measurements only for mean and standard deviation

measurements <- variables[grep("mean\\(\\)|std\\(\\)",variables[,2]),]
final_x <- final_x[,measurements[,1]]

# Naming the columns

colnames(final_x)   <- measurements[,2]
colnames(final_y)   <- "activity"
colnames(final_subj) <- "subject"

# Final data set with activities and subjects as factors

finalset <- cbind(final_subj, final_y, final_x)
finalset$activity <- factor(finalset$activity, levels = activities[,1], labels = activities[,2]) 
finalset$subject  <- as.factor(finalset$subject) 

# Summary data set and export

tidyset <- finalset %>% group_by(activity, subject) %>% summarize_all(funs(mean))
write.table(tidyset, file = "./tidydata.txt", row.names = FALSE, col.names = TRUE) 

