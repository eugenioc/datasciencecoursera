```r
path <- "C:\\Users\\eugenio\\Desktop\\r working\\UCI HAR Dataset"
tr_x_data <- as.data.frame(read.table(paste(path,"\\train\\X_train.txt",sep=""),header=TRUE,fill=TRUE))
tr_names_x_data <- as.data.frame(read.table(paste(path,"\\features.txt",sep=""),header=TRUE,fill=TRUE))
colnames(tr_x_data) <- tr_names_x_data[,2] 
tr_y_data <- as.data.frame(read.table(paste(path,"\\train\\y_train.txt",sep=""),header=TRUE,fill=TRUE))
colnames(tr_y_data) <- "f_activity"
tr_s_data <- as.data.frame(read.table(paste(path,"\\train\\subject_train.txt",sep=""),header=TRUE,fill=TRUE))
colnames(tr_s_data) <- "f_subject"
merge_train <- cbind(tr_s_data,tr_y_data,tr_x_data)
tx_x_data <- as.data.frame(read.table(paste(path,"\\test\\X_test.txt",sep=""),header=TRUE,fill=TRUE))
colnames(tx_x_data) <- tr_names_x_data[,2] 
tx_y_data <- as.data.frame(read.table(paste(path,"\\test\\y_test.txt",sep=""),header=TRUE,fill=TRUE))
colnames(tx_y_data) <- "f_activity"
tx_s_data <- as.data.frame(read.table(paste(path,"\\test\\subject_test.txt",sep=""),header=TRUE,fill=TRUE))
colnames(tx_s_data) <- "f_subject"
merge_test <- cbind(tx_s_data,tx_y_data,tx_x_data)
final_merge <- rbind(merge_train,merge_test)
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
allowed_col <- rbind(tr_names_x_data[grepl("f_activity",tr_names_x_data[,2]),],tr_names_x_data[grepl("f_subject",tr_names_x_data[,2]),],tr_names_x_data[grepl("mean",tr_names_x_data[,2]),],tr_names_x_data[grepl("std",tr_names_x_data[,2]),])
val <- c(1,2,as.numeric(allowed_col[,1])+2)
filtered_final_merge <- final_merge[,val]
library("reshape2")
library("plyr")
fdata <- melt(filtered_final_merge)
fdata  <- ddply(fdata , .(f_activity, variable), function(fdata ) mean(fdata$value))
colnames(fdata) <- c("Activity", "Var_type", "MeanValue")
write.table(fdata,file=paste(path,"\\result.txt",sep="" ),row.name=FALSE)
print("end of script")
```
