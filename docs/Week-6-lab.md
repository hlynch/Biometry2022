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
##       [,1]      [,2]      [,3]      [,4]      [,5]      [,6]      [,7]
##  [1,]   NA 0.5792809 0.4238268 0.6280202 0.9767637 0.1226788 0.2025554
##  [2,]   NA        NA 0.2402976 0.3973756 0.6074117 0.0757772 0.1223536
##  [3,]   NA        NA        NA 0.9710173 0.5324963 0.2879757 0.5052336
##  [4,]   NA        NA        NA        NA 0.6738338 0.3854254 0.6088176
##  [5,]   NA        NA        NA        NA        NA 0.1633283 0.2752026
##  [6,]   NA        NA        NA        NA        NA        NA 0.6355353
##  [7,]   NA        NA        NA        NA        NA        NA        NA
##  [8,]   NA        NA        NA        NA        NA        NA        NA
##  [9,]   NA        NA        NA        NA        NA        NA        NA
## [10,]   NA        NA        NA        NA        NA        NA        NA
##             [,8]      [,9]     [,10]
##  [1,] 0.67237712 0.8747356 0.9157011
##  [2,] 0.90945662 0.5212164 0.7262396
##  [3,] 0.29566938 0.5879169 0.5015070
##  [4,] 0.45415306 0.7298257 0.6227636
##  [5,] 0.69093625 0.9135793 0.9059634
##  [6,] 0.09218886 0.1751726 0.1652714
##  [7,] 0.15088938 0.2975850 0.2738253
##  [8,]         NA 0.6028019 0.8067103
##  [9,]         NA        NA 0.8264529
## [10,]         NA        NA        NA
```

Now we can see how many of these p.values are "significant". We know these are false positives, because all the data were generated from the same distribution.


```r
false.positives<-sum(p.values<0.05,na.rm=T)
false.positives
```

```
## [1] 0
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
## 0.002413693
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
