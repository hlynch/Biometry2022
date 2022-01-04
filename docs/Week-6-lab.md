Week 6 Lab
=============
  
Today we will do a short exercise to illustrate the permutation method of dealing with multiple comparisons.

First, we will simulate 10 groups of data (say, heights) from the *same* distribution using the normal distribution and put the data for each group in a list for easy access:


```r
data.all<-list()
for (i in 1:10)
  {
    data.all[[i]]<-rnorm(10)  #Note the double brackets for a list
  }
```

Now we can compare each group pairwise using a t.test.


```r
p.values<-matrix(ncol=10,nrow=10)
for (i in 1:9)
  {
    for (j in (i+1):10)
      {
        p.values[i,j]<-t.test(data.all[[i]],data.all[[j]])$p.value 
      }
  }
p.values
```

```
##       [,1]      [,2]      [,3]      [,4]      [,5]       [,6]      [,7]
##  [1,]   NA 0.5153206 0.9939040 0.1478703 0.5194742 0.57709862 0.8141473
##  [2,]   NA        NA 0.5655032 0.4634849 0.9923048 0.27168776 0.7636243
##  [3,]   NA        NA        NA 0.2215343 0.5697407 0.61900872 0.8256193
##  [4,]   NA        NA        NA        NA 0.4539530 0.08275705 0.3749450
##  [5,]   NA        NA        NA        NA        NA 0.27356026 0.7689029
##  [6,]   NA        NA        NA        NA        NA         NA 0.4908684
##  [7,]   NA        NA        NA        NA        NA         NA        NA
##  [8,]   NA        NA        NA        NA        NA         NA        NA
##  [9,]   NA        NA        NA        NA        NA         NA        NA
## [10,]   NA        NA        NA        NA        NA         NA        NA
##            [,8]       [,9]      [,10]
##  [1,] 0.3835782 0.16126982 0.01513494
##  [2,] 0.7572296 0.34298359 0.07026922
##  [3,] 0.4289664 0.19018161 0.04107095
##  [4,] 0.7845471 0.59841648 0.14930895
##  [5,] 0.7500310 0.33838387 0.06687942
##  [6,] 0.2085029 0.09016855 0.01402848
##  [7,] 0.5916463 0.27996435 0.09274334
##  [8,]        NA 0.51970382 0.23411681
##  [9,]        NA         NA 0.82388316
## [10,]        NA         NA         NA
```

Now we can see how many of these p.values are "significant". We know these are false positives, because all the data were generated from the same distribution.


```r
false.positives<-sum(p.values<0.05,na.rm=T)
false.positives
```

```
## [1] 3
```

We could correct this using the Bonferonni method:


```r
k<-45
new.threshold.B<-0.05/k
new.threshold.B
```

```
## [1] 0.001111111
```

```r
false.positives.B<-sum(p.values<new.threshold.B,na.rm=T)
false.positives.B
```

```
## [1] 0
```

We could correct this using the Dunn-Sidak method:


```r
k<-45
new.threshold.DS<-1-((1-0.05)^(1/k))
new.threshold.DS
```

```
## [1] 0.001139202
```

```r
false.positives.DS<-sum(p.values<new.threshold.DS,na.rm=T)
false.positives.DS
```

```
## [1] 0
```

We could correct this using the randomization method. This requires simulating data under the null hypothesis to generate a null distribution of p-values.



```r
p.values.all<-c()
min.p.values.all<-c()
for (k in 1:1000)
  {
    data.null<-list()
    for (i in 1:10)
      {
        data.null[[i]]<-rnorm(10)  #Note the double brackets for a list
      }
    p.values.null<-matrix(ncol=10,nrow=10)
    for (i in 1:9)
      {
        for (j in (i+1):10)
          {
            p.values.null[i,j]<-t.test(data.null[[i]],data.null[[j]])$p.value 
          }
      }
    p.values.all<-c(p.values.all,c(p.values.null)[!is.na(c(p.values.null))])
    min.p.values.all<-c(min.p.values.all,min(c(p.values.null)[!is.na(c(p.values.null))]))
  }
new.threshold.R<-quantile(min.p.values.all,probs=c(0.05))
new.threshold.R
```

```
##          5% 
## 0.001756964
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
