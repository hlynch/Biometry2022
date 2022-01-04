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
##       [,1]      [,2]       [,3]      [,4]       [,5]         [,6]        [,7]
##  [1,]   NA 0.6245149 0.09378197 0.8717578 0.44726432 0.0118243483 0.037783055
##  [2,]   NA        NA 0.24258150 0.6484560 0.27071401 0.0070518715 0.021605752
##  [3,]   NA        NA         NA 0.2175260 0.04244082 0.0006795997 0.001889582
##  [4,]   NA        NA         NA        NA 0.71782449 0.1351706947 0.268981357
##  [5,]   NA        NA         NA        NA         NA 0.1291698812 0.317899408
##  [6,]   NA        NA         NA        NA         NA           NA 0.473309628
##  [7,]   NA        NA         NA        NA         NA           NA          NA
##  [8,]   NA        NA         NA        NA         NA           NA          NA
##  [9,]   NA        NA         NA        NA         NA           NA          NA
## [10,]   NA        NA         NA        NA         NA           NA          NA
##             [,8]       [,9]     [,10]
##  [1,] 0.78031906 0.18832024 0.3947608
##  [2,] 0.89321958 0.11081068 0.2484470
##  [3,] 0.24402917 0.01637287 0.0470145
##  [4,] 0.73820934 0.43165512 0.6358728
##  [5,] 0.38044107 0.57864731 0.8686858
##  [6,] 0.02090710 0.38614966 0.2305380
##  [7,] 0.05812501 0.75077062 0.4803460
##  [8,]         NA 0.17453075 0.3377053
##  [9,]         NA         NA 0.7244934
## [10,]         NA         NA        NA
```

Now we can see how many of these p.values are "significant". We know these are false positives, because all the data were generated from the same distribution.


```r
false.positives<-sum(p.values<0.05,na.rm=T)
false.positives
```

```
## [1] 10
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
## [1] 1
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
## [1] 1
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
## 0.002021805
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 2
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
