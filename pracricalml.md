---
title: "Practical Machine Learning course work"
output:
  pdf_document: default
---

## Coursera Practical Machine Learning assignment

The goal of the current assignment is to create a working classificator of the weight lifting type (variable "classe").

We start the assignment by loading data. The file is filled with different NA values, like empty cells or errors of previous calculations. We will label them accordingly. 


```r
set.seed(33833) # for consistensy of results
train <- read.csv("pml-training.csv", quote="\"", header=TRUE, na.strings=c("NA","","#DIV/0!"))
```

Leaving out only the columns where there're no NAs

```r
t1 = train[colSums(is.na(train)) == 0]
```

Then we do a quick overview of the columns. We see that the X and timestamps should better be removed: they might add nonexistent continuity to the model. So we remove all variables that might be obsolete. 


```r
t2 = t1[c(2,8:60)]
```

Then we train the model with pre-processing, using random forests. As the amount of calculations is too big for a home laptop, we use only 25% of the set. The accuracy will still be pretty OK. (Initially, I took 10%, but the accuracy was relatively poor, approx. 85%)


```r
t4_25 = t2[sample(nrow(t2),nrow(t2)*0.25),]
```


```r
rf_25 = train(classe ~., data=t4_25, method="rf",importance=TRUE,preProcess="pca")
```

```r
results = confusionMatrix(predict(rf_25,t1),t1$classe)
```

As we know, in random forests, there is no need for cross-validation or a separate test set to get an unbiased estimate of the test set error. It is estimated internally, during the run. 


```r
test <- read.csv("pml-testing.csv", quote="\"", header=TRUE, na.strings=c("NA","","#DIV/0!"))
answers = predict(rf_25, test) # this is to be uploaded to Coursera
```

Model accuracy on train data is 0.945. The model accuracy on test set (while uploading to Coursera) was 90%. 
