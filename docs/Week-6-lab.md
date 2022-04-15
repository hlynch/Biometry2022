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
##       [,1]       [,2]      [,3]       [,4]       [,5]      [,6]      [,7]
##  [1,]   NA 0.04073783 0.4020104 0.08034377 0.78977321 0.9520483 0.4164154
##  [2,]   NA         NA 0.1571741 0.96749207 0.08139273 0.1235305 0.1733970
##  [3,]   NA         NA        NA 0.24089579 0.60155127 0.5772532 0.9943736
##  [4,]   NA         NA        NA         NA 0.13415691 0.1640966 0.2545712
##  [5,]   NA         NA        NA         NA         NA 0.8837730 0.6097563
##  [6,]   NA         NA        NA         NA         NA        NA 0.5816691
##  [7,]   NA         NA        NA         NA         NA        NA        NA
##  [8,]   NA         NA        NA         NA         NA        NA        NA
##  [9,]   NA         NA        NA         NA         NA        NA        NA
## [10,]   NA         NA        NA         NA         NA        NA        NA
##            [,8]      [,9]     [,10]
##  [1,] 0.7313637 0.8437957 0.1029354
##  [2,] 0.2069737 0.1088271 0.4890611
##  [3,] 0.7961823 0.6170329 0.3869188
##  [4,] 0.2568289 0.1571351 0.5864837
##  [5,] 0.8937183 0.9693561 0.1983524
##  [6,] 0.8105724 0.9170807 0.2538539
##  [7,] 0.7963822 0.6224749 0.4125056
##  [8,]        NA 0.8759004 0.4004341
##  [9,]        NA        NA 0.2427058
## [10,]        NA        NA        NA
```

Now we can see how many of these p.values are "significant". We know these are false positives, because all the data were generated from the same distribution.


```r
false.positives<-sum(p.values<0.05,na.rm=T)
false.positives
```

```
## [1] 1
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
## 0.001646717
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
