Prediction Report
========================================================

This is an R Markdown document for the **Data Science/Practical Machine Learning** assignment write-up. The purpose of this assignment is to predict the class label for 20 testing instance based on a nearly 11MB training set collected by hand-on psychical devises. Training data and Testing data are all provided in csv which can be read into R for the purpose of training classification models. 

- 1. Read in the data from pml-training excel, let R to auto fit the attribute types


```r
ndata <- read.csv("D:\\Machine Learning\\R\\pml-training.csv")
```

- 2. By checking the pml-training.csv in Excel, you can find there're actually many NA values inside the data set. Therefore, I checked the proportion of such NA values in a certain attribute max_roll_belt. To make sure about the NA proportion, I also check the  proportion for several other attributes like max_picth_belt, avg_roll_belt and avg_roll_arm. By looking at the histogram below, you can find the NA values occupy for only 20% in the max_roll_belt, and that's the same for all the other attributes. Such small proportion is not significant enough to delete such attributes.

```r
ndata$max_roll_belt<-as.numeric(ndata$max_roll_belt)
hist(ndata$max_roll_belt, main='', xlab='max_roll_belt')
```

![plot of chunk NA hist](figure/NA hist.png) 
- 3. Check the relationship between the mentioned NA attributes with one factor column: new_window, and plot using codeslike below. Then we can find the NA values only occurs when new_window equals to 'no', for all attributes contains NA value.  

```r
{
  library("ggplot2")
qplot(ndata$new_window,ndata$max_roll_belt, color=ndata$classe)
##qplot(data$new_window,data$max_picth_belt, color=data$classe)
##qplot(data$new_window,data$avg_roll_belt, color=data$classe)
##qplot(data$new_window,data$stddev_yaw_arm, color=data$classe)
}
```

```
## Warning: Removed 19216 rows containing missing values (geom_point).
```

![plot of chunk na-factor relationship](figure/na-factor relationship.png) 
- 4. Go back to check the test data set, and it is apparent that the new_window equals to 'no' for the 20 predicting instance. Therefore, it is fair enough to delete all the NA attributes now for the purpose of predicting the 20 instance.Also, the time stamp are not relevant to the prediction on the classe, and the names are another factor attributes which seems not helpful in building models using other numeric attributes, so also delete them here.

```r
{
  tdata<-ndata[,-c(1,2,3,4,5,6,12,13,14,15,16,  17,18,	19,	20,	21,	22,	23,	24,	25,	26,	27,	28,	29,	30,	31,	32,	33,	34,	35,	36,	50,	51,	52,	53,	54,	55,	56,	57,	58,	59,	69,	70,	71,	72,	73,	74,	75,	76,	77,	78,	79,	80,	81,	82,	83,	87,	88,	89,	90,	91,	92,	93,	94,	95,	96,	97,	98,	99,	100,	101,	103,	104,	105,	106,	107,	108,	109,	110,	111,	112,	125,	126,	127,	128,	129,	130,	131,	132,	133,	134,	135,	136,	137,	138,	139,	141,	142,	143,	144,	145,	146,	147,	148,	149,	150)]
## I used Excel to pick up the index for such attributes, R loops with if conditions make be a easier approach.
}
```
- 5. Check to see whether we still have near zero attributes within the retained data set tdata. Based on the csv result, we have eliminated near zero attributes in the tdata so far.

```r
{
library("caret")
nsv<-nearZeroVar(tdata, saveMetrics=TRUE)
nsv
}
```

```
## Warning: package 'caret' was built under R version 3.1.1
```

```
## Loading required package: lattice
```

```
##                      freqRatio percentUnique zeroVar   nzv
## num_window               1.000       4.37264   FALSE FALSE
## roll_belt                1.102       6.77811   FALSE FALSE
## pitch_belt               1.036       9.37723   FALSE FALSE
## yaw_belt                 1.058       9.97350   FALSE FALSE
## total_accel_belt         1.063       0.14779   FALSE FALSE
## gyros_belt_x             1.059       0.71348   FALSE FALSE
## gyros_belt_y             1.144       0.35165   FALSE FALSE
## gyros_belt_z             1.066       0.86128   FALSE FALSE
## accel_belt_x             1.055       0.83580   FALSE FALSE
## accel_belt_y             1.114       0.72877   FALSE FALSE
## accel_belt_z             1.079       1.52380   FALSE FALSE
## magnet_belt_x            1.090       1.66650   FALSE FALSE
## magnet_belt_y            1.100       1.51870   FALSE FALSE
## magnet_belt_z            1.006       2.32902   FALSE FALSE
## roll_arm                52.338      13.52563   FALSE FALSE
## pitch_arm               87.256      15.73234   FALSE FALSE
## yaw_arm                 33.029      14.65702   FALSE FALSE
## total_accel_arm          1.025       0.33636   FALSE FALSE
## gyros_arm_x              1.016       3.27693   FALSE FALSE
## gyros_arm_y              1.454       1.91622   FALSE FALSE
## gyros_arm_z              1.111       1.26389   FALSE FALSE
## accel_arm_x              1.017       3.95984   FALSE FALSE
## accel_arm_y              1.140       2.73672   FALSE FALSE
## accel_arm_z              1.128       4.03629   FALSE FALSE
## magnet_arm_x             1.000       6.82397   FALSE FALSE
## magnet_arm_y             1.057       4.44399   FALSE FALSE
## magnet_arm_z             1.036       6.44685   FALSE FALSE
## roll_dumbbell            1.022      83.78351   FALSE FALSE
## pitch_dumbbell           2.277      81.22516   FALSE FALSE
## yaw_dumbbell             1.132      83.14137   FALSE FALSE
## total_accel_dumbbell     1.073       0.21914   FALSE FALSE
## gyros_dumbbell_x         1.003       1.22821   FALSE FALSE
## gyros_dumbbell_y         1.265       1.41678   FALSE FALSE
## gyros_dumbbell_z         1.060       1.04984   FALSE FALSE
## accel_dumbbell_x         1.018       2.16594   FALSE FALSE
## accel_dumbbell_y         1.053       2.37489   FALSE FALSE
## accel_dumbbell_z         1.133       2.08949   FALSE FALSE
## magnet_dumbbell_x        1.098       5.74865   FALSE FALSE
## magnet_dumbbell_y        1.198       4.30129   FALSE FALSE
## magnet_dumbbell_z        1.021       3.44511   FALSE FALSE
## roll_forearm            11.589      11.08959   FALSE FALSE
## pitch_forearm           65.983      14.85577   FALSE FALSE
## yaw_forearm             15.323      10.14677   FALSE FALSE
## total_accel_forearm      1.129       0.35674   FALSE FALSE
## gyros_forearm_x          1.059       1.51870   FALSE FALSE
## gyros_forearm_y          1.037       3.77637   FALSE FALSE
## gyros_forearm_z          1.123       1.56457   FALSE FALSE
## accel_forearm_x          1.126       4.04648   FALSE FALSE
## accel_forearm_y          1.059       5.11161   FALSE FALSE
## accel_forearm_z          1.006       2.95587   FALSE FALSE
## magnet_forearm_x         1.012       7.76679   FALSE FALSE
## magnet_forearm_y         1.247       9.54031   FALSE FALSE
## magnet_forearm_z         1.000       8.57711   FALSE FALSE
## classe                   1.470       0.02548   FALSE FALSE
```
- 6. Set the training/testing partition using the training data set. Note that all the cross validation during model training will be done only on the training partition inside the training data set. The test data partitioned here is only for checking the robust and accuracy for the training model in case the model is over fitting.

