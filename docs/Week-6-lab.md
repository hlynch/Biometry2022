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
##       [,1]      [,2]      [,3]      [,4]       [,5]       [,6]      [,7]
##  [1,]   NA 0.9218466 0.6634103 0.8712935 0.06740934 0.97086857 0.9821525
##  [2,]   NA        NA 0.7020439 0.7601245 0.05370724 0.87176411 0.9192840
##  [3,]   NA        NA        NA 0.4818362 0.11179779 0.57153781 0.7117930
##  [4,]   NA        NA        NA        NA 0.02431630 0.87508625 0.9196251
##  [5,]   NA        NA        NA        NA         NA 0.03156086 0.1299791
##  [6,]   NA        NA        NA        NA         NA         NA 0.9970850
##  [7,]   NA        NA        NA        NA         NA         NA        NA
##  [8,]   NA        NA        NA        NA         NA         NA        NA
##  [9,]   NA        NA        NA        NA         NA         NA        NA
## [10,]   NA        NA        NA        NA         NA         NA        NA
##            [,8]       [,9]      [,10]
##  [1,] 0.3623486 0.84018380 0.32369354
##  [2,] 0.2850561 0.90954710 0.31504954
##  [3,] 0.1785295 0.76669811 0.53172962
##  [4,] 0.3763550 0.65145738 0.17850117
##  [5,] 0.0136854 0.05307818 0.30587937
##  [6,] 0.3203449 0.76609740 0.22305220
##  [7,] 0.4539991 0.85658456 0.41693915
##  [8,]        NA 0.23352415 0.07232281
##  [9,]        NA         NA 0.33653457
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
## 0.001675708
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
