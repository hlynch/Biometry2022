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
##  [1,]   NA 0.6418972 0.1159721 0.3260638 0.82602953 0.9664994 0.2375949
##  [2,]   NA        NA 0.3438722 0.7408236 0.70881343 0.6993916 0.5248781
##  [3,]   NA        NA        NA 0.3261552 0.05277816 0.1799052 0.7706303
##  [4,]   NA        NA        NA        NA 0.21058431 0.4264649 0.6248893
##  [5,]   NA        NA        NA        NA         NA 0.8914612 0.1974568
##  [6,]   NA        NA        NA        NA         NA        NA 0.3063137
##  [7,]   NA        NA        NA        NA         NA        NA        NA
##  [8,]   NA        NA        NA        NA         NA        NA        NA
##  [9,]   NA        NA        NA        NA         NA        NA        NA
## [10,]   NA        NA        NA        NA         NA        NA        NA
##             [,8]       [,9]      [,10]
##  [1,] 0.89487357 0.77174176 0.81983422
##  [2,] 0.55465002 0.46992957 0.72121524
##  [3,] 0.08354621 0.06919261 0.06101185
##  [4,] 0.24717649 0.19978617 0.24039583
##  [5,] 0.69386553 0.56611443 0.98391810
##  [6,] 0.87191022 0.76125245 0.88468088
##  [7,] 0.18618506 0.15255767 0.20986577
##  [8,]         NA 0.86924438 0.69057327
##  [9,]         NA         NA 0.56511254
## [10,]         NA         NA         NA
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
##         5% 
## 0.00165648
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