```r
{
set.seed(3472)
inTrain<-createDataPartition(y=tdata$classe, p=0.75, list=FALSE)
training<-tdata[inTrain,]
testing<-tdata[-inTrain,]
}
```
- 7. Check whether we should standardize the data inside trialing partition. The purpose of standardization is to avoid weighting to much for some tributes with large values and to cost less on training the model. Based on the picture below, we can see the data points are ranged around -100~100, which is in real need for standardization in reprocessing.

```r
{
  
density<-data.frame(mean, sd)
for (i in 2 : 53)
{ 
  mean<-mean(training[,i])
  sd<-sd(training[,i])
  density<-rbind(density, c(mean, sd))
}
plot(density)
}
```

![plot of chunk check range](figure/check range.png) 
- 8. Start training with preprocessing using methods center, scale to standardize the training data set, use method of PCA to generate the most variant attribute that covers 90% of variety for the whole training attributes . The classification model used is KNN (K nearest neighbors) of which the K will auto fitted by the model during 10 fold cross validation ( using the code trControl). 
     

```r
{
  
preObj<-preProcess(training[,-54], method=c("center", "scale","pca"), thresh=0.9)
predata<-predict(preObj,training[,-54])
knnfit<-train(training$classe~., data=predata, method='knn',tuneLength = 9,trControl = trainControl(method = "cv"))
#it's takes several minutes to generate the model!
#testing

pretest<-predict(preObj, testing[,-54])
confusionMatrix(testing$classe, predict(knnfit, pretest))

}
```

```
## Warning: package 'e1071' was built under R version 3.1.1
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 1359   12   17    7    0
##          B   20  901   25    1    2
##          C    4   13  821   17    0
##          D    1    2   32  767    2
##          E    1    4    4   13  879
## 
## Overall Statistics
##                                         
##                Accuracy : 0.964         
##                  95% CI : (0.958, 0.969)
##     No Information Rate : 0.282         
##     P-Value [Acc > NIR] : < 2e-16       
##                                         
##                   Kappa : 0.954         
##  Mcnemar's Test P-Value : 5.67e-05      
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity             0.981    0.967    0.913    0.953    0.995
## Specificity             0.990    0.988    0.992    0.991    0.995
## Pos Pred Value          0.974    0.949    0.960    0.954    0.976
## Neg Pred Value          0.993    0.992    0.981    0.991    0.999
## Prevalence              0.282    0.190    0.183    0.164    0.180
## Detection Rate          0.277    0.184    0.167    0.156    0.179
## Detection Prevalence    0.284    0.194    0.174    0.164    0.184
## Balanced Accuracy       0.985    0.977    0.952    0.972    0.995
```
- 9. Read in the test data set for the real prediction, here we should apply the same preprocssing approach for the test data as for the training set.

```r
{
  testdata <- read.csv("D:\\Machine Learning\\R\\pml-testing.csv")
udata<-testdata[,-c(1,2,3,4,5,6,12,13,14,15,16,    17,18,  19,  20,	21,	22,	23,	24,	25,	26,	27,	28,	29,	30,	31,	32,	33,	34,	35,	36,	50,	51,	52,	53,	54,	55,	56,	57,	58,	59,	69,	70,	71,	72,	73,	74,	75,	76,	77,	78,	79,	80,	81,	82,	83,	87,	88,	89,	90,	91,	92,	93,	94,	95,	96,	97,	98,	99,	100,	101,	103,	104,	105,	106,	107,	108,	109,	110,	111,	112,	125,	126,	127,	128,	129,	130,	131,	132,	133,	134,	135,	136,	137,	138,	139,	141,	142,	143,	144,	145,	146,	147,	148,	149,	150)]

utest<-predict(preObj, udata[,-54])
class1<-predict(knnfit, utest[,-54])
class1
}
```

```
##  [1] B A A A A E D B A A B C B A E E A B B B
## Levels: A B C D E
```
