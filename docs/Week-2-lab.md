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
## 1 2 4 5 7 8 
## 2 1 1 1 1 4
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
## [1] 0.0233
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
## [1] 2.733442
```

```r
UL.boot
```

```
## [1] 6.313158
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##   2.5%  97.5% 
## 2.7975 6.3000
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
##    [1] 5.6 5.3 5.1 4.6 5.2 5.3 4.0 4.0 5.3 3.6 5.5 4.3 4.2 3.9 4.2 6.2 4.1 4.5
##   [19] 4.6 2.7 4.5 4.6 4.9 5.2 3.9 4.8 5.2 3.4 3.5 2.2 4.4 2.8 4.2 4.8 5.7 4.6
##   [37] 4.2 2.2 3.1 2.6 3.2 3.8 4.7 3.7 2.0 5.3 6.2 4.6 3.5 3.4 6.1 5.5 5.1 4.2
##   [55] 4.5 5.9 5.5 4.3 5.2 3.2 3.7 5.6 3.6 4.3 5.0 6.0 3.0 3.7 3.7 4.5 3.9 6.4
##   [73] 6.1 4.4 3.7 5.0 5.0 4.2 4.3 3.4 5.2 5.2 5.8 3.7 4.8 4.9 3.8 3.6 5.3 4.7
##   [91] 5.7 6.0 4.8 4.2 4.0 4.2 3.2 4.6 4.1 5.4 5.9 3.7 2.4 2.8 3.9 2.8 5.5 4.9
##  [109] 5.5 3.6 5.2 5.4 5.6 3.8 5.7 5.4 5.9 5.3 3.5 3.2 2.8 4.9 5.1 5.5 3.5 5.6
##  [127] 5.3 3.5 5.3 4.2 3.6 3.4 4.6 4.3 4.7 4.1 5.0 5.9 3.7 3.2 3.8 3.9 3.8 3.5
##  [145] 5.0 4.2 4.1 4.1 4.8 3.1 3.1 4.1 4.6 4.3 6.2 5.1 5.4 5.1 5.1 4.5 4.1 6.1
##  [163] 4.7 5.1 3.6 5.0 4.0 4.3 4.4 5.3 3.8 2.8 5.0 3.3 3.5 5.5 4.0 4.2 5.4 4.0
##  [181] 4.1 6.0 6.7 3.7 3.8 5.5 4.1 5.6 3.7 3.6 3.0 4.6 5.6 3.7 5.4 4.8 3.7 4.4
##  [199] 3.1 3.6 4.6 4.3 5.6 4.7 4.3 5.1 4.7 5.7 2.8 4.7 4.7 4.6 4.1 5.6 3.3 4.7
##  [217] 4.9 5.9 4.8 4.8 3.5 5.3 5.5 4.7 4.8 3.8 4.4 4.0 3.8 4.1 5.8 4.8 6.4 5.5
##  [235] 4.5 5.2 4.3 3.5 4.6 5.9 2.7 5.0 4.4 3.7 5.6 5.5 3.8 3.8 4.8 3.5 3.4 4.6
##  [253] 5.8 3.3 6.1 3.0 3.6 4.4 5.0 4.4 4.5 4.9 4.3 5.5 4.1 4.5 4.3 3.7 4.4 4.8
##  [271] 3.6 5.1 3.4 6.7 4.0 3.8 4.9 5.2 5.5 3.5 6.2 5.3 5.8 4.4 5.3 4.4 4.5 4.3
##  [289] 3.1 5.2 5.1 4.7 3.9 4.9 4.1 6.9 4.8 3.6 4.4 4.5 2.0 4.7 3.7 4.5 3.0 3.5
##  [307] 4.6 5.0 4.2 4.9 3.6 4.2 6.3 4.7 3.0 3.4 4.5 5.2 3.6 2.6 4.2 5.0 3.4 3.2
##  [325] 3.9 4.2 5.5 4.7 5.4 6.6 4.8 4.0 2.5 4.4 3.9 3.0 4.6 4.2 5.0 5.5 4.2 6.1
##  [343] 3.1 4.5 5.0 3.9 4.0 5.0 5.8 4.1 5.0 5.8 2.7 4.9 3.1 4.3 4.6 4.0 5.2 4.2
##  [361] 6.0 4.1 3.3 3.9 4.3 2.9 4.8 3.5 5.9 7.3 5.7 3.9 3.8 4.4 5.0 5.1 6.1 5.1
##  [379] 3.6 3.2 4.8 4.6 6.2 4.6 3.6 5.1 5.6 6.0 3.8 3.2 5.1 3.5 2.7 4.7 4.8 5.7
##  [397] 3.4 4.2 5.2 4.2 3.6 3.4 5.9 3.7 2.9 4.6 3.6 5.1 5.9 4.6 5.0 5.4 4.4 5.4
##  [415] 3.4 3.3 3.9 4.3 4.5 4.4 3.7 3.9 3.9 2.5 5.3 4.6 3.7 2.9 5.4 4.8 4.2 4.4
##  [433] 5.6 4.5 4.1 6.3 4.4 3.6 4.5 3.3 4.8 5.2 6.0 5.8 3.9 5.4 3.7 3.1 3.9 4.5
##  [451] 4.0 4.5 3.8 3.3 3.8 5.1 4.2 4.7 1.9 6.7 3.6 3.4 5.0 4.5 5.6 5.3 5.0 4.4
##  [469] 5.1 5.4 3.6 3.8 3.9 4.6 3.0 4.1 5.3 4.4 5.9 3.2 4.6 3.2 4.7 4.2 3.0 4.9
##  [487] 4.6 4.6 3.7 3.0 5.6 3.3 5.4 5.4 4.5 3.7 4.8 5.1 3.3 3.7 5.6 4.5 4.7 4.4
##  [505] 2.9 5.6 4.9 3.9 4.0 4.4 4.3 3.8 4.1 5.7 3.5 5.0 5.5 5.7 4.6 4.1 5.4 4.4
##  [523] 4.1 3.9 5.5 4.5 6.3 5.8 4.2 6.1 4.5 3.9 3.8 5.4 4.6 6.0 6.0 5.5 6.1 4.3
##  [541] 3.9 4.1 4.8 4.0 2.3 4.3 4.5 3.5 5.4 4.3 3.0 5.3 4.9 3.9 4.8 3.1 5.0 6.1
##  [559] 5.0 4.7 4.2 5.6 5.1 5.9 3.8 4.8 4.8 5.7 4.2 4.8 4.7 4.2 3.8 4.5 5.2 5.0
##  [577] 5.1 4.5 2.8 5.4 5.7 5.7 4.2 4.9 4.1 4.7 6.0 6.0 3.3 5.6 4.4 5.8 4.6 6.4
##  [595] 4.7 5.5 4.2 4.8 6.3 3.8 6.4 4.8 4.5 3.0 4.4 4.3 5.0 6.6 4.3 3.8 5.8 3.9
##  [613] 5.2 5.2 4.5 3.8 4.7 5.6 4.1 3.1 5.2 4.8 5.0 5.7 4.2 5.5 5.5 4.9 4.6 4.7
##  [631] 4.1 5.5 4.3 5.3 3.2 5.0 6.4 4.4 3.3 3.7 6.2 4.1 3.7 4.8 3.8 4.6 4.1 5.0
##  [649] 4.2 5.4 3.2 4.1 6.1 3.5 6.0 4.7 3.0 3.4 3.4 3.7 4.4 6.0 4.3 5.6 4.4 3.0
##  [667] 5.3 3.2 6.4 4.3 4.0 6.2 4.0 4.0 6.2 3.4 4.0 3.8 3.4 5.6 5.7 3.7 5.0 4.2
##  [685] 4.3 4.9 6.5 2.8 3.6 5.0 4.4 3.1 3.8 5.0 5.5 3.4 3.8 4.8 4.9 5.1 4.0 5.8
##  [703] 5.7 4.4 5.3 4.6 4.0 4.4 4.6 5.3 3.2 5.0 4.5 2.5 4.7 5.2 2.8 4.4 4.6 4.0
##  [721] 4.4 3.8 4.9 5.3 3.2 4.4 4.7 5.2 4.7 3.9 4.3 4.1 4.5 6.3 4.8 4.1 6.1 4.1
##  [739] 4.2 4.6 4.4 6.0 4.3 4.1 4.5 6.4 4.9 3.6 4.2 4.1 4.9 6.3 4.8 4.0 3.8 4.6
##  [757] 4.2 5.9 4.7 4.4 5.1 5.3 4.7 5.4 5.1 3.1 4.0 3.7 4.4 4.7 4.1 4.6 4.7 2.7
##  [775] 3.7 5.7 4.1 4.9 4.0 4.8 4.7 4.5 4.6 5.2 4.9 4.0 6.3 4.9 4.6 2.8 5.5 4.3
##  [793] 2.7 4.0 5.2 4.3 5.3 4.9 6.3 3.3 3.3 5.9 5.5 5.4 4.7 4.6 5.0 4.2 4.9 3.9
##  [811] 5.0 2.5 3.6 4.0 5.5 4.6 2.7 4.1 5.6 3.9 4.4 5.8 3.1 4.9 5.4 4.1 4.9 3.3
##  [829] 4.9 4.5 4.7 2.8 4.9 4.4 5.4 3.7 5.6 4.2 3.4 5.0 4.2 2.9 4.6 4.2 6.0 4.6
##  [847] 5.0 5.2 3.8 4.0 4.6 4.3 6.3 4.6 3.7 5.3 5.6 4.3 5.2 3.6 5.8 5.4 4.0 3.6
##  [865] 3.6 4.4 4.8 5.8 4.2 5.1 5.4 4.4 5.3 4.2 4.4 5.3 5.1 4.2 3.6 4.5 3.8 3.9
##  [883] 3.9 5.6 5.4 5.2 3.2 3.2 2.5 5.4 6.3 4.9 3.9 4.8 6.0 4.3 3.5 5.2 6.0 4.3
##  [901] 2.6 3.5 4.6 3.3 4.7 4.9 5.1 6.8 3.9 3.4 5.3 5.5 5.4 4.3 4.1 4.1 3.7 6.8
##  [919] 4.1 5.8 3.4 4.8 5.6 2.8 2.9 4.7 4.9 2.7 3.5 4.5 4.8 4.2 5.6 5.6 4.6 4.5
##  [937] 4.1 4.2 4.4 4.7 4.6 4.1 5.4 5.9 5.3 4.8 3.6 5.7 3.7 3.8 4.2 5.4 4.1 4.3
##  [955] 3.8 6.0 6.3 4.1 5.2 4.2 4.9 4.7 3.5 5.0 5.1 4.0 4.1 3.6 4.6 4.3 3.7 4.5
##  [973] 5.0 4.9 4.0 4.9 5.2 4.3 4.3 5.5 2.9 2.2 3.9 3.0 4.4 4.2 4.7 3.6 4.4 3.7
##  [991] 3.8 5.8 4.1 6.2 4.3 3.9 3.3 3.4 4.6 3.9
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
##   2.8   6.3
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
##    [1] 4.0 4.2 2.9 4.2 3.6 4.2 3.9 5.3 4.5 6.2 4.0 3.5 4.7 6.4 3.7 4.9 5.8 5.2
##   [19] 4.7 4.7 3.9 3.3 4.4 3.6 5.3 5.8 4.3 5.4 3.3 4.9 2.4 3.9 4.3 4.3 3.9 4.9
##   [37] 5.0 6.0 4.2 4.4 6.2 5.1 3.5 4.1 4.3 5.6 4.3 4.6 2.9 3.6 3.5 3.5 4.8 3.8
##   [55] 4.9 3.4 5.0 4.1 4.5 6.2 4.8 3.4 4.7 5.1 5.0 4.4 4.1 5.9 2.3 5.4 4.5 3.3
##   [73] 5.2 2.7 5.0 4.5 5.9 5.8 5.2 3.9 5.7 4.9 4.6 3.8 5.0 5.5 5.6 5.4 3.8 3.4
##   [91] 6.1 4.0 4.2 4.0 5.0 6.0 6.3 5.3 5.0 6.1 4.6 4.7 4.4 2.7 5.2 4.8 4.0 4.3
##  [109] 4.6 4.4 4.0 6.1 4.6 3.7 4.8 4.4 3.6 5.0 2.6 5.2 3.7 4.0 4.4 4.7 5.9 4.8
##  [127] 4.9 5.1 4.3 5.2 4.5 5.5 4.3 4.5 3.4 5.1 2.2 6.5 4.0 3.1 4.0 4.8 5.9 4.7
##  [145] 5.8 3.7 3.4 5.0 5.2 6.4 4.7 6.1 4.0 4.4 3.3 2.8 5.0 4.5 5.6 5.2 3.5 4.2
##  [163] 4.0 4.8 3.5 4.8 4.2 3.8 4.7 5.7 5.6 5.1 4.0 4.3 4.7 6.1 5.8 4.3 3.8 6.4
##  [181] 6.1 3.2 4.6 4.6 4.0 4.3 4.0 5.1 4.2 4.9 4.8 3.7 3.6 5.0 6.1 4.5 3.9 3.0
##  [199] 4.4 5.9 3.9 4.7 4.0 5.4 5.3 3.9 6.4 6.0 4.8 4.2 5.8 4.8 6.0 5.6 4.9 4.7
##  [217] 4.4 5.5 3.5 5.0 4.5 5.0 4.2 4.4 5.8 4.0 3.6 3.0 4.3 4.6 5.1 4.8 3.9 4.1
##  [235] 2.9 4.0 4.9 5.9 3.3 5.4 4.9 4.5 3.0 4.4 4.0 2.8 4.3 3.2 3.2 5.3 5.9 5.5
##  [253] 4.9 3.5 3.2 5.4 3.9 6.2 5.4 4.9 5.0 3.0 5.5 6.0 4.5 4.1 4.0 5.7 3.5 4.4
##  [271] 4.2 5.0 4.6 3.6 4.8 5.3 4.1 5.7 6.0 5.6 4.7 4.8 3.7 4.4 6.7 4.9 5.4 4.4
##  [289] 4.6 4.6 6.7 5.9 4.0 6.0 4.7 4.4 5.6 3.8 4.4 5.5 4.9 4.3 3.7 4.9 3.9 6.0
##  [307] 5.3 3.9 5.2 3.3 3.4 4.6 5.4 3.4 5.0 4.7 3.5 5.7 4.0 6.0 2.9 4.3 4.6 5.7
##  [325] 3.7 5.2 4.7 5.6 4.4 3.7 5.0 4.4 5.6 4.9 6.2 4.8 6.0 5.2 3.8 4.7 4.5 4.9
##  [343] 4.4 6.2 4.7 3.0 3.4 2.7 4.8 4.1 4.7 4.6 5.0 5.6 4.3 3.7 4.1 4.7 4.1 5.4
##  [361] 4.9 3.5 4.3 3.9 5.1 5.2 4.6 6.1 2.3 6.9 5.3 3.1 4.9 4.5 4.5 3.5 3.3 3.9
##  [379] 4.8 3.4 3.5 4.2 3.7 4.1 4.8 2.3 4.7 3.7 4.5 5.2 4.4 5.3 5.1 3.5 4.0 4.3
##  [397] 4.1 5.2 4.7 5.0 4.8 3.3 4.0 4.4 3.8 4.6 5.1 6.0 4.4 5.0 6.0 3.9 4.5 5.8
##  [415] 6.3 4.8 3.3 4.2 3.4 4.0 4.3 6.5 4.8 5.5 3.5 3.5 4.1 2.7 4.7 4.4 5.6 3.8
##  [433] 5.0 4.6 3.8 3.7 4.3 5.9 4.3 4.5 4.8 4.0 3.2 3.5 5.5 4.1 3.5 5.3 5.4 5.7
##  [451] 5.8 3.3 3.8 3.3 3.7 3.9 5.7 4.2 2.8 5.0 5.7 5.0 5.7 3.9 4.4 5.3 5.4 5.3
##  [469] 3.2 4.6 4.1 2.6 3.6 4.0 4.2 3.8 4.9 4.7 4.0 3.6 3.7 6.1 6.0 4.1 3.2 4.1
##  [487] 4.4 3.0 4.1 5.1 6.1 2.7 4.4 5.0 3.8 5.0 3.9 3.9 5.5 5.6 4.4 4.5 3.6 3.8
##  [505] 5.5 4.7 3.0 2.6 5.4 4.1 4.2 4.5 4.5 5.0 5.1 4.9 5.2 3.5 4.3 3.1 4.6 3.9
##  [523] 5.3 4.0 2.6 3.8 4.8 3.2 3.6 4.2 4.1 4.8 4.1 3.4 6.3 5.3 3.5 5.1 5.5 4.7
##  [541] 3.2 4.7 5.6 4.5 4.7 4.6 3.0 3.6 3.8 5.4 5.5 3.7 4.1 5.3 5.5 4.8 4.6 3.2
##  [559] 3.2 4.4 4.2 3.3 5.0 5.3 2.6 4.4 4.3 4.7 3.8 4.4 3.5 4.3 3.0 4.9 4.7 5.3
##  [577] 6.2 4.2 4.2 3.8 4.7 4.3 5.0 4.0 2.7 4.3 4.7 5.7 4.5 4.2 4.2 3.1 3.8 6.1
##  [595] 4.7 4.4 5.3 3.7 3.7 4.8 4.3 3.7 4.1 5.4 3.4 5.2 4.2 5.6 4.8 4.1 3.7 5.1
##  [613] 4.5 4.3 4.3 5.5 3.4 4.1 4.7 4.5 4.4 4.1 4.8 3.5 6.2 4.1 4.2 4.8 4.7 3.9
##  [631] 6.3 4.8 5.3 4.3 5.5 3.8 4.9 5.8 4.7 2.7 5.2 4.7 4.8 4.3 4.6 4.9 5.0 4.9
##  [649] 5.0 4.1 5.3 4.4 4.0 4.3 3.7 5.7 4.8 2.9 4.2 5.6 4.0 5.5 3.5 5.9 5.1 3.8
##  [667] 5.5 4.9 4.3 4.1 3.7 4.0 5.5 5.4 3.3 6.0 3.9 4.9 4.4 5.5 3.1 3.1 6.2 5.0
##  [685] 4.2 5.5 5.4 4.4 4.7 4.7 3.3 5.0 5.7 3.3 3.8 4.6 3.6 4.7 4.1 4.2 5.6 4.9
##  [703] 6.1 4.7 4.5 4.9 4.9 5.1 5.6 6.7 3.8 4.9 4.8 4.2 6.0 4.6 4.8 5.2 3.9 5.5
##  [721] 4.7 4.3 3.8 4.7 5.1 6.1 5.0 4.4 5.3 4.0 5.1 4.0 3.0 4.7 3.2 3.7 4.2 4.3
##  [739] 3.8 3.0 3.3 5.3 4.8 3.4 3.4 4.3 3.4 6.0 4.5 3.9 4.8 4.9 3.9 5.0 5.2 4.7
##  [757] 3.4 5.6 2.8 1.9 4.7 5.9 3.9 5.4 5.0 4.7 4.4 4.5 4.6 3.2 4.1 4.5 6.3 6.2
##  [775] 3.9 5.4 4.4 3.6 4.1 3.6 3.9 4.2 3.4 4.3 3.8 3.8 2.7 4.9 4.4 5.0 5.0 5.8
##  [793] 4.8 3.6 4.9 5.2 3.2 6.1 4.6 5.5 5.9 3.5 3.6 4.3 5.1 5.8 4.1 4.0 3.5 4.2
##  [811] 3.3 3.9 3.3 5.0 5.0 5.0 4.9 3.5 3.4 4.2 4.0 4.4 4.9 5.9 5.0 4.3 4.9 6.0
##  [829] 5.0 5.1 5.0 3.4 3.2 4.1 5.4 4.8 3.0 4.7 4.3 5.0 5.5 5.3 5.8 4.1 2.0 6.3
##  [847] 4.0 2.8 4.3 4.9 4.7 5.3 5.6 4.8 4.9 4.5 4.4 5.2 4.9 5.0 5.1 4.7 5.3 4.0
##  [865] 4.9 4.0 6.3 3.8 4.4 4.6 5.0 3.9 3.9 3.5 5.2 3.5 3.2 2.3 4.5 6.0 5.6 3.8
##  [883] 5.0 3.4 3.8 6.0 5.4 4.7 5.4 5.1 5.1 4.7 4.0 4.2 3.1 3.6 5.4 5.2 4.2 4.7
##  [901] 3.5 4.9 4.7 5.1 5.8 6.9 4.7 5.4 3.1 4.1 5.5 3.8 5.6 4.9 4.6 4.4 3.3 3.5
##  [919] 4.1 4.4 5.0 4.1 4.9 4.1 4.8 4.5 3.9 3.8 5.3 4.3 5.1 2.8 6.3 4.7 4.9 3.5
##  [937] 4.0 3.9 3.2 5.4 4.5 4.9 6.4 4.5 5.6 4.6 3.6 5.1 5.5 3.7 4.5 4.3 3.9 5.6
##  [955] 6.0 5.5 5.3 3.7 4.8 4.9 6.1 3.9 3.5 4.4 3.8 3.7 5.7 4.3 2.5 5.6 3.7 3.3
##  [973] 4.9 4.5 5.0 3.3 5.3 4.5 3.6 5.0 4.9 4.6 5.8 5.5 6.4 3.1 3.4 2.2 3.2 4.2
##  [991] 2.8 4.0 5.0 3.1 5.3 4.5 3.7 5.4 3.5 4.6
## 
## $func.thetastar
## [1] 0.0213
## 
## $jack.boot.val
##  [1]  0.484883721  0.417027027  0.269637883  0.233236994  0.025000000
##  [6] -0.004907975 -0.149157303 -0.210619469 -0.367398119 -0.481303116
## 
## $jack.boot.se
## [1] 0.932096
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
##    [1] 3.4 6.5 5.8 5.6 5.6 4.0 5.1 5.4 5.2 2.5 3.7 5.0 5.2 5.1 2.7 4.4 4.8 3.9
##   [19] 6.4 3.9 4.1 3.3 2.7 5.7 4.7 4.5 4.0 3.6 3.8 4.1 4.4 4.2 2.3 5.1 4.8 4.4
##   [37] 4.5 3.4 4.9 4.8 2.6 4.0 4.1 6.1 4.7 4.9 3.5 4.4 4.7 5.5 3.0 3.9 4.6 4.3
##   [55] 5.1 4.2 3.4 4.5 4.9 4.7 3.2 5.9 3.0 6.9 4.6 4.4 5.1 5.2 4.5 5.8 4.3 4.8
##   [73] 4.2 5.3 4.2 4.0 4.2 4.2 5.3 5.0 5.5 6.0 4.3 3.9 4.1 3.6 3.4 3.1 5.8 4.7
##   [91] 4.4 4.9 6.6 4.6 4.0 4.2 5.1 4.0 3.7 4.7 5.5 3.9 3.9 4.3 5.5 5.7 5.2 4.1
##  [109] 5.3 6.0 3.1 4.6 4.0 2.9 4.4 4.9 4.4 4.7 4.1 5.6 4.6 4.5 5.0 4.3 3.3 2.8
##  [127] 3.6 4.6 4.3 4.5 3.8 4.2 4.0 4.5 3.5 5.6 4.0 3.1 4.8 3.3 3.3 6.7 3.3 3.5
##  [145] 4.3 4.2 4.5 4.7 4.6 3.8 3.5 4.7 4.1 3.8 4.2 4.7 4.8 3.3 4.7 5.2 3.4 3.5
##  [163] 4.9 4.3 4.2 4.7 6.2 5.3 4.8 3.7 4.5 4.1 5.3 3.9 4.3 4.3 4.0 5.1 5.6 5.2
##  [181] 6.5 4.4 4.0 3.3 6.2 4.0 4.3 3.5 5.0 5.3 4.6 3.4 6.1 3.7 4.3 3.6 3.7 6.2
##  [199] 5.4 4.7 3.3 4.9 3.7 3.7 5.3 5.7 4.9 3.9 4.2 4.5 4.6 4.8 4.1 4.3 4.9 4.9
##  [217] 3.1 5.4 4.2 5.2 4.2 5.1 4.0 3.3 2.7 4.4 5.2 5.1 3.5 3.3 2.2 5.0 5.2 5.6
##  [235] 3.5 5.2 4.2 6.1 4.2 3.8 4.4 5.2 4.8 4.7 4.5 4.1 4.8 2.9 4.9 3.4 5.2 4.6
##  [253] 4.8 2.9 4.7 5.3 5.2 4.7 4.1 5.2 4.8 4.1 3.0 4.3 3.0 4.2 3.3 3.9 5.3 3.9
##  [271] 3.0 5.0 3.5 4.6 4.5 3.4 4.8 5.6 4.6 6.1 4.6 4.8 4.6 4.9 5.9 3.5 3.9 3.1
##  [289] 3.6 4.0 4.1 5.0 4.4 4.8 4.5 5.1 3.8 4.2 4.1 6.4 4.9 4.0 4.1 3.2 5.1 2.7
##  [307] 4.0 6.7 5.0 4.0 3.4 3.3 4.8 4.1 3.8 3.1 3.7 5.2 5.6 3.3 3.4 3.8 5.3 4.8
##  [325] 4.2 4.7 5.6 2.9 3.8 4.6 3.5 5.0 4.5 6.2 4.7 4.5 5.2 2.9 4.7 3.8 4.0 5.6
##  [343] 5.8 5.0 5.4 5.2 3.5 3.9 4.7 3.4 4.5 2.9 3.8 4.5 5.7 3.8 4.1 5.9 5.2 5.5
##  [361] 2.1 4.2 2.7 5.1 5.5 4.3 4.7 1.9 3.6 3.7 4.2 3.9 4.0 4.0 2.8 3.4 3.9 3.2
##  [379] 4.1 3.8 4.5 6.1 5.2 6.3 4.2 5.8 5.3 3.4 5.2 5.4 5.5 4.0 4.7 4.1 4.5 4.4
##  [397] 3.5 4.5 4.5 4.0 3.8 5.0 4.3 5.2 4.2 5.1 3.9 2.8 3.9 4.4 6.2 4.0 5.4 4.1
##  [415] 3.1 5.0 4.6 3.4 3.9 4.8 5.1 4.1 4.0 5.2 4.8 4.9 5.0 5.9 4.1 5.0 3.7 3.5
##  [433] 4.5 4.7 3.4 4.1 5.1 3.2 5.0 5.9 5.3 3.3 4.6 4.9 4.7 5.6 3.7 4.0 4.5 4.6
##  [451] 4.5 6.6 4.5 5.0 5.2 4.1 5.0 5.4 4.6 4.9 3.9 5.0 4.4 2.8 5.1 2.8 4.8 4.4
##  [469] 3.8 5.4 4.9 4.6 6.5 4.9 4.5 4.4 3.6 5.1 3.4 4.3 4.6 5.5 5.1 5.5 4.5 6.0
##  [487] 5.1 7.1 4.5 5.0 3.9 5.2 3.5 4.2 4.9 4.4 5.1 5.0 3.6 4.3 4.5 4.7 4.0 5.1
##  [505] 5.0 4.2 4.5 4.6 5.6 3.5 4.7 3.8 3.8 5.1 4.5 5.1 4.5 2.6 5.4 3.2 3.8 4.1
##  [523] 4.5 5.2 4.8 5.2 7.0 5.2 3.8 4.6 5.6 3.0 3.3 4.6 4.0 3.8 3.6 5.4 5.1 4.2
##  [541] 4.8 5.0 3.9 3.9 5.4 4.1 4.5 3.6 4.8 6.0 4.1 4.4 6.7 2.7 5.0 4.8 5.2 4.4
##  [559] 4.5 5.5 3.8 2.5 5.4 4.3 4.4 4.2 4.6 4.1 4.0 3.9 2.8 6.0 5.3 3.9 3.7 4.2
##  [577] 4.6 4.2 4.2 3.1 3.7 4.4 3.9 3.8 3.7 4.0 5.8 3.6 4.1 5.0 4.0 4.7 5.1 2.2
##  [595] 4.9 5.0 4.4 4.5 5.3 3.3 5.9 6.1 4.2 3.9 5.4 2.9 4.9 4.0 4.6 5.2 5.0 3.5
##  [613] 3.4 2.3 4.3 4.0 5.2 3.3 4.7 4.9 4.2 4.7 5.3 3.8 4.9 3.8 6.1 5.1 4.3 5.3
##  [631] 4.1 4.0 5.2 3.9 5.5 5.2 3.1 3.9 3.9 5.8 5.4 3.8 5.3 4.2 4.5 5.4 6.3 4.5
##  [649] 3.3 4.5 4.3 4.7 5.2 4.4 4.7 6.2 4.1 3.9 4.6 3.8 4.0 5.0 4.7 5.6 4.6 4.4
##  [667] 3.3 4.2 3.3 3.9 5.4 5.0 4.6 3.5 4.8 4.2 4.3 5.0 4.7 5.3 6.6 4.0 5.5 5.0
##  [685] 5.6 3.4 4.7 5.4 3.8 4.1 3.9 4.8 4.2 4.7 5.1 4.2 5.0 4.8 3.7 3.7 4.7 2.9
##  [703] 4.4 4.1 3.1 7.0 4.2 4.6 3.8 4.5 4.8 5.1 3.6 5.0 5.4 5.2 4.5 5.3 4.9 3.6
##  [721] 4.7 4.0 4.9 5.7 4.3 5.0 5.3 5.2 4.5 5.5 3.1 4.5 5.6 5.4 4.1 3.6 4.3 3.8
##  [739] 5.8 4.0 4.7 5.1 5.1 3.3 5.2 3.0 5.7 4.0 4.9 6.4 5.5 4.3 3.2 4.5 3.8 4.4
##  [757] 4.7 4.1 6.8 4.3 3.6 5.2 4.2 4.7 4.4 5.1 4.2 5.4 2.7 3.3 4.8 4.4 4.9 4.0
##  [775] 4.9 5.9 4.3 4.2 4.1 3.8 5.6 5.7 4.7 5.7 4.6 5.5 2.9 3.6 5.9 5.1 2.5 4.5
##  [793] 5.6 5.5 3.8 3.7 4.4 5.4 4.1 4.7 4.8 5.1 2.8 5.6 3.7 4.6 4.4 6.5 3.6 5.1
##  [811] 4.6 5.5 4.9 5.1 3.7 4.3 3.5 3.6 3.8 4.1 5.6 5.3 3.8 4.4 4.7 3.9 4.3 4.8
##  [829] 4.3 4.5 4.4 2.3 5.3 5.3 2.6 4.7 6.5 3.9 4.4 3.2 5.1 4.0 3.8 4.6 3.3 5.0
##  [847] 5.0 2.7 4.1 5.1 2.9 6.3 3.6 4.5 4.4 3.5 5.4 4.4 6.0 7.1 5.6 4.7 3.3 5.7
##  [865] 4.9 6.6 5.3 5.6 2.2 5.6 4.8 4.4 3.9 6.3 4.5 5.0 4.9 4.3 4.4 5.2 3.9 5.0
##  [883] 4.1 4.1 4.2 4.0 4.9 4.2 4.5 5.0 4.1 5.4 4.1 4.2 4.6 4.4 3.6 4.9 5.1 4.8
##  [901] 5.1 3.4 4.8 4.1 4.2 4.0 2.5 5.6 4.9 5.3 4.2 4.3 4.7 5.1 4.8 5.2 3.8 4.2
##  [919] 3.4 5.2 3.7 4.5 5.3 3.5 4.0 4.1 3.8 5.0 6.0 4.1 5.4 2.6 3.5 4.2 4.6 5.0
##  [937] 5.1 4.7 5.5 5.1 4.2 4.3 3.6 4.8 5.4 5.4 4.6 7.2 5.3 5.2 3.1 4.1 4.2 4.7
##  [955] 4.3 3.5 4.2 4.5 4.9 3.3 3.9 2.5 5.5 4.1 5.3 4.6 4.0 4.3 5.4 4.1 4.3 3.4
##  [973] 4.9 5.6 3.8 4.8 5.1 5.7 5.5 5.2 4.9 4.3 5.7 3.0 4.8 3.6 4.4 5.5 4.7 4.4
##  [991] 6.0 4.3 4.0 5.9 5.7 3.5 3.5 2.1 5.5 4.1
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.400 5.256 5.200 5.200 5.000 5.000 4.900 4.700 4.700 4.400
## 
## $jack.boot.se
## [1] 0.8744279
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
## [1] -0.7715067
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
##     shape       rate  
##   4.210772   6.867410 
##  (1.813453) (3.141231)
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
## [1] -0.56336216 -0.14996942 -0.50807965  0.38192182  0.08039893  1.59200084
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
##    [1] -0.1163127647  0.0550960565 -0.8210591439 -0.3173515674  0.5496474165
##    [6]  0.4700353880 -0.5062190352  0.5301765141  0.4437689047 -0.9526059711
##   [11] -0.6706346606 -0.5853802688  0.5098755459 -0.9518463653 -0.9877232297
##   [16] -1.2013243548  0.3209726018 -1.0278901536 -0.1167829240  0.1774013385
##   [21] -0.0300215166 -2.0535312429 -1.3093545279 -0.0800834450 -0.3582815559
##   [26] -0.6265737779 -0.2735646873 -0.4677444189 -0.9443692810 -0.5812080923
##   [31]  0.8791505226 -1.0826001661 -0.9675730915 -0.8012343992  0.0414706700
##   [36]  0.2226739151 -1.2779307298 -1.4076110938  0.6164912931 -0.3058718693
##   [41] -0.9171540813 -0.0353154191 -0.3263738748 -1.0202046176 -0.7982497589
##   [46]  0.4929354396 -1.0550255753 -0.0609620207 -0.5308288052 -0.6417429446
##   [51] -1.0569828200 -0.3769432271  0.0245795787 -0.7386208246 -0.2959180925
##   [56] -0.2819667782 -0.7736502174 -0.4675057657 -1.1302307507  0.3034476018
##   [61]  0.3817355591 -0.0909637006  0.0741864172 -0.4803116846 -0.4461855930
##   [66] -0.5269018558 -0.8521761490 -0.6159307921 -0.8575854196 -0.3093866220
##   [71] -0.7018152833 -0.7839426184 -0.6109234102 -0.1085247657 -0.5928894024
##   [76] -0.1841446030 -0.6322197933 -1.1197579477 -0.1985538577 -1.4833765126
##   [81] -0.5193998994 -1.4204342234 -0.1167829240  0.4145518117 -1.4101102741
##   [86] -0.6187184056 -0.5825519924 -0.2416875844 -0.1798654688 -0.9898274260
##   [91] -0.2130137140 -0.5768664383 -1.2896331887 -0.5717925180  0.1011251776
##   [96] -1.2414225418  0.1424346650  0.2676362957 -0.7330352872 -1.4168523171
##  [101] -0.7628940377 -0.8636457031 -0.8879068378  0.0168548777 -0.3205629633
##  [106] -0.1314352579  0.3231394477 -0.7568999642 -0.7699970182 -1.6756581545
##  [111] -0.8259778751 -0.8003643913 -0.6085781354 -0.4359060673 -0.1097624226
##  [116]  0.1315659275 -0.3153939895 -0.5520924604 -0.0029132596 -0.0550748193
##  [121] -0.3446604913  0.3052968849 -0.5793079502 -0.1378550747 -0.3193863255
##  [126] -0.7286626178 -0.7212344706  0.6953197629  0.3375061332 -1.2278073465
##  [131] -0.3608601120  0.0114146335 -0.7632986710 -0.6111067788 -0.4871066726
##  [136] -0.9514759822 -0.6873169710  0.1226183025 -0.4150717635 -1.0821230093
##  [141] -0.5171838724 -0.1772193639  0.0350638423  0.0401882527 -0.7829105079
##  [146]  0.0366348078  0.2884689521 -0.9303348753  0.0133385380 -0.3455964874
##  [151] -1.0207810920 -0.4934418448 -0.8293050894 -0.1169677764 -0.3341196722
##  [156]  0.1400585614 -1.0146699235 -0.2739694010 -0.5658566419 -1.0807494198
##  [161] -0.6371366108 -0.3908836662  0.9267107232 -0.2224999712 -0.2699269057
##  [166] -0.5922912571  0.0921282862 -0.9051950954  0.2703852362 -1.1263222410
##  [171] -0.8799644608 -0.4027165653 -0.4475339266 -0.6239393616 -0.4436228969
##  [176] -0.0329784113 -1.3065140912 -0.0608352465 -0.5437772485 -0.4946354323
##  [181] -1.0835433943 -1.1504043184 -0.2697037049 -1.9542170500 -0.5981249649
##  [186] -0.7567279147  0.1714296688  0.0453760714  0.0498534556 -1.2595067558
##  [191] -0.5369747375 -0.5164546091 -0.8988193746 -0.7721224617  0.4969665533
##  [196] -0.7929767243 -0.6507061453 -1.0249242328 -0.4705872017 -0.3213062240
##  [201] -0.7650582489 -0.6527560606 -0.3181587521 -0.5671928154 -0.0491374911
##  [206] -1.5661926028 -1.7935031388 -0.4943942085  0.3400628399 -1.0580836049
##  [211] -0.5861421386 -0.8299085522 -0.9431373663  0.0500455389  0.6806246412
##  [216] -0.7813556127 -0.8531722134 -0.3737370832 -0.8464605913 -0.8086744665
##  [221] -0.5839360023 -0.2739967649 -0.9272840161  0.0128867883 -0.1588146261
##  [226] -0.3878047845 -0.5363067393  0.6212701028 -1.4367743092 -0.3003258915
##  [231] -1.1168444546  0.0438945898 -1.0902961421 -0.4687268656 -1.2996095288
##  [236]  0.2314166611  0.1513180448 -0.3419550015  0.0856384654 -0.2615097483
##  [241] -0.5794786208 -1.2442776268 -0.5758592866 -0.5810823650 -0.6605409580
##  [246] -0.0207769319 -0.6817239656 -0.3397781726 -1.5172672138 -0.5516005921
##  [251] -1.2127947954  0.7591560118  0.0946891487  0.8032748845 -1.2207212477
##  [256] -1.2162383989 -1.5917961649 -0.6244829936 -0.2652148441  0.7019382485
##  [261] -0.8949805861 -0.0317053204  0.2742829558 -0.5008206806 -1.1094917929
##  [266] -0.9353596496 -0.4330161511 -1.6383678752 -1.4735100943 -0.5843490903
##  [271]  0.0254194079 -0.6154415764 -1.1241435786 -0.6566410093  0.0880688810
##  [276] -0.7997332225 -1.3948111598 -1.1341615465 -0.2356658853 -0.6596272274
##  [281]  0.6122413799 -0.0876216495 -1.2549719399 -0.0439702882 -0.4124519978
##  [286] -0.3620937275 -0.0007859118 -0.8740365910 -0.6248174056 -0.0532309660
##  [291] -1.1396825387 -1.2662916576  0.2877389983 -0.6233608214 -0.3586486099
##  [296] -0.5497427712  0.4545929796 -0.7406866488 -0.9897575149  0.0465750224
##  [301] -0.1823484082 -0.3524247865 -1.1024718365 -0.1251324154  0.1872420154
##  [306] -1.2223451979 -0.5004759070  0.0378871813 -0.4374351654 -1.4165679355
##  [311] -1.2040000725 -0.5203087732 -0.8454688039 -1.4338051324 -0.1849167631
##  [316] -0.2470162771 -1.1330689042 -1.1253332572  0.1015355916 -1.6922902663
##  [321] -0.2968328959  0.0793194230 -0.1695749932  0.1792054257  0.2025100600
##  [326] -0.7623168561 -1.2497648116 -0.2116194825 -0.8825269178 -0.7637708757
##  [331] -0.3830545892 -1.1623221094  0.0390176312 -1.1515470227 -0.7518975960
##  [336]  0.1538502160 -0.1933227134 -1.0241147761 -0.7740444550 -0.6600285665
##  [341] -0.3051020573 -0.6503928683 -0.6301544496 -0.1007181344 -0.0252317866
##  [346] -0.3263738748 -0.3019259492 -0.2110830554 -0.4637886336 -0.2724745463
##  [351] -0.3562221597 -1.6696349534 -1.6558984148 -1.2669073483  0.8794506189
##  [356] -0.3936756976 -0.5649700609 -0.0439702882 -0.5171408235 -0.2565433222
##  [361] -0.4460170436 -1.2236299416 -0.7526723858 -1.3705270728  0.8848108202
##  [366] -0.3663610826 -0.6448235722 -0.6488283814 -0.3890482783 -0.6325466432
##  [371] -0.6433906983 -0.7119820029 -0.6910361837 -1.1416862723 -0.1476924262
##  [376]  0.0486055563 -1.4545550397  0.9195321635 -0.3819426490  0.8471762348
##  [381] -0.6812705640 -1.5372254884 -1.0472898602 -0.7542262651  0.2991928152
##  [386] -0.5546212788 -1.8762357604 -0.4134192213  0.7089770400  0.2149551826
##  [391] -0.6142142593 -0.6810518887 -0.1907478744 -0.5019203955 -0.0882856664
##  [396] -0.9610255639 -0.8407553609  0.2768759576 -0.6548951472 -1.4602974861
##  [401] -0.7161158040 -0.4893838134 -0.7829467219  0.2617506894 -0.7819194134
##  [406] -1.0425154903 -0.9853394031  0.2592375650  1.1089909682 -1.0072313711
##  [411]  0.0084374425 -0.1703895337 -0.6207477602  0.6647756781 -1.0505842032
##  [416]  0.1503722055  0.1928696026 -0.6301618613 -0.8554168280  0.0831370765
##  [421] -0.6577332679  0.5948203705 -0.6234552491  0.2346405689 -1.4204414660
##  [426] -0.0745874933 -0.5068573922 -0.8146423944  0.7033107415 -0.2309100302
##  [431] -0.3900167050  0.0974543348  0.1054807533 -0.8376371534 -0.4872971246
##  [436] -0.0876216495 -0.2538469604 -0.5963768297 -1.1111236612  0.0447371706
##  [441] -1.0749729952  0.0086213853 -1.6437885014 -0.2802267297  0.0386932562
##  [446]  0.1008085933  0.0189124085 -0.2683418338  0.2019211673 -0.5788075163
##  [451]  0.1940144250 -0.1593079897 -0.5425542791 -0.4603277872  0.0286671834
##  [456] -1.2161081154 -0.5216154152 -0.6424321281 -0.9166403424  0.1614411536
##  [461] -1.1886597182  0.2636881402 -0.3639067683 -2.1253194163 -0.3674544187
##  [466] -1.2571978787  0.3005726895  0.2026837455 -0.8394969316 -1.2258806133
##  [471] -1.8295844155  0.3898014867 -0.0719392211 -0.3839285543 -0.3190505026
##  [476] -0.7414223684 -1.3593256881 -0.6338219698 -1.0414102689 -1.3014246132
##  [481] -0.7600878269 -0.5559917046 -1.7119851090 -0.7514479471 -0.3851841290
##  [486]  0.2418262896 -0.8879068378 -0.9608689155 -0.8314916336 -0.2136056484
##  [491] -0.2986224651 -0.0949586076 -0.6028698438  0.0723490796 -0.2625988075
##  [496] -0.7134442676 -1.0341202122 -0.8554168280  0.4704260000 -0.6566410093
##  [501] -0.6728693287 -0.9606975381 -0.5169340103 -0.4705872017  0.5951587870
##  [506] -0.8444654071 -0.9542158493 -0.9132217630  0.6212979522 -0.8718787267
##  [511] -0.5954471717 -0.3543136149  0.9313212313 -0.5203133015 -0.0593792416
##  [516] -0.3649927367 -1.0181042444 -0.1730658602 -0.3047160920 -1.0161283927
##  [521] -1.0955314239 -0.1934346844 -1.1784795908 -0.9773047264 -1.2447333384
##  [526] -1.4085547853 -0.8333342610 -0.3918955446 -0.9791508928 -0.3120533637
##  [531] -0.7554851841 -0.5333167539 -0.8846562449  0.3277749208 -1.1838311122
##  [536] -0.3538398640 -0.2616738293 -0.7432868579 -1.6209338065 -0.7514015994
##  [541] -0.8773061589 -0.7662079566  0.2003088154 -0.3169860152 -0.6812343462
##  [546] -0.2929953364 -0.2650614983 -0.7368142534 -0.9220074155  0.0379994721
##  [551] -0.5869711362 -0.9819462328 -1.5469119415 -0.5087554646 -0.1409747386
##  [556] -0.5760527933 -0.6136756990  0.2869929518  0.0517969469  0.1226175414
##  [561] -1.0197842532 -0.3527419600  0.2611227911 -0.2351447796 -0.0204705896
##  [566] -0.7622092919  0.0071126880 -0.8747687477 -0.8775934070 -0.2677594618
##  [571]  0.0248813105  0.3726536905 -0.2488650408 -0.7492763041  0.3756153974
##  [576] -1.1719543317 -0.3413354112 -0.9127482001 -0.4675057657 -0.3857285670
##  [581] -0.4634331140 -0.9677433881 -0.0312207549 -0.9095793384 -0.8332175146
##  [586] -0.2167495962 -0.8689373413 -0.7293033814 -0.8251584407 -0.3097595801
##  [591] -0.5071540331 -0.7149544017 -0.3835312779 -0.5355800014 -0.2470162771
##  [596] -0.7853745648  0.2269178573 -0.6586565821 -1.0244592699 -0.6473410075
##  [601] -0.5028715189 -0.9043272976 -0.5028715189  1.3371699298 -1.4253386770
##  [606] -0.7150191144 -0.5779744151 -1.1302307507 -0.5649700609 -0.6090671757
##  [611] -0.6371140890 -1.1674036227  0.1465745368 -0.1703895337 -0.5869711362
##  [616] -0.9548079688 -0.3894717511 -1.0589956904  0.0105288393 -0.6244829936
##  [621] -0.4119010678  0.1640102517 -0.3060297168 -1.8994139851  0.5929938382
##  [626] -1.4673413338 -0.0598449987 -0.4412351157 -0.4527204064 -1.2436435813
##  [631] -0.7897983057 -0.0719392211  0.2952701581 -0.8383993633  0.3193862113
##  [636] -0.2530593285 -0.1165468086  0.2145088671  0.9206999754 -1.0854286344
##  [641] -0.8319113797  0.8311289723 -0.5112479359 -0.6863491818 -0.6442614461
##  [646] -0.4848443417 -0.9317868190  0.6108483204 -0.6119978579  0.0493778106
##  [651]  0.1467827702 -0.4841467201 -1.1555620306 -1.6410641897 -0.2391252266
##  [656] -0.8544054424 -0.4590572834  0.2114015525 -1.4137491839 -0.6676019966
##  [661] -1.2855265123 -1.1110771855 -0.5019203955 -1.3945387023 -0.2425274318
##  [666] -0.8879859314 -0.1154833743 -1.3462127917 -0.7540656523 -0.2470847870
##  [671] -0.2328032535 -0.2447740779 -0.9425569737 -0.2623598297 -0.8689721728
##  [676]  0.6415583381 -0.8263444656 -0.5599964937 -1.2699448824  0.0873691288
##  [681] -0.6574456484 -0.4306378689 -0.3582384128 -0.7922545884  0.6077552806
##  [686] -0.4813304371 -0.7247045836 -0.2883908668 -0.3471127285 -0.0175335198
##  [691] -0.9160521221 -0.6335392580 -0.6910721018  0.1952841411 -0.5252510372
##  [696] -0.3520465432 -0.4249396967 -1.2139897280 -0.3675099044 -0.8324604753
##  [701] -0.5606388703 -0.7747587231 -0.7058551717 -0.5487217744 -0.5706743769
##  [706] -0.8227370645  0.1574447486 -0.1527135162 -0.5223733818 -0.1567184593
##  [711] -0.9820978053 -1.6279571613 -0.3118099612 -0.7371331043 -0.4902254607
##  [716] -1.3797442799  0.2304871903 -0.4989357425 -0.4814358107 -0.5482315982
##  [721] -0.3778358868 -1.0205451921 -0.2156118956 -1.1253288209  0.9238903988
##  [726] -0.7841242291 -0.2094964049 -0.8196702558 -1.0393619298 -1.0359826916
##  [731] -0.6408739211 -0.4820567945 -0.7509066777 -0.6534614840 -0.8334243480
##  [736] -0.7126545818 -0.8148065928 -0.9831938977 -0.5636498292  0.0128453423
##  [741] -1.0559793930 -0.5574655444 -0.1404215416 -0.5671928154 -0.0246342046
##  [746]  0.1329065309 -0.7939742680  0.2735500195 -0.3679002754 -0.4550738949
##  [751] -0.8419749262 -1.0984431498  0.2446343195 -1.2003760769 -1.1911864376
##  [756] -0.1929561260 -0.7600280043 -0.5142519042 -0.4229531922 -2.3268237173
##  [761] -0.1145457666  0.6372328188 -0.1455135786 -0.8843301460 -0.3094921172
##  [766] -0.8762040486 -0.3432806231 -0.7589102678 -0.0708789395 -0.4806931987
##  [771]  0.3460025510 -0.7787526485 -0.6361356730 -0.8530064605  0.3539012798
##  [776] -1.0126938294  0.2111699032 -0.7031822909 -0.4711758122 -0.3655065670
##  [781] -1.4678364475 -1.2663797419 -0.9843146437 -0.5773769207 -0.3529084260
##  [786] -0.7816520521 -0.2350627221  0.0741864172 -1.4696440179 -0.2829253080
##  [791]  1.0847717263 -1.3769366401  0.6595463503 -0.3741091584  0.1810526507
##  [796] -1.0889177172 -0.1410204801 -0.7857359629 -1.0877967502 -0.3267301093
##  [801] -1.7021879333 -1.0225332055  0.3709694515 -0.5278541127  0.2545855502
##  [806]  0.3819303952 -0.7553505021  0.2072470208 -0.8740365910 -0.2899465516
##  [811] -0.1496565694 -1.4696440179 -1.0276466247 -1.2102236797 -0.5019585673
##  [816] -1.2108076081  0.1949087880 -1.1286646219 -0.7098898158 -0.4802236859
##  [821] -1.5789965373 -0.1476805191 -0.7311855034 -1.1047723628 -1.3462127917
##  [826] -0.6248949526 -1.0264956457 -0.2821209062 -0.3938357460 -0.4983655798
##  [831]  0.3642068420 -0.1338253156 -0.5014549969 -0.8462429742 -0.8838405761
##  [836] -0.0850584932 -0.5910819619 -0.4603210808  0.7532848995 -0.1532483936
##  [841] -0.3003175721 -1.1925499574 -0.2758842539 -1.5188687213 -0.4669450971
##  [846] -0.1702572894 -0.2016028617  0.0988993789 -1.7076648889 -0.1912862582
##  [851] -0.5649507799 -0.8382075843 -0.7152041336 -0.9302863232  0.3809654116
##  [856] -1.0996987687 -0.7715066993 -0.4590372415  0.5070412593 -0.0765040728
##  [861] -1.2546232416 -1.8290460269 -0.8817787185  0.4691002996 -0.1994393826
##  [866] -1.3034571655 -1.1855186783 -0.2141579953 -1.1751218653 -0.6044844985
##  [871] -0.2533241565 -0.6697749488 -0.3878048080 -0.6678842455 -0.8776231766
##  [876] -0.5966353534  0.3752922244  0.4208185203 -0.6264024292 -0.6120735937
##  [881] -0.5303170333 -0.8475217955 -0.4707832197 -0.6974408490 -0.7671315478
##  [886]  1.3812802222 -0.9548937540  0.7622927637 -1.1037879287 -0.6861463710
##  [891] -0.7484615498 -1.2315373717 -1.3731803671  0.0540457935  0.7658798905
##  [896] -0.5516730974  0.2806374661  0.5227707929 -0.3284420531  0.3976668782
##  [901] -1.0716074079  0.6671142661 -0.2924149973 -0.8006470199 -1.0670188244
##  [906] -0.7572716696 -0.6888539756 -0.8263340769 -0.2267809111  0.0720955547
##  [911] -0.0406291940 -0.9980305545 -1.0696687184  0.1987437713 -1.2831320543
##  [916] -0.5909073345 -0.1600554358 -0.4373183901 -0.2701474728 -0.5549366773
##  [921] -0.2113847885 -0.9690475362  0.1367191620  0.2585582577 -0.6595270744
##  [926] -0.1036061553  0.2092051446  0.2114015525 -0.0999756482  0.7499026291
##  [931] -0.3598276611 -1.5257004544 -0.2152565580 -0.6285216289 -0.8920766237
##  [936] -1.0284302123 -0.3871603023 -0.4069940577 -0.4990328028 -1.4599673485
##  [941] -1.3806332101 -0.5339908386 -1.0757434505  0.2540895058 -0.7887243873
##  [946] -1.0095677009 -1.3446265720  0.3073881056 -0.3455725491 -0.3035814919
##  [951] -0.5149384413 -0.7830390565 -0.9758374467 -0.2964190766 -0.9722330410
##  [956] -0.4800862895 -0.3749860268  0.1866237612 -0.7329879109 -0.5557156859
##  [961]  0.1237448664  0.9317274620  0.4313454176 -0.2583450744 -0.5418319656
##  [966] -0.1593079897 -0.3415945236 -1.2136166341 -1.1791863006  0.2610603152
##  [971]  0.7530734462 -0.9764781374 -0.9110685722 -0.2819301820 -0.5722462075
##  [976] -0.2602896801 -0.5729862849  0.2032929973 -0.9008640542 -0.7841197707
##  [981] -0.0067859021 -0.5889520417 -0.9859284367  0.0962466407  0.3291055286
##  [986] -1.1361747844 -0.4721122495 -0.4593202236 -0.9598323343  0.1492507748
##  [991]  0.0381710455 -0.8877601587 -0.5957199236 -0.8797452622 -1.1491833264
##  [996]  0.3424470218 -0.1779295988 -0.6078466857  0.6647229500 -0.9082914396
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
```

```r
fit2
```

```
##       mean          sd    
##   0.61314759   0.22654455 
##  (0.07163968) (0.05065195)
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
## [1] -0.1578480 -0.4689677 -0.3383851 -0.6251143 -0.4434994  0.7914003
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
## [1] 0.0455
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9071729
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
## t1*      4.5 0.01611612   0.9211664
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 1 3 4 5 7 8 9 
## 1 1 1 3 1 2 1
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
## [1] 0.003
```

```r
se.boot
```

```
## [1] 0.8757728
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

