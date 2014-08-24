```r
path <- "C:\\Users\\eugenio\\Desktop\\r working\\UCI HAR Dataset"

#modify the above line with the path to the UCI HAR Dataset folder on your PC


#read X_train data
tr_x_data <- as.data.frame(read.table(paste(path,"\\train\\X_train.txt",sep=""),header=TRUE,fill=TRUE))

#read features data
tr_names_x_data <- as.data.frame(read.table(paste(path,"\\features.txt",sep=""),header=TRUE,fill=TRUE))

#assign name of the feature to the x_train dataset
colnames(tr_x_data) <- tr_names_x_data[,2] 

#read y_train data
tr_y_data <- as.data.frame(read.table(paste(path,"\\train\\y_train.txt",sep=""),header=TRUE,fill=TRUE))

#assign name of the feature to the y_train dataset
colnames(tr_y_data) <- "f_activity"

#read subject train data
tr_s_data <- as.data.frame(read.table(paste(path,"\\train\\subject_train.txt",sep=""),header=TRUE,fill=TRUE))

#assign name f_subject to the subject data
colnames(tr_s_data) <- "f_subject"

#merge the train data
merge_train <- cbind(tr_s_data,tr_y_data,tr_x_data)



# execute the same steps on the test data--------------

tx_x_data <- as.data.frame(read.table(paste(path,"\\test\\X_test.txt",sep=""),header=TRUE,fill=TRUE))

colnames(tx_x_data) <- tr_names_x_data[,2] 

tx_y_data <- as.data.frame(read.table(paste(path,"\\test\\y_test.txt",sep=""),header=TRUE,fill=TRUE))

colnames(tx_y_data) <- "f_activity"

tx_s_data <- as.data.frame(read.table(paste(path,"\\test\\subject_test.txt",sep=""),header=TRUE,fill=TRUE))

colnames(tx_s_data) <- "f_subject"

merge_test <- cbind(tx_s_data,tx_y_data,tx_x_data)

#------------------------------------------------------

#merge the train and test data

final_merge <- rbind(merge_train,merge_test)

#assign meaningful activity name 

final_merge$f_activity <- ifelse(

	final_merge$f_activity == 1, "WALKING",
	ifelse(final_merge$f_activity == 2,"WALKING_UPSTAIRS",
        ifelse(final_merge$f_activity == 3,"WALKING_DOWNSTAIRS",
	ifelse(final_merge$f_activity == 4,"SITTING",
	ifelse(final_merge$f_activity == 5,"STANDING",
	ifelse(final_merge$f_activity == 5,"LAYING", "NOT CLASSIFIED"
)
)
)
)
)
)

#calculating allowed column (only column containing "mean" and "std" in the name)

allowed_col <- rbind(tr_names_x_data[grepl("f_activity",tr_names_x_data[,2]),],tr_names_x_data[grepl("f_subject",tr_names_x_data[,2]),],tr_names_x_data[grepl("mean",tr_names_x_data[,2]),],tr_names_x_data[grepl("std",tr_names_x_data[,2]),])

val <- c(1,2,as.numeric(allowed_col[,1])+2)

#filtering the data only for the allowed column 

filtered_final_merge <- final_merge[,val]

#load reshape2 and plyr

library("reshape2")
library("plyr")

#melt the data to make it tydy in long format
fdata <- melt(filtered_final_merge)

#grouping the data aggregating on activity and type of feature

fdata  <- ddply(fdata , .(f_activity, variable), function(fdata ) mean(fdata$value))

#assign column names 
colnames(fdata) <- c("Activity", "Var_type", "MeanValue")

#write the tidy dataset to the folder UCI HAR Dataset with name result
write.table(fdata,file=paste(path,"\\result.txt",sep="" ),row.name=FALSE)

print("end of script")


```
