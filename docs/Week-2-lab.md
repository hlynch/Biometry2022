Week 2 Lab
=============

Confidence intervals
-----------------------

Before getting too far, we need to formally define a confidence interval. 

A 95th percentile confidence interval say “If I repeat this procedure 100 times using 100 different datasets, 95% of the time my confidence intervals will capture the true parameter”. It does NOT say that there is a 95% chance that the parameter is in the interval.

**Quiz time! (Don't worry, not a real quiz)**

*Important note*: This is an area where Aho is **WRONG**. Aho is correct on only one point. It is true that *once the 95th CI has been constructed*, it is no longer possible to assign a $\%$ to the probability that that CI contains the true value or not. Because that CI, once created, either DOES or DOES NOT contain the true value. However, we often talk about the interval in the abstract. **<span style="color: orangered;">When we say "There is a 95$\%$ chance that the interval contains the true value" what we mean is that there is a 95$\%$ probability that a CI created using that methodology would contain the true value.</span>**

Do not let Week 2 pass by without fundamentally understanding the interpretation of a confidence interval. 

So now we know the general idea behind confidence intervals but we don't yet know how to calculate them. To do that, we'll actually walk through an example of bootstrap using pennies. Each of you should have gathered the ages of ten pennies. (If a penny was made in 2021, that penny would be 1 year old, etc.)

*Data*: 10 pennies that the students have

*Test statistic*: Median

Lets say we are trying to find the median age of all pennies in circulation. We can't figure this out exactly, because we can't collect all the pennies in circulation, but we each have a sample of 10 pennies. The median age of the pennies in our sample is a reasonable estimate for the median age of all pennies in circulation. 

What is our uncertainty about that number? How far might our estimate of the median age be from the true median age? In this case, we don't know the underlying distribution of penny ages. (Let's brainstorm this for a bit. Do we have any guesses what this distribution might look like? What might be a reasonable distribution to describe the shape of penny age?) 

Let’s use bootstrapped samples to calculate the s.e. associated with that estimate.

Procedure: 
1. Sample WITH REPLACEMENT a group of 10 pennies. (To sample with replacement you will have to sample one penny, write down the age, and repeat that 10 times.)
2. Calculate the median age from that sample of pennies.
3. Repeat

Do this a few times with your actual physical pennies, and then once you get the idea, you can make a vector in R of your penny ages and use R to speed up the sampling. Don't forget to sample with replacement.

Gather a collection of 100 median estimates, each one calculated using a different bootstrapped dataset. Calculate the Bias and the Variance of the estimator for the Median.

We now want to take this one step further and estimate the confidence intervals for the median age of a penny in circulation. We actually have two primary mechanisms for generating confidence intervals for the statistic.

**Method #1**: Simply take the quantiles directly from the distribution of $\hat{\theta}^{*}$:

$$
\theta_{LL} = \mbox{2.5th percentile of } \hat{\theta}^{*}
$$
$$
\theta_{UL} = \mbox{97.5th percentile of } \hat{\theta}^{*}
$$

Notice that (by construction) 95$%$ of the $\hat{\theta}^{*}$ values fall in the interval $(\theta_{LL},\theta_{UL})$. <span style="color: orangered;">This is the very definition of the 95th percentile confidence interval.</span>

**OR** 

**Method #2**: We can use the Normal approximation:

We have a second method that won't make 100\% sense until next week, but it turns out that if we assume the bootstrapped estimates follow a Normal distribution, 

$$
\hat{\theta^{*}} \sim N(\hat{\theta},\hat{se}^{2})
$$

we can use the fact that the 95th percentile confidence interval is approximately given by:

$$
\hat{\theta}_{LL}=\hat{\theta}-1.96*\hat{se}
$$
$$
\hat{\theta}_{UL}=\hat{\theta}+1.96*\hat{se}
$$

It turns out that 95$\%$ of the probability for a Standard Normal distribution lies between (-1.96$\sigma$,+1.96$\sigma$). (We will show this more formally next week.) 

NB: If you are going to go through the trouble of doing the bootstrap sampling, I don’t know why you would make a Normal approximation at the very end to construct the CIs. I recommend Method #1.

**<span style="color: green;">Checkpoint #1: Use your penny data to calculate the 95th percentile confidence interval using Method #1 and Method #2. What did you get?</span>**

Testing hypotheses through permutation
------------------------------------

These examples use data on the speeds of the top 20 racing pigeons from a race in Alma, GA on February 7,2021. 

**Example #1**: Use permutation methods to test whether Cock or Hen birds fly at different speeds (speeds are in meters-per-minute) (in other word: $H_{0}$: No difference in speeds between the C and H groups):

C=$\{1359.8,1355.3,1355.1,1353.0,1349.8,1348.8,1345.2\}$

H=$\{1357.5,1356.4,1355.1,1353.5,1353.2,1352.5,1350.0,1349.8,1346.2,1344.9,1344.4,1343.9,1342.6\}$

**<span style="color: green;">Checkpoint #2: Is this a one-tailed or a two-tailed test?</span>**

Make sure that you understand what is being done here, as this example is very closely related to the problem set.


**Example #2**: Using the same data, provide a 95% confidence interval for the difference in mean speed based on 1000 bootstrap samples

Note that these two approaches are very closely related. Do you see why either approach can be used to test the null hypothesis? **<span style="color: green;">Checkpoint #3: What is the null hypothesis here?</span>**

**Example #3**: Now we will do one slightly more complicated example from Phillip Good's book "Permutation tests: A practical guide to resampling methods and testing hypotheses":

Holmes and Williams (1954) studied tonsil size in children to verify a possible association with the virus \textit{S. pyrogenes}. Test for an association between \textit{S. pyrogenes} status and tonsil size. (Note that you will need to come up with a reasonable test statistic.)

