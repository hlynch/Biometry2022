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
## 0 1 4 5 6 7 9 
## 2 2 1 1 2 1 1
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
## [1] 0.0265
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
## [1] 2.804487
```

```r
UL.boot
```

```
## [1] 6.248513
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##   2.5%  97.5% 
## 2.7975 6.2025
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
##    [1] 5.5 4.0 3.9 4.9 4.5 5.5 4.4 4.4 4.0 5.6 5.2 6.4 3.8 4.2 4.9 5.9 4.3 4.0
##   [19] 4.8 6.0 3.1 4.9 3.8 3.4 4.8 5.1 3.5 3.3 4.8 5.9 7.3 4.1 3.6 3.2 6.8 4.8
##   [37] 5.8 4.2 6.6 3.8 3.7 5.2 5.7 3.5 4.0 4.4 5.9 5.0 4.5 4.2 5.0 5.5 4.9 5.2
##   [55] 4.5 4.8 2.5 4.3 3.0 4.2 6.0 4.6 3.4 4.2 5.1 2.9 4.0 3.1 4.9 5.8 4.3 4.7
##   [73] 5.4 3.8 5.1 4.6 5.2 4.8 4.9 3.4 6.0 3.1 5.3 3.8 4.7 5.1 4.4 2.6 4.4 5.6
##   [91] 6.4 6.1 5.2 5.6 4.8 5.3 4.3 4.2 5.3 3.9 3.8 2.9 5.1 3.9 4.4 5.9 3.3 4.4
##  [109] 4.9 3.0 4.3 5.1 5.6 3.7 4.9 3.9 3.7 4.0 5.3 4.5 4.7 3.6 4.1 3.6 3.5 4.3
##  [127] 4.4 3.6 4.8 5.1 5.9 3.6 3.8 4.2 4.0 4.7 5.7 4.0 5.3 4.4 4.3 7.0 3.5 4.0
##  [145] 4.1 5.4 4.9 5.3 5.0 2.8 4.6 3.2 4.3 3.5 3.6 5.7 4.2 4.2 4.9 3.7 5.2 4.7
##  [163] 4.6 4.2 3.4 3.3 4.1 5.5 4.6 3.4 5.1 4.7 4.0 6.1 4.4 4.7 4.3 4.4 4.1 5.6
##  [181] 4.9 4.2 6.4 4.6 6.3 4.8 4.3 5.9 4.2 4.1 4.3 3.5 3.7 4.5 2.9 5.3 4.4 3.4
##  [199] 4.9 4.7 1.5 2.9 5.4 6.1 4.6 5.4 5.5 4.3 4.5 2.7 5.5 3.1 5.2 3.2 4.8 4.8
##  [217] 3.4 3.8 5.0 5.2 4.3 3.9 4.3 4.4 4.1 4.9 5.2 5.3 3.4 5.5 4.8 5.0 5.8 4.6
##  [235] 6.0 4.8 5.0 2.6 3.8 5.2 5.7 3.5 5.0 4.7 3.5 4.1 5.7 2.2 6.2 6.0 4.4 4.5
##  [253] 6.0 4.6 5.8 4.5 4.9 3.9 3.6 5.1 6.4 4.3 2.9 3.6 4.7 3.1 5.7 4.3 4.9 3.7
##  [271] 4.0 3.6 4.4 3.3 5.0 4.6 5.8 3.9 5.7 3.7 3.5 5.5 5.5 4.7 4.3 4.5 5.1 3.4
##  [289] 5.1 2.8 5.3 4.8 3.7 2.8 4.0 3.8 4.5 5.4 5.6 4.4 3.0 4.7 4.9 4.6 4.8 4.2
##  [307] 5.1 5.9 5.8 4.8 3.7 4.4 3.9 6.2 4.4 4.5 4.0 4.2 4.7 4.7 2.6 2.8 4.6 3.4
##  [325] 5.1 5.5 5.6 5.8 6.4 6.3 5.0 4.1 4.1 4.8 5.5 5.0 3.6 4.3 5.3 3.2 4.5 4.0
##  [343] 4.9 3.4 5.9 3.9 3.9 4.5 4.7 5.2 3.0 4.9 3.6 3.4 4.3 4.8 5.1 3.5 4.7 5.0
##  [361] 4.2 4.2 5.9 3.9 4.8 4.4 5.2 4.6 3.7 4.4 4.8 2.5 3.7 4.1 4.8 4.3 5.2 4.7
##  [379] 2.5 5.3 4.2 4.6 3.3 4.5 5.7 6.0 6.1 3.9 4.8 2.3 4.3 4.2 2.5 3.4 4.3 4.7
##  [397] 5.0 2.7 5.0 5.9 4.1 5.8 4.9 4.5 4.3 4.8 5.0 5.0 1.9 6.5 5.3 4.3 3.6 5.2
##  [415] 4.0 5.1 4.9 4.8 4.1 5.2 5.3 5.1 4.7 2.9 3.6 5.0 4.1 5.0 5.5 3.2 5.0 5.8
##  [433] 3.8 4.9 3.6 3.7 4.5 4.8 3.3 3.5 3.7 4.1 5.1 4.8 4.0 2.5 4.6 5.2 5.4 5.7
##  [451] 5.2 4.3 4.3 4.6 3.5 3.8 4.4 3.9 5.6 5.1 3.0 3.6 5.8 5.0 3.5 3.6 4.6 3.0
##  [469] 3.7 5.8 3.7 3.9 5.7 3.4 5.2 4.6 4.1 4.3 5.9 3.6 4.3 4.0 5.4 4.0 3.2 5.9
##  [487] 3.0 4.3 4.9 5.6 4.7 5.8 5.2 5.2 5.6 5.5 6.9 5.7 4.3 3.0 3.1 5.6 3.5 2.9
##  [505] 5.0 4.2 3.2 4.8 3.0 5.1 4.5 5.3 4.5 6.4 3.4 4.5 5.2 4.9 5.8 4.1 4.9 5.4
##  [523] 5.2 4.5 4.4 3.6 3.5 5.4 3.6 4.6 3.4 4.2 5.5 4.4 2.5 4.2 6.1 5.3 3.6 4.6
##  [541] 3.9 3.8 3.9 4.6 5.1 4.0 4.2 4.3 5.3 4.6 3.4 5.7 5.7 4.2 3.3 2.9 4.0 3.8
##  [559] 5.1 5.6 5.5 4.6 3.5 4.5 3.2 4.6 5.7 4.9 3.9 5.9 4.5 4.0 3.7 5.0 4.2 4.6
##  [577] 5.0 3.7 5.9 6.9 3.2 2.0 6.1 3.9 5.5 4.9 5.2 3.5 4.8 4.5 6.2 4.2 5.9 4.3
##  [595] 3.8 3.9 5.1 5.1 5.2 4.3 2.4 3.6 4.5 3.4 4.8 5.5 4.7 4.1 4.9 3.8 4.7 4.6
##  [613] 5.0 5.5 4.6 4.5 2.7 5.7 5.3 3.0 4.4 3.5 3.8 5.4 4.5 4.1 5.5 5.1 4.6 2.8
##  [631] 4.0 7.6 5.3 4.3 4.6 5.4 4.5 3.7 4.4 4.1 3.6 5.3 4.8 5.1 4.0 4.4 4.8 4.3
##  [649] 5.3 5.8 3.2 6.2 4.5 3.8 4.9 4.6 3.5 4.9 3.7 4.7 4.4 4.2 4.7 3.1 4.7 3.4
##  [667] 4.9 3.2 4.5 4.8 3.4 5.5 4.5 5.1 5.1 5.8 4.0 4.3 4.9 4.5 2.6 4.1 4.1 5.8
##  [685] 4.4 4.1 6.1 3.8 4.1 3.3 5.6 5.7 3.1 4.2 5.3 4.6 4.5 4.3 3.6 3.1 4.8 5.6
##  [703] 5.9 5.5 4.9 3.5 4.7 3.6 5.4 4.6 5.7 4.5 3.6 4.5 3.9 5.4 3.9 3.7 4.6 5.8
##  [721] 3.3 4.6 4.4 4.1 5.0 6.6 4.9 4.8 5.4 5.8 3.9 4.6 5.2 5.2 4.7 5.4 3.8 4.0
##  [739] 2.9 3.3 5.1 5.1 4.5 3.7 5.2 4.3 4.1 4.2 4.2 6.2 3.5 4.3 5.4 5.5 5.0 5.4
##  [757] 3.7 4.2 2.8 4.6 4.9 4.5 4.6 4.4 5.0 4.5 4.8 5.4 5.2 5.2 4.0 4.1 4.9 4.8
##  [775] 5.6 5.9 5.2 4.6 3.7 5.6 2.6 4.6 6.1 5.7 4.3 6.2 4.6 4.3 4.5 4.3 3.9 4.7
##  [793] 3.8 3.9 5.4 3.1 4.7 3.0 5.1 5.2 4.8 3.0 4.6 4.3 4.2 3.8 1.9 4.7 3.4 5.8
##  [811] 4.7 5.2 4.9 4.7 6.5 4.8 4.8 6.1 4.4 4.7 5.2 3.6 3.2 3.2 6.1 3.8 5.2 1.9
##  [829] 4.6 5.1 4.0 4.6 4.2 5.1 3.1 7.0 6.0 4.3 3.6 4.8 4.7 4.1 6.1 4.7 5.5 4.1
##  [847] 5.2 5.6 3.9 3.2 4.6 3.0 4.6 4.5 3.1 5.0 3.6 4.6 5.0 3.9 5.3 6.0 6.0 2.4
##  [865] 3.8 5.1 5.8 4.4 2.7 6.7 3.7 5.2 4.0 4.0 5.9 5.8 3.0 4.6 3.4 5.8 4.8 3.1
##  [883] 4.5 3.4 2.8 4.4 6.3 5.8 4.9 4.5 4.5 4.8 4.9 3.6 4.6 4.7 4.9 5.5 4.1 4.1
##  [901] 4.6 4.5 4.3 5.1 5.1 4.7 4.3 4.3 6.6 2.4 3.4 4.5 5.8 4.7 4.3 6.1 3.9 5.8
##  [919] 5.8 4.6 6.2 5.3 5.0 7.4 2.9 4.5 3.6 4.1 5.0 4.0 5.3 3.3 4.1 3.8 4.6 5.2
##  [937] 5.2 5.0 4.5 3.6 4.4 4.4 2.9 5.7 4.1 5.7 3.7 3.0 4.9 5.2 5.0 5.1 4.2 4.2
##  [955] 4.4 4.6 5.9 4.6 4.9 5.2 3.3 4.4 4.6 5.1 4.5 5.0 3.0 3.8 3.7 4.7 5.5 4.4
##  [973] 4.4 4.9 5.1 5.1 5.3 4.4 4.2 5.5 3.5 3.1 5.2 2.6 3.3 3.0 4.3 3.4 4.2 3.5
##  [991] 3.2 4.9 2.8 4.7 3.6 3.9 3.6 4.6 3.7 5.7
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
##    [1] 5.1 4.7 4.1 5.0 3.9 2.2 3.4 3.9 5.2 4.9 4.1 5.0 3.9 5.3 5.7 5.0 4.1 4.7
##   [19] 4.0 4.1 4.9 2.4 4.9 5.3 5.1 5.6 4.1 5.0 5.6 4.1 3.9 3.8 5.3 4.0 5.7 5.2
##   [37] 4.1 4.3 5.1 5.4 5.0 5.6 3.2 4.1 4.1 4.2 4.0 5.3 3.5 4.7 4.4 3.8 3.9 3.9
##   [55] 5.1 5.3 2.2 4.8 5.0 4.7 4.4 5.3 2.9 4.4 4.7 5.4 2.8 4.8 5.1 4.1 5.8 4.9
##   [73] 6.1 6.6 4.2 3.5 4.1 5.5 5.5 5.2 5.0 3.5 5.0 6.0 4.0 2.9 5.8 4.6 3.9 4.8
##   [91] 5.3 5.7 3.6 4.4 2.8 4.2 6.6 3.6 6.5 4.7 4.1 4.6 3.6 5.2 3.0 4.0 4.0 3.9
##  [109] 4.7 4.6 3.9 4.1 4.1 4.0 3.4 4.1 2.7 4.9 5.8 4.7 4.5 5.0 2.6 5.5 4.4 5.5
##  [127] 4.0 5.2 2.9 4.0 3.8 3.7 3.5 4.1 4.1 5.1 4.1 4.0 3.9 4.1 4.2 3.7 5.4 5.5
##  [145] 4.7 5.3 5.1 4.7 5.1 3.1 4.8 5.9 4.3 4.8 6.4 4.6 4.6 5.9 5.4 4.4 4.9 3.9
##  [163] 4.9 4.5 6.3 5.4 3.8 4.8 3.6 5.1 4.8 3.7 5.0 3.1 5.2 3.3 5.0 5.5 2.2 3.7
##  [181] 5.2 3.9 3.3 4.9 4.0 5.0 3.6 4.5 5.5 4.6 3.6 5.5 4.4 5.4 4.5 3.8 4.4 4.2
##  [199] 4.1 6.1 4.1 5.1 3.3 4.2 6.4 4.3 4.2 2.9 5.4 5.3 4.6 3.5 5.6 3.4 6.7 2.4
##  [217] 5.0 5.8 3.1 3.9 3.9 3.7 3.1 3.0 4.3 2.2 5.6 4.1 3.4 5.1 4.1 3.5 2.8 3.6
##  [235] 2.9 3.6 3.4 5.2 2.7 4.3 6.0 5.5 5.1 4.3 2.9 1.4 4.1 5.2 5.3 3.2 3.5 3.4
##  [253] 6.8 4.6 4.8 4.2 5.0 4.6 3.7 3.7 5.7 4.4 4.3 4.8 6.7 3.9 5.2 3.5 3.9 4.8
##  [271] 5.3 3.9 4.7 5.3 5.3 4.7 5.5 4.8 4.8 4.8 4.4 4.5 5.4 3.9 3.5 4.2 4.7 5.8
##  [289] 4.3 4.1 4.9 5.4 4.9 4.8 4.9 4.4 3.6 4.2 3.7 5.7 4.9 6.5 4.0 4.5 6.2 4.1
##  [307] 3.7 5.6 4.9 5.7 3.0 6.4 3.1 4.9 4.6 5.7 4.7 4.0 4.4 2.6 4.6 3.9 4.3 3.5
##  [325] 3.7 4.4 4.8 4.0 5.2 4.3 4.3 6.0 2.7 5.0 5.2 4.2 3.2 3.5 5.2 3.3 4.3 4.6
##  [343] 4.2 3.9 3.5 5.2 5.8 6.3 3.5 3.8 3.2 5.0 4.5 5.4 4.9 5.4 4.7 3.9 3.6 3.7
##  [361] 4.4 3.9 4.1 5.8 5.5 4.8 4.5 4.7 4.6 4.6 6.0 5.5 5.6 5.5 3.0 5.1 4.1 5.8
##  [379] 5.2 3.5 3.5 5.2 4.3 4.8 4.5 5.1 5.9 4.5 3.9 6.4 4.9 4.8 4.4 3.9 5.2 4.4
##  [397] 4.6 2.8 4.8 3.0 7.0 4.0 2.5 5.9 5.0 4.4 3.1 6.4 3.6 5.2 4.8 5.7 4.5 3.5
##  [415] 3.2 5.6 4.6 4.9 4.7 4.4 4.4 4.2 5.6 4.4 4.8 5.5 4.2 5.4 2.7 5.4 4.7 4.8
##  [433] 3.9 3.5 2.6 4.0 3.4 6.7 4.7 4.3 5.8 4.2 4.4 3.4 3.7 2.4 3.6 4.1 5.2 4.1
##  [451] 4.1 3.9 3.4 6.8 5.9 3.8 3.0 4.5 4.1 4.9 5.1 4.1 3.6 4.7 3.7 5.5 4.6 4.4
##  [469] 2.6 5.7 5.0 5.5 3.5 4.4 4.4 4.5 3.7 4.6 4.6 4.5 3.3 4.2 4.9 5.6 5.2 4.8
##  [487] 2.6 5.0 4.3 4.9 5.0 3.9 5.1 3.8 5.8 5.4 3.4 3.5 5.5 5.1 2.2 4.5 5.0 5.3
##  [505] 5.9 5.3 4.5 3.3 3.8 5.3 3.4 4.2 3.2 3.0 3.6 4.4 4.1 4.4 5.4 5.8 2.6 4.5
##  [523] 5.8 5.5 5.2 2.9 4.1 5.1 3.8 3.5 4.4 4.3 4.3 5.0 3.3 4.0 5.0 4.1 3.8 4.9
##  [541] 4.4 4.5 3.2 2.6 4.4 4.2 5.2 6.4 4.6 4.3 5.2 4.9 3.2 4.3 2.3 3.9 3.7 4.1
##  [559] 5.0 6.5 3.1 4.0 5.6 4.2 4.5 3.2 6.0 6.7 5.0 4.8 4.5 3.9 5.1 4.5 5.2 5.2
##  [577] 5.1 5.1 5.1 2.8 5.2 3.8 2.4 5.6 5.4 5.2 5.0 3.9 6.0 4.3 4.1 3.8 4.7 5.1
##  [595] 4.6 4.9 4.7 4.5 4.6 5.3 6.0 5.1 5.3 4.1 6.5 4.7 3.6 4.3 4.4 4.4 4.7 4.4
##  [613] 3.0 5.6 3.7 6.1 3.7 5.1 3.0 3.5 4.0 4.1 5.2 3.9 4.7 4.5 4.6 5.1 4.6 5.1
##  [631] 4.4 3.6 5.9 3.6 4.8 3.0 4.9 5.9 6.1 4.6 5.8 5.0 5.4 4.5 4.9 4.1 6.6 5.5
##  [649] 4.9 4.1 3.5 3.6 2.8 3.1 4.5 5.7 2.8 4.5 5.0 5.4 5.0 5.3 3.2 3.4 4.8 5.1
##  [667] 3.3 4.2 4.9 4.0 3.7 5.6 4.1 4.1 5.9 3.7 5.8 3.6 4.3 5.2 3.0 3.2 5.3 4.2
##  [685] 3.5 4.3 4.6 2.2 4.2 3.2 4.1 4.1 5.1 5.6 4.1 4.5 4.9 3.9 4.6 5.8 6.2 3.7
##  [703] 4.2 4.8 4.9 5.3 4.4 5.0 6.0 5.0 5.6 5.4 4.8 4.0 4.3 5.4 4.6 5.1 5.8 5.2
##  [721] 6.1 3.8 3.2 6.0 4.6 2.9 3.1 5.0 3.5 5.5 4.5 5.4 5.1 4.3 5.0 6.1 5.1 4.5
##  [739] 4.0 5.5 4.7 5.9 4.5 4.4 4.9 3.1 4.4 4.5 4.4 5.3 4.0 3.1 3.2 4.3 4.4 4.2
##  [757] 4.6 4.4 3.7 4.3 4.5 3.4 4.1 3.5 2.9 5.3 5.7 5.2 4.5 4.1 5.4 3.9 4.6 3.2
##  [775] 4.8 5.7 4.1 6.1 4.9 6.0 4.4 3.4 4.0 5.0 3.5 5.0 4.9 3.3 4.7 5.2 5.2 4.6
##  [793] 5.5 3.7 4.3 4.4 5.2 4.7 3.9 5.8 4.4 3.0 3.0 5.2 2.3 3.9 5.6 5.6 5.7 4.1
##  [811] 3.5 4.5 3.5 5.5 4.9 5.1 5.2 4.3 4.0 5.1 5.4 4.3 4.4 5.1 5.5 4.1 3.7 5.2
##  [829] 4.3 2.6 5.9 3.6 4.0 5.0 5.2 4.8 4.6 4.2 5.9 4.9 5.1 5.8 5.3 3.9 3.5 3.2
##  [847] 4.7 4.2 3.4 5.2 4.4 5.0 5.3 4.2 3.5 4.4 3.9 5.2 6.2 5.2 5.1 4.8 4.3 4.4
##  [865] 3.2 4.9 4.2 4.3 5.1 4.0 5.6 5.1 5.2 5.8 3.8 4.8 4.9 2.8 4.2 4.5 4.2 3.0
##  [883] 4.1 4.6 4.2 3.7 2.7 4.6 5.0 5.3 3.5 4.5 5.1 6.6 4.6 3.4 3.9 3.8 5.2 4.8
##  [901] 2.8 4.0 4.3 5.4 4.3 3.4 3.9 4.0 6.8 3.6 3.7 2.8 5.0 3.4 4.7 5.8 4.7 4.5
##  [919] 4.3 5.8 4.6 6.2 4.8 4.8 3.1 3.5 3.1 4.3 5.9 4.9 4.6 4.7 3.8 4.9 4.1 4.2
##  [937] 5.4 4.2 5.1 5.3 5.3 5.5 4.0 4.8 4.8 4.6 4.7 4.7 3.7 5.0 5.3 5.9 4.0 5.5
##  [955] 2.6 4.7 3.3 6.8 3.9 4.7 4.7 4.1 4.2 3.3 3.6 4.6 2.2 3.0 4.3 4.5 6.2 4.7
##  [973] 4.4 5.0 3.7 5.1 4.5 4.9 5.4 4.3 4.1 2.3 5.3 3.1 5.2 4.5 4.1 2.8 4.3 3.1
##  [991] 4.6 4.2 4.9 3.9 4.4 6.2 3.3 4.5 4.6 2.9
## 
## $func.thetastar
## [1] -0.0078
## 
## $jack.boot.val
##  [1]  0.50144928  0.39880597  0.27352113  0.13666667  0.03786667 -0.04913295
##  [7] -0.14230769 -0.33525836 -0.39563953 -0.50600000
## 
## $jack.boot.se
## [1] 0.9732274
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
##    [1] 5.4 4.4 5.2 4.0 4.0 4.4 3.6 4.3 5.0 5.3 5.5 3.4 4.7 4.6 5.0 3.9 5.5 4.4
##   [19] 5.0 6.7 5.1 5.3 4.2 4.6 5.5 4.4 3.7 3.3 4.8 6.2 2.4 4.4 4.0 2.5 4.7 4.2
##   [37] 3.3 4.0 4.7 6.1 4.8 4.7 4.9 4.8 3.7 6.5 3.6 5.6 5.0 4.5 4.6 6.2 5.5 3.5
##   [55] 5.8 3.3 4.0 5.2 5.1 4.7 5.4 5.3 4.0 3.8 4.0 4.5 4.4 5.5 6.4 3.3 4.5 3.5
##   [73] 3.9 5.0 6.0 3.9 6.1 4.8 3.5 3.5 3.9 5.6 1.9 4.3 3.8 4.6 5.2 6.0 5.8 5.2
##   [91] 3.8 4.5 2.7 3.6 5.9 5.8 5.9 5.1 3.3 4.2 5.6 4.0 3.8 4.4 5.3 4.3 3.6 5.6
##  [109] 5.3 4.2 4.0 4.6 5.7 5.0 3.6 6.0 4.8 4.8 3.6 4.8 4.2 5.9 3.6 4.5 3.7 4.5
##  [127] 5.5 3.8 5.1 3.3 4.1 5.5 5.4 4.8 6.6 5.6 4.6 5.4 4.4 4.6 5.9 4.1 5.2 4.4
##  [145] 4.7 4.8 4.3 6.2 4.0 4.7 3.2 4.6 4.3 3.0 2.7 3.9 3.7 5.2 4.5 3.9 3.2 4.3
##  [163] 3.8 3.9 3.4 4.1 4.7 3.8 4.6 3.5 4.3 5.9 2.4 4.3 5.9 4.4 3.0 2.9 3.7 3.9
##  [181] 2.7 4.0 4.7 4.1 4.7 6.6 4.7 5.2 4.5 4.5 5.0 5.4 4.7 4.2 5.6 2.5 4.6 3.9
##  [199] 5.6 4.1 4.4 6.2 5.9 5.4 4.9 5.5 4.3 5.3 4.0 4.3 5.4 5.1 5.0 3.8 4.0 4.9
##  [217] 3.8 6.1 5.6 5.2 4.7 5.9 6.3 5.7 3.7 6.1 4.2 5.0 2.8 6.9 3.8 2.3 5.1 5.6
##  [235] 3.6 4.8 4.5 5.6 6.2 3.6 4.2 3.6 3.7 4.2 4.6 3.1 5.0 5.5 5.3 3.5 3.5 5.2
##  [253] 5.5 3.9 4.7 6.0 3.2 4.1 5.1 4.1 3.5 3.7 3.8 3.6 3.7 2.7 4.0 4.1 4.2 4.4
##  [271] 3.5 5.0 7.0 4.2 4.6 3.5 3.8 4.1 5.3 3.3 3.6 4.5 4.8 4.4 4.8 4.3 3.5 5.1
##  [289] 3.2 4.2 3.9 3.5 3.8 5.6 4.7 3.5 3.9 4.8 5.5 3.4 3.9 6.0 3.4 5.2 4.6 5.7
##  [307] 6.3 4.0 3.8 3.8 4.5 4.8 3.5 3.2 3.3 3.3 4.2 3.4 5.1 4.9 3.6 4.5 4.3 6.4
##  [325] 4.9 2.0 4.4 3.7 3.7 3.1 5.7 4.1 4.8 5.0 3.3 4.2 3.4 4.7 4.3 3.9 4.1 5.2
##  [343] 4.2 4.2 4.0 5.7 4.8 5.9 4.7 3.6 3.6 4.7 6.1 5.0 4.9 5.1 5.5 4.3 4.9 5.1
##  [361] 3.9 4.8 5.2 4.5 4.5 3.7 4.0 5.4 3.0 3.0 5.6 6.5 5.4 5.1 6.0 5.1 4.6 4.3
##  [379] 4.0 4.5 5.2 5.2 5.8 5.5 2.5 5.4 4.9 4.7 2.6 4.0 3.5 4.6 4.7 5.0 5.8 3.6
##  [397] 4.8 5.3 3.3 5.0 3.7 5.4 4.9 5.0 6.3 5.2 4.8 4.6 3.0 4.0 5.5 4.5 6.0 3.4
##  [415] 5.6 3.0 5.6 4.9 5.2 5.7 5.0 3.8 3.5 4.4 2.8 4.4 5.0 4.9 3.6 4.9 5.6 4.4
##  [433] 6.5 3.8 4.6 6.2 3.9 3.6 4.7 6.9 3.6 4.3 3.6 5.9 5.3 4.0 4.4 3.6 4.9 4.0
##  [451] 5.3 4.5 3.1 5.0 3.3 5.3 3.6 4.1 5.8 3.7 5.2 5.4 4.1 5.1 4.4 5.5 6.6 4.2
##  [469] 5.0 4.6 3.9 4.5 5.2 3.4 4.9 3.9 4.0 5.2 4.4 5.6 5.7 5.1 3.8 3.8 4.1 4.0
##  [487] 5.4 4.2 4.9 5.4 3.7 4.6 4.4 3.5 4.4 4.2 4.6 4.5 3.1 3.5 3.2 5.0 5.8 5.4
##  [505] 5.8 2.8 4.6 5.0 4.4 3.8 5.0 4.2 4.4 4.6 4.0 4.6 4.0 4.3 5.7 4.6 5.8 2.0
##  [523] 3.8 3.6 4.6 3.2 4.6 5.1 4.0 3.7 4.0 4.0 3.7 4.2 2.5 5.1 3.8 4.5 4.9 4.9
##  [541] 4.3 4.8 5.1 5.0 3.6 5.2 4.4 2.7 4.5 5.3 3.6 4.3 5.0 3.9 4.0 5.6 4.2 5.0
##  [559] 4.9 3.7 3.2 4.4 5.4 4.6 3.6 3.7 4.7 4.2 4.7 5.1 4.6 2.9 3.9 5.9 4.7 3.8
##  [577] 2.6 4.6 4.2 2.5 3.4 3.5 3.9 4.5 5.1 5.0 4.4 5.2 4.4 5.1 5.2 4.4 4.7 4.8
##  [595] 5.2 3.9 5.1 5.7 6.6 5.8 5.3 5.6 4.3 4.8 4.5 4.3 4.9 4.1 4.1 5.2 5.6 5.2
##  [613] 4.5 2.4 4.1 4.6 4.0 4.9 5.9 4.8 4.6 5.2 4.3 5.8 3.9 4.4 3.5 3.4 4.8 5.2
##  [631] 3.6 4.7 3.6 2.6 4.2 4.6 3.9 6.4 3.6 3.6 3.6 4.6 5.8 3.9 5.1 5.3 3.5 2.4
##  [649] 3.8 6.8 2.5 4.3 3.6 3.7 3.3 4.9 4.7 4.4 4.9 4.3 5.2 5.0 3.5 6.3 3.3 3.9
##  [667] 2.9 5.9 4.7 5.0 4.7 6.0 4.3 5.3 4.7 3.9 3.6 5.9 4.9 5.1 4.0 2.3 5.8 3.7
##  [685] 4.9 4.5 5.0 4.1 4.6 5.5 3.6 4.6 5.0 4.3 5.2 3.8 3.9 4.6 2.8 3.5 3.7 4.7
##  [703] 4.9 4.6 3.7 4.0 5.4 3.8 4.6 4.4 4.6 4.6 5.8 4.1 6.2 4.5 4.9 3.7 6.1 2.6
##  [721] 4.6 4.9 3.6 3.6 4.6 4.9 5.2 3.0 5.1 5.7 3.6 4.9 4.6 6.2 5.1 5.5 4.2 3.5
##  [739] 5.7 4.1 5.0 4.9 4.9 6.1 4.7 3.8 4.9 4.1 4.4 5.4 3.4 5.6 4.2 5.4 5.3 5.8
##  [757] 3.0 4.8 5.3 4.2 4.4 4.3 2.6 3.5 4.9 3.3 2.7 5.0 5.7 4.3 4.3 3.5 5.5 5.6
##  [775] 4.8 4.6 5.2 4.2 4.7 3.6 5.2 4.3 4.2 3.8 4.5 4.4 5.4 6.3 4.1 4.3 4.1 3.0
##  [793] 3.7 5.5 4.7 4.0 4.1 4.7 4.7 3.8 3.7 4.0 3.3 4.0 5.0 5.7 4.0 5.3 4.4 6.8
##  [811] 3.8 5.3 5.1 4.3 3.7 4.1 4.7 4.4 5.3 4.5 4.4 4.1 4.0 4.0 3.5 3.8 3.7 5.6
##  [829] 3.9 4.2 4.4 3.6 4.9 5.9 4.6 4.9 4.2 4.9 4.0 3.1 5.2 4.9 3.6 4.1 5.8 4.0
##  [847] 3.5 4.1 5.4 4.7 6.3 5.1 4.9 4.7 6.1 4.0 4.3 5.7 4.6 3.7 6.2 4.5 4.2 5.2
##  [865] 4.3 4.1 3.9 5.0 3.9 4.8 4.3 5.7 5.3 4.3 6.4 5.3 3.9 4.2 4.8 4.7 3.2 4.3
##  [883] 5.8 4.6 2.9 5.3 6.1 4.8 4.8 5.9 4.9 4.9 3.5 4.1 3.4 6.1 3.4 6.5 5.1 5.4
##  [901] 4.4 2.0 5.7 4.0 4.5 3.9 5.5 3.3 4.4 4.4 4.6 4.0 3.6 4.3 3.6 5.1 4.7 4.8
##  [919] 6.7 4.7 2.9 5.0 3.7 3.3 5.0 4.8 3.8 3.7 4.8 4.7 4.8 4.9 3.6 5.0 5.8 4.0
##  [937] 5.7 4.8 4.7 5.0 3.8 4.1 3.3 4.0 6.0 5.0 3.8 6.1 4.6 4.5 4.4 2.6 4.0 6.4
##  [955] 5.1 5.3 4.6 4.2 6.5 4.1 5.9 4.1 5.7 3.3 4.0 4.5 5.1 3.6 4.7 5.2 4.5 4.5
##  [973] 3.7 5.1 4.8 5.5 5.3 6.5 4.2 2.5 5.6 3.3 5.1 4.2 4.9 3.1 4.7 6.2 5.3 5.1
##  [991] 4.8 4.6 4.5 5.0 3.8 2.2 4.1 5.7 3.8 4.5
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.6 5.4 5.3 5.2 5.1 5.0 4.9 4.7 4.7 4.5
## 
## $jack.boot.se
## [1] 0.9877247
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
## [1] -0.1821818
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
##   4.289694   6.100425 
##  (1.848652) (2.789110)
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
## [1]  0.52291498  0.09836759  1.41228313  1.22601013 -0.41304896  0.64038276
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
##    [1] -0.2881323441  0.3035708463  0.9498519583 -1.0642760804 -0.3614046543
##    [6]  0.5883096550 -0.0071690835 -0.2972796278 -0.2891750568 -1.0450455877
##   [11] -0.3476012829 -0.5294601218 -1.1426856560  1.4209677692  0.0245814009
##   [16] -0.0005461700 -0.5399449012 -0.9834453173  0.2337176343  0.5321578341
##   [21]  0.1914936575 -0.5653201776 -0.0981692435  1.0021487015 -0.3048069762
##   [26]  0.0540098672 -0.5340842181 -0.2578772941 -0.0000865479 -0.2017850069
##   [31] -0.9846795082 -0.2975928543  0.6918856429 -0.1859590706 -1.8203391297
##   [36]  0.9800722837  1.0833344823  0.6281205324  0.1555913926 -1.0312993010
##   [41] -0.0408550911 -0.0832589965  0.1531869672  0.5730056072  0.9990317829
##   [46]  0.0603875154 -0.5388866538 -0.3882282969 -0.7499059801 -0.4584570651
##   [51] -0.6246951755  0.4466378606  0.7248807188 -0.2456993454  1.1713467519
##   [56] -0.0015525540  0.3052746994  0.4254971252  0.1067075109  0.2318599024
##   [61]  0.3033154429  1.0348812925  0.1148871388 -0.1976148824 -0.4170775036
##   [66] -0.6221468504 -0.5113085906  0.3809637322  0.0789893232 -0.2730829778
##   [71]  0.1759451217 -1.2652600276  0.3494849031  0.2461212034  0.7972782957
##   [76] -0.0712478946 -0.7283752692  0.1689462901 -0.2514765230 -0.2229954827
##   [81] -0.2800159456  0.3541029779 -0.3548002310 -0.4158454728 -0.0008565743
##   [86] -0.4452129124 -0.2959084179 -0.3956105100  0.6562004587 -0.4741129278
##   [91] -0.1296560008 -0.9825239622  0.0174779622 -0.4104310946 -0.1022652455
##   [96] -0.6610267758 -1.0668881593 -0.0926967532 -0.5927832283  1.0776008714
##  [101] -1.1971998102 -0.6234525575 -0.0239614580 -0.4613703254  0.2185606415
##  [106]  0.7935353272  0.4729409093  0.3508864082  0.3408258757  0.6253677877
##  [111] -0.4010009301 -0.5415420627 -0.0059884222  0.7206812861 -0.2849675653
##  [116] -0.9397953562  0.2855386779  0.5714042311 -0.7302002085 -0.5664708057
##  [121] -0.2980705649  0.4221571292 -0.2088869766 -0.4493912751 -0.2174972984
##  [126]  0.1550603512  0.7209687283  0.4396035017  0.3630639613 -0.1244577127
##  [131] -0.5273681895 -0.5539766915  0.0047652230  0.0254468844 -0.3063187299
##  [136] -0.2726867169 -0.0585444445 -2.4451359854  0.6774240962  0.5367369967
##  [141] -0.2364150469  0.7636373517 -0.2530973364  0.4093209687 -0.1810389508
##  [146]  0.4595462608 -0.1819900329 -0.7443506733 -0.5230951533 -0.1661370184
##  [151] -0.4306323165  0.3285000949 -0.4143936094  1.4959399387 -0.6441651094
##  [156] -0.2194538091 -0.2669895448  1.7045514025  0.1448273122  2.1191529598
##  [161]  0.3954014313 -0.3343320146 -0.2864093588 -0.1811824431  0.0603875154
##  [166] -0.0097017877 -0.7373114601 -0.5001486219 -0.0634734079 -0.5841907689
##  [171] -0.5579143600 -0.4134262162  0.5573026969 -0.4174757427 -0.4843709841
##  [176] -0.1611270857 -0.5646899181  0.4464898422 -0.2436469533  0.3713074795
##  [181] -0.1184311074  0.2099407200  1.0190608758 -0.5183492181 -0.2493245061
##  [186]  0.3002760173 -0.2762996983 -0.0355221820 -0.1243603845  0.0497526635
##  [191] -0.5561779415 -0.4094226456  0.0343830573 -0.3412450495  0.9011383047
##  [196] -0.2816135489 -0.9510429160 -0.3702443163  0.7369855520  0.3339721746
##  [201]  0.1784526579 -0.6841251094 -1.3970235335  0.4162077864  0.1416575886
##  [206] -0.3451749537 -0.2197928555 -0.2371910507 -0.2504216369 -0.6518163498
##  [211] -0.4557675808  0.1020038444 -0.4462413621 -0.3558808081 -1.5270337352
##  [216]  0.3124184365 -0.9803287340 -0.1087394261  0.2492991458  0.6857378433
##  [221]  0.9605720578 -0.9429959622  0.6466832324  0.2423709124 -0.3073738336
##  [226] -0.0490597210 -0.0625886989  0.3422205076  0.2652522277 -0.0305813825
##  [231]  0.2980709791 -0.1307769109 -0.5755242632 -0.5535461566 -0.1792201388
##  [236]  0.6516906700  0.3089054112  0.2536401472 -1.3111450099 -0.0643400760
##  [241] -0.8562273320 -0.3428557525 -0.5329253059  0.5102225538  0.1271317599
##  [246] -0.1639889836 -0.2761204249  0.0916090182 -0.0643400760 -0.4923205559
##  [251]  0.0724459715  1.0064385356 -0.0154471803 -0.0782390862 -0.0749559059
##  [256] -0.0589714515 -0.5530561628 -0.5793965234  0.6435151031 -0.1027339831
##  [261]  0.1111190215  0.8389016373  0.0166364642 -0.2531658436  0.0223821376
##  [266] -0.4478439482 -0.8589320234  0.2769037620 -0.9371130924 -0.7273161004
##  [271]  0.0351327927 -0.6212313236 -0.1802372589 -0.7493323927 -0.7649094191
##  [276]  1.1985514502  0.5727397059 -0.0753704961 -0.6421475866 -0.4172809794
##  [281]  0.1682907632  0.0305255762  0.8798465413  1.0191986014 -0.0265931574
##  [286] -0.3299191048  0.5101566778  0.1767359237  0.0098398139  1.1160251857
##  [291]  0.3490378156 -0.7773666963 -1.4664188362 -0.4582186824 -0.4600879288
##  [296] -1.2202914695  0.5056329294  0.0890491593  0.7212223891  0.3124069497
##  [301]  0.6341243503 -0.2683683718 -0.6758149504  0.6041429763 -0.2546320617
##  [306] -0.4942772911 -0.4239794120 -0.1025186951 -0.2432253022 -1.7155782149
##  [311]  0.6239927908  0.8585253769 -0.5802559956 -0.1611270857  0.7118743187
##  [316]  0.3810615663 -0.3068316736  0.2758643957 -1.2064097246 -0.0114622107
##  [321]  0.8749440328 -0.5825278080  0.0486041831  0.3443994229  0.0146603346
##  [326] -0.7159802934 -0.4990740170  0.2574878821 -0.4990740170  1.0334732641
##  [331]  1.2602960556  0.8015047122  0.0435014358  0.7729756882 -0.3623004191
##  [336]  0.5688975763 -0.4902206842 -0.5944952197  0.0994540510  0.1632045329
##  [341] -0.5264111105 -1.4555440931 -0.3639019210 -0.3396118819 -0.4580586689
##  [346]  0.4373116296  0.8326376031 -0.2316092008  0.4880242657 -0.1682364740
##  [351]  0.0948745026 -0.7969485753 -0.6676594482 -1.1179203585  0.1051447019
##  [356]  0.6868362132 -0.5326969864 -0.2976262842  0.0161575020  0.7079694475
##  [361]  0.9522083791 -0.1455276316  0.6558319608 -0.9805919127 -0.5738568075
##  [366]  0.8113439089 -0.8491914973 -0.3094076242  0.2015233419  0.5820814429
##  [371]  0.4056933684 -0.4907973156  0.5094296442  1.6970698532 -0.8004216783
##  [376] -0.2129637876  0.3266758373  0.2111202552  0.4198969345 -1.6029227869
##  [381] -0.6457676548 -0.7147624069  0.4770200422 -0.1789166676  0.4004726353
##  [386] -0.3396118819 -0.7256747471 -1.7949299341  1.1098610181  0.3456104624
##  [391] -0.2406937475 -0.8603527379 -0.0531987672 -0.5447121568  1.0190969464
##  [396]  0.6355835253 -0.1938079435 -0.1470594219  0.7665680470 -0.6180537743
##  [401] -0.1794685303  0.5722793912 -0.5428726393 -0.6056307827  0.1877565434
##  [406] -0.4152981449  0.4959287023 -1.2459190649 -0.2121238945 -0.4005194583
##  [411] -0.2876531337  0.3091850213 -0.4127598394  0.0103498270  0.4704857662
##  [416]  0.7895160189  0.5960924952 -0.6717729742  0.4068481554  0.8340453993
##  [421]  0.5560392973  0.4227495182 -0.4464186025 -0.2472510963 -0.1275420016
##  [426]  0.2092773693  0.7139357115  0.7237516796 -1.4679637768 -0.4902338867
##  [431] -0.4995883234  0.1720160025 -0.1906939703  0.6069175174 -0.6300981763
##  [436] -0.3112585266 -0.2482911941 -0.5056056600  0.6480999544  0.7139357115
##  [441] -0.0954583500 -0.6795395946 -0.2602340607 -0.0137134301 -0.1200738896
##  [446] -0.6084701978  0.1563827493 -0.4750501176  0.6031263866  0.6155922360
##  [451]  0.2746729568 -0.4834157539  0.0616430154  0.5552659377 -0.6199459034
##  [456]  0.0370586200 -0.8162920609 -0.3931649267  0.1049703583 -0.4499275404
##  [461]  0.8974990727 -0.0181913461  1.0294944729  0.7245224424 -0.1724813674
##  [466]  0.5190651817 -0.4367470013 -0.6961040910 -0.3482856143  0.8839756963
##  [471] -0.7959130818  0.2198013754 -0.4873283199 -0.0923512860  0.5730946564
##  [476] -0.4140601682  0.0593733431  0.1882993998  0.6035016355 -0.4845778309
##  [481] -0.1693602648  0.8237229333  0.9274819313  0.4636520626 -1.1271363192
##  [486] -0.6155664059  0.0539440787 -0.1354731890 -1.1510187847 -0.2404320908
##  [491]  0.4242787385 -1.1499757151  0.0042211773 -0.4200388578 -1.5031787916
##  [496]  0.8392425307 -0.8472122660  0.0518550723  0.7549952014 -0.4813571851
##  [501]  0.6481564325 -0.3724768356 -0.1930188128 -0.2876572040 -0.7270217636
##  [506]  0.0401523031  0.1739655267 -0.2423093455 -0.0931762095 -0.6522541262
##  [511] -0.1328557400 -0.7117032368  0.6376503586 -0.2026754804 -0.2812165645
##  [516]  0.5738295049 -0.1155752551 -1.3419308361  0.3341380073  0.2951101624
##  [521] -0.6092740854  0.0224350037  0.3746773278  0.9426244164  0.3751703798
##  [526]  0.8656134502 -0.5055607468 -0.3117842509  0.2791239440  0.2473657934
##  [531]  0.0827763752 -0.1889753348 -0.5955775194  0.6807478654 -0.1517036339
##  [536]  0.6956384642  0.3714463981  0.4734270071 -0.8821120766  0.4378804848
##  [541] -0.0870823808 -0.6355690862  0.1598530292  0.4544607493  0.4375486330
##  [546]  0.4355959139  0.0915560506  0.6609178856  0.3954568955  0.0326548215
##  [551] -0.1473244623 -0.3942309684  0.5269404999  0.0306826939  0.2575386820
##  [556] -0.2939592426 -0.3453068580 -0.8839371963 -0.2308721930  0.8245055567
##  [561]  0.1082042039 -0.7086535587  0.3976438412 -0.4923843670  0.8563042801
##  [566] -0.8749453553 -0.4924413829 -0.3086722381  0.2094309094 -0.1416195294
##  [571] -0.5093898998  0.5164564933 -1.2798086599  0.0915560506  0.0442617256
##  [576] -0.3329773365 -0.7025532850 -0.2239231177 -0.5551965677 -0.8341528877
##  [581]  0.1423273956 -0.1099583713 -0.5404840092  0.2034735502  0.1423185274
##  [586] -0.0572129422  0.0601629778 -0.3766898798 -0.9281882774 -0.5567341788
##  [591] -0.9293147421  0.3487270674 -0.0492703333 -0.8054503487  0.9381508904
##  [596]  0.6455071433 -0.4117269383  0.6523128482 -1.1044675379  0.0925434357
##  [601]  0.1262170643  0.7865054130  0.7977408973  0.4266530109 -0.7547139863
##  [606]  0.6885841463  2.0872462452 -0.6424147160 -0.0957511045  0.1726478691
##  [611]  0.6737442581  0.5249888912 -0.5862914981 -0.6974425905 -0.1448828360
##  [616] -0.0201543963 -0.1033213180  0.2158613928 -0.3845675319  0.0188909037
##  [621] -0.8989289485  0.0364877617  1.3218492153 -0.1799105446 -1.4082221693
##  [626] -0.0565173932 -0.4210476363 -0.4183684066  0.2430482150  0.4457817689
##  [631] -0.9192676650  0.8573351956 -0.2770155074 -0.8116297393 -0.0136806300
##  [636] -0.5526683196 -0.6199459034 -0.8482365448 -0.2254229866 -0.3957714237
##  [641] -0.6743850687 -0.4229743177 -0.0814423433 -0.7104674625  0.8184859780
##  [646]  0.4411667300 -0.6464385116  0.0574985786 -0.7322787599  0.5573132878
##  [651] -0.8472211245  0.2967631346  0.8762651026 -0.4937744668  0.0374311639
##  [656] -1.5057348386  0.5969207725  0.3552878449 -0.5924373602 -0.4810641874
##  [661]  0.0391829953 -0.7127355449  0.2855357218 -0.4066840438 -0.6102051451
##  [666] -0.4279222112  0.6242362639 -0.1463921894  0.3280578182  0.2025704467
##  [671] -0.0253122717  1.0530460543 -0.4965260496  0.8185326066  1.6062519850
##  [676] -0.2886110268 -0.4555508056  0.5387015667 -0.7418424549  0.3461396086
##  [681] -0.8936774082 -0.1591033340  0.8065754111  0.4517561651  0.6455071433
##  [686]  0.8162979550  0.1710769678  0.7035502641 -0.4215242875  0.5843897438
##  [691]  0.3864805830 -0.3444607433  1.3981817510  0.7539269325  0.2188219141
##  [696] -0.0354513439 -0.1004092629 -0.8372854675  1.6023478146 -0.3668378671
##  [701] -0.3872892383 -0.5884850299  0.1030996321 -0.8855003934 -0.0163745275
##  [706]  0.1140574152  0.1307767556 -0.8722961650  0.0603257131 -0.4578577565
##  [711] -0.3115001011 -1.8032928930  0.4670138634 -0.3788359602 -0.0505835632
##  [716] -0.0036519499 -0.3330495789  0.7324283162 -0.2839715665 -0.0266123672
##  [721] -0.3396241022 -0.2961901084 -1.1386882374 -0.0645541891 -0.5249964911
##  [726]  0.0570507560  0.2659310487  0.1807029364 -1.4335766410  0.7947080510
##  [731] -0.3773757576  0.6554645577 -0.1684402562 -0.4571514300  0.7286304343
##  [736] -1.1391976002 -0.1102443546 -0.7281497405 -0.3943020051 -0.8206534220
##  [741]  0.5206997002 -0.2392024596 -0.5530558795  1.1776908343  0.5735791232
##  [746] -0.0993126637 -0.0397922572 -0.4826509603  0.1021154164 -0.5009680205
##  [751] -0.8127192430  0.1726883057 -0.4590710828  0.1762375137 -0.0746452980
##  [756] -0.0861393771 -0.2907553154  0.5802576827 -0.1284915374  0.7168374315
##  [761] -0.6097433557  0.4566606364 -0.3813197344 -0.0897275778  0.2480126672
##  [766] -0.6180537743  0.3815156138 -0.2631337431  0.4399894790  0.7217804331
##  [771] -0.3090958860  0.7343119252 -0.5526683196  0.1218916163  0.2361129048
##  [776] -0.2459967105 -0.3162176062 -0.5056056600  1.2942174321 -0.6431676451
##  [781] -0.4537312938 -0.4337286589 -0.7421618638 -0.6252545399  1.1766870621
##  [786]  0.3329048172 -0.6199459034 -0.2833713070  0.2876669670 -0.0770957021
##  [791] -0.0983286266  0.7688199381 -0.4045322796  0.0028136942 -0.3709911027
##  [796] -0.6142444528 -0.2404320908  0.2324654072 -0.5334147906  0.1743944429
##  [801] -0.0347673220  0.2155586508 -0.5021906362 -0.3287359370  0.8839702556
##  [806]  0.8054172221 -0.6156006896 -0.4232828168 -0.3958029909  0.5183006257
##  [811]  0.3101182337 -0.6828003911 -0.1255794050  0.0038572469 -0.1602520714
##  [816] -0.5221609964 -0.9549608282 -0.5312351399 -0.3434835373 -0.0428817386
##  [821]  0.9103053034  0.4051671966  0.5014715942 -0.4973403109  0.0016461371
##  [826] -0.2097584008  0.1899345227 -0.4265518485 -0.7246781686 -0.0382579333
##  [831] -0.4694362662 -0.1870453806  0.8924770360 -0.3219779635 -0.2212269788
##  [836] -0.2373765978 -0.0625886989  1.0843889972 -0.5375624718  0.0458753679
##  [841] -0.2519451895 -0.4341850480  0.5799669824 -0.3090958860  0.3706542852
##  [846]  0.1153044275 -0.1190248709  0.4013299342 -0.1194370172  0.4266530109
##  [851] -0.5093318063 -0.7065502524  0.0220750956  0.0081766304  0.4379983090
##  [856] -0.2838032135  0.0689122193 -0.6219708939  0.3420343011 -0.0687985959
##  [861]  0.8664285658  1.1250878689 -0.1936703560 -0.7844244120  0.7177103287
##  [866]  0.6884038631  0.4689037508  0.9138533050  0.6246311801  0.5097163494
##  [871] -0.2617593137  0.2842898011 -0.8149273438  0.1033631515 -0.0445952025
##  [876]  0.1005020919  0.4423280492  0.6133040310  0.5778650624  0.0194945053
##  [881]  0.8140855680  0.0142459667 -0.6298748924  1.1000352560 -0.4925935346
##  [886] -0.3826369613 -0.2624719192  0.1784526579  0.2248586468  1.0885406037
##  [891]  1.1847940363  0.3607546574 -0.7549857824  2.0168293633 -0.8183002002
##  [896] -0.9345766734 -0.4819364516 -0.9486289136 -0.5145325295 -0.7681055984
##  [901] -0.3695101544  0.0261668178 -0.1686038113 -1.2547075464  0.1945613331
##  [906]  0.7282641374 -0.3432728514  1.0641078959  0.2943422158 -0.6048260393
##  [911] -0.5202682335 -0.2874995031  0.3408258757 -0.4067700293  0.2578081648
##  [916] -0.2770155074 -0.3416058422 -0.7656350474 -0.4793357090  0.7486527979
##  [921] -0.1288344911 -0.6626339500 -0.4313532859 -0.2949615476 -0.5597016160
##  [926] -0.5619857928 -0.4222660258 -0.4427744430 -0.1356748527  0.9949148344
##  [931] -0.3242145070 -0.3558981992  1.5229636098 -0.1068824369 -0.1376749949
##  [936]  0.4237435536  0.0181003973  0.3355429739  0.4551727864 -0.4099421965
##  [941]  0.0448379405  0.1926460862  0.0448831483 -0.9386389539  0.6537794462
##  [946] -0.2097204662  0.1465270395  0.2705908833  0.3574634066  0.4342157593
##  [951]  0.5935430450 -0.0917164884  1.4895684215 -1.0702221534 -0.2056616349
##  [956]  0.4242787385 -0.6852511142  0.4669271294  0.3912172044  0.3275060998
##  [961]  0.1570239174 -0.3803170535  0.3493201879  0.0356762049  1.5667326414
##  [966]  0.4686747658 -1.3128108347 -0.5498081042 -0.1899062724  1.3817070007
##  [971]  0.6126439017  0.2211257816 -0.1422457216 -0.2685174619 -0.1234445617
##  [976]  1.0108816611  0.8269359195 -0.4439244550 -0.3298060178 -0.6569542943
##  [981]  0.1784526579 -0.2368256227  0.5850907082 -0.1465203691  0.6089259362
##  [986]  0.5437295817 -0.0766953030 -0.3665193424  0.6895827538 -0.3275805414
##  [991]  0.2256594610 -0.5407623273 -0.7838401951  0.0418597812 -0.1249553756
##  [996]  0.4699193385  0.0329976667  0.3854877475  0.0161958715  0.3844291817
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
##   0.70317520   0.28273460 
##  (0.08940853) (0.06321793)
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
## [1]  0.43288757 -0.03952317 -0.01672033  1.00121301  0.14449044 -0.26453616
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
## [1] -0.0432
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9159545
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
## t1*      4.5 -0.03043043   0.9131827
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 1 2 3 4 5 8 
## 2 1 1 1 2 1 2
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
## [1] 0.0286
```

```r
se.boot
```

```
## [1] 0.912159
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

