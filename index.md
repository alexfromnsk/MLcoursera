---
title: "Untitled"
output: html_document
---

Hello, guys!
First thing what I did I’ve loaded datasets and libraries (sorry I commented all R code to prevent it running then knitting HTML)


```{r}
#library(caret)
#library(Hmisc)
#library(ggplot2)
#library(plyr)
#library(randomForest)
#load training set file
#trdataAll <-read.csv("./pml-training.csv")
#load testing set file
#tedataAll <-read.csv("./pml-testing.csv")

```

When I have plotted all the variables versus “classe” and made 2 conclusions:
1.  I should leave only variables containing raw data from sensors plus user_name and classe
2.	Regression models should not be an option because a) it is classification, not regression; b) on plots variables versus “classe” seems to be wide distribution of outcomes (here is example below)


```{r, echo=FALSE}
#qplot(trdata[,"classe"],trdata[,"pitch_dumbbell"])
```


```{r}
#manually selected variables containing raw data from sensors plus user_name and classe
#goodVars <-c(2,8,9,10,11,37,38,39,40,41,42,43,44,45,46,47,48,61,62,63,64,65,66,67,68,84,85,86,113,114,115,116,117,118,119,120,121,122,123,124,152,153,154,155,156,157,158,159,160)
```

colnames(trdata)

[1] "user_name"         "roll_belt"         "pitch_belt"        "yaw_belt"         
 [5] "total_accel_belt"  "gyros_belt_x"      "gyros_belt_y"      "gyros_belt_z"     
 [9] "accel_belt_x"      "accel_belt_y"      "accel_belt_z"      "magnet_belt_x"    
[13] "magnet_belt_y"     "magnet_belt_z"     "roll_arm"          "pitch_arm"        
[17] "yaw_arm"           "gyros_arm_y"       "gyros_arm_z"       "accel_arm_x"      
[21] "accel_arm_y"       "accel_arm_z"       "magnet_arm_x"      "magnet_arm_y"     
[25] "magnet_arm_z"      "roll_dumbbell"     "pitch_dumbbell"    "yaw_dumbbell"     
[29] "gyros_dumbbell_x"  "gyros_dumbbell_y"  "gyros_dumbbell_z"  "accel_dumbbell_x" 
[33] "accel_dumbbell_y"  "accel_dumbbell_z"  "magnet_dumbbell_x" "magnet_dumbbell_y"
[37] "magnet_dumbbell_z" "roll_forearm"      "pitch_forearm"     "yaw_forearm"      
[41] "gyros_forearm_y"   "gyros_forearm_z"   "accel_forearm_x"   "accel_forearm_y"  
[45] "accel_forearm_z"   "magnet_forearm_x"  "magnet_forearm_y"  "magnet_forearm_z" 
[49] "classe"            "id"    

```{r}
#trdata <-trdataAll[,goodVars] #training data set
#tedata <-tedataAll[,goodVars] #testing data set
```

I saw a Random Forest method lecture and decided to try it, by the way I’ve used k-fold cross-validation to measure approximate out-of-sample error, here is code below. I made 10 subsets from training data file set. I used RandomForest function from RandomForest package instead of train from caret package, because RandomForest is MUCH faster. I you run the code you can see a fancy progress bar ;)

```{r}
# I will use k-fold cross-validation
#k = 10 #Folds
# sample from 1 to k, nrow times (the number of observations in the data)
#trdata$id <- sample(1:k, nrow(trdata), replace = TRUE)
#list <- 1:k
# prediction and testset data frames that we add to with each iteration over
# the folds
#prediction <- data.frame()
#testsetCopy <- data.frame()
#Creating a progress bar to know the status of CV
#progress.bar <- create_progress_bar("text")
#progress.bar$init(k)
#for (i in 1:k){
  # remove rows with id i from dataframe to create training set
  # select rows with id i to create test set
  #trainingset_2 <- subset(trdata, id %in% list[-i])
  #testset_2 <- subset(trdata, id %in% c(i))
  # run a random forest model
  #mymodel <- randomForest(data=trainingset_2,x=trainingset_2[,-c(49,50)],y=trainingset_2[,49],data = trainingset_2, ntree = 100)
  #temp <- as.data.frame(predict(mymodel, testset_2))
  #append this iteration's predictions to the end of the prediction data frame
  #prediction <- rbind(prediction, temp)
  #append this iteration's test set to the test set copy data frame
  #keep only the classe column
  #testsetCopy <- rbind(testsetCopy, as.data.frame(testset_2[,49]))
#  progress.bar$step()
#}
# add predictions and actual classe values
#result <- cbind(prediction, testsetCopy[,1])
#names(result) <- c("Predicted", "Actual")
#confusionMatrix(result[,1],result[,2])
```

