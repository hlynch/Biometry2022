Week 2 Lab
=============

Confidence intervals
-----------------------

Before getting too far, we need to circle back and make sure we understand what is meant by a confidence interval. 

A 95th percentile confidence interval say “If I repeat this procedure 100 times using 100 different datasets, 95% of the time my confidence intervals will capture the true parameter”. It does NOT say that there is a 95% chance that the parameter is in the interval.

**Quiz time! (Don't worry, not a real quiz)**

*Important note*: This is an area where Aho is **WRONG**. Aho is correct on only one point. It is true that *once the 95th CI has been constructed*, it is no longer possible to assign a $\%$ to the probability that that CI contains the true value or not. Because that CI, once created, either DOES or DOES NOT contain the true value. However, we often talk about the interval in the abstract. **<span style="color: orangered;">When we say "There is a 95$\%$ chance that the interval contains the true value" what we mean is that there is a 95$\%$ probability that a CI created using that methodology would contain the true value.</span>**

Do not let Week 2 pass by without fundamentally understanding the interpretation of a confidence interval. 

Testing hypotheses through permutation
------------------------------------

These examples use data on the speeds of the top 20 racing pigeons from a race in Alma, GA on February 7,2021. 

**Example #1**: Use permutation methods to test whether Cock or Hen birds fly at different speeds (speeds are in meters-per-minute) (in other word: $H_{0}$: No difference in speeds between the C and H groups):

C=$\{1359.8,1355.3,1355.1,1353.0,1349.8,1348.8,1345.2\}$

H=$\{1357.5,1356.4,1355.1,1353.5,1353.2,1352.5,1350.0,1349.8,1346.2,1344.9,1344.4,1343.9,1342.6\}$

**<span style="color: green;">Checkpoint #1: Is this a one-tailed or a two-tailed test?</span>**

Make sure that you understand what is being done here, as this example is very closely related to the problem set.


**Example #2**: Using the same data, provide a 95% confidence interval for the difference in mean speed based on 1000 bootstrap samples

Note that these two approaches are very closely related. Do you see why either approach can be used to test the null hypothesis? **<span style="color: green;">Checkpoint #2: What is the null hypothesis here?</span>**

**Example #3**: Now we will do one slightly more complicated example from Phillip Good's book "Permutation tests: A practical guide to resampling methods and testing hypotheses":

Holmes and Williams (1954) studied tonsil size in children to verify a possible association with the virus \textit{S. pyrogenes}. Test for an association between \textit{S. pyrogenes} status and tonsil size. (Note that you will need to come up with a reasonable test statistic.)

<div class="figure" style="text-align: center">
<img src="Table2categories.png" alt="Data on tonsil size and S. pyrogenes status. Source: Good (1994)" width="40%" />
<p class="caption">(\#fig:unnamed-chunk-1)Data on tonsil size and S. pyrogenes status. Source: Good (1994)</p>
</div>

Now lets consider the full dataset, where tonsil size is divided into three categories. How would we do the test now? **<span style="color: green;">Checkpoint #3: What is the new test statistic? (There are many options.)</span>** What 'labels' do you permute?

<div class="figure" style="text-align: center">
<img src="Table3categories.png" alt="Fill dataset on tonsil size and S. pyrogenes status. Source: Good (1994)" width="50%" />
<p class="caption">(\#fig:unnamed-chunk-2)Fill dataset on tonsil size and S. pyrogenes status. Source: Good (1994)</p>
</div>

Basics of bootstrap and jackknife
------------------------------------

To get started with bootstrap and jackknife techniques, we start by working through a very simple example. First we simulate some data


```r
x<-seq(0,9,by=1)
```

This will constutute our "data". Let's print the result of sampling with replacement to get a sense for it...


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 1 2 3 6 7 8 
## 2 1 2 1 1 2 1
```

Now we will write a little script to take bootstrap samples and calculate the means of each of these bootstrap samples


```r
xmeans<-vector(length=1000)
for (i in 1:1000)
  {
  xmeans[i]<-mean(sample(x,replace=T))
  }
```

The actual number of bootstrapped samples is arbitrary *at this point* but there are ways of characterizing the precision of the bootstrap (jackknife-after-bootstrap) which might inform the number of bootstrap samples needed. *In practice*, people tend to pick some arbitrary but large number of bootstrap samples because computers are so fast that it is often easy to draw far more samples than are actually needed. When calculation of the statistic is slow (as might be the case if you are using the samples to construct a phylogeny, for example), then you would need to be more concerned with the number of bootstrap samples. 

First, lets just look at a histogram of the bootstrapped means and plot the actual sample mean on the histogram for comparison



```r
hist(xmeans,breaks=30,col="pink")
abline(v=mean(x),lwd=2)
```

<img src="Week-2-lab_files/figure-html/unnamed-chunk-6-1.png" width="672" />

Calculating bias and standard error
-----------------------------------

From these we can calculate the bias and standard deviation for the mean (which is the "statistic"):

$$
\widehat{Bias_{boot}} = \left(\frac{1}{k}\sum^{k}_{i=1}\theta^{*}_{i}\right)-\hat{\theta}
$$


```r
bias.boot<-mean(xmeans)-mean(x)
bias.boot
```

```
## [1] -0.0164
```

```r
hist(xmeans,breaks=30,col="pink")
abline(v=mean(x),lwd=5,col="black")
abline(v=mean(xmeans),lwd=2,col="yellow")
```

<img src="Week-2-lab_files/figure-html/unnamed-chunk-7-1.png" width="672" />

$$
\widehat{s.e._{boot}} = \sqrt{\frac{1}{k-1}\sum^{k}_{i=1}(\theta^{*}_{i}-\bar{\theta^{*}})^{2}}
$$


```r
se.boot<-sd(xmeans)
```

We can find the confidence intervals in two ways:

Method #1: Assume the bootstrap statistics are normally distributed


```r
LL.boot<-mean(xmeans)-1.96*se.boot #where did 1.96 come from?
UL.boot<-mean(xmeans)+1.96*se.boot
LL.boot
```

```
## [1] 2.648022
```

```r
UL.boot
```

```
## [1] 6.319178
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##  2.5% 97.5% 
##   2.7   6.4
```

Let's compare this to what we would have gotten if we had used normal distribution theory. First we have to calculate the standard error:


```r
se.normal<-sqrt(var(x)/length(x))
LL.normal<-mean(x)-qt(0.975,length(x)-1)*se.normal
UL.normal<-mean(x)+qt(0.975,length(x)-1)*se.normal
LL.normal
```

```
## [1] 2.334149
```

```r
UL.normal
```

```
## [1] 6.665851
```

In this case, the confidence intervals we got from the normal distribution theory are too wide.

**<span style="color: green;">Checkpoint #4: Does it make sense why the normal distribution theory intervals are too wide?</span>** Because the original were were uniformly distributed, the data has higher variance than would be expected and therefore the standard error is higher than would be expected.

There are two packages that provide functions for bootstrapping, 'boot' and 'boostrap'. We will start by using the 'bootstrap' package, which was originally designed for Efron and Tibshirani's monograph on the bootstrap. 

To test the main functionality of the 'bootstrap' package, we will use the data we already have. The 'bootstrap' function requires the input of a user-defined function to calculate the statistic of interest. Here I will write a function that calculates the mean of the input values.


```r
library(bootstrap)
theta<-function(x)
  {
    mean(x)
  }
results<-bootstrap(x=x,nboot=1000,theta=theta)
results
```

```
## $thetastar
##    [1] 2.9 5.2 3.2 5.3 4.9 5.0 6.3 4.5 5.6 5.0 4.1 4.8 6.0 5.5 5.1 4.0 3.1 4.5
##   [19] 5.9 4.2 4.5 4.8 6.0 3.6 4.7 5.1 3.0 4.2 2.8 5.1 4.3 5.0 3.7 3.4 6.2 3.7
##   [37] 5.7 5.2 5.8 3.6 6.0 4.3 5.2 5.5 4.6 5.4 4.3 6.3 3.6 4.4 3.3 5.2 5.1 4.3
##   [55] 4.3 3.9 4.7 3.8 4.3 4.4 4.4 5.0 2.9 5.6 4.2 4.8 5.3 3.3 4.2 3.5 5.2 4.8
##   [73] 3.8 4.1 4.1 5.1 4.8 3.0 4.0 3.7 4.4 3.6 3.8 4.5 4.7 3.5 5.1 5.2 3.7 4.0
##   [91] 5.6 5.3 5.3 4.6 2.1 5.0 3.8 4.8 3.1 4.0 4.9 4.5 4.6 5.8 4.7 4.1 3.6 5.3
##  [109] 4.1 4.9 3.6 4.0 2.8 3.5 4.8 4.4 3.9 6.4 4.4 5.0 5.4 5.0 3.9 6.3 4.2 5.1
##  [127] 3.1 5.6 4.8 5.4 3.0 4.3 4.9 3.8 4.6 6.2 4.4 5.8 4.9 4.7 3.8 4.7 5.6 3.9
##  [145] 4.6 5.6 3.5 4.7 4.7 4.8 4.2 4.3 4.6 3.0 3.5 3.4 2.4 6.0 4.2 5.6 3.0 3.6
##  [163] 5.1 3.1 4.9 3.2 6.8 4.7 5.2 3.6 2.3 3.9 5.3 3.7 3.9 5.2 4.1 5.5 4.5 6.1
##  [181] 6.1 4.9 5.5 4.6 4.5 4.4 4.3 5.2 3.6 2.8 3.8 2.8 3.1 2.4 4.2 4.4 3.3 4.5
##  [199] 5.9 3.6 4.7 3.4 4.6 4.8 5.0 2.8 4.8 4.1 4.5 5.2 4.7 4.5 3.6 5.1 4.6 5.2
##  [217] 4.7 4.0 3.5 5.0 5.3 4.3 4.0 3.6 3.4 5.5 4.3 5.5 4.5 5.2 6.3 4.4 3.6 4.6
##  [235] 5.6 4.1 5.2 3.5 5.4 4.3 4.8 4.2 5.0 4.3 3.7 4.3 4.9 5.1 4.0 5.2 3.5 3.7
##  [253] 3.9 4.3 5.5 6.0 5.3 3.5 4.0 4.6 4.5 5.5 2.8 4.5 5.5 4.1 5.1 4.2 4.8 5.8
##  [271] 4.3 5.6 5.0 3.9 5.1 3.7 3.1 3.3 6.9 5.6 4.3 3.9 5.2 4.2 4.7 4.1 3.8 2.9
##  [289] 6.2 5.0 5.2 4.2 3.0 3.3 5.3 5.1 5.5 3.5 3.7 3.4 3.5 4.5 3.0 3.4 3.1 3.9
##  [307] 4.1 2.1 5.7 5.1 4.5 3.8 3.9 3.6 5.6 5.2 3.7 4.5 5.1 4.7 3.3 4.7 4.2 4.8
##  [325] 4.1 4.0 4.6 4.9 5.1 5.0 5.5 5.0 4.8 3.7 3.6 2.2 5.4 6.6 4.5 4.2 3.9 5.1
##  [343] 5.5 4.1 3.0 5.6 3.8 4.4 4.8 5.1 5.6 4.8 4.2 5.0 5.9 6.7 3.7 5.3 5.7 5.0
##  [361] 6.4 5.1 5.1 5.2 5.1 5.6 3.9 3.9 4.2 4.4 3.9 3.4 3.9 4.2 3.9 3.5 5.4 4.7
##  [379] 4.4 6.1 4.9 6.9 5.0 4.7 5.5 3.1 5.1 3.4 5.0 4.1 4.0 5.2 3.4 3.3 5.0 4.1
##  [397] 4.0 5.6 4.6 5.5 4.0 4.5 3.6 5.5 4.0 5.4 5.1 5.6 3.5 3.2 4.8 2.6 4.4 5.3
##  [415] 5.1 4.8 5.2 4.7 5.9 3.0 4.2 4.6 4.0 5.3 4.3 5.2 4.1 6.4 3.8 5.0 4.5 4.2
##  [433] 5.6 5.3 5.2 5.1 5.2 5.5 4.5 5.7 5.4 4.1 3.5 4.5 4.9 4.2 4.0 3.9 4.2 4.6
##  [451] 4.3 4.3 4.7 5.3 5.3 4.8 5.1 5.6 3.7 5.7 6.1 5.3 3.4 4.9 4.9 4.7 4.8 3.5
##  [469] 5.1 3.2 6.0 4.2 2.8 4.3 2.7 4.3 5.3 4.4 5.5 4.6 5.4 5.1 5.2 6.1 5.2 4.4
##  [487] 3.7 3.6 4.3 5.0 5.6 5.5 4.7 3.1 4.1 5.7 5.3 6.2 6.3 3.8 3.4 4.9 4.2 4.9
##  [505] 4.4 3.5 4.0 4.6 3.5 5.9 3.0 3.5 4.6 3.3 4.2 2.5 5.2 5.2 5.4 4.4 5.3 6.0
##  [523] 4.3 6.0 2.5 4.2 2.8 4.7 4.1 4.8 3.9 4.0 2.5 4.9 4.3 4.6 4.0 4.1 4.1 5.6
##  [541] 5.7 3.9 4.7 5.3 3.4 2.1 2.4 3.2 4.2 3.2 4.3 5.5 4.4 4.4 5.5 4.2 3.9 4.9
##  [559] 4.5 3.7 5.7 4.9 2.6 3.1 4.9 5.2 4.1 2.7 4.9 4.7 3.7 4.5 3.3 4.2 3.8 3.5
##  [577] 4.0 4.3 4.0 3.8 4.3 4.2 4.2 5.0 4.0 4.9 4.7 4.5 4.4 6.5 6.4 4.4 4.5 4.5
##  [595] 3.9 5.6 5.5 3.9 5.1 4.6 3.6 3.4 4.7 3.8 3.3 6.3 3.3 3.8 3.4 4.6 6.4 4.4
##  [613] 4.2 4.2 3.7 4.7 3.0 3.5 4.0 5.0 4.1 4.1 3.7 5.1 4.3 3.7 3.6 3.0 5.0 4.6
##  [631] 5.5 5.3 4.1 3.6 3.9 4.6 3.9 4.0 2.3 4.7 4.6 4.8 3.9 2.7 4.0 6.0 3.5 4.7
##  [649] 3.9 5.1 5.4 5.0 4.6 5.8 3.8 4.3 4.6 3.3 5.1 5.5 4.2 4.7 4.5 5.5 3.9 3.6
##  [667] 5.2 3.7 4.8 4.4 4.5 5.5 4.1 3.8 4.6 5.2 4.4 5.3 5.1 3.5 4.0 4.7 5.5 5.6
##  [685] 4.7 6.3 5.0 3.8 5.4 4.7 4.3 4.4 4.4 4.1 4.1 4.9 4.0 4.0 4.9 5.5 4.3 6.6
##  [703] 4.5 4.1 3.4 4.1 4.4 4.2 5.3 5.3 5.1 4.3 4.0 5.5 3.3 3.1 2.7 3.4 5.4 3.3
##  [721] 4.7 6.3 3.7 5.1 4.5 5.8 5.2 4.8 4.6 4.2 4.4 4.9 4.0 5.2 4.0 4.7 4.4 5.5
##  [739] 5.2 5.3 4.7 3.0 5.6 3.8 6.0 5.7 4.7 5.4 4.6 5.7 4.4 4.6 4.9 4.2 3.8 4.1
##  [757] 4.3 5.5 3.5 3.2 5.4 5.6 3.0 3.6 4.0 5.7 4.3 4.4 6.2 4.0 4.8 4.5 5.3 4.2
##  [775] 5.0 5.8 5.9 4.8 4.0 4.1 4.8 4.6 4.1 5.4 5.7 4.2 4.8 4.8 4.7 4.0 3.3 3.5
##  [793] 5.2 2.2 5.0 5.0 4.6 5.4 6.8 3.8 5.2 4.6 3.4 4.1 4.3 4.1 5.9 4.0 4.7 3.1
##  [811] 3.9 4.9 2.0 5.5 4.7 3.7 3.4 3.7 5.5 3.5 4.2 5.2 4.9 4.9 3.5 4.4 3.3 4.8
##  [829] 3.2 3.7 4.3 5.8 4.7 2.8 3.7 5.4 4.6 5.4 4.6 3.6 4.5 5.4 4.8 3.6 5.4 3.3
##  [847] 3.5 4.4 2.6 4.6 4.4 4.8 5.4 5.6 6.5 2.7 6.0 3.4 2.5 5.6 4.4 4.2 4.4 3.8
##  [865] 4.3 4.0 3.5 4.0 4.7 4.0 4.5 5.9 3.5 3.6 5.4 4.4 6.5 5.4 5.3 3.9 3.2 4.5
##  [883] 1.7 2.9 5.3 5.9 4.8 5.8 4.8 3.6 6.3 5.4 5.1 2.7 2.7 5.7 4.9 3.9 5.1 4.5
##  [901] 2.7 4.6 4.5 3.9 3.9 4.8 4.0 3.7 1.6 4.9 4.1 5.5 5.1 4.6 4.1 4.1 4.1 3.3
##  [919] 3.6 5.5 5.3 5.7 3.6 4.2 2.6 3.5 4.4 4.3 4.7 3.6 4.2 3.4 3.8 4.8 5.5 4.7
##  [937] 5.8 4.9 4.1 2.7 5.5 5.5 4.0 5.5 4.9 4.4 5.3 4.4 4.5 4.0 6.4 5.2 3.5 3.2
##  [955] 4.6 3.9 4.4 3.5 4.4 2.8 3.9 3.6 4.4 6.5 4.5 4.4 3.6 6.0 5.1 5.5 3.2 4.3
##  [973] 5.0 5.4 4.8 4.9 3.8 4.9 4.3 3.7 3.3 6.3 3.8 3.9 3.4 4.3 4.0 4.5 3.4 5.1
##  [991] 4.2 4.0 4.5 2.1 5.7 4.2 3.4 4.9 4.9 4.8
## 
## $func.thetastar
## NULL
## 
## $jack.boot.val
## NULL
## 
## $jack.boot.se
## NULL
## 
## $call
## bootstrap(x = x, nboot = 1000, theta = theta)
```

```r
quantile(results$thetastar,c(0.025,0.975))
```

```
##  2.5% 97.5% 
##   2.7   6.3
```

Notice that we get exactly what we got last time. This illustrates an important point, which is that the bootstrap functions are often no easier to use than something you could write yourself.

You can also define a function of the bootstrapped statistics (we have been calling this theta) to pull out immediately any summary statistics you are interested in from the bootstrapped thetas.

Here I will write a function that calculates the bias of my estimate of the mean (which is 4.5 [i.e. the mean of the number 0,1,2,3,4,5,6,7,8,9])


```r
bias<-function(x)
  {
  mean(x)-4.5
  }
results<-bootstrap(x=x,nboot=1000,theta=theta,func=bias)
results
```

```
## $thetastar
##    [1] 3.6 4.9 6.2 4.8 5.6 2.2 3.9 4.8 3.1 5.3 3.9 4.8 3.4 4.7 4.0 5.7 4.5 4.2
##   [19] 3.6 4.9 4.4 4.3 4.6 4.0 5.1 4.4 3.2 5.9 5.3 5.1 5.5 4.8 3.6 5.3 4.7 5.5
##   [37] 4.8 4.3 4.0 3.8 5.0 4.4 5.3 4.4 3.0 4.4 3.3 6.7 3.8 2.5 6.1 4.6 4.3 4.5
##   [55] 4.6 3.9 2.9 3.6 3.2 4.2 4.2 2.5 4.0 4.2 2.9 4.7 4.0 4.7 3.8 4.4 3.8 4.8
##   [73] 3.6 4.2 6.0 3.9 4.6 5.4 3.0 4.5 1.9 5.1 5.7 4.3 4.4 4.6 4.9 5.1 4.6 4.7
##   [91] 4.7 4.6 5.7 4.8 4.6 5.4 3.3 4.2 2.8 3.1 3.5 5.0 5.2 3.4 5.0 4.5 4.6 5.7
##  [109] 4.6 3.8 3.8 3.9 4.9 5.4 4.4 6.1 4.2 5.2 4.1 4.1 5.4 3.6 5.1 3.8 5.0 4.7
##  [127] 5.4 5.3 2.5 5.7 3.7 4.1 2.3 4.6 4.4 2.8 3.8 5.7 5.3 3.7 4.9 4.0 4.3 4.6
##  [145] 3.1 6.3 4.3 5.1 3.6 5.3 4.2 2.8 4.3 4.1 5.2 4.4 3.6 4.5 4.6 3.9 4.6 3.5
##  [163] 4.2 4.6 3.2 4.7 4.7 4.5 5.2 4.8 4.5 3.6 4.6 4.2 4.3 4.1 4.8 4.8 3.7 4.6
##  [181] 4.9 5.4 5.0 3.8 5.2 6.1 3.9 5.3 5.3 4.3 4.1 4.8 5.8 4.1 3.6 5.1 3.9 5.1
##  [199] 5.1 5.2 5.0 3.6 3.8 5.3 5.9 4.3 4.6 3.5 3.0 4.0 5.2 3.8 4.8 4.3 5.3 3.7
##  [217] 4.2 5.6 4.6 5.3 3.1 5.9 5.1 3.4 3.7 4.8 5.7 4.9 5.8 5.8 4.2 5.8 3.4 5.5
##  [235] 5.3 4.5 1.9 4.6 3.6 5.3 4.0 5.8 5.6 5.9 3.9 3.5 5.3 4.3 5.2 3.1 5.0 2.2
##  [253] 2.6 3.4 5.0 3.2 4.3 5.8 4.6 3.3 4.9 5.5 5.1 3.2 6.6 5.4 3.3 4.1 3.8 3.5
##  [271] 4.1 3.9 4.1 3.7 5.4 4.6 5.2 4.0 6.4 4.4 3.3 4.4 4.3 4.5 5.1 4.0 3.8 4.9
##  [289] 5.2 4.5 4.2 6.3 3.0 4.7 4.5 5.2 5.9 5.6 3.1 5.2 6.2 4.5 4.4 3.7 4.1 7.0
##  [307] 5.0 4.0 3.5 3.7 5.2 3.6 3.8 4.2 6.2 6.4 5.8 4.4 4.4 3.5 4.7 3.6 4.6 4.6
##  [325] 3.2 4.2 3.5 4.6 4.8 2.2 3.6 5.3 6.2 2.5 4.8 4.7 3.0 5.2 3.7 4.6 3.8 5.8
##  [343] 3.7 5.4 3.5 4.6 5.9 4.3 4.4 5.1 4.8 4.9 4.9 4.5 4.7 4.6 5.3 4.0 5.2 5.9
##  [361] 4.1 4.1 2.6 4.3 5.2 3.3 5.3 4.0 4.7 4.5 4.4 5.3 5.9 6.5 3.9 5.5 3.0 4.4
##  [379] 4.3 3.7 5.2 6.7 6.6 4.6 4.8 4.1 5.8 5.7 4.3 3.9 4.2 4.6 3.8 3.0 4.9 4.0
##  [397] 5.6 5.3 5.5 4.7 4.5 5.1 4.7 4.3 6.4 4.8 5.2 4.3 4.1 3.2 4.0 5.2 4.7 5.0
##  [415] 4.5 2.8 4.5 5.0 4.3 4.5 3.1 4.6 5.3 4.3 5.9 3.7 5.5 3.7 3.8 4.0 4.7 4.6
##  [433] 5.2 5.2 4.2 3.4 4.3 5.5 4.8 5.7 4.5 5.7 5.0 3.3 3.7 4.6 5.2 4.3 5.5 5.1
##  [451] 4.4 4.7 5.5 3.7 4.1 5.3 4.7 5.5 5.4 3.2 5.3 4.7 4.3 4.3 5.7 4.2 5.0 4.6
##  [469] 4.4 3.8 5.4 4.6 4.5 4.5 5.2 4.9 2.4 5.4 5.1 4.2 4.3 4.4 4.6 4.1 5.4 2.8
##  [487] 2.9 2.4 4.7 4.9 3.4 5.0 4.1 4.7 6.4 4.3 4.0 4.5 5.7 5.3 4.2 2.8 3.7 6.6
##  [505] 2.9 1.8 5.3 4.6 3.3 3.8 5.0 4.3 3.3 4.4 4.7 3.0 5.5 5.1 3.6 4.8 3.7 4.3
##  [523] 3.9 5.2 5.9 4.3 5.2 5.4 5.7 3.8 5.0 4.5 5.9 5.1 2.7 2.8 4.7 4.3 4.6 5.0
##  [541] 4.0 4.9 4.7 4.0 4.2 2.0 6.5 4.3 4.0 3.9 5.0 4.2 5.7 4.6 4.8 4.1 4.9 6.3
##  [559] 4.1 4.5 3.9 4.7 5.0 4.5 3.7 3.9 4.3 6.0 3.6 3.8 5.7 5.1 2.9 5.6 4.5 4.3
##  [577] 4.6 5.5 4.5 3.7 3.7 5.2 6.7 5.6 4.6 6.4 4.0 3.7 4.0 3.1 4.4 3.7 2.2 4.6
##  [595] 3.2 3.9 4.4 3.9 3.5 5.2 5.6 3.4 5.8 2.8 5.6 5.8 4.2 5.2 5.9 4.9 6.0 4.8
##  [613] 4.7 4.8 3.9 5.2 3.3 5.4 3.4 3.9 4.7 4.9 6.4 5.0 4.3 3.7 4.0 4.3 3.4 5.9
##  [631] 4.0 6.9 6.2 4.9 4.5 5.0 3.5 5.5 5.0 5.2 4.4 4.9 3.6 4.8 4.3 3.0 4.0 4.9
##  [649] 5.7 5.3 3.8 5.2 4.6 5.2 5.3 3.5 4.7 5.0 2.7 4.1 3.5 4.6 4.5 4.6 5.5 5.0
##  [667] 3.9 5.1 5.2 5.6 5.7 5.1 4.4 3.6 4.1 5.0 5.3 4.4 5.6 5.6 4.2 6.3 4.2 5.4
##  [685] 3.8 4.2 5.0 4.2 5.7 4.6 3.5 4.0 5.9 4.9 3.6 5.9 5.5 4.6 5.5 5.4 4.2 5.9
##  [703] 6.0 3.9 3.9 5.3 4.7 5.5 4.3 5.6 4.6 6.0 6.0 5.8 5.5 3.4 3.9 5.6 4.2 4.8
##  [721] 4.1 4.5 4.7 3.4 4.6 4.4 5.0 5.8 4.8 3.4 3.3 6.1 4.8 4.0 3.7 4.7 6.3 4.3
##  [739] 4.5 4.2 5.3 5.0 4.2 4.0 5.8 2.1 4.9 5.2 6.3 5.5 4.7 5.0 6.2 5.6 4.9 5.0
##  [757] 5.6 3.3 5.5 5.4 2.8 3.8 2.1 3.4 4.6 3.3 4.5 3.6 3.7 5.7 6.0 4.8 4.9 5.0
##  [775] 3.7 3.8 3.2 4.2 4.3 4.5 3.9 3.2 4.1 5.0 6.5 3.0 4.9 4.1 5.0 3.4 3.4 5.0
##  [793] 3.5 4.4 4.7 4.6 4.4 3.2 4.9 3.8 5.1 4.6 3.1 6.2 5.4 5.2 5.1 4.0 4.4 4.1
##  [811] 5.7 3.6 4.8 5.3 4.6 5.1 3.7 4.3 4.5 4.1 5.4 3.7 5.1 4.6 5.8 3.3 4.5 4.6
##  [829] 3.9 4.4 4.8 2.8 4.0 5.6 4.9 6.7 4.3 4.4 4.9 4.6 3.5 5.5 5.1 2.4 3.1 4.3
##  [847] 4.1 4.8 4.4 5.2 4.1 5.9 3.5 4.6 4.7 4.5 3.6 6.4 5.2 3.7 4.1 3.3 6.0 1.8
##  [865] 4.1 6.9 3.3 4.7 4.1 5.0 3.8 4.3 5.4 3.3 4.9 4.1 3.1 5.2 3.2 4.6 3.6 5.0
##  [883] 4.9 3.9 5.0 5.0 3.7 6.1 5.9 5.2 3.7 4.3 5.4 4.8 3.7 4.3 3.6 3.7 3.4 5.3
##  [901] 4.6 5.3 3.6 6.4 4.3 3.3 5.0 5.1 2.7 4.5 2.6 5.9 4.5 3.5 5.2 3.7 3.8 4.5
##  [919] 5.3 4.4 3.6 5.1 4.4 3.9 3.8 2.9 4.0 5.1 2.8 3.6 4.0 4.6 5.3 3.4 4.5 6.4
##  [937] 5.1 4.4 4.1 3.4 3.7 4.1 3.8 4.4 2.0 5.5 4.8 4.5 3.4 5.0 4.8 3.6 4.7 4.0
##  [955] 4.7 3.4 4.5 3.5 4.2 3.8 4.1 4.9 6.5 4.9 4.5 4.4 4.1 6.4 4.9 4.1 5.8 3.9
##  [973] 5.3 3.3 4.1 3.3 6.0 5.0 4.2 4.7 4.7 3.6 4.5 2.8 3.3 5.0 4.5 4.0 6.1 4.1
##  [991] 4.1 4.2 4.9 5.8 4.8 4.8 4.0 4.2 5.1 4.3
## 
## $func.thetastar
## [1] 0.0105
## 
## $jack.boot.val
##  [1]  0.49552632  0.39884393  0.29244186  0.16288089  0.05747126 -0.06856287
##  [7] -0.19644970 -0.19438202 -0.36796657 -0.48054795
## 
## $jack.boot.se
## [1] 0.9326075
## 
## $call
## bootstrap(x = x, nboot = 1000, theta = theta, func = bias)
```

Compare this to 'bias.boot' (our result from above). Why might it not be the same? Try running the same section of code several times. See how the value of the bias ($func.thetastar) jumps around? We should not be surprised by this because we can look at the jackknife-after-bootstrap estimate of the standard error of the function (in this case, that function is the bias) and we can see that it is not so small that we wouldn't expect some variation in these values.

Remember, everything we have discussed today are estimates. The statistic as applied to your data will change with new data, as will the standard error, the confidence intervals - everything! All of these values have sampling distributions and are subject to change if you repeated the procedure with new data.

Note that we can calculate any function of $\theta^{*}$. A simple example would be the 72nd percentile:


```r
perc72<-function(x)
  {
  quantile(x,probs=c(0.72))
  }
results<-bootstrap(x=x,nboot=1000,theta=theta,func=perc72)
results
```

```
## $thetastar
##    [1] 4.4 3.1 5.1 3.5 3.7 2.7 3.7 3.8 5.3 4.9 2.1 4.7 5.6 4.0 4.9 5.2 5.1 4.6
##   [19] 3.9 4.9 5.8 2.3 4.7 2.7 4.2 4.8 3.7 4.9 4.0 4.2 2.9 3.8 4.8 4.9 6.8 4.2
##   [37] 5.9 5.0 5.2 6.2 4.4 5.9 4.7 3.8 5.5 4.6 2.9 3.6 4.6 4.5 4.7 3.3 5.0 3.2
##   [55] 3.8 4.7 5.1 4.2 5.0 4.1 3.6 1.8 3.9 4.6 3.5 4.4 5.6 7.0 5.2 4.8 4.2 4.0
##   [73] 3.7 3.9 4.9 3.0 4.5 5.8 5.3 3.6 4.1 2.5 4.8 3.6 5.5 3.7 6.3 5.0 5.8 4.6
##   [91] 3.1 3.5 5.3 5.2 4.7 3.6 5.5 5.0 4.1 3.6 4.1 3.7 4.0 5.7 4.7 4.7 4.9 5.0
##  [109] 4.5 4.5 6.5 4.2 5.4 4.2 4.8 5.1 4.6 3.3 5.4 5.6 1.9 3.9 3.8 6.1 3.8 3.9
##  [127] 5.9 4.7 3.4 4.9 4.6 5.8 5.0 4.9 5.0 3.8 4.9 3.8 5.2 3.8 3.5 4.4 5.7 4.9
##  [145] 5.1 5.2 3.6 5.4 3.3 4.1 6.5 4.4 3.8 3.1 5.7 2.8 3.7 4.7 3.8 5.9 4.8 5.6
##  [163] 4.6 4.3 5.6 3.2 2.3 3.9 5.0 3.0 4.4 5.3 4.5 4.0 3.5 3.2 3.6 4.2 5.6 3.9
##  [181] 3.9 4.5 5.6 6.1 3.2 3.8 5.4 5.4 4.5 4.1 4.8 4.2 2.8 4.3 4.7 4.8 4.9 3.5
##  [199] 3.9 3.3 5.0 6.0 4.3 3.8 3.9 3.1 4.5 4.6 5.1 5.5 4.7 5.1 5.1 2.7 4.2 3.9
##  [217] 3.8 4.7 3.9 4.6 4.6 5.6 5.3 4.7 2.0 5.2 4.3 5.4 5.9 5.9 4.8 4.0 3.7 5.1
##  [235] 5.6 4.8 4.4 4.2 2.8 5.7 4.1 3.9 5.7 5.1 4.3 5.3 3.5 4.8 3.7 3.7 3.1 3.6
##  [253] 3.7 3.7 4.1 4.0 3.4 4.3 5.4 2.1 6.4 4.6 5.5 2.7 5.2 3.8 3.8 4.7 4.0 5.8
##  [271] 4.3 3.6 5.0 3.6 4.5 4.6 4.3 4.2 4.1 4.9 5.2 5.3 4.1 5.8 3.4 3.0 4.0 5.2
##  [289] 3.9 4.7 4.9 3.9 3.7 5.4 3.8 3.5 5.2 5.7 3.3 5.0 4.3 4.5 3.9 3.8 4.2 3.9
##  [307] 5.0 2.9 5.6 3.8 3.7 3.3 3.0 4.5 4.8 5.2 4.4 4.9 5.6 4.3 5.1 6.0 4.0 4.2
##  [325] 3.1 4.6 4.1 3.8 5.8 5.9 5.9 4.1 5.7 4.2 3.8 3.7 5.3 3.3 3.4 4.8 4.6 5.6
##  [343] 5.5 5.2 3.5 5.0 4.5 4.1 4.0 4.0 3.6 4.1 3.3 4.5 4.4 4.3 5.2 3.9 5.1 4.8
##  [361] 2.9 4.2 3.3 2.8 4.4 4.5 5.3 5.6 5.1 4.4 3.4 5.5 4.7 4.3 4.7 4.6 6.0 3.7
##  [379] 4.7 5.1 5.9 4.2 5.0 4.9 4.4 4.0 3.7 4.2 3.3 4.2 5.4 4.8 4.3 3.4 4.6 4.7
##  [397] 3.2 4.6 5.9 4.1 4.3 4.7 3.6 3.8 4.9 5.6 4.6 4.5 5.4 4.4 3.6 3.4 4.2 6.5
##  [415] 3.1 4.7 4.6 4.4 4.9 4.9 3.7 2.7 3.3 4.0 5.1 4.2 3.9 4.6 4.5 4.3 4.1 4.2
##  [433] 4.3 5.1 4.7 6.7 5.2 5.4 4.2 3.0 4.9 3.0 4.6 3.9 5.5 3.7 3.7 4.0 3.1 3.8
##  [451] 5.1 3.9 4.8 3.1 4.8 3.6 4.9 4.7 5.2 3.7 5.2 4.1 4.9 3.0 5.0 6.6 4.7 4.1
##  [469] 6.6 5.8 5.0 4.4 4.7 5.3 6.4 4.6 5.8 2.2 6.0 5.2 4.6 4.8 5.9 5.9 4.4 3.9
##  [487] 4.1 3.4 4.7 5.1 6.6 3.6 3.9 4.8 5.7 4.3 4.3 4.4 5.5 3.9 3.6 4.4 4.9 2.6
##  [505] 4.3 4.1 4.3 4.2 4.7 6.3 3.8 5.2 4.0 4.0 4.4 3.0 4.2 5.7 4.7 3.6 5.3 2.8
##  [523] 4.1 4.7 4.8 4.2 3.7 4.5 5.0 5.3 5.5 5.8 4.3 2.9 2.7 2.6 4.4 6.3 5.0 4.9
##  [541] 4.8 4.3 4.3 4.1 3.8 4.1 5.4 3.7 4.4 3.7 5.7 4.0 5.6 5.1 4.9 6.3 3.4 5.4
##  [559] 3.9 5.0 5.6 6.0 3.5 5.5 5.2 4.9 6.1 5.2 3.1 4.4 4.4 4.3 6.4 5.2 4.4 6.1
##  [577] 4.3 5.1 2.8 5.6 4.5 6.8 4.0 5.3 3.9 5.3 4.5 5.3 4.1 5.6 4.9 3.9 4.1 5.6
##  [595] 4.0 4.8 5.9 4.4 4.9 3.6 4.6 4.5 4.9 4.1 4.3 5.1 3.5 4.0 2.6 4.3 6.0 5.7
##  [613] 5.0 3.5 4.7 5.1 3.6 5.1 2.9 5.8 6.0 4.8 4.3 5.0 3.1 4.8 3.9 4.0 3.6 4.8
##  [631] 5.7 5.1 5.6 5.8 4.0 3.5 5.7 4.8 4.3 6.0 4.1 4.8 3.6 4.0 2.1 6.2 5.2 4.0
##  [649] 4.6 5.8 5.4 4.5 2.8 5.0 5.2 3.6 4.0 3.8 5.8 5.8 3.7 3.8 6.4 4.4 2.3 3.5
##  [667] 3.2 5.7 5.1 2.9 6.2 4.0 5.9 4.5 2.9 5.7 3.5 4.5 4.0 3.4 4.8 4.2 5.6 4.2
##  [685] 5.9 6.2 3.8 3.9 4.3 4.4 5.3 4.8 4.1 5.5 2.9 4.4 4.4 4.9 3.4 4.6 4.6 5.4
##  [703] 3.0 4.7 4.0 3.4 5.2 3.9 5.9 4.0 5.3 4.2 6.2 4.2 4.8 4.3 4.5 5.4 3.6 5.4
##  [721] 4.7 6.0 5.0 4.0 4.8 3.6 4.0 3.8 6.0 4.5 3.4 5.4 3.9 4.3 3.9 6.0 6.2 4.4
##  [739] 3.8 4.3 4.1 4.4 4.2 6.1 5.7 4.7 5.7 6.1 3.7 6.3 5.8 2.7 4.0 4.0 5.3 3.8
##  [757] 3.7 5.4 5.3 6.4 4.0 6.1 4.1 3.9 4.7 4.2 3.5 3.0 5.0 3.8 4.4 4.8 4.1 5.9
##  [775] 4.4 3.0 4.7 4.1 4.4 4.2 5.3 6.5 4.2 3.9 3.3 5.3 5.6 3.6 3.5 4.5 4.0 4.4
##  [793] 6.1 5.6 3.1 3.5 3.6 7.7 6.4 6.5 3.7 4.7 3.9 2.9 4.0 4.1 4.4 3.6 5.0 4.1
##  [811] 4.7 3.5 4.0 5.1 5.3 4.3 3.8 4.2 5.6 3.8 3.3 3.7 4.3 5.8 3.9 4.3 3.6 4.4
##  [829] 4.1 3.6 4.8 3.8 5.4 5.0 4.4 3.0 5.2 5.2 4.5 5.3 4.7 2.9 5.4 4.0 4.9 4.8
##  [847] 5.4 4.6 4.7 4.5 2.5 4.3 4.4 3.5 4.6 3.3 3.0 5.4 5.8 3.6 5.4 4.4 3.8 4.7
##  [865] 3.0 5.5 4.2 4.9 5.0 4.0 4.0 2.7 5.1 4.2 3.7 4.8 4.0 6.0 4.8 5.1 4.2 4.8
##  [883] 3.8 5.1 4.7 2.7 5.5 4.8 3.5 4.2 4.4 4.3 5.0 3.3 2.9 6.0 4.4 5.2 4.4 4.6
##  [901] 4.7 2.7 3.7 4.9 4.8 5.3 3.7 3.6 3.4 5.0 4.7 4.3 5.6 4.7 5.4 4.7 5.9 6.0
##  [919] 5.8 3.7 5.8 4.5 4.6 4.4 4.8 6.5 6.4 4.9 2.8 6.3 4.5 3.7 5.2 5.9 5.4 5.8
##  [937] 3.2 3.6 3.9 4.6 4.6 4.2 3.6 4.0 3.6 5.5 4.0 6.4 5.5 5.1 4.5 4.6 4.8 5.9
##  [955] 4.9 3.1 2.6 4.0 3.4 4.0 5.6 4.8 4.5 5.7 5.7 4.5 3.9 4.1 3.8 4.4 5.4 4.4
##  [973] 4.9 4.9 2.9 3.9 4.2 4.1 4.6 5.2 3.9 4.7 3.1 5.5 2.9 3.5 5.2 5.0 3.7 5.0
##  [991] 4.8 5.9 4.8 5.4 5.9 4.3 4.7 4.4 3.8 3.3
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.5 5.5 5.3 5.2 5.2 5.0 4.8 4.8 4.6 4.4
## 
## $jack.boot.se
## [1] 1.065317
## 
## $call
## bootstrap(x = x, nboot = 1000, theta = theta, func = perc72)
```

On Tuesday we went over an example in which we bootstrapped the correlation coefficient between LSAT scores and GPA. To do that, we sampled pairs of (LSAT,GPA) data with replacement. Here is a little script that would do something like that using (X,Y) data that are independently drawn from the normal distribution


```r
xdata<-matrix(rnorm(30),ncol=2)
```

Everyone's data is going to be different. With such a small sample size, it would be easy to get a positive or negative correlation by random change, but on average across everyone's datasets, there should be zero correlation because the two columns are drawn independently.


```r
n<-15
theta<-function(x,xdata)
  {
  cor(xdata[x,1],xdata[x,2])
  }
results<-bootstrap(x=1:n,nboot=50,theta=theta,xdata=xdata) 
#NB: xdata is passed to the theta function, not needed for bootstrap function itself
```

Notice the parameters that get passed to the 'bootstrap' function are: (1) the indexes which will be sampled with replacement. This is different that the raw data but the end result is the same because both the indices and the raw data get passed to the function 'theta' (2) the number of bootrapped samples (in this case 50) (3) the function to calculate the statistic (4) the raw data.

Lets look at a histogram of the bootstrapped statistics $\theta^{*}$ and draw a vertical line for the statistic as applied to the original data.


```r
hist(results$thetastar,breaks=30,col="pink")
abline(v=cor(xdata[,1],xdata[,2]),lwd=2)
```

<img src="Week-2-lab_files/figure-html/unnamed-chunk-17-1.png" width="672" />

Parametric bootstrap
---------------------

Let's do one quick example of a parametric bootstrap. We haven't introduced distributions yet (except for the Gaussian, or Normal, distribution, which is the most familiar), so lets spend a few minutes exploring the Gamma distribution, just so we have it to work with for testing out parametric bootstrap. All we need to know is that the Gamma distribution is a continuous, non-negative distribution that takes two parameters, which we call "shape" and "rate". Lets plot a few examples just to see what a Gamma distribution looks like. (Note that the Gamma distribution can be parameterized by "shape" and "rate" OR by "shape" and "scale", where "scale" is just 1/"rate". R will allow you to use either (shape,rate) or (shape,scale) as long as you specify which you are providing.

<img src="Week-2-lab_files/figure-html/unnamed-chunk-18-1.png" width="672" />


Let's generate some fairly sparse data from a Gamma distribution


```r
original.data<-rgamma(10,3,5)
```

and calculate the skew of the data using the R function 'skewness' from the 'moments' package. 


```r
library(moments)
theta<-skewness(original.data)
head(theta)
```

```
## [1] 0.9424202
```

What is skew? Skew describes how assymetric a distribution is. A distribution with a positive skew is a distribution that is "slumped over" to the right, with a right tail that is longer than the left tail. Alternatively, a distribution with negative skew has a longer left tail. Here we are just using it for illustration, as a property of a distribution that you may want to estimate using your data.

Lets use 'fitdistr' to fit a gamma distribution to these data. This function is an extremely handy function that takes in your data, the name of the distribution you are fitting, and some starting values (for the estimation optimizer under the hood), and it will return the parameter values (and their standard errors). We will learn in a couple weeks how R is doing this, but for now we will just use it out of the box. (Because we generated the data, we happen to know that the data are gamma distributed. In general we wouldn't know that, and we will see in a second that our assumption about the shape of the data really does make a difference.)


```r
library(MASS)
fit<-fitdistr(original.data,dgamma,list(shape=1,rate=1))
# fit<-fitdistr(original.data,"gamma")
# The second version would also work.
fit
```

```
##      shape       rate   
##    7.776332   10.550864 
##  ( 3.405688) ( 4.773197)
```

Now lets sample with replacement from this new distribution and calculate the skewness at each step:


```r
results<-c()
for (i in 1:1000)
  {
  x.star<-rgamma(length(original.data),shape=fit$estimate[1],rate=fit$estimate[2])
  results<-c(results,skewness(x.star))
  }
head(results)
```

```
## [1]  0.089262190  1.980038893 -0.040563195 -0.659745344 -0.559094317
## [6]  0.003080041
```

```r
hist(results,breaks=30,col="pink",ylim=c(0,1),freq=F)
```

<img src="Week-2-lab_files/figure-html/unnamed-chunk-22-1.png" width="672" />

Now we have the bootstrap distribution for skewness (the $\theta^{*}$ s), we can compare that to the equivalent non-parametric bootstrap:


```r
results2<-bootstrap(x=original.data,nboot=1000,theta=skewness)
results2
```

```
## $thetastar
##    [1]  1.4947017725  2.0684246548  0.4709013846  0.6709088136  1.5477608175
##    [6]  1.1132267774  1.0291918463 -0.0598595067  1.4211712910  0.5220819864
##   [11]  1.1639137086  0.2100056815  0.7026426310 -0.0359112355  0.5921298147
##   [16]  1.0510090401  0.0330767309  0.4719152479  0.8038361305  0.6252279286
##   [21]  1.1719242667  0.7751397866  0.0838363796  1.1920721857  0.5291923909
##   [26]  0.7543993034  0.7135759509  0.3889824205 -0.0714092196  0.6031627158
##   [31]  0.7487104580 -0.1147127615  1.3360439554  1.6145771643  1.2702010474
##   [36]  0.5987171798  0.6098206231  1.8070126825  1.0758474263  1.3495196255
##   [41]  0.9998409275  1.1688531545  0.7091277807  1.9383128414  0.9197287398
##   [46]  0.3335445045  0.6859450835  0.6303300142  0.3962637634  0.2875943057
##   [51]  0.3442563949  1.1632396495  1.2817397543  1.4161338626  1.5946541223
##   [56]  0.5321180931  1.1899893191  0.4490606237  2.2781839883  1.3441248859
##   [61]  0.9889733241  1.3705081413  0.4537641056  0.5161664033  0.7218139695
##   [66]  1.1747455712  0.7974057618  0.6732348762  0.5522765445  1.1188937298
##   [71]  1.3240198532  0.9888005930  0.0481118021  0.9222774781  0.5622226526
##   [76]  0.6185335645  0.6106632186  1.4625708484  1.6457800979  1.3189031586
##   [81]  1.6105807392  0.7266065858  1.6697594585  2.0519972674  0.9092605854
##   [86]  1.3523687619  0.8277513333  2.1372985006  0.2895734524  0.4824016237
##   [91]  1.1527152039  0.7738625030  0.8375743427  0.0052736031  2.6119548420
##   [96]  1.2695649250  0.8113810609  1.7915975638  0.2739927370  0.6378868048
##  [101]  0.0786839739  2.0184298897  0.8628034137  0.7239949874  0.3498292884
##  [106]  1.1921545992  0.5308371218  2.0117880693 -0.2742760707 -0.0575072848
##  [111]  1.2003923847  1.4632330089  1.1284305149  0.5018262984  1.1675189024
##  [116]  1.2975393374  1.3606539731  0.8568985077  0.6935127538  0.9793363368
##  [121]  0.4258824697  1.2834751649  0.9297964197  0.9978273413  0.6205641573
##  [126]  1.4708640193  0.7161662951  1.6192984079  0.5099858311  0.5346832607
##  [131]  0.1588551858  1.9944014471  0.5885814690  1.0205259838  0.7008487012
##  [136]  0.1117836234  0.4786888983  1.3647541159  1.0274712436  1.3368300458
##  [141]  1.2048147366  1.2147614614  2.2390562297  0.3273586265  0.3027046096
##  [146]  0.1931608902  2.0854754583  0.5529749146  1.0518214993  1.9575322133
##  [151]  0.9591585510  0.7061999300  1.2368920594  0.7815381466  0.6522739391
##  [156]  0.6867158037  1.1828677125  1.0680300548  1.3441248859  0.5738197807
##  [161]  1.1969957195  0.7032515416 -0.0032516619  1.3239795993  0.6953831400
##  [166]  1.2403684065  0.3056941530  0.4172293663  1.1805037246  0.8207412267
##  [171]  0.6913079871 -0.3667498843  0.6385223835  0.9984137534  1.7386725260
##  [176]  0.1493963736  1.4866905216  0.7688475956  1.4586595877  0.9933458239
##  [181]  0.7545764806  0.1967582029  0.3434084993  1.1421714943  1.5363691923
##  [186]  0.7730220939  0.0073876556  0.9802422324  0.1708348621  0.4803474094
##  [191]  0.2637586880  1.0214357462  1.0499000527  0.1410856722  0.9244663012
##  [196]  1.8524567590 -0.0679349420  0.7330554685  0.0101200505  0.5578145788
##  [201]  0.5346832607  1.1709754346  0.9627184118  0.6786585560  0.2023044816
##  [206]  0.5552106283  0.1383483323  0.1625049542  1.3353013065  0.2461283820
##  [211]  0.3134271441  0.1528627389  2.2479656255  0.8932682991 -0.0696698566
##  [216]  1.4761784609  1.6904262214  0.5060792904  0.7981388317  0.6822430281
##  [221]  1.7898054499  0.3862899809  2.4545455681  1.1308807143  0.3121499427
##  [226] -0.1248718124  0.7817749862  0.5279182535 -0.2266591775  1.3233363160
##  [231]  1.3084884155 -0.4199373639  1.1761219281  2.0826938189  0.6257520500
##  [236]  0.8945720329  0.8163429779  1.5326712880  0.6741424449  0.4537822315
##  [241]  1.5310357192  0.4990336122  1.0997746196  0.3062695141  1.1824497243
##  [246]  0.5220819864  0.8138225159  1.3625665013 -0.1406840506  1.9477225340
##  [251]  1.3188198131 -0.1998759198  1.1555855293  0.3250210052  0.7410834078
##  [256]  0.2665685767  0.5012793925  1.7264889208  0.9486118912  1.0843599929
##  [261]  0.9266003132  1.2595105554  0.1122869126  0.4536564749  1.2300640538
##  [266]  1.9551757212  0.7948541822  0.9348339197  1.2239483939  1.0786168916
##  [271]  1.4644395532  0.0226920296  1.0744368606  1.1808366376  0.6807288414
##  [276]  0.9569490428  0.8626427164  0.1137630046  0.9933458239  0.1348187713
##  [281]  0.0200909648  0.9341994218  0.2704411162  0.8759095173  0.7673144330
##  [286]  1.8587117194  0.3655919597  0.6442147853  1.2170097706  1.2060172262
##  [291]  0.7541473303  1.9942347284  1.4694048290  0.9794286524  0.3612827459
##  [296]  1.4757046881  0.0363290115 -0.2650293549  0.3091965036  0.8808475116
##  [301]  0.0944163442  1.4324193226  0.3830491941  1.6022646100  0.6136250431
##  [306]  0.5577800288 -0.0520272283  0.5393055203  1.3245973457  1.4077612859
##  [311]  1.9856484918  1.1465037934  0.7685794068  0.7234334422  1.0998546164
##  [316]  0.4690442595  0.6678726877  0.0874205908  0.3703060183  0.4574217558
##  [321] -0.0247881829  1.0078708922  1.6612226908  1.7780659812  1.5711691060
##  [326]  0.3468663493 -0.2072551888  0.8018011479  0.3349961136 -0.1918665029
##  [331]  1.6983873796  0.8786579706  0.7170913403 -0.0430926077  1.8603460538
##  [336]  1.5155716443  1.4381251079  0.0452086924  1.1942210330  1.3516735307
##  [341]  1.3292669782  1.6867312341  1.4880778332  0.9552467488  0.3997952045
##  [346]  0.3793774805  0.3929453784  0.4690991458  1.1919669065  0.7383372487
##  [351]  0.6319999217  1.0225470415  0.8472225045  1.8251906609  1.8921448937
##  [356]  2.0602268659  0.6130974985  1.1909195673  0.4553834245  1.2285962074
##  [361]  1.2664128195  1.8389480508  0.0532621703  0.3145720969  0.4967903216
##  [366]  1.7198140559  1.0378575467  0.4738775796  1.0089685085  0.9160983434
##  [371]  1.0010317358  1.2950507174  0.8402546124  0.7522302724  0.1258104716
##  [376]  0.1214621810  1.0375588599  0.8025479188  1.2665442525  0.7539618391
##  [381]  0.1260756439  0.5242679393 -1.0549404376  1.8149621043  0.1519122275
##  [386]  1.7823905880  0.6154278691  1.0350267994  1.2726668140  0.8082414666
##  [391]  1.0645167184  0.6889723957  0.9663569417  1.1521019988  0.2396982029
##  [396]  1.4576274168  0.5673173562  1.7421590270  0.7163680030  0.5272745028
##  [401]  0.1854303263  1.1661963314  1.6727744176 -0.8457188430  0.6119258954
##  [406]  1.0619189644  1.2363918941  0.6968565589  0.5747417063  0.3034902895
##  [411]  0.9442293395  1.0068953723  0.5821459301  1.9372855604  0.2646665337
##  [416]  0.7853822633  0.9196437276  2.5565176455  1.3314709284  0.5895798844
##  [421] -0.5843653591  0.9549024225  0.7122111440  0.2453533198  0.5315549133
##  [426]  0.3748907987  0.8412133096  0.3883813485  0.7152070099  0.3485613820
##  [431]  0.6424119958  0.2554615626  0.2974373270  0.6921070820  1.6428601937
##  [436]  0.7053278092  0.5152957486  2.2156474261  0.7234334422  0.5866324163
##  [441] -0.0383672661  1.2019455763  0.3168572522  1.2829483930 -0.0186084943
##  [446]  0.7270409314  1.0191644940  0.8418540717  0.4577325672  0.7008487012
##  [451]  1.2190816537  2.1872522326  1.2441817908  1.7335753830 -0.0299818064
##  [456]  0.7008487012  0.8670966071  0.9604782997  1.3957693878  1.3083418314
##  [461]  1.1823576972  0.5191666035  0.6934167151  0.8267691626  1.4213241968
##  [466]  0.8132746894  0.1186550560  0.7472946370  0.9204354244  1.1667281486
##  [471]  0.7113633290  1.5482222116  0.6482519927  0.2813045293  0.4172293663
##  [476]  0.7721140756 -0.5084822774  0.2881319208  1.0286658316  0.7773703365
##  [481]  0.9210409927  0.3516647010  0.9059215606  0.0904938831  1.1484726695
##  [486]  0.3070872831  0.7467907694  1.0057949743  0.3287392939  1.0040622992
##  [491]  0.6515226761  1.1958534157  1.2868503643  0.4827278246  0.9289518138
##  [496]  0.5041002832  1.2900433158  0.0792462618  0.9393195888  0.6873098201
##  [501]  0.8007754801  1.3001379140  0.6741424449  2.1882685100  0.7226773737
##  [506]  1.8974761094  0.6891387306  0.6117157371  0.9835077059  1.4894039440
##  [511]  0.4806903393  1.1389095544  1.6815914635  1.8503175790  0.1576754650
##  [516]  0.2491265017  1.9702438956  0.4294278097  1.3787859020  1.2382786617
##  [521] -0.9117118859  0.1376153640  1.2033020448  1.1676603541  1.3657657734
##  [526]  0.5540294741  0.9085125223  1.1184797504  0.4502524784  0.7841108599
##  [531]  1.2677960622  0.5291232919  0.2699648863 -0.2283062755  1.0881835007
##  [536]  0.2829329777  2.3453183860  0.9044529535  1.7048530133 -0.0687593502
##  [541]  0.9993774807  0.5435462886  0.3285671821  1.2801075501  0.9666987222
##  [546]  1.9834314834  0.2089911483  0.1403147987  1.1187144385  0.5681762268
##  [551]  2.0981394443  0.8253621010  0.7484008308  0.1746443524  0.7788494539
##  [556] -1.0615937814  1.3295436884  1.1418882826  1.2947790343  0.6911856438
##  [561]  0.6179190472  0.9424201720  1.0007096744  1.7483368642  1.9482066011
##  [566]  1.9150427658  2.1108906413  2.1628858296  1.4188245024  2.0090899702
##  [571]  0.3683405758  0.5446231591  0.3888747248  0.5654728853  0.5866438334
##  [576]  0.7869060044  0.8927877658  0.1130670845  0.7176059587 -0.5662807691
##  [581]  1.1504771839 -0.2709132506  1.0617891926  0.4326699991  0.4693277972
##  [586]  1.0202992728  0.4077901325  0.5027068542  1.3273961658 -0.4691868332
##  [591]  1.3475391525  0.1668990059  2.3352558744  1.2432716008  0.9143769447
##  [596]  0.4813230235  1.4413384480  0.4658320715  1.3772361017  0.5198788868
##  [601]  0.1347444196  0.9461437197  1.2964920840  0.7072809753  1.6701973818
##  [606]  0.4187832074  0.6828085079  1.1247501525  1.7639533138  0.6546145741
##  [611]  0.5225455406  0.7222537850  0.8602311313  0.2544922047  1.1373970240
##  [616]  0.1333985237  0.8064753415  2.0306079971  1.2541581594  0.3453475741
##  [621]  0.7962238255  2.2521961699  1.0359037892  1.1162246386  1.4574555072
##  [626]  1.0721493308  0.8210844350  1.9659080251  2.0492008090  0.9630844545
##  [631]  0.3033950580  1.3743769136  1.3446512830  2.0426823222  1.0020480249
##  [636]  0.5443059878  0.9665947698  1.2901064491  0.6451848939  2.2277386580
##  [641]  0.7280525775  0.8644607798  0.4481793393  2.0632254934  0.5343597888
##  [646]  1.6098187449  0.3034855734  0.6910663987  0.9499812841  1.2372682954
##  [651] -0.0789652558  0.9708031505  1.6533762990 -0.1289571051  1.7462292350
##  [656]  0.9086749994  1.1561583066  1.4683082286  0.6914633195  0.6505906909
##  [661]  1.2120911212  1.2178409462  1.0398187981  0.8102184688  0.4895291618
##  [666]  0.7833644517  0.8980137557  1.0992623166  0.6411619028  1.7443894185
##  [671]  0.7807448162  1.6044450211  0.7563469029  1.3984800475  0.0588724254
##  [676]  1.1544925043  1.6295233419  0.8801463193  0.9044529535  1.0884855911
##  [681]  0.5903560085  1.1744969225  0.0725116338  0.0513526120  0.4316412874
##  [686] -0.1290209853  0.0974799073  0.5339296331  0.5485747513 -0.2563277325
##  [691]  1.2080258687  1.3370952151  0.9525391259  0.4665818196  0.9618458463
##  [696]  0.2936414714  1.2295144836  0.9362951215  0.7618023188  1.5012805071
##  [701]  2.1266173273  0.8358378410  2.0747801530  1.9218209252  1.2303359677
##  [706]  0.8653011458  0.6566484220  1.2358476045  0.7372614762 -0.2467022964
##  [711]  0.5689989496  0.9995557656  0.1839247942  1.5733888815  0.4921490091
##  [716]  0.4683180457  1.2328413965  1.3089385689  0.7940106482  1.9913596776
##  [721]  1.0853616398  1.6763439419  1.3467664300  0.3460167969  0.7128413272
##  [726]  1.9015780224  0.6783985348  0.8200861141  0.9348524157  0.3168572522
##  [731]  0.1118787439  0.1359450286  0.8804339334  1.4920820079  1.5091890278
##  [736]  2.0834618303  1.2403956589  1.1631151944 -0.1698029081 -0.0487067615
##  [741]  0.5124298654  2.3336649676  1.0280194321  1.1807626957  1.2716843183
##  [746]  0.9417371331  1.2187817458  0.8621889696 -0.0923130872  1.2011228052
##  [751]  1.2641581659  0.9922671070  0.5604392673 -0.0511938552  0.6999741599
##  [756]  0.6657995147  0.9448038350  0.0020976443  1.5111729190  0.6523589120
##  [761]  0.9863981460  0.8150975927  1.2337724920  1.3727827715  0.7404149253
##  [766]  1.1276878456  0.7605327796  0.3597495332  0.8879290256  1.3955834084
##  [771]  1.4829482473  1.4716874405  0.8234546691  0.4955037633  1.6989167880
##  [776]  1.7326331763  0.8031569802  1.2134301688  0.5436413350  0.6766228885
##  [781]  0.9225779942  0.5839461729  0.3612889329  1.1781667292  0.3137762131
##  [786]  0.5762990592 -0.0373888758  1.2951129299  0.9257388501  0.1508909667
##  [791]  0.8726228976  0.4757851550  0.7541995637  0.9107985714 -0.9030045685
##  [796]  0.2815542163  0.8377605517  2.0704560450  0.5445330325  0.7150588009
##  [801]  1.0300573905  1.8688076157  1.5524575938  1.3920810544  0.1673706889
##  [806]  1.2966521789  0.7962918553  1.0670984541  0.6550999413  1.2708294563
##  [811]  0.0371419720  0.2941037948  2.0200838837  0.2834840081  1.5077902793
##  [816]  1.9755454476  0.8748636443 -1.3686281035  0.9830252014  1.4774808529
##  [821]  0.2593981388  0.7039654961 -0.0776868532  1.8384804164  1.4220304276
##  [826]  0.5394417956  1.7057627240  0.3415312071  0.9614483691  1.1149210089
##  [831]  0.7424946936  1.5562889416  0.4226542476  0.9092342775  1.2138207325
##  [836]  1.1067183863  1.1764219320  0.9528286145  1.2240259410  0.2416801072
##  [841]  0.1335202533  1.3659104802  0.7216628642  0.7035410049  0.6288389371
##  [846]  1.7323570211  0.4760776826  0.6466626739 -0.1961892338  1.0617114919
##  [851]  1.1584066102  0.7088547677 -0.2680467503  0.1228874167  0.9167616161
##  [856]  1.2057160989  1.2935070861  0.7332028929  1.4593736024  0.4704024319
##  [861] -0.3022055399  0.5717600808  0.8140091850  1.3127958429  0.2196019738
##  [866] -0.3041788036  0.4249909196  0.1095598326 -0.3527669428 -0.0980641031
##  [871]  0.8810900595  0.6625273488  0.9297123334  1.1577907439  0.7979813603
##  [876]  0.1328515147  0.4629446837  0.5086338393  0.0002285332  0.7041030254
##  [881]  1.9830265452  0.2953524563  1.1304004553  0.8371859127  1.3211530162
##  [886]  0.6080515737  0.4595490246  0.7612900997  2.0570037771  0.5757744284
##  [891]  1.2216992438  0.7219959026  0.9094412261  0.7795765783  0.5956656174
##  [896]  0.9863100474  0.3165478882  1.1082640693  0.7730189691  1.8014073621
##  [901]  0.6080646684  0.2076087082  0.0897150635  0.6267013995  0.1736882342
##  [906]  1.2532792668  1.1881807551  1.2604847775  1.0230147683  0.0344288481
##  [911]  2.1083229814  1.3826639167  0.5453532252 -0.2195919792  1.7775418960
##  [916]  1.8663569130  0.8809280949  1.1742589802  0.5413742960  1.9708861189
##  [921]  1.2619404424  1.2525282556  0.7988753141  1.4947017725  0.1481956497
##  [926]  0.2991610477  1.1959486062  2.5206540674  1.1631151944  1.0411978051
##  [931]  0.5482630497 -0.5468169685 -0.2927873758  1.0219213607  0.4473477412
##  [936]  0.6445051841  0.5184860436  1.1472918609  0.9441717521  1.4029546620
##  [941]  0.5399147572  0.3197911428  1.4322702667  0.8190915811  0.6054732105
##  [946]  0.6262407060  0.2876749046  0.5922307003  1.2532914621  0.9918193187
##  [951] -0.0439797771  0.4864531718  1.5081572408  1.0126430996  0.5752928737
##  [956]  0.2593981388  1.2200362314  0.7971450937  0.4341726384  1.5061524392
##  [961]  0.6334508471  1.3702795077  0.1635889083  0.9496792514  0.1152405917
##  [966]  1.9026200151  0.6751359514 -0.6784948510  1.2897558670  1.3130872890
##  [971]  0.9087702337  1.2562373341  2.1300320143  0.5450123488  0.7927122509
##  [976]  1.0990858900  0.4981397211  1.6838000430  0.3637404636  1.2133913020
##  [981]  1.2408092270  0.6795725464  0.5775267849  1.1406482596  0.9545936536
##  [986]  1.0483507373  0.8849831387  0.7447540195  1.9468833201  0.7382233646
##  [991]  1.1233533202  1.2239483939  0.9944740510  0.6895601641  0.1076830942
##  [996]  0.8912828240  1.7194511958  0.3536345352  1.4564629141 -0.0376051487
## 
## $func.thetastar
## NULL
## 
## $jack.boot.val
## NULL
## 
## $jack.boot.se
## NULL
## 
## $call
## bootstrap(x = original.data, nboot = 1000, theta = skewness)
```

```r
hist(results,breaks=30,col="pink",ylim=c(0,1),freq=F)
hist(results2$thetastar,breaks=30,border="purple",add=T,density=20,col="purple",freq=F)
```

<img src="Week-2-lab_files/figure-html/unnamed-chunk-23-1.png" width="672" />

What would have happened if we would have fit a normal distribution instead of a gamma distribution?


```r
fit2<-fitdistr(original.data,dnorm,start=list(mean=1,sd=1))
```

```
## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced
```

```r
fit2
```

```
##       mean          sd    
##   0.73702568   0.28271814 
##  (0.08940333) (0.06321340)
```

```r
results.norm<-c()
for (i in 1:1000)
  {
  x.star<-rnorm(length(original.data),mean=fit2$estimate[1],sd=fit2$estimate[2])
  results.norm<-c(results.norm,skewness(x.star))
  }
head(results.norm)
```

```
## [1] -0.1703753  0.7394832 -0.4372429  0.5145575 -0.3503036 -0.7147646
```

```r
hist(results,breaks=30,col="pink",ylim=c(0,1),freq=F)
hist(results.norm,breaks=30,col="lightgreen",freq=F,add=T)
hist(results2$thetastar,breaks=30,border="purple",add=T,density=20,col="purple",freq=F)
```

<img src="Week-2-lab_files/figure-html/unnamed-chunk-24-1.png" width="672" />

All three methods (two parametric and one non-parametric) really do give different distributions for the bootstrapped statistic, so the choice of which method is best depends a lot on the situation, how much data you have, and what you might already know about the underlying distribution.

Jackknifing is just as easy at bootstrapping. Here we will do a trivial example for illustration. We will write a little function for the mean even though you could put the function in directly with 'jackknife(x,mean)'


```r
theta<-function(x)
  {
  mean(x)
  }
x<-seq(0,9,by=1)
results<-jackknife(x=x,theta=theta)
results
```

```
## $jack.se
## [1] 0.9574271
## 
## $jack.bias
## [1] 0
## 
## $jack.values
##  [1] 5.000000 4.888889 4.777778 4.666667 4.555556 4.444444 4.333333 4.222222
##  [9] 4.111111 4.000000
## 
## $call
## jackknife(x = x, theta = theta)
```

**<span style="color: green;">Checkpoint #6: Why do we not have to tell the 'jackknife' function how many replicates to do?</span>**

Let's compare this with what we would have obtained from bootstrapping


```r
results2<-bootstrap(x,1000,theta)
mean(results2$thetastar)-mean(x)  #this is the bias
```

```
## [1] 0.0224
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.8990476
```


Everything we have done to this point used the R package 'bootstrap' - now lets compare that with the R package 'boot'. To avoid any confusion (a.k.a. masking) between the two packages, I recommend detaching the bootstrap package from the workspace with


```r
detach("package:bootstrap")
```


The 'boot' package is now recommended over the 'bootstrap' package, but they give the same answers and to some extent it is personal preference which one prefers to use.

We will still use the mean as the statistic of interest, but we will have to write a new function for it because the syntax of the 'boot' package is slightly different:


```r
library(boot)
theta<-function(x,index)
  {
  mean(x[index])
  }
boot(x,theta,R=999)
```

```
## 
## ORDINARY NONPARAMETRIC BOOTSTRAP
## 
## 
## Call:
## boot(data = x, statistic = theta, R = 999)
## 
## 
## Bootstrap Statistics :
##     original     bias    std. error
## t1*      4.5 0.07227227   0.8881439
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 6 7 8 9 
## 2 2 2 2 2
```

```r
xmeans<-vector(length=1000)
for (i in 1:1000)
  {
  xmeans[i]<-mean(sample(x,replace=T))
  }
mean(x)
```

```
## [1] 4.5
```

```r
bias<-mean(xmeans)-mean(x)
se.boot<-sd(xmeans)
bias
```

```
## [1] -0.0545
```

```r
se.boot
```

```
## [1] 0.8749773
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

