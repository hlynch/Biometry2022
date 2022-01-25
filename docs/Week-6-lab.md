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
##       [,1]      [,2]      [,3]      [,4]      [,5]      [,6]       [,7]
##  [1,]   NA 0.9474443 0.9219740 0.8365646 0.5984380 0.3749043 0.44625241
##  [2,]   NA        NA 0.8737293 0.7790921 0.6522472 0.4196980 0.41432867
##  [3,]   NA        NA        NA 0.9400754 0.5524951 0.3522945 0.53538606
##  [4,]   NA        NA        NA        NA 0.3903260 0.1963134 0.47843060
##  [5,]   NA        NA        NA        NA        NA 0.7009385 0.19065568
##  [6,]   NA        NA        NA        NA        NA        NA 0.09820267
##  [7,]   NA        NA        NA        NA        NA        NA         NA
##  [8,]   NA        NA        NA        NA        NA        NA         NA
##  [9,]   NA        NA        NA        NA        NA        NA         NA
## [10,]   NA        NA        NA        NA        NA        NA         NA
##            [,8]       [,9]       [,10]
##  [1,] 0.8152184 0.14697715 0.144508907
##  [2,] 0.7569094 0.13535355 0.172580979
##  [3,] 0.9235604 0.21178833 0.146799723
##  [4,] 0.9774473 0.10855226 0.035314293
##  [5,] 0.3614327 0.03950052 0.331570372
##  [6,] 0.1741011 0.01593490 0.577514108
##  [7,] 0.4759409 0.51499608 0.024905121
##  [8,]        NA 0.09798461 0.025813192
##  [9,]        NA         NA 0.001735497
## [10,]        NA         NA          NA
```

Now we can see how many of these p.values are "significant". We know these are false positives, because all the data were generated from the same distribution.


```r
false.positives<-sum(p.values<0.05,na.rm=T)
false.positives
```

```
## [1] 6
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
##         5% 
## 0.00183192
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 1
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
