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
##       [,1]       [,2]      [,3]       [,4]      [,5]       [,6]      [,7]
##  [1,]   NA 0.06878694 0.4057293 0.52706898 0.4426618 0.88160841 0.5042449
##  [2,]   NA         NA 0.2711591 0.03285814 0.1122694 0.09209158 0.2364272
##  [3,]   NA         NA        NA 0.18119733 0.7860098 0.49637309 0.8900237
##  [4,]   NA         NA        NA         NA 0.1857589 0.44797688 0.2331979
##  [5,]   NA         NA        NA         NA        NA 0.56025830 0.9333854
##  [6,]   NA         NA        NA         NA        NA         NA 0.6020288
##  [7,]   NA         NA        NA         NA        NA         NA        NA
##  [8,]   NA         NA        NA         NA        NA         NA        NA
##  [9,]   NA         NA        NA         NA        NA         NA        NA
## [10,]   NA         NA        NA         NA        NA         NA        NA
##             [,8]      [,9]      [,10]
##  [1,] 0.67119560 0.7051684 0.97875521
##  [2,] 0.03309759 0.2371258 0.05376844
##  [3,] 0.22138634 0.7559897 0.38344022
##  [4,] 0.80452356 0.3736060 0.49137270
##  [5,] 0.22007337 0.8748882 0.40283069
##  [6,] 0.57084378 0.8001670 0.89375538
##  [7,] 0.29075186 0.8495729 0.48961569
##  [8,]         NA 0.4671886 0.63181818
##  [9,]         NA        NA 0.70718063
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
## 0.001923822
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
