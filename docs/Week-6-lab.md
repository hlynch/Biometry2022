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
##       [,1]      [,2]      [,3]      [,4]       [,5]      [,6]      [,7]
##  [1,]   NA 0.9406149 0.4351477 0.7960240 0.54575948 0.7364057 0.5999490
##  [2,]   NA        NA 0.2502671 0.8113666 0.48573383 0.7363334 0.5680734
##  [3,]   NA        NA        NA 0.2482515 0.08619166 0.2057950 0.1375174
##  [4,]   NA        NA        NA        NA 0.72565537 0.9361246 0.7782945
##  [5,]   NA        NA        NA        NA         NA 0.7880883 0.9632680
##  [6,]   NA        NA        NA        NA         NA        NA 0.8379604
##  [7,]   NA        NA        NA        NA         NA        NA        NA
##  [8,]   NA        NA        NA        NA         NA        NA        NA
##  [9,]   NA        NA        NA        NA         NA        NA        NA
## [10,]   NA        NA        NA        NA         NA        NA        NA
##            [,8]      [,9]       [,10]
##  [1,] 0.8471902 0.8991924 0.228952885
##  [2,] 0.8736113 0.9435047 0.095177887
##  [3,] 0.2151378 0.2271217 0.005480208
##  [4,] 0.9203516 0.8568139 0.316758029
##  [5,] 0.6042402 0.5301748 0.464957508
##  [6,] 0.8478147 0.7818629 0.353855083
##  [7,] 0.6757848 0.6099180 0.500016189
##  [8,]        NA 0.9275867 0.169640633
##  [9,]        NA        NA 0.115542580
## [10,]        NA        NA          NA
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
## 0.001554852
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
