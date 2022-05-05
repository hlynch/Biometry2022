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
##       [,1]      [,2]      [,3]       [,4]       [,5]       [,6]      [,7]
##  [1,]   NA 0.9200059 0.9006884 0.13862947 0.77282885 0.44284078 0.7004112
##  [2,]   NA        NA 0.9704097 0.09382588 0.82490886 0.44416273 0.6157706
##  [3,]   NA        NA        NA 0.11049465 0.86499563 0.52144400 0.6189214
##  [4,]   NA        NA        NA         NA 0.09023983 0.02884206 0.3142340
##  [5,]   NA        NA        NA         NA         NA 0.66731957 0.5270193
##  [6,]   NA        NA        NA         NA         NA         NA 0.2863827
##  [7,]   NA        NA        NA         NA         NA         NA        NA
##  [8,]   NA        NA        NA         NA         NA         NA        NA
##  [9,]   NA        NA        NA         NA         NA         NA        NA
## [10,]   NA        NA        NA         NA         NA         NA        NA
##             [,8]      [,9]      [,10]
##  [1,] 0.48760382 0.9803986 0.12229780
##  [2,] 0.33590817 0.8950066 0.07202044
##  [3,] 0.39034960 0.8778186 0.09369912
##  [4,] 0.23574772 0.1346071 0.92103802
##  [5,] 0.31093130 0.7479404 0.07605956
##  [6,] 0.07006911 0.4090112 0.01724361
##  [7,] 0.89808895 0.7093737 0.31618693
##  [8,]         NA 0.4837739 0.20331343
##  [9,]         NA        NA 0.11612532
## [10,]         NA        NA         NA
```

Now we can see how many of these p.values are "significant". We know these are false positives, because all the data were generated from the same distribution.


```r
false.positives<-sum(p.values<0.05,na.rm=T)
false.positives
```

```
## [1] 2
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
## 0.001519297
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
