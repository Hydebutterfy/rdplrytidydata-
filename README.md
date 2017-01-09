# rdplrytidydata-
#Our University, the web is not good enough to download the files through R(always with curl problems).
# I download the files from websites and save it in the boot file.
#1，load the data into R and merges the training and the test sets to create one data set.
test_x<-read.table("./test/X_test.txt")
View(test_x)
test_y<-read.table("./test/y_test.txt")
View(test_y)
test_subject<-read.table("./test/subject_test.txt")
View(test_subject)
testdata<-cbind(test_subject,test_y,test_x)
View(testdata)#all the testdata

train_subject<-read.table("./train/subject_train.txt")
View(train_subject)
train_x<-read.table("./train/X_train.txt")
View(train_x)
train_y<-read.table("./train/y_train.txt")
View(train_y)
traindata<-cbind(train_subject,train_y,train_x) 
View(traindata) #dall the traindata

alldata<-rbind(traindata, testdata)#merge the test and train data

#Get the name of vars from feature file and name the vars.
featuresdata<-read.table("features.txt",stringsAsFactors = FALSE)[,2] #the second var is the feature
temp<-c("subject","activity")
featuresdata<-c(temp,featuresdata)
names(alldata)<-featuresdata
remove(temp)
names(alldata) #check the result

# Extracts only the measurements on the mean and standard deviation for each measurement
featuresposition<-grep(("mean\\(\\)|std\\(\\)"), featuresdata)  #get the position of "mean() and std()",use escape character"\\" to mean "(" and ")",
selectdata<-alldata[,c(1,2,featuresposition)] #store the mean and std data in selectdata.

#Uses descriptive activity names to name the activities in the data set
activityname<-read.table("activity_labels.txt") #read the activity name file.
selectdata$activity<-factor(selectdata$activity, levels = activityname[,1], labels = activityname[,2])  #replace the activity name.

#Appropriately labels the data set with descriptive variable names. 
names(selectdata) <- sub("\\()", "", names(selectdata)) #remove the "()" from var name. "()" only have one, so i use sub.
names(selectdata) <- gsub("-", "", names(selectdata))  #remove all "-" from var name. 
names(selectdata) <- sub("^t", "time", names(selectdata)) #replace the "t" by "time".
names(selectdata) <- sub("^f", "frequence", names(selectdata)) #replace the "f" by "frequence".
tolower(names(selectdata)) #all the letter are lower.

#From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.
library(dplyr)               
meandata<-selectdata%>%                    
    group_by(subject,activity) %>%     #group the data by activity and subject.
    summarise_each(funs(mean))          

write.csv(meandata, "meandata.csv")  
