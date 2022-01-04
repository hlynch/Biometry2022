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
##       [,1]      [,2]       [,3]      [,4]      [,5]       [,6]      [,7]
##  [1,]   NA 0.8182887 0.25556554 0.7805247 0.9789965 0.70738587 0.5724571
##  [2,]   NA        NA 0.07495053 0.4851364 0.6587272 0.82203448 0.3068522
##  [3,]   NA        NA         NA 0.2654901 0.1009050 0.08736903 0.4970882
##  [4,]   NA        NA         NA        NA 0.6832988 0.43144779 0.7041918
##  [5,]   NA        NA         NA        NA        NA 0.56473313 0.4256630
##  [6,]   NA        NA         NA        NA        NA         NA 0.2858272
##  [7,]   NA        NA         NA        NA        NA         NA        NA
##  [8,]   NA        NA         NA        NA        NA         NA        NA
##  [9,]   NA        NA         NA        NA        NA         NA        NA
## [10,]   NA        NA         NA        NA        NA         NA        NA
##            [,8]      [,9]     [,10]
##  [1,] 0.6719365 0.6521191 0.8207796
##  [2,] 0.3564963 0.4284936 0.9638386
##  [3,] 0.3276291 0.5075275 0.1571726
##  [4,] 0.8526424 0.8001692 0.5775038
##  [5,] 0.5097557 0.5664201 0.7403260
##  [6,] 0.3339987 0.3804132 0.8927463
##  [7,] 0.8242989 0.9441877 0.4053680
##  [8,]        NA 0.9090752 0.4772377
##  [9,]        NA        NA 0.4905019
## [10,]        NA        NA        NA
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
## 0.001661318
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