<div class="figure" style="text-align: center">
<img src="Table2categories.png" alt="Data on tonsil size and S. pyrogenes status. Source: Good (1994)" width="40%" />
<p class="caption">(\#fig:unnamed-chunk-1)Data on tonsil size and S. pyrogenes status. Source: Good (1994)</p>
</div>

Now lets consider the full dataset, where tonsil size is divided into three categories. How would we do the test now? **<span style="color: green;">Checkpoint #4: What is the new test statistic? (There are many options.)</span>** What 'labels' do you permute?

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
## 0 1 3 5 6 8 9 
## 2 1 1 1 2 2 1
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
## [1] -0.0376
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
## [1] 2.707562
```

```r
UL.boot
```

```
## [1] 6.217238
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##  2.5% 97.5% 
##   2.7   6.2
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

**<span style="color: green;">Checkpoint #6: Does it make sense why the normal distribution theory intervals are too wide?</span>** Because the original were were uniformly distributed, the data has higher variance than would be expected and therefore the standard error is higher than would be expected.

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
##    [1] 5.0 4.7 4.2 4.2 4.8 4.3 4.3 6.2 3.7 5.3 3.5 5.3 5.7 3.3 3.8 4.8 4.2 5.5
##   [19] 3.5 4.0 5.4 3.9 3.6 4.6 5.5 4.9 5.7 2.9 4.2 4.6 4.5 3.4 4.4 4.8 6.0 2.3
##   [37] 3.5 5.4 4.1 4.0 3.9 2.8 2.9 3.8 4.5 4.9 3.6 4.6 4.1 7.4 2.9 4.8 5.2 2.5
##   [55] 3.9 4.2 4.0 4.6 4.3 5.9 3.7 3.9 4.5 2.9 3.8 3.1 2.9 5.0 3.9 4.3 3.8 4.3
##   [73] 4.2 5.7 3.9 2.5 3.6 5.7 4.3 4.2 2.5 4.8 3.3 4.1 4.1 4.6 4.3 5.2 4.8 4.3
##   [91] 5.5 4.1 4.9 4.7 5.4 4.6 4.1 3.9 4.4 5.0 5.4 2.9 4.4 4.3 3.3 3.6 3.7 5.0
##  [109] 4.5 5.2 6.6 3.9 3.3 5.1 5.4 3.1 3.7 3.1 6.9 4.9 3.6 4.7 4.9 4.1 4.0 3.7
##  [127] 5.7 5.9 3.7 4.2 2.4 4.2 3.8 4.1 3.8 5.0 4.1 4.5 3.6 2.8 7.4 4.3 4.8 4.9
##  [145] 4.3 3.1 5.1 5.2 6.1 4.4 4.1 4.9 6.0 5.4 4.8 4.1 5.5 4.1 4.8 3.1 6.5 5.5
##  [163] 4.8 5.8 3.8 3.8 5.4 4.2 4.1 5.4 4.9 4.9 4.1 4.1 5.2 5.3 2.5 4.8 4.3 4.7
##  [181] 4.4 2.7 5.1 5.3 4.4 3.6 4.0 4.4 5.1 5.5 3.0 4.1 4.6 5.6 4.1 4.0 5.6 4.6
##  [199] 5.0 3.7 5.6 2.9 4.7 4.9 5.0 5.8 4.3 5.5 4.7 5.6 4.6 4.6 3.9 4.4 6.3 5.5
##  [217] 6.4 5.7 4.0 2.7 4.6 7.1 4.9 4.8 5.1 3.1 5.4 4.1 3.2 5.2 4.9 5.3 3.7 4.6
##  [235] 4.7 5.1 5.3 4.8 5.2 3.2 4.1 5.5 4.2 5.2 5.2 4.3 4.1 3.9 5.9 5.1 6.5 4.3
##  [253] 3.7 3.3 3.5 4.8 3.2 3.9 4.3 3.4 4.9 5.5 4.9 2.9 3.5 4.2 5.2 2.8 2.6 3.1
##  [271] 4.5 5.0 4.0 3.9 5.4 4.8 5.5 5.6 3.6 4.8 3.6 4.8 3.5 2.8 5.4 4.4 4.2 4.3
##  [289] 6.1 3.4 3.5 5.7 3.7 4.3 4.7 4.5 3.9 6.2 4.4 4.8 3.9 4.5 4.3 3.9 4.9 3.4
##  [307] 3.9 1.9 4.3 5.0 3.0 4.7 3.9 5.2 4.6 5.7 5.9 4.9 5.3 5.2 5.2 1.7 5.0 5.0
##  [325] 4.8 4.2 3.8 4.7 3.2 4.2 4.2 4.3 5.1 3.9 5.6 4.9 4.4 4.6 3.3 5.9 4.2 5.1
##  [343] 3.2 5.2 3.8 4.0 4.3 3.5 4.1 3.7 5.9 4.1 6.0 6.1 4.1 5.0 5.6 4.5 5.6 2.8
##  [361] 4.3 5.1 3.9 3.3 3.4 5.1 5.6 4.4 3.4 3.9 5.4 5.4 3.3 4.7 5.3 3.8 2.9 4.1
##  [379] 3.4 2.5 5.0 5.5 5.5 2.9 4.9 4.9 6.1 3.8 4.9 3.6 4.9 4.4 3.9 4.5 3.7 3.2
##  [397] 5.0 3.9 4.5 4.3 3.9 4.5 4.0 6.0 5.2 2.7 3.5 3.8 3.6 6.2 4.3 5.3 5.7 3.3
##  [415] 3.7 3.8 4.8 4.6 4.6 6.0 3.0 5.3 3.5 4.3 3.1 3.4 4.3 4.7 4.5 5.4 4.3 3.6
##  [433] 4.7 4.1 5.0 5.7 5.7 4.7 3.9 6.2 5.2 6.2 4.4 4.9 4.0 5.3 5.1 5.8 7.4 3.4
##  [451] 2.2 5.4 3.7 6.1 4.9 5.2 5.2 5.5 4.9 4.1 5.3 4.0 4.7 2.9 3.7 5.0 3.8 4.4
##  [469] 5.3 3.2 5.5 2.2 2.7 3.1 3.7 5.8 4.7 3.5 3.0 5.3 2.4 4.5 4.4 6.4 2.9 5.4
##  [487] 3.8 4.6 3.9 4.6 5.6 4.7 4.8 4.4 3.6 4.7 4.3 3.5 3.7 5.1 4.6 5.2 4.9 3.3
##  [505] 4.6 3.4 6.0 4.2 5.1 4.3 4.3 4.5 4.7 4.4 5.3 4.1 3.8 5.2 2.5 5.2 5.8 4.2
##  [523] 6.0 4.1 3.2 5.9 4.2 4.7 4.4 6.1 2.2 5.7 5.3 4.6 5.6 4.1 4.8 3.9 5.5 2.7
##  [541] 3.0 6.0 3.7 3.8 5.6 4.2 4.7 5.0 3.3 4.7 3.7 6.4 3.4 7.0 4.1 5.0 3.9 4.7
##  [559] 4.3 4.0 4.7 5.2 3.0 4.0 3.8 5.1 5.5 3.9 6.1 4.2 5.4 2.9 3.9 3.6 5.2 6.2
##  [577] 4.3 4.8 4.9 6.1 4.2 3.0 4.8 4.3 4.3 5.1 5.6 5.1 4.5 3.7 4.0 5.0 5.2 3.8
##  [595] 6.1 4.7 3.6 4.1 4.2 3.9 6.2 4.5 4.8 3.7 3.9 4.1 4.3 5.7 4.3 3.7 3.3 3.6
##  [613] 3.3 4.0 6.6 3.0 5.4 4.2 5.4 4.1 2.9 5.0 4.2 4.6 4.3 4.2 4.7 5.2 5.0 5.2
##  [631] 4.8 4.2 4.7 5.8 4.7 5.6 4.0 5.5 5.4 4.1 5.6 4.9 3.1 3.1 4.6 4.2 4.9 2.9
##  [649] 5.0 4.0 3.4 3.6 5.5 2.8 4.9 4.0 5.2 2.6 2.4 3.5 4.8 5.3 2.3 2.7 5.8 3.9
##  [667] 4.5 4.0 3.6 4.9 2.8 3.8 4.9 5.1 5.2 4.8 5.1 4.6 3.9 4.6 3.6 4.5 6.0 5.2
##  [685] 4.4 4.8 4.5 4.6 4.9 5.9 3.6 3.8 5.5 4.9 4.5 3.7 2.7 4.8 5.5 3.8 5.1 3.6
##  [703] 4.5 5.1 5.6 5.4 5.1 4.6 4.5 4.4 4.7 4.7 3.7 5.0 4.7 5.9 5.8 2.6 3.7 5.1
##  [721] 3.2 4.4 4.6 5.9 7.0 5.7 3.5 5.4 4.2 4.5 5.6 2.8 4.9 4.9 2.9 3.0 6.8 5.1
##  [739] 5.2 5.5 5.1 3.6 4.7 5.3 4.9 4.3 5.1 3.7 5.2 1.7 4.0 4.0 4.8 3.5 3.5 5.3
##  [757] 5.0 3.0 4.3 5.8 5.1 5.0 7.0 3.8 6.2 4.7 4.7 4.3 4.1 5.0 4.8 4.5 3.8 5.0
##  [775] 3.3 5.3 3.7 3.8 5.1 4.8 4.1 4.0 3.9 5.3 3.4 4.9 4.2 6.8 5.1 4.4 4.9 5.8
##  [793] 5.3 3.5 6.2 4.8 4.3 4.5 3.3 3.1 3.3 5.2 3.9 5.0 5.1 4.7 4.4 5.9 5.3 4.6
##  [811] 3.3 6.1 4.3 3.4 4.8 4.5 5.1 4.8 4.2 4.7 3.7 5.6 4.1 3.5 5.7 3.2 3.6 2.6
##  [829] 4.0 3.8 3.9 5.0 6.4 5.2 4.0 5.0 3.5 6.0 3.5 3.9 4.3 4.5 5.4 6.3 5.1 4.5
##  [847] 4.2 4.0 4.1 3.4 4.6 5.8 5.4 4.7 6.2 5.0 3.1 5.9 4.4 4.4 3.5 4.3 5.3 3.5
##  [865] 4.5 2.9 4.1 3.3 4.3 6.2 3.4 5.0 5.1 5.5 3.1 4.1 3.7 3.8 3.5 5.2 4.2 4.2
##  [883] 5.3 4.0 1.7 5.3 5.0 4.4 4.7 4.5 5.6 4.7 5.0 4.0 4.3 5.1 3.1 5.6 6.3 3.6
##  [901] 4.5 3.9 4.5 3.7 5.9 4.1 4.4 5.2 2.7 4.8 2.3 3.3 5.2 5.7 4.7 5.8 5.2 5.3
##  [919] 3.8 5.9 4.9 5.6 4.3 5.3 4.0 4.6 4.1 5.8 4.1 5.5 3.3 5.4 5.1 4.8 4.4 5.4
##  [937] 4.7 5.5 3.7 5.1 3.2 4.9 3.3 5.2 4.1 5.7 5.3 6.3 5.0 4.9 5.1 4.7 5.2 3.0
##  [955] 4.5 4.1 3.7 5.4 4.6 3.4 4.8 5.3 3.5 4.4 4.5 3.8 4.5 5.0 6.1 4.2 5.2 5.2
##  [973] 4.6 3.6 4.3 4.3 5.5 2.9 4.8 4.2 3.1 4.0 5.3 4.2 6.1 5.6 4.7 5.1 4.6 6.3
##  [991] 3.4 4.9 5.2 4.3 4.7 5.2 3.9 5.5 4.6 4.5
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
##   2.7   6.2
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
##    [1] 6.4 5.6 7.1 3.1 4.5 2.9 5.1 4.1 3.0 3.8 5.8 4.8 3.7 4.5 4.2 4.0 3.9 5.2
##   [19] 5.0 2.9 4.2 4.7 5.4 3.6 5.4 4.4 5.1 6.1 3.3 6.8 3.6 4.8 4.9 4.3 4.7 5.5
##   [37] 5.0 4.2 5.4 3.5 4.4 5.9 3.9 4.8 5.3 4.4 5.2 5.6 3.5 3.6 2.8 4.5 4.6 3.0
##   [55] 4.3 4.6 5.2 5.1 4.1 5.0 5.6 3.6 3.3 4.5 5.3 5.2 3.2 6.1 4.2 5.2 4.6 3.7
##   [73] 3.1 5.0 5.7 5.7 5.5 4.0 3.8 3.1 4.1 3.7 4.0 4.6 4.6 4.6 5.2 5.1 6.6 5.4
##   [91] 4.5 4.2 6.6 4.2 2.8 4.4 5.9 4.1 3.9 3.4 4.7 4.4 4.9 4.1 4.2 4.9 5.3 5.2
##  [109] 6.2 2.3 3.5 4.7 4.1 4.7 6.1 5.1 4.9 3.5 4.6 4.7 6.9 5.3 3.5 3.6 4.2 4.0
##  [127] 5.7 5.2 4.3 6.1 4.8 4.7 4.9 6.5 3.4 4.3 2.7 4.0 5.1 3.7 5.3 4.6 5.8 3.3
##  [145] 4.2 5.6 3.2 3.7 4.0 4.4 4.1 5.2 3.4 3.8 3.2 4.3 4.2 3.1 3.8 3.8 4.9 5.1
##  [163] 3.4 5.6 5.1 4.0 5.3 4.1 5.8 4.0 4.5 4.2 2.7 3.9 5.1 4.8 4.9 4.9 4.0 3.5
##  [181] 5.6 3.6 3.9 5.0 2.9 4.9 4.3 4.6 4.4 4.1 4.7 5.1 3.7 6.3 4.1 3.8 3.7 4.5
##  [199] 4.0 5.1 3.7 5.7 6.0 4.8 5.3 4.4 5.0 3.8 4.7 3.5 4.9 4.2 2.8 5.7 6.5 5.6
##  [217] 2.2 4.5 4.2 3.4 4.9 4.9 2.8 4.2 4.9 5.5 4.8 3.4 5.8 4.0 3.3 3.9 2.8 3.8
##  [235] 5.0 4.9 4.2 4.7 5.0 4.2 5.2 5.5 3.6 4.6 6.3 3.4 4.3 3.1 3.7 5.5 4.6 5.7
##  [253] 5.2 3.4 5.1 4.8 6.2 5.0 2.9 4.3 4.0 2.6 3.6 5.1 3.9 3.3 3.9 4.3 5.0 4.5
##  [271] 4.8 4.1 4.9 4.6 4.0 6.8 3.5 5.4 5.0 4.7 7.0 5.2 5.2 4.6 4.6 3.7 3.3 4.3
##  [289] 4.1 4.7 4.4 4.6 4.6 5.3 5.1 4.3 2.8 5.8 3.6 3.9 5.3 3.7 5.7 5.4 3.3 2.6
##  [307] 4.3 3.8 4.4 4.9 6.1 4.0 5.2 4.8 4.8 5.3 3.7 4.6 3.9 4.1 3.2 4.6 1.8 4.4
##  [325] 4.8 3.5 3.1 4.0 5.8 3.0 5.3 4.6 3.2 4.9 5.0 4.0 3.8 6.3 3.6 4.4 5.1 3.3
##  [343] 4.5 3.3 3.4 6.0 3.8 3.6 5.4 3.5 3.8 3.8 5.6 3.6 5.2 4.8 3.8 3.0 4.0 4.5
##  [361] 5.0 4.9 3.6 4.7 6.8 5.1 5.1 4.6 4.6 4.7 2.0 4.6 4.6 5.2 3.8 4.8 4.1 5.1
##  [379] 4.2 5.1 3.8 4.4 4.5 4.6 4.1 6.7 2.1 5.6 3.5 4.9 5.8 3.9 4.4 4.8 2.7 4.8
##  [397] 4.2 5.0 4.4 6.1 4.4 4.9 2.4 5.6 3.6 4.1 5.3 3.7 5.4 5.8 4.2 4.7 4.8 6.5
##  [415] 6.1 4.8 4.2 4.7 4.6 5.1 4.7 4.1 4.6 3.4 5.6 4.6 4.9 5.0 5.0 4.2 4.6 4.4
##  [433] 5.1 4.2 4.1 3.6 5.3 3.1 6.2 3.8 4.1 3.9 5.7 3.7 3.8 3.9 3.6 5.1 4.9 5.0
##  [451] 4.1 5.7 4.1 5.6 5.4 6.1 4.5 4.0 5.6 3.2 4.8 4.6 5.4 2.8 3.7 3.8 2.6 5.3
##  [469] 4.2 5.8 3.5 4.9 4.8 3.5 5.2 3.8 3.5 2.8 4.8 4.9 4.8 3.4 4.1 5.3 2.9 5.0
##  [487] 6.0 4.2 5.7 3.9 3.5 3.3 5.8 4.5 4.4 2.6 4.9 6.1 4.4 5.2 4.6 4.7 3.5 4.2
##  [505] 4.0 4.4 5.2 2.7 5.0 5.6 5.6 4.2 4.3 4.6 4.7 3.3 5.4 6.2 4.2 5.1 5.4 4.3
##  [523] 5.1 4.5 3.7 5.5 5.1 6.7 4.0 5.3 4.7 6.0 4.5 4.5 4.4 4.7 3.7 5.8 6.0 4.3
##  [541] 5.5 3.7 5.4 4.4 4.9 4.2 2.6 4.4 4.9 4.4 4.8 4.7 4.9 3.4 5.7 4.8 4.5 3.4
##  [559] 3.2 4.4 4.4 2.7 4.8 4.2 5.2 3.9 3.6 5.3 2.4 3.9 3.1 4.6 5.5 5.0 3.9 4.4
##  [577] 5.6 3.9 5.4 5.4 3.4 4.8 4.9 2.7 5.0 5.4 5.0 5.4 4.3 4.8 5.6 4.4 4.9 3.4
##  [595] 3.8 3.8 4.0 4.3 3.9 2.5 4.0 3.6 4.5 4.4 3.6 4.9 4.8 3.1 3.9 4.7 3.8 5.5
##  [613] 3.4 3.3 4.0 3.8 4.3 4.9 4.4 4.9 4.4 3.7 4.1 4.4 4.0 5.0 5.4 3.0 2.1 4.0
##  [631] 5.2 4.8 5.0 3.8 4.3 3.2 3.5 5.1 4.0 4.0 3.8 5.7 4.6 3.6 5.7 4.9 3.8 7.2
##  [649] 5.3 4.1 3.0 6.7 4.9 3.4 6.7 6.2 4.8 5.3 2.4 3.6 4.8 5.3 4.6 5.2 3.7 3.8
##  [667] 3.4 3.7 4.1 3.5 3.1 5.0 4.0 4.3 5.4 4.0 3.8 3.9 3.5 3.8 5.4 4.7 4.1 3.6
##  [685] 3.1 6.3 4.6 4.7 4.9 5.1 3.7 5.0 3.3 3.5 5.9 3.0 4.6 4.7 4.9 3.6 5.3 3.6
##  [703] 3.6 3.2 3.9 5.0 4.8 4.8 4.5 5.3 2.7 5.4 3.6 5.2 4.4 4.5 3.3 3.8 3.5 5.3
##  [721] 5.6 5.3 5.4 6.8 6.9 4.4 5.1 4.0 3.0 5.1 6.0 3.8 4.1 2.5 5.7 3.9 4.5 5.5
##  [739] 2.5 3.7 4.9 2.9 6.2 5.1 5.4 5.2 3.4 5.4 4.9 3.1 4.7 5.4 3.7 2.7 5.9 4.0
##  [757] 3.3 4.6 3.5 4.9 3.5 4.4 5.2 4.5 5.8 4.1 5.8 2.4 5.1 5.3 3.7 5.1 4.6 4.1
##  [775] 3.6 5.5 5.1 4.5 3.8 2.3 3.6 5.8 4.0 4.5 5.3 3.7 4.8 4.0 4.1 5.2 3.5 2.7
##  [793] 2.9 4.1 6.2 3.8 5.4 5.2 3.9 5.0 3.9 2.0 3.6 3.5 5.0 3.7 3.8 4.3 4.0 3.3
##  [811] 2.7 3.5 3.7 3.9 5.2 3.8 5.9 5.7 4.4 4.0 4.7 6.0 3.4 6.3 4.3 4.6 2.7 5.2
##  [829] 4.5 5.3 3.5 4.0 4.1 5.1 3.3 6.4 3.1 2.9 5.3 5.7 3.3 6.2 4.2 2.8 6.3 5.7
##  [847] 4.0 3.8 5.7 4.8 5.7 4.2 5.2 3.0 2.9 3.7 5.6 4.8 3.9 3.5 3.7 3.6 3.8 4.2
##  [865] 1.8 3.7 4.5 4.3 6.3 4.3 5.9 4.7 5.6 6.0 4.1 5.5 4.3 3.8 4.3 4.1 4.7 4.9
##  [883] 4.5 4.6 3.3 5.7 3.9 2.9 5.1 4.3 5.7 4.5 4.7 4.3 5.2 3.9 3.8 4.0 6.5 4.1
##  [901] 4.1 4.3 3.9 3.0 4.9 5.6 3.1 4.4 5.4 5.9 4.4 4.4 4.8 4.0 5.2 4.3 3.9 4.1
##  [919] 4.0 3.8 5.2 4.0 4.0 4.5 3.2 3.9 5.1 4.9 5.2 4.1 4.3 3.9 3.7 4.9 2.7 3.6
##  [937] 4.3 4.6 3.9 4.7 4.6 3.9 3.9 3.3 3.2 5.1 4.2 5.4 3.6 4.7 5.2 3.0 3.8 5.8
##  [955] 3.2 4.6 4.2 4.7 3.1 4.5 5.0 6.9 5.5 5.0 2.8 3.7 4.5 4.1 4.2 5.1 4.0 5.7
##  [973] 4.5 3.8 4.5 2.4 6.0 3.4 3.8 4.7 3.6 4.8 4.9 4.7 4.7 4.7 4.7 3.9 4.5 4.3
##  [991] 4.3 4.3 4.1 4.0 5.0 4.7 5.4 3.9 3.2 4.3
## 
## $func.thetastar
## [1] -0.0529
## 
## $jack.boot.val
##  [1]  0.520000000  0.403144654  0.217469880  0.149549550  0.004444444
##  [6] -0.092741935 -0.301457726 -0.368529412 -0.448979592 -0.594857143
## 
## $jack.boot.se
## [1] 1.0679
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
##    [1] 4.3 3.7 5.1 4.4 5.5 4.7 5.9 4.7 3.9 4.5 5.4 6.1 5.3 4.0 5.4 4.8 5.5 5.9
##   [19] 4.0 4.0 3.5 6.6 5.3 3.7 4.1 4.0 3.7 4.4 5.1 4.7 5.8 5.2 4.6 4.8 4.1 3.5
##   [37] 6.2 4.1 4.0 3.6 4.3 4.5 5.4 4.0 3.0 4.7 5.4 4.1 2.1 4.3 5.5 5.5 5.9 4.3
##   [55] 4.9 4.3 3.7 4.4 3.8 4.9 3.9 3.7 3.0 4.9 4.5 5.3 4.2 5.1 3.8 6.3 4.1 3.6
##   [73] 4.8 3.0 4.7 5.3 4.3 4.2 5.2 4.9 5.1 3.6 6.8 4.8 5.3 5.1 3.8 3.7 4.6 5.4
##   [91] 4.7 6.1 5.7 5.0 4.1 4.1 3.4 3.0 4.0 6.1 4.4 4.2 5.4 4.5 4.1 3.3 4.9 4.4
##  [109] 3.0 4.7 3.5 3.3 4.8 5.6 2.7 3.3 3.0 3.6 4.4 3.7 5.3 4.0 5.2 5.0 4.2 5.5
##  [127] 3.2 3.7 4.7 4.0 3.5 2.9 3.6 5.2 3.9 4.0 2.2 3.7 5.3 5.2 4.9 4.7 4.8 4.8
##  [145] 4.8 4.1 4.1 4.5 5.9 2.8 4.4 5.0 4.7 3.8 5.0 2.5 3.3 3.6 4.2 5.1 6.3 3.7
##  [163] 3.3 4.7 4.7 4.7 6.1 5.0 2.9 4.6 4.2 4.8 4.6 6.0 5.6 5.0 4.0 2.9 4.4 3.7
##  [181] 4.3 3.3 3.6 5.0 5.5 4.4 2.6 3.2 2.2 4.1 3.8 4.0 4.7 3.7 3.5 6.1 4.2 4.4
##  [199] 4.3 4.6 4.1 3.9 3.4 4.7 5.9 4.1 4.4 3.8 5.8 5.2 4.3 3.8 4.5 4.0 4.7 6.3
##  [217] 4.7 5.3 4.7 3.4 6.1 3.9 3.1 5.5 3.7 4.5 4.5 4.5 3.9 6.1 4.9 5.3 5.1 3.9
##  [235] 5.3 4.9 4.6 5.4 5.2 3.3 5.9 5.2 3.5 4.3 5.2 3.4 5.2 5.9 4.7 6.1 5.2 5.0
##  [253] 4.2 3.6 3.8 4.2 4.3 5.1 5.6 5.2 4.9 3.9 4.9 4.4 4.2 4.3 5.3 4.3 4.3 4.7
##  [271] 4.1 3.0 3.6 3.9 4.0 3.9 5.4 3.0 1.9 4.5 4.9 3.9 5.9 4.9 4.3 4.3 4.7 4.2
##  [289] 6.4 5.0 4.7 4.2 3.7 3.7 3.6 3.9 5.0 5.2 4.3 4.8 4.6 4.0 2.8 5.6 5.0 4.8
##  [307] 4.5 5.1 3.9 5.5 4.3 3.5 4.3 3.7 4.3 4.6 5.7 4.3 5.7 5.1 4.3 5.1 4.5 3.4
##  [325] 4.3 5.1 4.8 5.6 4.0 3.8 5.1 3.3 3.0 3.1 4.8 5.3 4.8 3.6 4.8 4.4 4.1 5.3
##  [343] 3.5 4.3 3.5 4.3 4.9 5.0 3.9 5.4 6.1 6.1 4.6 6.5 5.4 4.6 4.4 5.5 3.3 5.5
##  [361] 3.2 5.5 4.4 3.9 2.3 3.7 3.9 4.2 4.3 5.0 4.0 5.2 4.0 5.0 3.5 5.8 2.8 5.4
##  [379] 4.6 4.3 4.5 5.3 4.5 3.8 4.9 6.2 6.0 5.0 3.2 4.7 2.6 4.8 6.3 4.9 4.7 3.5
##  [397] 4.5 5.0 5.4 4.1 3.9 3.8 5.9 4.4 4.9 3.3 4.7 6.5 3.8 4.0 4.0 6.1 4.3 5.1
##  [415] 2.7 4.7 4.1 4.6 4.4 3.6 5.8 4.2 4.9 4.6 6.0 4.4 5.9 4.8 4.4 5.4 4.6 3.9
##  [433] 2.1 4.4 3.5 4.8 4.9 4.2 6.3 2.9 3.8 4.7 4.7 5.2 5.3 3.0 3.1 4.7 5.0 5.3
##  [451] 4.8 5.1 4.8 3.9 5.0 4.7 3.0 5.7 6.0 5.3 5.1 4.2 4.4 4.5 5.9 4.8 4.8 4.1
##  [469] 5.6 5.4 3.6 4.3 4.2 3.9 5.4 4.7 6.0 6.0 4.3 5.2 2.7 4.1 3.6 3.7 4.6 4.8
##  [487] 3.2 4.6 2.2 3.5 3.6 3.8 3.4 4.1 4.4 5.5 4.0 2.9 3.4 3.8 4.4 4.0 4.1 3.8
##  [505] 4.2 3.7 5.4 4.3 4.5 3.7 4.4 4.4 4.6 3.4 2.4 4.5 4.4 4.6 4.4 4.2 5.8 3.0
##  [523] 4.0 4.3 3.8 5.1 3.6 5.1 4.5 4.4 4.0 4.6 4.2 4.4 3.7 3.8 3.8 3.8 4.0 5.0
##  [541] 5.4 4.8 5.5 3.9 3.3 4.8 5.6 4.1 4.5 5.0 3.8 5.0 5.6 4.0 3.1 5.6 6.6 5.3
##  [559] 6.8 5.4 4.2 4.5 3.9 4.2 3.9 4.2 3.8 5.2 4.6 3.9 5.6 4.4 6.2 5.4 4.4 5.3
##  [577] 3.7 5.4 4.2 4.4 4.0 4.3 4.5 5.2 3.7 5.4 4.6 4.4 4.7 3.0 6.0 4.0 3.5 6.1
##  [595] 4.5 4.8 3.0 3.7 4.4 5.6 4.4 5.3 6.5 3.9 4.4 6.1 5.7 4.8 5.2 4.8 5.6 5.5
##  [613] 3.8 3.5 3.1 4.3 3.2 4.5 5.7 6.7 3.8 5.1 4.6 3.1 5.2 4.1 3.0 6.1 5.7 5.0
##  [631] 4.4 4.3 4.5 4.6 6.2 4.5 5.4 3.6 4.7 3.6 3.7 5.3 3.9 4.3 2.9 2.9 4.1 4.9
##  [649] 4.6 3.2 4.4 4.2 4.7 5.6 5.4 3.4 2.9 4.8 3.8 3.3 3.6 2.8 5.2 6.0 4.7 3.4
##  [667] 5.1 5.3 3.9 6.1 3.3 4.2 4.9 4.6 3.0 4.9 5.9 3.6 3.5 3.8 4.6 3.5 4.2 5.4
##  [685] 3.5 3.9 5.4 5.2 4.1 3.9 5.3 5.3 5.2 5.1 4.3 4.8 5.2 3.3 4.2 5.9 4.9 4.1
##  [703] 4.1 5.0 5.4 4.1 5.5 4.2 2.6 5.7 1.8 3.9 3.2 4.5 3.6 5.2 4.9 5.0 5.9 6.1
##  [721] 4.2 3.2 4.6 4.9 5.0 4.7 5.0 4.0 4.9 6.4 5.1 5.8 4.6 3.9 6.0 4.3 4.7 4.6
##  [739] 3.5 4.4 6.4 6.1 5.1 5.1 5.9 5.0 5.2 4.0 4.7 4.3 4.4 5.1 4.1 3.2 4.0 4.2
##  [757] 5.0 3.8 4.4 5.2 3.7 5.6 4.2 2.8 4.0 5.5 3.8 5.1 3.8 4.5 4.4 3.6 4.8 3.8
##  [775] 4.8 4.2 4.5 4.0 4.4 5.8 3.6 4.5 6.0 4.7 5.1 5.8 3.9 5.3 4.2 5.5 5.4 3.3
##  [793] 4.2 3.9 4.2 3.0 5.0 4.0 4.4 5.7 3.5 4.2 4.0 5.5 4.6 5.1 4.0 3.6 3.9 2.4
##  [811] 3.9 4.9 4.1 4.4 3.2 3.5 3.9 5.1 3.0 5.0 5.3 5.6 3.8 5.5 6.3 4.8 5.1 3.6
##  [829] 4.1 4.9 5.2 5.1 4.9 4.1 4.1 5.1 4.4 5.0 4.0 5.5 3.0 4.3 4.0 3.9 5.3 2.9
##  [847] 5.2 4.9 5.0 3.9 6.0 2.8 3.8 4.7 3.0 5.0 3.3 2.5 3.4 4.1 3.1 4.5 3.8 4.0
##  [865] 5.1 5.1 5.5 4.6 4.5 4.3 5.2 4.5 4.0 6.4 2.3 5.3 4.7 5.0 5.4 3.5 3.8 4.1
##  [883] 3.4 4.4 3.7 4.2 4.7 5.3 3.9 3.7 4.6 3.1 3.7 4.3 4.2 3.9 4.4 3.5 4.4 4.9
##  [901] 3.7 4.6 5.9 4.9 5.8 5.2 4.9 4.7 4.8 4.2 3.2 4.6 4.7 4.1 5.7 3.9 5.5 3.9
##  [919] 3.7 3.1 4.5 4.7 5.5 5.4 5.5 4.0 3.4 3.0 4.0 4.5 2.9 5.3 4.3 3.3 4.5 3.7
##  [937] 5.3 4.6 4.9 4.5 4.8 5.3 4.0 6.3 5.0 3.8 3.2 4.1 5.0 5.0 3.8 3.4 4.9 4.3
##  [955] 4.7 4.5 5.3 3.2 4.6 4.4 5.2 4.7 4.4 5.1 4.7 6.4 4.5 6.3 6.1 5.3 5.6 4.0
##  [973] 5.2 5.1 3.0 6.1 4.3 4.4 5.2 4.1 3.4 2.9 5.4 3.3 4.0 5.6 5.8 4.4 6.1 4.9
##  [991] 4.0 6.4 2.6 2.4 5.4 3.6 3.8 3.3 4.2 4.5
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.400 5.400 5.300 5.200 5.100 4.996 4.800 4.700 4.600 4.400
## 
## $jack.boot.se
## [1] 0.99902
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
## [1] -0.545317
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
##   5.912816   9.204056 
##  (2.573151) (4.180463)
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
## [1]  0.90042971  0.14707333  0.28038439  0.47057329 -0.07238708 -0.79816791
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
##    [1] -1.176000046 -0.588475556 -0.018743686 -0.358812911  0.267618693
##    [6] -2.296166511 -0.558879772 -0.394200425 -0.341656757 -0.967855879
##   [11] -0.654452092  0.073962424 -0.472822518 -0.195226510 -0.257002318
##   [16] -0.543701864 -0.926205020 -0.259428093 -0.514318447 -0.254652531
##   [21]  0.403737021 -0.666027984 -1.844890229 -0.065053251  0.384723566
##   [26] -0.719243577 -0.958647072 -0.164867886 -1.590362224  0.197164161
##   [31] -1.437160815 -0.289487420 -0.883094291 -0.695920089 -0.839443273
##   [36] -1.600126439 -0.417883466 -1.792844147 -0.641558527  0.621403548
##   [41]  0.235568413 -0.752289943  0.005967985  0.219753832  0.375720618
##   [46] -1.089122405 -0.980105130 -0.810423877 -1.196092199 -0.614444338
##   [51]  0.121020278 -0.240778330 -1.963932004 -0.726436830 -0.539022558
##   [56] -0.336575709 -1.223891944 -0.813073568 -0.976407075 -0.853917632
##   [61]  0.054041615 -0.484990042  0.074814343 -1.023216947 -0.199349789
##   [66] -0.059433737 -0.054738005 -0.569370007 -0.319633576  1.038530775
##   [71] -0.693116179 -0.434237881 -0.300611342  0.254765884 -0.469166312
##   [76] -0.318274946 -1.107226779 -0.872089847 -0.806590052  0.098784486
##   [81] -0.929647374 -0.967687466  0.155772113  0.127638679 -0.822163443
##   [86] -0.636634722 -0.757026352 -1.011335345 -0.967855879 -0.919288770
##   [91]  0.489666936 -1.106758450  0.057633562 -0.225182515 -0.591089555
##   [96] -1.047709351 -0.729980492  0.443313101 -0.526011652 -0.436086230
##  [101] -0.320265286 -0.869351173 -0.528458722 -0.377942304  0.121366631
##  [106] -0.900899040 -0.763386473 -0.361112319 -0.710059578  0.385395723
##  [111] -0.613732682  0.416104999 -0.122920334 -0.136182456 -1.091645044
##  [116] -0.745998929 -0.128745919 -0.434697886 -1.050864406 -0.495110942
##  [121] -0.722158627 -1.002726795 -0.546957844 -1.670700884 -0.265320021
##  [126]  0.419230383 -1.011211118 -0.938463784 -1.253861896 -0.244038561
##  [131] -0.527866235 -0.547548287 -1.342613135 -0.742218339 -0.690149096
##  [136]  0.222536533 -0.273944510  0.002432092  0.931134296  0.472802378
##  [141] -1.694631042  0.025282159  0.125434435  0.459980771 -0.829240485
##  [146] -0.349222197 -0.579199087  0.902333305 -0.523816371 -0.075525552
##  [151] -1.155886884 -1.041537126  0.094341813  0.253182188  0.153752976
##  [156] -1.163542547 -1.243113175 -0.348896691 -0.522663465 -0.392996477
##  [161] -1.730126270 -0.537466169 -0.525263220 -0.882600115 -1.697218356
##  [166] -0.594394993 -1.572259161 -0.689854062 -0.935830324  0.242942380
##  [171] -0.829185967  0.187562719 -1.179388926 -0.218687337 -1.264729729
##  [176] -0.374854500 -0.483011060 -0.099279673 -0.796902816 -1.229305130
##  [181] -0.164617023 -0.022121599 -0.484669511 -0.042737583 -0.751986096
##  [186]  0.157778581  0.088552015 -0.079347319  0.444167197 -0.696384508
##  [191]  0.691122511 -0.530007361 -0.279294991 -0.449993100 -1.238552668
##  [196] -0.055194056 -0.232085951 -0.308685277 -0.339762046  1.119912801
##  [201] -0.471042935 -0.659145837 -1.125962355 -0.240778330 -0.539787219
##  [206] -0.419606571 -0.530007361  0.937238092 -0.213989933 -1.132234283
##  [211] -0.977275256  0.089280719 -0.747759550 -1.024901051  0.234152914
##  [216] -0.455301779 -0.411104483 -0.915843659 -0.668125124  0.146243742
##  [221] -0.153883493  0.550738358  0.582782890 -0.589514497 -0.609446315
##  [226] -0.442056900 -0.979821703 -1.467928238 -1.801518010 -1.008625199
##  [231] -1.521170239 -0.530721667 -0.185361431 -0.567759975 -0.137205747
##  [236]  0.413991054 -0.610514635  0.006235056 -0.216669621 -0.361434974
##  [241] -0.289002708 -0.703761556 -0.896138519 -1.712200317 -0.498538662
##  [246]  0.934920178 -0.973005257 -0.267946557  0.025284962  0.884407026
##  [251] -1.287820142 -0.455024741 -1.348297099 -1.645122505 -0.057743150
##  [256] -0.613959868 -0.627802225 -0.135556061 -1.031563713 -0.714276959
##  [261] -0.935830324 -1.574994164 -0.691256169 -0.166453181 -0.290146037
##  [266] -1.101435716  0.032161831 -1.100483213 -1.266549402 -0.872265985
##  [271] -0.781462620 -1.089631651  0.224880497 -0.561465764  0.276237251
##  [276] -1.043678926 -0.464904493 -0.239220978  0.126605240 -1.870532749
##  [281] -0.867267731 -0.594772760 -0.494778754  0.125669764 -0.528458722
##  [286] -0.797858271  0.096740584 -0.124524903 -0.737169376 -0.137205747
##  [291]  0.200333258 -1.325029765 -0.548151609 -0.559830670 -0.746834992
##  [296] -0.375430710 -0.738768190  0.002318767 -0.718270085 -0.229083103
##  [301] -0.476844640 -1.916087879 -0.175113928 -0.096261454 -0.451323781
##  [306] -0.274560143 -0.186589811 -0.663660862 -0.958470707 -0.872828619
##  [311] -0.452196331 -1.349186492 -1.547319629 -0.615017746 -0.888266173
##  [316] -0.659247580 -0.720376521  0.395178183 -1.262910588 -0.472228875
##  [321]  0.014795941 -0.453705391 -0.592038494 -0.213608881 -1.030756546
##  [326] -0.813905245 -0.719208100 -1.103795040 -0.992128930 -0.952282131
##  [331]  0.170081256 -0.508058601 -0.264140644 -0.272754180  0.459134153
##  [336] -0.467786750 -0.421587061 -0.724675011 -1.045148671 -0.284198543
##  [341] -0.420834338 -0.239220978  0.721073871 -1.359415688 -1.081997290
##  [346] -1.007548739 -0.224286178 -0.543759296 -0.350912134 -1.468935321
##  [351] -0.203624926 -0.587183092 -0.699928756 -0.696752268 -0.595095502
##  [356] -1.023064524  0.386837256 -0.611125607 -1.082165629 -0.564632703
##  [361] -0.875831212 -0.071356991  0.297812482 -0.730795163 -0.834757075
##  [366] -0.267744516 -1.111883739 -0.353827060 -0.304759908 -0.568005050
##  [371] -0.456191225 -0.078115252  0.812243112 -1.353276767 -0.683261533
##  [376] -0.883009300 -0.647211959  0.203027235 -1.039285696  0.199911655
##  [381]  0.268598000 -0.684494854 -0.667085131  1.316633686 -0.138820289
##  [386] -0.295476370  0.141480596 -1.185124554  0.030419489  0.355210338
##  [391] -0.709992738  0.141417275 -0.212512091  0.041257434 -0.275435840
##  [396] -1.517633382  0.019126730 -0.635468907 -0.003644338 -0.078321838
##  [401] -0.929500861 -0.750323317 -0.267548746 -1.377938124 -0.240038790
##  [406] -0.608801014 -0.613732682 -0.601024306  0.210332381 -0.612474578
##  [411] -0.526594125 -0.273771820 -1.481023505  0.355685960 -0.632620417
##  [416] -0.579333062 -1.387393283 -0.514358975 -0.229890132  0.159512950
##  [421] -0.193462663  0.006762197 -1.174753865 -1.376368157 -0.432094892
##  [426] -0.601794502 -0.854549501 -0.174021926 -0.997460102 -0.743121348
##  [431] -1.151065116  0.080955847 -0.443886368 -0.382876474 -0.721396779
##  [436] -0.096120407 -0.282023587  0.270845688 -1.201268130 -0.286660689
##  [441] -0.526595621 -0.784178852 -1.788380696 -1.100483213 -0.273335578
##  [446] -0.794541664 -1.866402432 -0.696633193 -1.475236668 -0.604528214
##  [451] -0.641271676 -0.983088399 -0.519588545 -0.480662199 -0.576079464
##  [456]  0.088589630 -1.024110120  0.463737096 -1.021542368 -1.085332005
##  [461] -1.231547449 -0.588831881 -0.763604891 -1.234139620 -0.162255779
##  [466] -0.210739589  0.036227114 -1.109770102  0.800281449  0.135010110
##  [471] -0.632197531 -0.386293948 -1.567414116 -0.929414707 -1.133499125
##  [476]  0.486469039 -1.441981641 -0.660450041 -0.602771422 -0.678023794
##  [481] -0.671136381 -1.372279668  0.141800218  0.379587335 -0.930831467
##  [486] -1.001664584 -1.593131297  0.071343427 -1.234476712 -0.788675518
##  [491] -0.520292218 -0.322046067 -1.636993640 -0.154859147 -0.590834962
##  [496] -0.241417402 -0.705949003 -1.006454252 -0.848497571 -0.842900107
##  [501] -0.501947849  0.555150568 -0.786077841 -1.739823255  0.768494315
##  [506] -0.236728801 -1.033112823 -0.626831770 -0.616722699 -1.153037041
##  [511] -1.169710936 -0.535401908 -0.119782975 -0.440675114 -0.472191909
##  [516] -1.114752761 -0.007654014 -0.611320205 -1.043471361 -1.550267163
##  [521] -1.410213660 -0.129386223  0.434242895  0.177088896 -1.353230197
##  [526] -0.927584546  0.008343420 -0.908641939  0.111972495 -0.821839750
##  [531] -0.972613253 -0.684552033 -0.521150743 -0.349482789 -1.078036626
##  [536] -0.662101338  0.079074266 -0.177055839 -2.142741249 -1.043772845
##  [541] -0.603742082 -0.425559714 -0.496432298 -0.630544416 -0.541508747
##  [546] -0.978160357 -0.856876198 -1.426538260 -1.116574118 -0.263833361
##  [551] -1.236657468 -0.170849234 -1.519517298 -1.103795040 -0.567254321
##  [556] -1.520860308 -0.470200292 -0.773703265 -0.844605486 -1.067142484
##  [561] -0.581341176  0.210156398  0.010142366 -1.577507738 -0.485628255
##  [566] -1.642174442 -1.105487518 -0.472822518  0.090743966  0.163497015
##  [571] -1.193423876 -0.608745914 -0.548243771 -0.958796201 -0.390746219
##  [576] -0.021031281 -0.361764098 -0.709885420 -1.153044347 -0.144495012
##  [581] -1.582665398 -0.306810100 -0.735629113 -0.738492927 -0.654972225
##  [586] -0.138787997  0.140737737 -0.675591285 -0.229980514 -0.481781660
##  [591] -0.329552370  0.309429974 -0.886866708 -0.775341252 -0.663655846
##  [596]  0.144342770 -0.980070455 -1.303019552 -1.129165231 -1.842251094
##  [601] -0.515249990 -0.577738914 -0.183661421 -0.497821261 -0.646154185
##  [606] -0.545974031 -0.819904731 -0.480589333  0.131486081 -0.598371472
##  [611]  0.047976751 -0.261280095  0.307423804  1.355735164 -0.495829592
##  [616]  0.029249900  0.925214541 -1.032448707 -1.905808082  0.546068911
##  [621] -0.958506751 -1.750892317 -0.851705323 -0.923180186 -1.137040786
##  [626] -0.675282129 -0.400645223 -1.097719000 -0.155680302  0.106269762
##  [631] -1.662565411  0.378439752 -0.785590297 -0.242817917  0.721572741
##  [636] -0.126286304  0.169709317 -0.054921972  0.031195988  0.125510002
##  [641] -0.244699033 -0.537080515  0.166399713 -0.259116433 -0.191409125
##  [646] -0.314919969  0.430231347 -0.740937192 -0.334252267 -1.080927268
##  [651] -0.620569228 -0.491712183 -1.145935101 -0.212210267  0.018720695
##  [656] -0.077598100 -0.162471657  0.009297866  0.030228512 -0.130870142
##  [661] -0.569370007 -1.278479291 -1.739622785 -0.368707568 -0.782118154
##  [666] -0.671927325 -1.093873268 -0.581767355  0.376581880 -0.949783877
##  [671] -0.286502482 -0.231600863 -0.630067567 -0.319076477  0.128151257
##  [676] -1.248479917 -0.780932562 -2.098931703 -0.323762883 -0.474912320
##  [681]  0.012174399 -1.068391813 -1.648057663 -0.125232226 -0.430783506
##  [686]  0.186918875 -0.523199286  0.316932033 -0.195632647  0.327488430
##  [691] -0.521901328 -0.271002777 -0.725010720 -0.785188978  0.456538098
##  [696]  0.155939293 -1.049057022 -0.602550383 -0.124966671 -0.592569490
##  [701] -0.807381135 -0.239159911 -0.369448995 -1.516290598  0.093703705
##  [706] -0.037598782  0.476021428 -0.723674076 -0.494680830  0.016895158
##  [711] -1.525513806 -1.495167101 -0.632731456  0.162283003 -0.738703071
##  [716] -0.954135473  0.095878289  0.275724545 -0.028329845 -0.271002777
##  [721] -1.289304588 -0.780430206  0.475590441 -0.127057599 -0.575350896
##  [726] -0.854286381 -0.105273857 -1.125965758  0.840047333 -0.672191768
##  [731] -1.125574453 -0.317265693 -1.086343263 -0.331619568 -1.591342297
##  [736] -0.280603345 -0.423976362 -0.281476969 -1.003980990 -2.047489527
##  [741] -1.286192946  0.507513562 -0.133832019 -0.723690680  0.056976332
##  [746] -0.585339264 -1.312849801 -0.565379654 -0.259510021 -0.814846012
##  [751] -1.109446588 -0.604265346 -1.353884293 -0.390654168 -0.328696215
##  [756] -0.802996271 -0.333592787  0.081287024 -0.741183591 -0.454018673
##  [761] -1.122675941 -1.428743771 -1.140737344 -0.581227133  0.242687083
##  [766] -1.148572589 -1.263385841 -1.351138278 -0.525243586 -0.486473089
##  [771] -0.798880544 -0.785109778 -0.612734817 -0.640632616 -0.318793507
##  [776] -0.251998094 -0.559017441 -0.802163892 -0.463561599 -0.796171803
##  [781] -0.439418617 -0.516942075 -0.809089578 -0.869351173 -0.706692348
##  [786] -1.118073383 -1.179684679 -0.648946719 -0.261091035 -1.654069935
##  [791]  0.809062579 -0.173184727 -0.211540765 -0.536509202  0.385595323
##  [796] -1.058941724 -0.139177129  0.384410927 -1.239443796  0.793422667
##  [801] -0.493096171 -1.312681977 -0.731759984 -0.026014170 -0.169609351
##  [806] -0.527770475 -1.218319152 -1.425097260 -0.247689271 -1.132613989
##  [811] -0.868184693 -0.862919682 -1.298084341 -0.467508621 -1.279669811
##  [816] -0.642451087 -0.268248164 -1.394116423 -0.305599256 -1.185886349
##  [821] -0.237136367 -0.909385230 -0.964978159 -0.880056384 -0.518768825
##  [826]  0.165257200 -0.207478473 -0.589253879  0.066138620 -0.554740306
##  [831] -1.449746758 -0.772745380 -0.304646064 -1.751396309 -0.217688251
##  [836]  0.242311022 -0.881259824 -0.448979546 -0.591298569 -0.837923023
##  [841] -0.601775602 -1.080620999  0.041065163 -0.095430617 -0.573427914
##  [846] -0.260791953  0.361569867 -0.984415841  0.153259316 -0.837919515
##  [851] -0.624989378 -1.849959928 -0.987228784 -0.001366112 -0.183117320
##  [856] -0.568694048  0.725279327 -0.530152040 -0.918095043 -0.326034099
##  [861] -0.956184664 -0.803929556 -0.160296872 -0.375326661 -0.516212102
##  [866] -0.256396630 -0.386580643 -0.724806301 -0.323038296  0.360539128
##  [871] -0.151116700 -0.944090378 -1.126917937 -0.073739380 -0.304190856
##  [876]  0.025414501  0.386332512 -0.856351193 -0.871385339 -1.417892223
##  [881]  0.400066318 -0.242050212 -0.718051806 -0.972113896 -0.752999396
##  [886] -0.273955520 -1.140373680 -0.075700677 -1.236536782 -0.889325453
##  [891] -0.142827533 -0.119801056 -0.307344214 -1.157139225 -0.583217442
##  [896]  0.065091338 -0.687894313 -0.826921842 -1.255254361 -1.031411970
##  [901] -0.528640950  0.041065163 -0.466817501 -0.464189179 -0.934703110
##  [906]  0.441312295 -0.659075250  0.155088392  0.061351657 -1.032605722
##  [911] -1.098784428 -0.602986955  0.338687631 -0.750959766 -0.011616299
##  [916] -0.378667756  0.431735284 -0.493359616 -0.220678966 -0.274334770
##  [921] -0.976799949 -0.398741097 -0.410632379 -0.668219151 -0.173105506
##  [926]  0.118322052 -0.183633788 -0.268213978  0.032687168  0.019184865
##  [931] -0.344317021 -0.949874787 -1.049583214 -0.973030986 -0.470755653
##  [936] -1.620280334 -0.802305552 -0.909973075 -0.607937135 -0.657998787
##  [941]  1.222909037 -0.470934871 -0.244742904 -0.341762789 -1.180225238
##  [946] -0.811805821 -0.492077777 -0.377891770 -0.663456951  0.077790101
##  [951] -0.971090377  0.156093807 -0.485874198 -0.606881846 -0.515692219
##  [956] -0.212694156 -1.963591435  0.332659196  0.054921651 -0.706504964
##  [961] -0.411731377 -0.152756961 -0.561639895 -0.545316961 -1.020584311
##  [966] -0.811366112 -1.043352005 -0.314047672 -0.176890838 -1.727466684
##  [971] -1.092169153 -1.345078246 -0.897619416 -0.623134269  0.434971584
##  [976]  0.047436706 -0.580068383 -0.815090353  0.302034643 -0.350680926
##  [981] -1.114259936 -0.596077534 -0.286184735  0.038154781 -0.889325453
##  [986] -0.136976147 -1.356307441 -1.414444562 -1.168519863 -0.959582531
##  [991] -1.100946080 -0.732444542 -0.125974178 -1.043499254 -0.843268131
##  [996] -1.217681816 -0.728442468 -0.942716310 -0.546013709 -1.042264222
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
```

```r
fit2
```

```
##       mean          sd    
##   0.64241544   0.23068210 
##  (0.07294809) (0.05157879)
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
## [1]  0.03367278 -0.01481444  0.50201133 -0.53311830 -0.13646076  0.44818762
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

**<span style="color: green;">Checkpoint #7: Why do we not have to tell the 'jackknife' function how many replicates to do?</span>**

Let's compare this with what we would have obtained from bootstrapping


```r
results2<-bootstrap(x,1000,theta)
mean(results2$thetastar)-mean(x)  #this is the bias
```

```
## [1] -0.0233
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9207578
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
##     original      bias    std. error
## t1*      4.5 -0.02402402   0.9208362
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 1 3 4 5 6 7 8 9 
## 1 2 1 1 1 1 1 2
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
## [1] 0.0141
```

```r
se.boot
```

```
## [1] 0.892585
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