Confusion Matrix and Statistics

          Reference
Prediction    A    B    C    D    E
         A 5578   16    0    0    0
         B    1 3777   14    0    0
         C    0    4 3405   20    3
         D    0    0    3 3193   11
         E    1    0    0    3 3593

Overall Statistics
                                          
               Accuracy : 0.9961          
                 95% CI : (0.9952, 0.9969)
    No Information Rate : 0.2844          
    P-Value [Acc > NIR] : < 2.2e-16       
                                          
                  Kappa : 0.9951          
 Mcnemar's Test P-Value : NA              

Statistics by Class:

                     Class: A Class: B Class: C Class: D Class: E
Sensitivity            0.9996   0.9947   0.9950   0.9928   0.9961
Specificity            0.9989   0.9991   0.9983   0.9991   0.9998
Pos Pred Value         0.9971   0.9960   0.9921   0.9956   0.9989
Neg Pred Value         0.9999   0.9987   0.9989   0.9986   0.9991
Prevalence             0.2844   0.1935   0.1744   0.1639   0.1838
Detection Rate         0.2843   0.1925   0.1735   0.1627   0.1831
Detection Prevalence   0.2851   0.1933   0.1749   0.1634   0.1833
Balanced Accuracy      0.9993   0.9969   0.9967   0.9960   0.9979

So as we see, Accuracy >99,5% and because we used cross-validation, it means this accuracy is not a result of over-fitting.  Accuracy was very good for me, so I decided to stick with RainForest method (and because RainForest function was worked very fast, and I didn’t want to spend several hours to try every method via caret train function).
If we don’t use cross-validation and just use 10% of samples for testing and 90% for training we will have approximate same results

```{r}
#inTrain = createDataPartition(trdata$classe, p = 9/10)[[1]]
#trdata_tr = trdata[ inTrain,]
#trdata_te = trdata[-inTrain,]
#modFit <- randomForest(data=trdata_tr,x=trdata_tr[,-c(49)],y=trdata_tr[,49],ntree = 100)
#confusionMatrix(trdata_te$classe,predict(modFit,trdata_te))
```

Confusion Matrix and Statistics

          Reference
Prediction   A   B   C   D   E
         A 558   0   0   0   0
         B   3 376   0   0   0
         C   0   1 341   0   0
         D   0   0   3 318   0
         E   0   0   1   0 359

Overall Statistics
                                         
               Accuracy : 0.9959         
                 95% CI : (0.992, 0.9982)
    No Information Rate : 0.2862         
    P-Value [Acc > NIR] : < 2.2e-16      
                                         
                  Kappa : 0.9948         
 Mcnemar's Test P-Value : NA             

Statistics by Class:

                     Class: A Class: B Class: C Class: D Class: E
Sensitivity            0.9947   0.9973   0.9884   1.0000   1.0000
Specificity            1.0000   0.9981   0.9994   0.9982   0.9994
Pos Pred Value         1.0000   0.9921   0.9971   0.9907   0.9972
Neg Pred Value         0.9979   0.9994   0.9975   1.0000   1.0000
Prevalence             0.2862   0.1923   0.1760   0.1622   0.1832
Detection Rate         0.2847   0.1918   0.1740   0.1622   0.1832
Detection Prevalence   0.2847   0.1934   0.1745   0.1638   0.1837
Balanced Accuracy      0.9973   0.9977   0.9939   0.9991   0.9997

I made prediction for 20 test variables using all samples from training data sets (as I mentioned before, I left only raw data from sensors and user_name and classe).
```{r}
#modFit <- randomForest(data=trdata,x=trdata[,-c(“classe”)],y=trdata[,”classe”],ntree = 100)
#predict(modFit,tedata)
```

```{r}
#(here is the answers vector)
```
So I also used RF model with cross-validation for 20 testing samples and got the same result, code below
```{r}
#train_control <- trainControl(allowParallel=TRUE,"method="cv",number = 5)
#modFit2 <- train(classe ~ ., method = "rf",data=trdata,trControl=train_control,ntree = 100)
#predict(modFit2,tedata)
#(here is exactly the same answers vector)
```
