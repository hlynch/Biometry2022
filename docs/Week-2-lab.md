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
## 1 2 3 5 6 7 9 
## 2 1 1 2 2 1 1
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
## [1] -0.0113
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
## [1] 2.73067
```

```r
UL.boot
```

```
## [1] 6.24673
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##   2.5%  97.5% 
## 2.6975 6.2000
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
##    [1] 3.8 4.2 2.1 1.5 3.6 5.7 3.6 4.3 4.8 5.5 5.1 5.2 5.3 3.6 4.9 5.8 4.7 5.3
##   [19] 4.7 4.9 3.4 3.7 4.7 4.1 4.5 4.3 4.7 4.6 5.3 5.7 4.3 4.6 6.0 4.1 3.9 4.6
##   [37] 4.8 4.6 5.2 3.4 4.6 4.4 5.6 5.6 3.7 6.2 5.7 4.3 4.2 3.9 4.4 5.6 4.7 3.9
##   [55] 3.8 4.8 3.2 4.3 2.8 3.9 4.2 2.7 1.8 5.6 5.7 6.0 4.6 4.7 3.5 5.0 1.9 4.8
##   [73] 6.5 5.7 5.8 5.1 4.2 4.3 3.8 4.8 5.0 2.6 3.1 4.8 6.4 4.0 4.9 4.5 5.2 5.6
##   [91] 3.9 3.4 3.0 4.8 6.1 4.2 4.0 4.3 4.5 6.0 4.1 2.6 3.2 4.3 4.6 5.8 4.8 4.0
##  [109] 5.6 5.7 4.6 3.1 3.0 2.9 6.1 5.0 2.5 3.7 3.3 4.5 3.1 4.5 3.1 5.1 5.3 3.5
##  [127] 5.0 4.6 4.3 4.5 4.2 4.3 4.2 6.0 4.5 4.3 4.2 3.6 5.5 5.3 3.9 3.8 5.0 3.8
##  [145] 4.8 4.0 4.9 2.4 6.5 4.6 3.6 4.5 4.4 4.1 4.6 6.3 4.6 3.6 3.6 3.4 4.3 5.1
##  [163] 3.9 5.6 5.0 4.1 4.6 4.9 3.9 4.1 4.8 4.7 5.0 5.5 3.0 4.0 2.0 5.2 3.9 3.9
##  [181] 3.1 3.9 5.0 5.0 3.2 3.2 4.3 4.8 3.0 4.8 6.2 4.1 4.9 5.0 3.0 5.4 5.4 5.0
##  [199] 5.1 4.0 4.3 4.0 5.9 4.7 4.4 4.3 3.4 5.2 4.2 4.3 5.0 3.9 4.8 4.7 5.3 4.6
##  [217] 5.7 5.2 4.9 3.3 3.9 6.0 4.5 4.6 4.7 4.1 5.4 4.8 5.2 4.2 6.0 4.0 3.4 5.9
##  [235] 4.4 5.1 4.7 3.0 5.6 6.5 4.7 4.4 5.0 5.1 5.4 4.2 3.6 4.3 5.3 3.8 3.1 4.6
##  [253] 3.2 4.7 5.5 3.7 4.4 5.0 3.7 4.6 4.4 4.4 2.4 3.0 4.2 4.9 4.5 6.0 3.7 5.8
##  [271] 5.9 4.2 2.8 5.4 4.5 2.9 5.0 3.1 3.7 3.6 4.6 3.6 6.0 3.5 3.8 4.3 5.7 4.8
##  [289] 4.8 5.0 3.6 4.8 4.2 6.1 4.5 4.1 5.4 4.0 3.3 3.4 4.8 4.3 4.0 4.5 2.3 4.0
##  [307] 3.9 4.6 3.9 2.9 4.1 6.1 5.5 6.2 3.4 2.8 4.0 3.5 3.6 5.2 3.9 4.2 2.7 6.2
##  [325] 4.2 3.9 4.0 4.3 4.4 5.0 4.6 2.8 3.5 5.4 4.5 4.3 2.8 4.5 5.6 4.3 3.9 4.1
##  [343] 2.2 3.8 3.8 3.4 5.0 4.9 6.2 3.9 3.6 5.0 5.5 3.4 3.9 2.7 5.6 5.8 5.6 4.8
##  [361] 3.9 4.7 3.8 6.0 5.7 5.0 5.2 6.3 5.0 4.0 3.9 4.2 4.6 2.8 3.3 3.9 4.4 4.0
##  [379] 5.7 3.6 3.5 4.5 3.8 5.7 4.7 3.0 5.7 4.9 4.2 4.3 4.9 6.0 4.7 5.9 3.9 2.1
##  [397] 3.5 7.1 5.0 5.2 5.4 4.4 5.1 4.8 5.5 5.3 3.9 4.8 3.3 5.1 4.6 3.8 4.4 3.8
##  [415] 2.6 4.5 4.6 4.7 3.8 4.3 5.3 4.7 4.7 4.2 4.5 5.2 6.1 3.4 2.9 4.2 5.2 3.8
##  [433] 5.1 2.6 4.1 4.5 4.2 4.1 3.7 2.4 3.7 5.9 3.6 5.4 5.9 3.9 4.4 3.9 5.8 4.5
##  [451] 4.1 3.9 4.1 5.3 4.4 3.6 4.7 2.8 4.7 3.6 3.7 3.7 4.5 4.7 5.3 6.7 4.5 4.2
##  [469] 4.3 5.9 4.7 5.9 5.1 4.4 4.0 4.2 4.8 4.6 4.2 3.7 5.0 5.2 4.0 3.7 6.2 4.6
##  [487] 2.8 4.3 4.5 4.3 4.1 5.3 4.6 3.4 4.8 4.1 3.9 4.5 4.3 5.1 3.2 5.3 4.0 3.1
##  [505] 2.6 2.5 4.7 4.8 4.4 5.4 5.5 3.9 4.3 1.7 4.4 3.7 4.2 3.9 4.4 4.1 5.4 2.6
##  [523] 6.2 2.9 5.2 4.2 5.0 3.6 6.4 5.1 3.2 5.2 3.7 4.9 4.5 4.4 4.1 4.9 4.1 3.8
##  [541] 3.5 3.9 2.8 5.0 4.9 3.5 4.5 6.2 3.5 4.8 3.7 5.7 4.6 4.6 3.1 4.0 3.8 3.4
##  [559] 5.9 5.3 5.3 4.8 3.4 3.5 5.7 5.9 5.0 5.4 6.6 4.6 5.0 5.7 3.9 4.6 4.4 4.2
##  [577] 4.0 3.5 5.5 3.9 4.7 4.0 6.2 4.0 4.3 3.5 4.6 5.4 5.4 5.1 4.2 4.2 5.4 3.1
##  [595] 4.7 3.3 5.4 4.1 3.2 5.9 4.2 5.4 4.6 3.9 4.7 4.7 2.8 4.1 4.1 3.9 4.7 4.9
##  [613] 2.2 3.3 3.8 3.1 2.7 5.5 3.7 5.5 4.9 5.9 7.0 4.8 5.6 3.9 3.7 3.5 3.6 4.9
##  [631] 4.1 1.7 3.9 4.9 4.4 5.5 5.2 5.4 4.1 3.4 3.9 5.1 3.9 4.7 6.0 6.6 5.1 5.8
##  [649] 4.5 3.8 4.9 4.0 3.9 4.2 5.3 4.2 5.3 5.5 6.2 5.2 4.1 4.2 5.9 3.7 5.8 6.7
##  [667] 5.3 4.5 5.2 4.6 5.5 4.5 7.0 3.4 4.3 2.8 5.7 5.7 5.9 3.9 3.7 5.6 5.2 6.1
##  [685] 3.5 4.3 3.2 3.4 5.0 3.6 5.5 2.5 4.4 4.1 4.5 5.4 5.1 3.8 2.8 4.5 3.9 4.1
##  [703] 4.5 5.6 5.1 5.1 4.4 4.2 5.1 5.7 2.8 4.5 5.1 5.2 4.5 5.7 5.8 3.5 4.6 3.8
##  [721] 3.5 3.9 4.5 2.8 2.6 5.4 3.9 3.7 5.1 3.6 3.8 3.5 3.8 3.7 4.9 4.1 5.8 5.1
##  [739] 3.8 5.6 6.0 5.6 4.2 4.6 4.3 3.4 4.9 5.6 5.3 5.6 4.4 5.9 5.9 4.2 4.6 4.8
##  [757] 4.5 4.2 4.0 4.2 5.8 2.5 5.8 3.8 4.1 5.1 3.9 3.0 4.2 4.4 6.3 4.2 4.5 3.3
##  [775] 4.6 4.8 4.7 4.7 4.3 4.1 3.6 4.2 5.4 5.8 4.8 5.0 4.9 5.2 5.1 4.2 4.6 3.1
##  [793] 5.2 5.9 5.0 5.9 4.7 4.3 4.4 4.8 5.1 5.0 6.5 4.6 2.8 5.7 6.2 4.3 3.2 6.6
##  [811] 5.9 3.1 4.2 2.8 3.7 3.0 5.8 4.7 4.4 5.2 3.7 3.2 4.2 5.1 4.2 5.2 3.9 4.6
##  [829] 3.2 5.0 5.1 4.2 4.9 4.7 4.6 4.7 3.5 5.5 5.4 4.9 3.6 3.6 4.3 5.4 2.5 5.1
##  [847] 3.5 4.9 5.2 4.0 4.6 6.2 4.0 4.8 2.8 5.7 5.1 5.1 3.0 3.6 3.7 4.3 5.6 4.0
##  [865] 5.0 4.0 4.6 2.5 3.4 4.8 4.4 5.4 4.8 4.5 5.1 3.5 3.4 2.8 4.8 4.4 4.7 4.0
##  [883] 5.1 3.7 4.9 4.1 2.2 4.9 4.6 4.0 4.7 4.7 3.7 4.9 5.0 4.0 4.7 4.4 5.0 3.8
##  [901] 4.8 5.6 5.5 5.3 5.2 6.8 5.5 4.5 4.9 4.8 3.9 5.7 3.5 4.3 4.7 6.1 3.4 5.3
##  [919] 4.2 4.9 3.8 3.7 3.3 3.3 4.4 3.2 6.2 3.6 5.6 3.5 5.0 3.7 4.0 4.9 4.0 4.7
##  [937] 4.2 4.2 5.8 3.4 5.4 4.2 4.7 5.0 3.8 2.9 3.3 6.2 4.7 4.8 4.2 4.1 5.3 3.1
##  [955] 4.6 3.4 6.1 4.8 5.2 4.4 2.8 4.0 4.6 5.6 5.2 4.5 4.1 2.8 3.0 4.4 5.5 3.2
##  [973] 6.0 4.5 4.4 4.5 6.3 4.0 4.6 4.7 5.7 3.5 3.5 4.3 4.5 5.5 6.3 5.2 4.2 4.4
##  [991] 3.9 4.5 3.9 4.3 2.9 3.9 4.8 4.6 5.3 4.3
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
##   2.6   6.2
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
##    [1] 5.0 4.9 4.1 4.4 4.8 3.1 5.2 4.5 4.8 4.4 5.3 6.1 6.6 5.7 5.2 5.2 4.2 5.4
##   [19] 3.6 5.0 6.7 5.0 3.6 4.5 5.5 4.1 4.0 3.4 5.3 5.7 3.4 3.8 4.5 4.7 3.9 4.4
##   [37] 6.0 4.4 5.0 3.4 4.3 4.5 4.5 3.4 3.1 4.7 4.5 2.6 4.7 3.4 3.1 4.0 4.6 3.5
##   [55] 2.7 4.8 6.5 6.0 3.3 3.1 4.7 3.3 3.2 3.8 4.5 5.2 5.5 4.3 6.0 3.7 2.5 5.3
##   [73] 3.9 4.8 3.6 3.7 5.5 4.7 5.2 4.1 2.8 5.0 4.3 3.5 4.5 4.9 4.9 5.3 3.7 4.9
##   [91] 3.4 6.8 5.2 4.9 5.6 3.3 5.1 5.8 5.6 6.0 5.4 6.5 5.9 4.1 2.9 5.4 3.8 4.5
##  [109] 4.8 4.2 4.7 4.9 5.0 6.8 4.6 5.3 2.2 3.3 6.6 2.2 3.0 4.2 3.9 3.4 4.8 4.4
##  [127] 3.7 6.7 4.5 4.1 4.6 6.3 4.7 3.9 5.4 2.9 3.9 5.4 5.2 3.9 4.0 5.7 3.3 5.6
##  [145] 3.8 4.6 5.7 3.9 3.7 2.5 5.9 5.2 4.1 3.9 4.0 4.2 4.2 4.7 6.0 5.3 3.0 4.9
##  [163] 5.0 5.5 4.0 4.6 4.4 4.4 3.7 4.5 4.9 4.5 5.9 4.1 5.5 3.2 3.2 5.2 4.3 4.3
##  [181] 4.7 3.4 4.4 4.9 4.5 4.5 6.9 4.4 5.0 4.5 4.8 3.7 4.5 4.8 3.2 5.5 4.7 4.4
##  [199] 4.9 5.7 3.8 4.8 3.6 3.4 4.8 3.9 3.9 3.8 3.6 3.6 3.5 4.1 4.8 5.3 4.4 4.7
##  [217] 4.5 6.0 5.4 2.8 5.7 4.2 4.2 3.8 5.0 4.6 5.1 3.4 4.5 3.9 4.9 3.8 6.1 6.8
##  [235] 5.4 6.0 5.5 5.6 4.6 4.0 6.2 4.3 5.0 4.5 2.2 5.0 4.1 3.4 4.2 4.8 3.6 4.2
##  [253] 4.1 4.3 3.8 4.6 5.3 4.9 3.7 4.3 3.7 5.0 4.8 4.1 4.0 6.6 5.1 4.1 4.4 4.8
##  [271] 5.5 4.2 3.8 5.3 5.8 3.9 4.1 3.1 3.5 5.0 2.9 3.6 6.2 4.6 3.8 4.5 4.3 3.8
##  [289] 5.1 4.0 3.5 4.4 3.9 5.5 3.8 4.6 5.0 4.1 4.1 5.0 3.1 4.3 3.9 4.1 4.7 5.6
##  [307] 5.2 5.9 4.7 4.3 4.5 3.5 3.0 4.6 5.3 4.2 6.3 3.3 4.6 4.6 3.9 5.2 2.6 5.2
##  [325] 4.8 4.7 5.9 3.9 3.8 5.5 4.6 4.1 4.6 4.4 3.9 3.9 2.5 5.1 3.2 6.3 3.4 4.0
##  [343] 5.5 3.4 4.6 5.4 4.8 6.1 4.4 4.1 2.9 4.2 4.5 5.8 5.4 4.5 4.9 4.8 5.7 5.1
##  [361] 3.6 4.6 5.3 5.5 4.6 4.2 5.2 2.8 4.2 4.7 4.2 4.7 3.8 5.1 4.6 4.8 5.6 4.8
##  [379] 4.9 3.9 4.4 3.4 4.2 4.0 4.1 4.8 4.2 5.1 4.6 4.8 3.6 3.6 5.1 2.9 4.5 5.3
##  [397] 5.4 3.1 6.4 5.9 4.7 4.6 4.9 5.4 5.8 4.7 3.1 5.5 5.1 3.8 4.0 2.8 4.7 4.2
##  [415] 3.6 4.7 4.8 3.8 5.0 4.9 4.4 4.9 5.6 5.7 4.9 4.9 2.5 4.9 2.8 5.3 5.8 3.4
##  [433] 5.0 6.4 4.2 3.8 4.1 4.2 5.7 6.4 3.4 4.2 4.8 3.3 4.8 3.8 5.6 3.2 3.6 3.7
##  [451] 4.1 6.0 4.4 3.8 4.0 4.4 4.9 4.3 3.3 5.1 4.9 5.4 3.5 5.9 5.4 5.8 4.8 5.9
##  [469] 2.8 5.3 5.7 3.8 4.2 3.5 5.5 5.1 6.6 3.7 4.3 5.0 4.2 6.2 5.4 3.4 4.4 3.7
##  [487] 3.9 3.5 3.1 4.9 4.2 5.0 5.5 4.0 3.2 3.2 3.6 4.7 4.7 4.9 3.0 3.1 3.7 6.1
##  [505] 5.8 4.9 3.3 4.6 4.4 4.7 3.9 3.6 5.6 4.5 5.4 6.1 3.3 4.4 4.7 5.6 4.7 5.4
##  [523] 4.0 4.3 5.3 5.9 3.0 2.4 6.1 5.0 4.2 4.9 3.8 5.0 4.9 5.8 7.0 4.2 4.1 4.8
##  [541] 3.5 5.0 4.9 6.3 4.6 4.6 4.2 5.6 4.0 4.9 3.0 4.2 4.7 4.5 4.4 3.6 4.6 4.4
##  [559] 5.2 4.3 4.8 5.2 5.1 4.2 3.1 4.5 4.2 4.8 4.4 3.8 3.8 5.0 5.9 4.3 3.7 4.1
##  [577] 4.5 6.7 3.5 5.6 3.8 4.2 3.4 3.7 4.3 4.1 4.4 5.4 4.6 4.5 3.1 4.8 4.0 4.9
##  [595] 4.9 4.9 4.0 2.7 6.1 4.1 4.5 4.6 5.3 5.1 5.7 3.8 5.7 3.9 4.9 4.6 3.5 5.3
##  [613] 5.3 4.8 4.6 3.9 5.9 6.0 3.7 4.8 5.6 5.6 5.5 4.3 3.3 6.2 4.9 3.6 6.8 3.1
##  [631] 3.9 4.0 4.0 4.2 5.8 4.6 5.2 3.9 5.4 3.2 5.1 4.5 4.9 5.9 6.1 5.0 5.5 4.4
##  [649] 3.9 5.2 4.1 5.0 4.4 3.8 4.6 3.8 4.2 4.5 3.0 4.2 3.9 5.7 4.4 4.4 5.1 3.5
##  [667] 5.4 6.0 3.6 3.0 4.3 4.2 4.3 5.6 5.0 5.7 5.2 6.5 4.6 2.1 3.0 5.7 5.3 5.1
##  [685] 4.3 3.8 3.8 3.9 4.0 3.6 4.6 3.5 5.7 5.4 3.9 5.1 4.3 2.1 3.8 6.6 3.1 4.6
##  [703] 3.5 5.0 4.9 3.9 5.8 3.5 6.3 5.1 3.0 3.4 6.3 5.7 4.1 4.7 5.4 5.2 3.8 5.0
##  [721] 3.5 4.1 3.4 4.1 5.0 6.0 5.4 6.1 3.6 4.3 3.9 6.1 3.9 3.9 2.8 5.6 4.6 5.0
##  [739] 4.9 3.4 3.8 4.9 5.0 4.5 5.3 3.9 4.4 5.2 4.7 5.4 3.8 5.5 4.2 4.0 4.3 3.7
##  [757] 4.6 4.1 4.3 3.7 4.7 3.8 4.0 5.8 5.3 3.8 4.1 4.7 3.2 2.7 4.2 4.9 3.8 4.4
##  [775] 4.4 3.8 3.6 5.3 5.8 3.8 2.4 5.1 3.4 3.3 2.6 3.0 3.7 5.7 4.0 3.4 4.8 4.2
##  [793] 4.7 2.8 4.9 4.7 3.0 4.6 3.9 5.0 5.2 5.3 4.9 4.5 4.0 4.7 5.0 6.6 5.1 4.7
##  [811] 4.9 4.1 5.9 5.1 4.4 3.8 5.4 5.0 3.9 4.1 4.3 3.7 3.0 3.3 3.6 3.4 5.2 5.7
##  [829] 4.7 5.4 5.6 5.8 4.6 4.7 3.1 3.9 5.0 3.0 4.9 3.2 4.9 4.3 5.2 4.1 3.9 3.1
##  [847] 4.6 4.2 4.4 5.6 4.6 4.0 4.4 4.0 2.6 4.4 4.6 4.9 6.3 4.2 3.6 5.3 5.2 4.0
##  [865] 5.7 5.3 3.7 3.3 4.0 5.2 3.9 4.4 5.0 4.1 5.2 4.5 2.2 5.2 5.1 5.5 4.2 6.4
##  [883] 2.6 2.4 4.0 4.0 4.8 3.9 4.8 5.1 4.9 3.6 3.8 3.2 4.7 5.8 5.0 4.4 4.7 4.3
##  [901] 4.4 5.8 4.7 5.4 5.9 4.1 3.4 6.9 4.7 4.0 3.3 5.3 4.1 4.3 3.5 4.9 3.2 3.7
##  [919] 5.9 2.4 6.1 3.3 3.8 4.5 4.9 5.4 4.9 3.2 2.9 4.7 3.3 3.4 4.4 3.7 4.9 4.0
##  [937] 3.3 3.5 4.3 6.7 4.6 4.9 4.5 4.5 2.1 5.0 4.5 4.2 3.4 4.4 3.3 4.8 5.0 4.3
##  [955] 4.3 2.5 6.1 5.1 5.5 3.9 4.1 5.6 5.2 5.3 4.0 5.4 5.2 4.1 6.0 3.4 4.0 4.2
##  [973] 4.1 4.5 5.4 3.0 5.0 3.8 5.4 4.6 4.4 4.9 4.6 4.5 5.1 4.3 4.7 5.7 4.9 5.7
##  [991] 3.9 4.5 3.5 4.7 5.1 3.8 4.1 4.9 5.4 3.9
## 
## $func.thetastar
## [1] 0.0036
## 
## $jack.boot.val
##  [1]  0.5261628  0.4224377  0.2866864  0.1724242  0.0860274 -0.0862069
##  [7] -0.1629738 -0.2485714 -0.3072046 -0.4798883
## 
## $jack.boot.se
## [1] 0.9442637
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
##    [1] 5.3 3.4 4.9 4.5 4.2 6.1 5.1 4.4 4.3 3.8 4.9 5.3 5.2 3.7 5.3 4.8 6.0 4.1
##   [19] 5.6 2.4 5.4 3.7 3.4 3.6 2.3 3.5 3.5 5.2 3.3 4.8 4.4 5.2 5.2 3.4 4.9 3.1
##   [37] 4.3 4.4 5.0 4.7 4.0 4.2 4.7 3.0 4.7 5.3 3.6 3.4 3.9 5.6 6.4 4.7 4.3 5.1
##   [55] 5.9 3.8 5.0 4.7 5.1 3.1 5.2 4.1 5.4 3.9 2.9 5.0 4.5 4.3 5.7 4.1 5.5 4.4
##   [73] 3.2 4.3 3.3 4.8 4.9 4.0 4.9 4.0 5.6 4.7 4.4 4.4 5.2 4.1 5.3 3.9 5.3 4.5
##   [91] 4.9 5.4 5.1 6.6 6.0 4.6 5.3 3.4 4.4 4.2 3.7 4.8 4.0 4.8 5.3 5.7 4.9 3.2
##  [109] 4.1 4.6 3.1 4.8 4.5 3.1 4.5 6.1 4.2 3.6 5.7 5.2 4.6 6.1 4.9 4.6 5.2 2.9
##  [127] 5.4 4.1 5.1 4.9 4.0 4.3 5.4 5.0 4.4 5.9 4.1 4.6 4.2 4.5 4.8 4.0 2.7 4.9
##  [145] 4.6 6.2 3.8 4.8 1.8 5.7 6.1 5.5 3.8 4.1 4.6 3.7 3.9 3.3 4.9 4.1 3.5 4.7
##  [163] 4.3 3.8 5.5 5.2 3.3 4.9 5.5 3.8 4.9 4.4 4.6 4.2 4.5 6.4 5.4 5.0 5.1 5.2
##  [181] 2.5 5.3 4.1 6.1 4.6 4.3 5.1 5.3 5.6 5.9 4.8 6.3 5.0 4.7 5.0 4.9 2.8 4.6
##  [199] 3.6 4.4 3.4 5.0 4.7 3.8 5.4 4.8 4.1 6.1 4.1 3.2 5.6 3.5 4.6 3.9 3.5 4.8
##  [217] 3.1 5.3 3.4 5.5 4.0 3.6 5.3 3.0 3.4 4.6 3.5 4.1 2.8 4.6 4.2 4.2 3.7 4.7
##  [235] 4.8 3.4 4.8 5.3 3.1 3.5 3.1 5.1 5.2 5.0 5.2 3.8 3.9 2.3 3.8 4.1 3.6 4.0
##  [253] 6.0 4.8 5.7 4.1 4.0 4.6 4.4 4.8 4.9 3.8 5.7 3.1 4.3 4.9 4.0 6.2 5.9 4.5
##  [271] 5.2 3.0 4.5 3.6 4.9 3.6 4.4 3.1 3.9 5.6 3.6 3.4 4.1 4.0 4.4 3.4 4.3 4.4
##  [289] 5.9 4.9 5.0 3.7 5.6 4.1 4.6 4.6 5.8 3.7 5.4 5.6 4.4 4.2 5.3 5.1 5.6 4.0
##  [307] 3.8 4.1 5.6 5.4 4.6 4.1 5.9 5.7 5.0 5.1 6.2 4.8 3.9 2.6 2.9 4.2 5.5 4.1
##  [325] 3.6 4.6 4.0 5.5 5.9 4.1 4.1 3.8 6.6 3.0 5.2 4.8 3.1 4.4 4.9 5.4 4.9 4.7
##  [343] 4.5 5.2 5.0 4.3 4.0 4.7 5.4 3.4 6.5 4.1 3.4 5.1 5.1 5.0 4.5 5.9 4.9 3.9
##  [361] 4.2 4.4 5.2 5.4 4.9 3.9 4.9 4.4 5.0 2.0 4.8 3.0 5.0 3.6 5.6 4.4 5.1 5.5
##  [379] 2.9 5.7 4.3 5.7 3.4 5.5 6.0 5.1 4.6 5.4 6.1 5.0 4.7 3.5 4.7 4.0 4.8 4.5
##  [397] 3.8 5.9 3.1 3.8 4.1 4.9 3.1 2.9 3.9 3.7 3.1 3.6 4.6 4.4 4.9 4.8 5.0 4.4
##  [415] 3.2 3.5 4.6 3.9 4.0 5.0 5.2 4.9 5.8 4.6 5.1 5.3 3.8 3.2 4.8 6.2 4.8 4.3
##  [433] 5.2 5.1 4.1 3.4 2.2 5.5 3.7 4.7 3.7 4.9 5.8 4.9 4.1 4.6 4.8 5.9 4.0 5.0
##  [451] 4.3 3.0 4.7 3.6 5.7 2.1 4.5 4.9 2.9 4.8 4.4 2.9 5.4 2.4 3.5 2.6 5.2 4.3
##  [469] 5.3 4.7 4.6 4.2 4.6 4.6 4.5 5.6 3.3 5.5 5.1 5.6 5.6 4.8 4.6 4.9 4.6 3.2
##  [487] 6.0 5.9 4.6 5.1 5.4 4.6 6.0 3.7 5.6 5.2 3.6 5.1 5.4 4.8 6.2 5.0 4.4 5.5
##  [505] 5.8 5.3 4.2 3.5 6.4 4.6 3.3 3.2 4.4 4.7 5.3 4.7 4.4 5.9 5.0 5.8 3.5 4.1
##  [523] 5.0 4.8 4.2 5.1 4.3 4.0 4.2 3.6 5.1 4.0 4.7 3.6 5.7 5.9 2.0 4.7 4.4 3.4
##  [541] 4.4 3.9 3.4 5.0 3.8 4.0 4.2 3.9 4.7 4.3 5.3 4.9 4.6 3.8 4.1 4.0 4.7 3.8
##  [559] 5.6 4.4 5.4 2.4 3.3 4.7 4.0 4.9 5.8 2.6 5.1 3.5 4.5 4.7 3.4 4.4 2.2 3.6
##  [577] 4.6 4.6 4.9 5.7 3.7 4.6 4.7 3.5 6.4 3.8 6.0 4.1 4.5 4.9 5.4 2.7 4.1 4.2
##  [595] 3.8 5.1 5.7 4.7 6.1 3.7 4.1 4.2 4.8 3.5 3.7 5.3 3.1 5.2 6.2 4.8 4.5 4.4
##  [613] 4.6 5.3 4.0 3.4 2.3 5.2 4.3 3.9 3.5 3.7 4.4 4.6 6.1 3.0 3.7 4.3 4.5 4.1
##  [631] 4.4 5.2 4.4 5.0 5.6 4.3 3.5 5.0 4.2 4.6 5.4 3.8 5.5 5.9 5.4 4.3 3.8 4.4
##  [649] 3.8 3.8 3.9 3.5 6.1 3.9 2.7 4.6 4.0 4.0 5.3 2.9 5.6 4.3 3.9 4.0 4.9 4.4
##  [667] 2.8 6.4 2.9 5.8 3.6 2.9 3.7 4.8 3.5 4.7 3.6 4.0 4.7 4.4 5.1 2.3 2.9 4.5
##  [685] 3.8 3.5 4.3 5.6 5.2 4.1 4.0 4.8 5.3 4.6 3.9 4.4 3.5 4.7 4.1 4.1 3.3 4.6
##  [703] 4.7 3.1 3.9 3.7 3.7 5.6 4.2 4.6 3.1 2.9 6.1 6.0 6.2 3.3 5.1 3.8 5.2 3.7
##  [721] 5.4 4.9 4.0 3.0 4.6 5.9 5.8 7.0 4.4 4.9 5.4 4.8 3.8 3.8 4.2 4.2 5.0 5.5
##  [739] 6.4 4.0 4.3 4.5 3.0 5.3 4.3 4.3 6.7 5.3 3.4 3.9 5.4 3.9 4.6 4.6 4.4 3.2
##  [757] 3.5 2.5 4.4 5.5 3.3 5.5 4.9 6.1 5.6 3.2 5.3 5.6 2.5 5.4 3.2 4.2 4.5 5.2
##  [775] 6.2 4.9 4.1 4.1 4.6 4.8 2.6 4.4 3.7 5.4 5.0 3.2 5.8 3.7 4.4 4.2 4.0 4.7
##  [793] 5.1 2.9 4.6 4.6 5.6 3.6 5.9 5.0 3.3 3.9 3.5 5.3 5.7 5.6 4.7 3.4 3.6 4.1
##  [811] 3.5 3.1 3.6 6.5 4.5 2.7 4.8 3.8 4.7 3.3 4.6 4.6 4.8 3.6 3.6 4.8 4.2 5.8
##  [829] 4.2 4.1 4.6 4.5 4.2 5.5 3.3 3.7 5.0 4.0 4.8 5.8 3.7 4.1 4.7 4.9 6.4 4.1
##  [847] 3.5 5.2 5.1 3.3 3.6 2.2 5.6 3.5 5.2 4.5 4.0 3.7 4.8 5.0 5.1 2.9 5.5 5.1
##  [865] 3.5 4.4 5.2 3.6 3.9 6.8 6.0 5.1 4.9 4.2 2.5 4.2 4.4 5.7 4.8 4.1 5.2 5.7
##  [883] 2.9 6.5 4.4 3.9 3.8 3.5 5.7 2.0 4.2 4.7 4.6 2.7 4.9 3.8 4.3 3.3 4.0 5.1
##  [901] 3.1 5.5 2.6 4.3 5.8 3.4 2.9 5.0 3.9 3.7 4.9 4.3 4.6 4.1 3.2 3.8 5.9 2.7
##  [919] 4.9 4.9 4.0 4.7 5.6 4.5 5.0 3.8 4.1 4.7 3.9 2.4 4.0 3.7 4.0 3.8 3.6 5.2
##  [937] 4.1 4.1 4.0 5.6 4.0 4.7 3.8 5.0 4.1 4.3 4.5 5.4 3.7 4.8 4.7 4.1 4.2 2.1
##  [955] 5.3 3.6 5.9 3.5 3.5 4.3 4.1 3.2 4.1 2.9 4.8 4.7 5.3 4.9 3.5 4.4 5.3 4.9
##  [973] 4.5 3.9 3.1 4.3 5.6 3.5 3.9 4.4 3.7 4.5 3.2 5.0 4.6 4.5 4.5 5.5 6.1 4.3
##  [991] 4.6 3.9 4.5 4.1 3.2 3.5 4.0 3.5 5.7 4.5
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.460 5.400 5.200 5.272 5.000 5.100 4.900 4.700 4.600 4.496
## 
## $jack.boot.se
## [1] 0.9524523
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
## [1] 0.9668171
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
##   2.516523   3.854369 
##  (1.059333) (1.795231)
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
## [1] -0.7967214 -0.3124276  0.3288133  0.9556768  0.8929068  0.2268938
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
##    [1]  0.380612825  0.599329183  0.847583585  0.749998032  1.212527958
##    [6]  0.412206676  1.296212541  1.199508563 -0.023069056  0.743778064
##   [11]  1.391249687 -0.109947599  1.932532093  1.244054931  0.555505783
##   [16]  0.595616661  0.956077243  0.623761755  0.517607902  1.270837297
##   [21]  1.465585211  0.146291853  0.999710282  0.522359647  1.279319531
##   [26]  0.965883948  1.250135915  1.588585872  0.696917705  1.315490096
##   [31]  0.674439103  1.739575490  0.308589061  0.090740530  0.404032342
##   [36]  1.548257774  0.967294890  0.450336928  0.727354918  0.818229739
##   [41]  0.797987200  0.043293319  2.260374076  1.204770845  0.716286030
##   [46]  0.560926793  1.239990283  0.623434202  0.727709513  0.682674616
##   [51]  0.718548553  0.355871258  0.140598433  1.125833152  1.237820440
##   [56]  1.632776156  0.749151822  0.587608047  1.383389762  1.478112153
##   [61] -0.059981659  0.480753819  1.263147453  0.570024866 -0.176887658
##   [66]  0.432743007  1.047236902  0.713321958  0.967651077  1.153398576
##   [71]  1.202179084  1.647059274  2.480541419  1.743459154  0.414913974
##   [76]  0.681341668  0.548671913  1.252986540  0.135924632  2.287831791
##   [81]  0.539832304  1.116630273  0.316015080  0.326142254  1.560588589
##   [86]  0.965726303  1.745560228  0.687964306  0.736042814  0.516082897
##   [91]  1.098536617  0.267262033  0.551193626  1.292621451  1.746139382
##   [96]  0.968842220  1.507998957  0.917543493  1.078863062  1.855196508
##  [101]  1.481076513  0.473213402  0.527484865  0.997439339  0.699471837
##  [106]  0.177309244  0.804826599  0.396846659  0.417227793  2.597558657
##  [111]  0.704545715  1.199621315  1.623495785  0.537628982  1.770907071
##  [116]  0.497504862  1.605003012  0.312209713  0.947729486  1.359364169
##  [121]  0.827403202  0.234297788  0.321851549  0.481582324  1.092498846
##  [126]  1.184619592  1.037285973  0.384467868  1.229191308  0.826902900
##  [131]  1.225384747  0.413501650  0.986833091  0.119907241  1.656920218
##  [136]  0.392495142 -0.253420339  1.331620688  1.214028743  1.222472097
##  [141]  1.225508025  1.539906843  1.539158973  1.231417168  0.933382707
##  [146]  0.970469579  0.891760363  0.350178062  0.173249890 -0.153203880
##  [151]  0.319628398  1.000705796  1.303239452  1.984026092  0.959008906
##  [156]  0.695404347  0.916380882  1.583921173  0.997600072  0.308235838
##  [161]  0.724564943  0.304717845  0.312005107  1.258888145  1.659321170
##  [166]  1.222462021  0.312083866 -0.429158899  1.615061949  0.743590062
##  [171]  0.729716578  0.711831058  0.779533144  1.612281582 -0.063171966
##  [176]  0.347125357  0.945723131  0.543604146  0.113400862  0.471818676
##  [181]  0.571963919  1.209705279  1.220696885  0.418440568  0.575544683
##  [186]  0.536133881  1.252269090  1.613864465  0.392109809  0.939419603
##  [191]  0.928334370 -0.147738602 -0.115400245  0.534264333  0.681497759
##  [196]  1.061010211  0.560438997  1.343495579  0.270701125  1.214941935
##  [201]  1.005975969  1.387939376  0.312147302  1.077713503  1.414167004
##  [206]  2.016633207  0.766290579  1.000254419  1.247677604  1.654636878
##  [211]  2.093153422  1.237746397  0.775987648  0.476504535 -0.235993545
##  [216]  1.125979771  0.483963768  0.498164159  0.828321016  0.647250485
##  [221]  1.361145567  0.323703056  0.458117449  0.345452995  0.718376934
##  [226]  0.114732243  0.747734232  0.736233570  0.626736463  1.556570357
##  [231]  1.453665841  0.816841400  1.080223548  1.019826785  0.303188491
##  [236]  0.513773354  0.006227315  1.022514246  0.856164224  1.069214492
##  [241]  0.441178394  2.052370453  0.190720651  0.601794477  0.704871282
##  [246]  0.382771705  0.778986367  0.720253552  0.724360508  1.585063375
##  [251]  1.094953163  0.418312951  1.658901850 -0.091491076  1.218735745
##  [256]  0.719428457  2.499785034  2.076714139  0.222184566  0.745571716
##  [261]  0.703911785  1.006011840  1.016343082  1.025216630  1.104445093
##  [266]  0.370395243  1.210420583  1.000258541  0.848884740  0.962677385
##  [271]  0.436033881  0.030956350  1.229880304  0.700671329  1.168366228
##  [276]  0.694988752  0.492399088  0.440523155  1.177157369  1.601028662
##  [281]  0.280157871  1.227472415  1.040060133  1.681688034  0.305026380
##  [286]  0.541517320  1.240648628  0.751699131  1.167608474  0.472394075
##  [291]  1.254256491  0.617906189  1.458262539  0.356330702  0.508021702
##  [296]  1.076957660  1.225130666  1.212540171  0.790634225  0.678865888
##  [301]  0.681061973  0.663007212  0.702663544  0.772995558  2.091679057
##  [306]  1.265078132  1.258712927  0.687827414  0.706277875  0.437321553
##  [311]  0.966111340  0.810080178  0.445913591  1.129643093  1.604065245
##  [316]  0.359238995  0.766064611  0.222854015 -0.023137131  0.918819105
##  [321]  0.185943938  0.487122003  0.694114927  1.714698515  1.357901129
##  [326]  0.725289853  0.748490200  1.033505471  0.695139383  1.185279223
##  [331]  1.703937792  0.554173645  0.411156374  0.924424746  1.219371852
##  [336]  0.973931317  1.197114023  2.055379083  2.346969575  1.257024744
##  [341]  1.557297109  1.223355474  0.969657884 -0.127533352  0.385153242
##  [346]  1.676779894 -0.433659768  0.453119407  1.071721562  0.451202101
##  [351]  0.273838925  0.430536120  0.138307699  1.228524773  1.643905565
##  [356]  1.569637499  1.202179084  1.637738231  0.833112907  0.890840345
##  [361]  0.770846120  1.241677006  1.481468625  2.029679390  0.669904466
##  [366]  0.609045667  0.629948865  0.094758785  1.036251166  0.524660651
##  [371]  0.959810865  0.907550379  1.283825888  1.558768746  0.052861874
##  [376] -0.121487035  1.007935326  2.006232196  0.322216577  0.715737903
##  [381]  0.727623172  1.254908803  1.904800574 -0.095276441  0.463284262
##  [386]  1.191337585  0.310276908  0.296890348  1.708252107  1.716493488
##  [391]  0.695960357  1.965817976  0.954739650  0.274696989  1.008736835
##  [396]  1.472073349 -0.028346567  1.173285714  0.783252860  0.560926793
##  [401]  1.212742533  1.288961655  1.200935316  1.289841607  0.762882452
##  [406]  1.149463957  1.264086680  2.041354921  0.438789461  0.197017340
##  [411]  2.061209877  1.588934114  0.264000477  0.160553287  2.540525805
##  [416] -0.034085749  0.261440438  0.728656654  0.764434500  0.970786798
##  [421] -0.328509799  2.021546404 -0.196187598  0.805079975  1.600824092
##  [426]  0.527552343  0.868868321  1.150436840  0.907117697  0.749980879
##  [431]  1.131084025  0.785401103  0.158944250  0.381797225  1.159815488
##  [436]  0.639298853  0.496210352  0.439573498  0.120956720  1.234378493
##  [441]  1.278109416  0.721813512  0.987705119  1.965078264  0.485882082
##  [446]  0.309347911  0.531405965  0.809560155  0.710084083 -0.448008955
##  [451]  0.954739650  0.540497298  0.361242538  0.577276357  1.317030293
##  [456]  0.490679190  0.675258330  0.375207048  1.155986324 -0.135442074
##  [461]  0.983466568  0.931065709  1.269340415  1.844029432  0.189868547
##  [466]  0.501655845  0.716744213  1.192531182  0.662269695  1.263181979
##  [471]  0.725157160  1.692495910  0.807094825 -0.256976066  0.795402353
##  [476] -0.559374503  0.353989736  0.410067207  1.242001922  0.289295680
##  [481]  0.875876907  1.190688636  1.146988998  1.234457015  0.451074958
##  [486] -0.110740474  1.178530119  1.322808901 -0.030182236  0.721438694
##  [491]  1.017061034  0.709814301  1.307282348  0.282628329  0.147459936
##  [496]  0.798748606  0.743784152  1.338944907  2.473791839  0.852255943
##  [501]  2.601577353  0.988211452  1.524808025  1.033235541  1.287915547
##  [506]  0.289530594  0.549580360  0.194859269  0.835906538  0.523423001
##  [511]  0.431700955  0.725208901  2.381031878  1.273184627  0.164067894
##  [516]  0.048879241  1.225537345  0.955497218  0.632450075  1.448441928
##  [521]  0.681777478  0.734655011  0.485151222  0.278002287  1.275392691
##  [526]  0.151191251  0.755128183  0.702508880  1.232519870  0.494846130
##  [531]  0.701115107  1.020029944  0.678046213  0.553904608  0.707799429
##  [536]  0.083407278  1.553754788  0.996849053  2.032826487  0.929844510
##  [541]  1.599737168  0.348009329  0.788523688  1.375225488  1.187377350
##  [546]  1.180542045  0.856034483  2.030459016  0.435252020  0.115411048
##  [551]  2.059575237  0.775032461  0.923981559  0.493486754  0.825405200
##  [556]  1.038487553  0.815110653  0.728808489  1.233602034  2.076225838
##  [561]  1.361129363  1.214568792  2.106870749  0.697759430  0.554646093
##  [566]  0.301705758  2.610380024  0.825338523  0.133190578  0.307105814
##  [571]  0.525833849  0.175085679  0.552788503  0.826849933  0.814682501
##  [576]  0.676392957  1.741919360  0.730035360  2.096203892  1.441824792
##  [581]  0.824453573  2.023359351  0.174386967  1.560447053  0.704250196
##  [586]  0.935584027  1.010107941  0.742011481  0.678832627  1.185377989
##  [591]  0.208066849  0.396846659  1.221658343  0.696337531  0.577146730
##  [596]  0.322917887  1.280885942  0.215239096  0.254391146  2.406095602
##  [601]  0.932943971  1.588960327  1.259090905  1.161460172  0.777371566
##  [606]  2.426987751  0.806769198  0.716590120  1.450120281  1.141643557
##  [611]  0.977988578 -0.058278926  1.252497557  1.205774032  1.368579412
##  [616]  0.051416632  0.926488645  0.190831639  1.492011853  0.333531624
##  [621]  1.482038999  0.140335864  0.392596758  1.529768105  0.721842459
##  [626]  0.100787039  1.007981229  0.535679016  1.706460970  0.610066076
##  [631]  1.153976126  0.151406864  0.720974500  0.750296601  0.847757298
##  [636]  1.983232701  1.897183208  0.184139986 -0.108048908  0.766725318
##  [641]  1.013037050  0.832829951  0.940936323 -0.269769929  0.684871659
##  [646]  0.986759559  0.583582501  1.304732629  1.579023284  0.298742258
##  [651]  1.137159638  0.852159607  0.711212669  0.724186325  0.026475405
##  [656]  0.696135973  1.223934937  1.202474749  1.289522856  0.526129781
##  [661]  0.550113456  0.819961245  1.515130245  0.789035100 -0.307768304
##  [666]  1.010058792  0.291836067  1.243615481  0.928916264  0.700219848
##  [671]  2.539163643  1.074645020  0.540796000  1.284404488  0.517293512
##  [676]  1.153398576  1.572553031  0.904425282  0.966721076  0.973409988
##  [681]  0.679517971  0.476482937  0.302630512  0.945581248  0.687595769
##  [686]  0.951539012  2.211816736  1.193768844  1.211136821  0.370597370
##  [691]  1.230521366  0.454982667  0.021368129  1.176457185  1.693362872
##  [696] -0.066493142  1.273117944  1.383401689  1.270756716  0.024186259
##  [701]  0.204673029  2.531699085  0.498293055  0.966450346  0.533784304
##  [706]  1.245256466  1.140526724  0.360859507  0.092340178  0.731467112
##  [711]  0.747614466  0.706005370  0.805124886  1.661307516  0.520143549
##  [716] -0.093672189  0.994406998  1.303935539  1.257759355  0.461993059
##  [721]  0.229554599  2.462175319  1.152349405  0.748687546  0.761393616
##  [726]  0.347569518  0.724795927  0.092340178  0.974828084 -0.025374126
##  [731]  1.976877499  0.540242922  1.274843490  1.318880138  1.374204442
##  [736]  2.062978936  1.750655575  0.402207621  1.208673992  0.678577289
##  [741]  1.219765412  0.210844314  0.713814209  1.231417168  0.583664772
##  [746]  0.434130989  1.135886604  1.269356271  0.767497924  0.307360002
##  [751]  0.135755936  1.940210219  0.534189299  2.589362791  1.669033205
##  [756]  1.192999220  0.220363100  1.315853751  1.381486620  1.562820549
##  [761]  0.824984918  0.396494866  0.208596503  2.047571160  1.241554681
##  [766]  1.261950554  0.735810385  1.699963401  1.638472136 -0.104043643
##  [771]  0.463222313  0.514553414 -0.148036474  0.564785421  1.187057098
##  [776]  1.043740408  1.953414306  0.775032461  1.192999220  1.068378162
##  [781]  2.564160353  0.743418693 -0.007373241  0.649176574  0.767201956
##  [786]  0.542111029  0.994406998  0.386714827  0.707207665  0.479732567
##  [791]  1.640129153  1.212506308  0.490339551  0.391101845  1.252283124
##  [796]  0.996543051  1.464951281  1.357423293 -0.327148982  1.049510138
##  [801]  0.189946528  0.860113114  0.478645667  0.920467799  0.307000466
##  [806]  0.729714866  1.197085538  0.307804135  0.350920024  2.570835933
##  [811]  1.270154742  1.194555720  1.004866986  0.076601971  1.221641974
##  [816]  0.744610758  1.454732725  0.490550186  0.745371427  0.921299741
##  [821]  1.217056115  0.631006723  1.034893392  0.990096927 -0.094154716
##  [826]  0.253403246  0.586085354  1.036251166  1.547361213  2.020712813
##  [831]  0.459006268  1.326749387 -0.272670212  1.983827333  0.639064826
##  [836]  2.144574929  0.241517749  1.452944984  2.039224622  0.756861482
##  [841]  2.220174825  0.369332832  0.281627285  1.260856617  0.581772803
##  [846]  0.515200278  0.781267116  1.928461446  0.916147128  0.130546733
##  [851]  2.024881337  0.806076454  1.098894311  0.811426667  0.485505529
##  [856]  0.283790443  0.284916939  0.696356624  0.429059887  0.185612120
##  [861]  1.198416830  0.997609596  1.403200704  0.310257603  1.714766698
##  [866]  2.305660802  0.896731698  0.734283825  1.651007311  0.337397029
##  [871]  1.259738226  0.300977107  1.654200555  1.287368633  2.001721201
##  [876]  0.684135421  0.719020725  0.326553328  1.316990982  0.356137005
##  [881]  0.477374548  1.977867290  0.349742049  1.817568587  2.051495547
##  [886]  1.037276374  1.657354852  2.547580765  2.036118628  0.089204260
##  [891]  0.866856515  0.762058782  1.373807505  1.211367341 -0.033902238
##  [896]  1.219198104  0.665096887  1.561525116  1.238458914  0.676392957
##  [901]  1.737484123  1.945627915  1.091231114  0.175180514  0.189310653
##  [906]  1.074539598  0.189868547  1.672084320  0.343535585  1.300483253
##  [911]  0.394945215  1.052794635  1.315727978  1.359577660 -0.098007426
##  [916]  1.169627754  1.244312784  0.505245544  1.399440688  0.832958492
##  [921]  0.525085944  0.843134800  0.922140478  0.322721475  0.307230253
##  [926]  0.463887646  1.228275036  1.953232802  0.814652890  0.490800698
##  [931]  1.215682999  1.836226830  0.692707478  0.310923679  1.097382635
##  [936]  0.939555344  1.676055153  1.803426873  0.968565983  0.765902681
##  [941]  2.570905578  2.407751350  0.850845649  1.655262815  1.994195354
##  [946]  1.181865280  0.113508301 -0.511145391  0.992690175  2.480787219
##  [951]  1.201687489  0.270613433  1.814454362  0.048277865  1.058578428
##  [956]  1.233346812  1.595976809  0.846547452 -0.206738638  1.239641535
##  [961] -0.035054346  1.047417493  2.355364341  1.556973988  0.977541840
##  [966]  1.160062713  1.557648316  1.158615399  1.237378094  0.632545975
##  [971]  0.083345563  0.856126847  1.200840612  0.968074815  1.210074358
##  [976]  2.020686277  0.513158869  1.718350023  2.062212260  1.225384747
##  [981]  0.774894063  0.958756198  1.188723022  0.708786212  1.181893548
##  [986]  0.484978768  0.561462114  2.530266693  0.727732154  1.832745851
##  [991]  0.110253219  1.268614990  1.236138474  0.443704632  0.995344426
##  [996]  0.805079975  1.284680758  0.647890937  0.707573680  1.193247984
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
##   0.65290286   0.44324365 
##  (0.14016595) (0.09910955)
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
## [1] -0.3961840 -0.4020091  0.7362620  0.2230438  0.3110074 -0.1513045
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
## [1] -0.0235
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9360523
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
##     original       bias    std. error
## t1*      4.5 -0.005705706   0.8617556
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 3 4 6 7 8 9 
## 1 1 2 1 2 3
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
## [1] -0.0074
```

```r
se.boot
```

```
## [1] 0.9431515
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

