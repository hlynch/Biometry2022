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
## 2 3 4 5 6 
## 4 2 1 1 2
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
## [1] 0.0067
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
## [1] 2.697229
```

```r
UL.boot
```

```
## [1] 6.316171
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
##    [1] 4.6 3.9 3.4 5.4 3.1 4.5 4.3 5.4 4.6 5.8 3.0 3.1 4.0 5.3 3.9 4.3 4.0 3.3
##   [19] 4.7 4.5 5.8 4.3 5.2 4.1 4.4 4.4 5.3 3.5 4.6 3.6 4.8 4.2 2.5 4.8 5.1 4.9
##   [37] 4.2 5.1 3.8 4.1 5.7 5.2 3.6 6.1 3.8 5.8 5.5 4.0 4.6 3.5 4.5 4.0 5.8 4.5
##   [55] 5.2 6.8 4.3 2.6 3.3 5.3 5.4 4.0 5.5 5.6 3.2 4.6 4.7 3.7 4.7 1.9 3.5 4.4
##   [73] 4.4 5.9 4.5 5.2 4.7 4.8 3.1 6.1 4.0 4.1 3.9 4.6 5.8 4.7 3.9 6.0 3.2 4.9
##   [91] 5.6 5.8 5.7 4.8 6.0 4.6 5.5 3.8 4.9 2.5 4.0 6.4 5.2 3.8 4.7 6.1 4.3 5.7
##  [109] 3.2 5.7 5.0 5.0 4.6 3.3 5.7 6.1 5.5 5.6 1.8 4.7 4.5 5.9 5.2 4.8 3.1 4.2
##  [127] 4.4 5.0 4.6 3.7 4.2 4.5 2.9 4.5 3.5 4.1 4.4 3.9 4.8 3.3 4.7 5.2 4.5 4.7
##  [145] 4.8 5.3 5.0 5.4 4.8 5.6 4.1 3.7 4.4 4.0 4.7 6.8 5.3 2.8 4.9 3.9 4.1 4.5
##  [163] 5.8 4.0 6.0 5.1 4.3 5.2 5.6 3.8 1.7 4.5 5.0 4.5 5.0 3.2 4.8 5.5 4.7 4.1
##  [181] 5.3 4.7 4.8 4.6 5.4 3.4 5.6 5.3 4.2 4.2 5.1 4.7 4.6 4.5 3.9 4.7 5.0 3.1
##  [199] 5.4 5.5 5.3 5.3 5.3 5.8 4.8 4.2 6.1 5.1 4.6 3.8 4.4 5.6 5.4 4.2 5.5 5.0
##  [217] 3.4 3.4 4.8 3.0 3.6 4.4 6.3 3.5 3.7 4.5 2.2 5.7 3.4 4.2 4.3 5.3 5.0 4.8
##  [235] 3.8 4.6 4.1 5.1 4.0 4.9 4.4 5.2 6.1 4.0 4.0 5.5 5.5 3.6 3.2 4.6 4.5 3.9
##  [253] 4.3 4.2 5.3 3.9 3.8 5.0 4.2 5.0 5.0 4.5 4.5 6.4 4.5 4.9 3.4 6.0 5.7 4.4
##  [271] 4.4 4.2 6.2 4.5 4.5 4.4 5.9 3.2 4.6 6.8 4.3 5.8 5.8 3.9 5.2 4.4 4.4 4.6
##  [289] 5.6 4.1 5.9 4.2 5.7 7.0 5.5 3.9 4.0 3.4 4.0 5.1 4.5 6.0 3.4 4.0 4.6 3.4
##  [307] 4.2 4.0 5.2 4.6 2.7 4.7 5.8 4.6 6.3 4.2 4.2 3.1 5.3 3.7 5.8 4.0 5.8 5.2
##  [325] 3.2 4.5 4.7 5.3 5.3 5.2 4.3 5.4 5.2 4.1 4.9 4.7 5.1 6.8 5.3 6.0 6.2 5.1
##  [343] 3.5 4.3 3.4 4.5 4.5 5.7 3.4 5.8 3.6 3.9 2.9 3.5 3.9 5.0 3.5 5.0 5.8 5.0
##  [361] 5.3 4.2 5.0 4.1 4.3 3.3 4.2 4.6 4.6 4.1 5.1 4.7 4.8 5.0 2.2 1.9 5.4 5.1
##  [379] 3.1 5.8 4.9 4.5 5.7 4.3 5.5 2.6 3.6 4.3 4.1 6.1 6.6 3.9 5.1 3.6 3.9 3.3
##  [397] 4.9 2.7 3.8 3.9 6.4 4.6 4.1 4.2 5.2 6.0 5.0 5.8 4.6 5.4 4.9 5.8 3.4 5.6
##  [415] 4.4 5.9 4.8 4.1 4.2 4.5 6.6 4.5 5.0 2.9 3.1 4.6 4.4 3.9 4.1 5.3 6.1 5.2
##  [433] 3.7 4.3 5.7 6.0 3.5 3.6 4.8 6.1 5.6 3.7 3.0 3.7 5.2 4.9 5.6 3.3 3.1 4.9
##  [451] 4.9 2.3 5.4 5.5 3.8 2.5 4.5 5.2 4.6 5.9 5.3 3.8 4.6 4.3 5.3 4.3 5.2 3.5
##  [469] 4.9 4.9 4.8 4.9 7.2 2.6 4.6 2.8 3.6 5.6 4.9 4.2 4.0 4.4 4.0 4.7 3.4 5.8
##  [487] 3.7 3.4 5.3 4.9 4.1 4.5 4.7 3.2 6.7 5.6 3.9 3.6 4.9 3.1 3.4 3.8 5.2 5.2
##  [505] 3.6 3.8 3.9 5.5 5.6 4.7 4.7 4.5 3.6 6.9 5.3 3.4 4.7 5.4 3.8 5.8 5.7 3.4
##  [523] 3.9 2.8 4.6 3.2 4.4 3.4 4.0 4.1 6.1 5.1 2.9 5.3 4.6 4.2 4.1 5.4 4.0 5.5
##  [541] 3.6 5.0 3.4 4.8 6.3 5.3 4.5 3.3 5.7 2.2 5.8 5.4 4.6 3.9 3.8 4.1 3.4 2.9
##  [559] 4.9 6.5 5.5 3.8 5.8 5.6 2.3 4.2 5.1 4.7 4.4 3.4 3.9 3.7 2.6 4.3 2.6 4.7
##  [577] 4.5 5.4 3.9 5.1 4.5 4.5 5.3 3.8 4.9 5.0 3.3 5.7 4.1 4.0 3.8 5.1 4.0 3.1
##  [595] 4.3 4.4 5.5 5.2 5.9 4.3 4.8 5.0 4.2 6.1 4.0 4.9 3.6 3.7 5.2 5.3 4.2 4.4
##  [613] 3.4 5.5 3.5 4.5 3.7 3.8 4.8 4.0 4.7 3.1 4.5 5.2 5.0 5.7 4.8 2.4 6.1 4.4
##  [631] 5.1 2.8 4.0 5.4 4.9 3.7 4.2 4.3 5.0 4.7 4.7 4.3 5.0 5.2 4.3 2.7 4.8 4.8
##  [649] 3.1 6.4 4.3 3.3 5.2 4.7 6.1 3.9 5.2 4.3 5.1 5.2 5.2 5.0 3.5 4.0 4.2 4.5
##  [667] 4.5 3.6 4.8 4.5 4.5 4.9 4.6 3.2 5.1 4.2 2.9 6.1 5.9 6.3 5.7 2.1 4.2 5.5
##  [685] 3.6 4.6 4.1 5.8 6.1 3.6 5.4 5.4 4.3 4.7 4.4 4.9 4.2 4.5 3.9 3.5 3.5 3.6
##  [703] 3.2 5.5 4.7 4.1 4.8 5.8 5.2 3.5 3.8 6.7 5.2 4.4 4.6 4.8 5.3 3.2 5.7 3.5
##  [721] 6.0 3.2 4.0 4.4 4.4 5.5 5.1 3.1 4.8 5.1 4.1 4.4 5.0 6.5 4.3 6.2 3.6 3.6
##  [739] 5.5 4.6 2.5 5.4 5.0 5.4 3.4 4.6 4.0 5.8 2.7 5.3 5.3 5.4 5.0 4.2 5.5 5.7
##  [757] 3.2 3.2 4.1 2.9 3.7 4.8 5.1 4.7 5.2 2.8 4.7 2.9 6.2 4.2 6.0 4.2 4.9 5.3
##  [775] 3.8 4.5 3.3 4.1 3.5 3.8 4.6 3.7 6.8 4.2 3.9 4.3 3.7 6.0 4.0 4.4 4.0 4.6
##  [793] 5.7 4.4 3.0 3.9 4.3 4.8 5.3 4.5 2.7 4.3 4.0 4.6 4.2 2.7 5.1 6.6 4.7 4.6
##  [811] 3.1 4.8 5.0 5.0 4.2 4.1 4.7 5.7 5.8 4.4 3.7 4.4 4.4 5.3 3.5 3.9 4.7 3.4
##  [829] 5.3 4.2 4.0 4.6 4.5 5.5 2.1 6.2 5.0 2.8 3.9 4.0 5.4 3.9 3.9 6.6 4.3 3.5
##  [847] 4.2 4.2 4.2 4.5 2.6 5.1 4.2 4.9 5.1 4.6 5.6 4.3 4.5 2.8 4.9 2.6 3.3 4.5
##  [865] 5.4 4.7 3.6 5.5 4.4 5.0 5.0 4.8 3.9 4.3 3.0 4.4 4.4 2.5 4.2 4.6 4.1 5.3
##  [883] 4.4 4.5 3.8 2.7 5.1 3.2 6.0 1.0 4.9 5.3 4.1 4.3 3.0 5.7 3.8 4.5 2.9 3.9
##  [901] 4.1 4.5 4.9 4.4 4.6 3.1 4.4 6.2 3.5 4.5 3.8 3.9 5.1 4.5 4.0 2.9 5.0 4.7
##  [919] 4.5 7.0 2.8 3.9 3.9 4.1 3.4 3.8 4.4 2.9 4.4 4.0 5.2 4.2 4.0 3.2 4.0 4.4
##  [937] 3.2 5.1 4.2 5.4 3.9 3.8 4.6 5.9 4.5 4.6 4.6 3.7 4.0 4.8 5.3 5.8 5.4 4.2
##  [955] 4.5 4.3 3.6 5.5 3.8 5.2 4.9 3.2 4.8 5.1 4.4 4.8 5.9 3.9 4.5 3.2 5.2 2.5
##  [973] 4.8 5.0 4.4 5.4 5.0 4.6 5.2 2.2 4.5 2.4 3.8 2.6 4.8 4.7 3.2 4.5 5.4 5.2
##  [991] 5.1 4.0 3.2 3.3 4.1 3.3 3.0 4.4 3.6 4.9
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
##   2.5%  97.5% 
## 2.6000 6.2025
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
##    [1] 4.9 5.0 4.0 5.2 4.1 3.5 4.6 3.5 3.6 7.4 5.8 3.5 3.3 5.6 6.4 4.2 2.6 5.6
##   [19] 4.8 5.7 4.9 5.1 4.5 4.8 6.2 3.5 5.5 2.5 4.4 5.1 6.0 4.5 5.1 3.7 4.0 4.9
##   [37] 5.6 1.8 4.3 3.8 5.5 4.3 5.5 5.0 3.0 4.8 5.0 4.1 5.4 5.1 3.5 3.5 3.6 3.2
##   [55] 2.9 5.7 5.9 4.3 5.2 6.5 3.9 4.6 4.5 4.9 5.1 5.3 5.6 4.4 5.4 4.8 4.5 4.3
##   [73] 3.8 3.4 5.6 6.0 5.0 5.0 4.0 5.0 3.9 5.6 5.6 3.1 5.6 4.0 3.3 4.5 3.8 5.5
##   [91] 3.0 4.2 4.3 4.5 4.4 5.9 5.1 5.1 3.5 5.2 6.0 3.9 3.6 4.6 5.1 4.1 5.7 4.9
##  [109] 4.0 4.4 3.5 6.3 3.1 5.0 3.4 4.1 3.4 4.0 4.4 3.2 5.2 2.1 3.5 4.9 6.0 3.0
##  [127] 3.5 4.0 3.5 3.6 3.6 5.6 4.3 4.0 3.4 5.7 4.6 3.9 2.1 4.6 4.1 4.1 4.2 4.9
##  [145] 4.8 4.8 2.9 2.4 5.0 4.7 4.8 4.9 3.4 4.2 6.1 4.9 4.2 4.4 5.5 4.7 6.0 4.7
##  [163] 4.0 3.8 4.9 2.6 4.1 4.3 3.7 4.9 4.1 5.3 5.9 3.7 4.4 4.9 3.0 4.3 3.6 4.6
##  [181] 4.4 5.7 5.5 2.9 5.7 3.8 5.1 3.9 3.8 7.0 3.2 3.2 4.9 5.8 2.8 4.1 5.0 4.2
##  [199] 6.5 5.7 3.1 4.2 5.2 3.8 4.0 3.8 3.2 5.2 5.2 5.9 4.8 3.9 4.9 4.0 4.6 3.2
##  [217] 5.3 4.4 3.4 5.5 4.7 4.1 4.6 6.5 5.4 5.0 4.4 5.2 3.9 4.7 7.2 3.9 5.8 4.4
##  [235] 5.8 4.0 4.7 4.2 4.5 6.0 6.4 5.9 5.4 5.4 4.5 4.7 5.7 4.2 4.1 4.2 4.7 5.5
##  [253] 5.4 3.9 4.4 3.7 4.1 5.9 4.3 5.9 3.8 5.8 4.3 3.4 6.9 4.6 4.1 3.7 4.6 4.0
##  [271] 3.4 3.8 3.8 4.8 4.5 4.0 2.2 6.0 5.0 4.6 4.2 2.3 3.5 2.5 3.8 3.8 4.5 3.6
##  [289] 4.3 3.3 4.6 3.4 5.5 3.9 3.4 5.4 5.6 7.0 5.1 4.6 4.5 3.3 4.7 5.4 4.3 5.2
##  [307] 4.0 5.5 5.4 3.8 3.8 4.9 5.3 4.2 3.8 4.8 3.7 3.9 3.2 3.1 5.4 3.9 6.4 5.1
##  [325] 5.2 4.0 4.2 4.9 5.7 4.6 4.2 4.8 4.2 3.6 6.1 5.9 4.7 3.9 4.9 4.2 4.7 5.4
##  [343] 4.5 4.8 3.3 5.0 4.4 3.6 4.4 5.4 3.8 3.8 2.9 5.1 4.8 6.5 3.4 5.9 4.4 2.5
##  [361] 3.7 3.9 4.3 4.6 5.0 4.9 4.8 4.5 5.3 4.9 4.7 2.7 5.0 4.8 4.2 4.6 3.3 5.0
##  [379] 3.9 6.6 3.8 7.2 3.2 3.6 4.9 3.5 5.9 3.8 3.3 5.4 4.0 3.9 4.9 4.4 5.9 4.0
##  [397] 4.8 3.6 5.1 4.1 2.8 3.4 4.8 3.5 2.9 2.5 5.3 4.3 4.2 5.3 4.5 3.9 3.7 5.1
##  [415] 4.8 5.8 3.4 3.3 4.1 4.4 4.6 6.6 4.4 4.8 4.8 4.3 3.8 4.9 5.4 5.9 5.1 5.1
##  [433] 5.0 4.2 4.4 4.5 3.5 5.4 2.6 4.3 4.9 4.3 4.2 5.1 6.1 5.5 3.8 4.9 3.4 5.3
##  [451] 5.1 4.5 4.4 5.8 3.3 5.9 4.2 4.5 4.6 3.3 5.4 4.1 4.3 4.0 4.7 3.3 4.0 5.0
##  [469] 5.4 4.5 3.5 3.4 4.6 4.3 2.8 1.9 5.3 3.5 5.6 5.9 6.1 4.4 4.6 2.9 5.4 4.4
##  [487] 3.1 4.4 4.4 3.5 4.4 4.2 5.9 6.1 3.5 3.5 3.5 4.6 5.2 6.6 4.9 5.2 4.4 5.1
##  [505] 3.5 4.4 4.6 6.0 4.8 4.3 5.2 4.1 6.8 4.5 4.5 3.6 3.2 4.9 3.6 4.9 5.6 4.3
##  [523] 3.6 5.2 3.3 5.0 4.3 5.6 5.1 3.8 4.1 5.3 3.7 5.0 5.8 3.1 4.6 4.4 4.4 4.0
##  [541] 5.2 3.5 2.6 4.1 5.2 6.6 6.0 6.2 4.2 4.1 2.8 4.7 5.7 3.3 4.2 4.0 4.2 3.3
##  [559] 4.0 4.5 3.9 6.1 4.5 6.2 5.3 4.5 5.9 3.6 5.0 4.5 2.6 6.2 4.0 3.8 2.8 3.6
##  [577] 4.4 5.4 4.1 4.6 5.5 5.5 5.3 3.3 4.1 3.5 3.4 6.1 4.5 6.0 6.0 6.2 3.8 6.4
##  [595] 5.3 4.5 5.3 5.1 3.7 4.4 5.7 5.3 4.5 4.1 3.6 6.2 5.4 7.0 5.1 4.0 4.2 5.7
##  [613] 4.6 5.8 5.2 4.9 4.3 4.0 4.2 5.9 2.8 5.6 3.9 5.4 4.5 2.5 3.9 5.0 4.4 3.2
##  [631] 4.3 3.8 4.5 3.6 5.4 3.1 4.6 5.0 3.8 2.8 5.9 3.5 5.1 5.6 3.4 5.1 4.9 3.5
##  [649] 3.1 6.0 4.0 5.6 4.6 3.2 4.9 5.6 3.9 5.0 5.2 4.5 3.7 4.7 4.6 3.2 3.7 4.4
##  [667] 4.6 4.6 3.8 3.6 5.3 4.3 3.6 4.8 4.0 3.3 4.8 4.6 5.2 6.3 3.8 5.6 4.7 3.8
##  [685] 4.0 4.1 6.0 5.3 3.4 5.5 4.7 4.3 2.6 6.6 4.9 5.8 4.8 4.1 4.8 5.6 5.0 3.6
##  [703] 5.7 4.7 4.1 5.4 3.0 4.4 3.7 3.7 4.4 5.0 5.4 3.9 5.3 3.9 4.6 4.4 5.0 3.1
##  [721] 3.7 5.7 3.6 2.8 6.0 4.4 5.0 5.0 3.7 4.4 2.8 5.1 5.6 4.9 4.5 5.6 5.1 6.2
##  [739] 3.9 3.8 6.5 3.0 3.4 5.1 5.6 5.3 5.1 3.8 3.9 2.7 4.3 4.2 5.6 3.6 3.7 4.3
##  [757] 5.4 3.8 4.0 4.4 5.0 5.1 4.8 3.6 4.9 3.0 6.0 3.6 4.9 2.9 3.5 6.3 3.7 4.5
##  [775] 4.2 4.2 6.5 3.0 4.4 5.6 5.3 4.6 6.6 3.3 5.0 5.1 5.1 3.6 3.5 5.4 3.8 3.5
##  [793] 4.4 4.7 4.5 3.4 4.2 5.8 4.9 2.4 5.6 6.6 3.6 6.6 4.8 4.5 5.3 2.4 5.6 5.9
##  [811] 3.4 4.0 4.3 4.8 5.6 6.0 4.3 5.4 5.6 3.9 4.9 4.0 5.5 4.4 4.4 3.6 4.7 4.9
##  [829] 3.8 4.3 4.9 5.3 5.7 4.1 2.6 4.6 3.8 5.8 4.2 6.4 2.1 5.9 4.7 4.4 3.4 6.7
##  [847] 4.7 5.1 6.9 4.3 4.0 4.6 3.8 6.1 4.9 4.8 4.5 4.6 5.0 3.9 3.1 3.0 4.8 5.0
##  [865] 2.9 3.8 4.4 4.5 3.8 4.1 5.8 6.0 4.9 3.1 2.9 4.4 4.8 4.3 4.2 2.5 4.3 4.1
##  [883] 2.4 5.1 3.4 3.7 4.9 3.4 5.1 5.5 2.6 5.5 6.3 3.5 5.3 4.4 5.2 5.1 3.0 5.7
##  [901] 3.4 3.7 3.9 4.8 4.1 4.4 5.1 3.9 3.3 4.1 4.6 5.3 5.8 4.3 4.6 4.8 4.9 3.5
##  [919] 3.0 5.5 6.0 3.9 3.6 4.4 6.2 3.8 4.3 3.8 4.7 2.7 3.5 4.6 3.2 3.9 5.1 5.6
##  [937] 4.5 4.5 3.2 2.9 3.9 4.0 3.4 5.0 3.7 3.5 5.0 4.6 3.1 5.4 5.0 3.6 4.5 4.0
##  [955] 3.8 3.8 5.1 4.7 3.9 5.3 4.6 5.7 4.6 5.1 4.8 5.4 2.9 6.2 3.7 2.8 3.7 3.1
##  [973] 5.3 3.6 5.0 4.4 4.7 4.0 4.4 4.1 3.2 4.7 5.4 3.5 3.3 5.9 3.9 6.1 5.6 5.2
##  [991] 6.4 5.2 5.0 2.5 3.5 5.8 3.7 5.0 4.4 5.5
## 
## $func.thetastar
## [1] 0.0066
## 
## $jack.boot.val
##  [1]  0.55887324  0.46346154  0.31424731  0.14535211  0.01623188 -0.06920904
##  [7] -0.17933884 -0.20780347 -0.44476744 -0.53314286
## 
## $jack.boot.se
## [1] 1.043148
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
##    [1] 5.6 4.7 3.5 6.1 5.8 4.1 4.6 2.8 4.3 5.9 4.4 6.0 4.0 3.1 5.3 5.7 4.6 3.3
##   [19] 4.5 4.5 5.1 4.6 3.7 5.4 3.3 3.5 4.0 5.0 4.0 4.4 4.4 2.5 4.2 3.3 4.2 4.4
##   [37] 5.2 5.9 5.1 4.7 4.2 5.7 3.3 4.3 4.2 4.4 3.8 4.0 3.5 2.7 4.8 4.8 5.1 3.4
##   [55] 3.6 4.4 5.3 4.0 4.2 5.9 5.7 4.7 5.1 4.3 5.5 4.1 3.6 4.7 4.2 3.3 5.3 3.8
##   [73] 5.0 5.1 4.8 4.6 4.6 4.4 5.3 2.9 4.0 5.0 3.2 4.8 4.1 4.2 5.2 3.3 4.8 4.4
##   [91] 5.9 4.2 4.9 3.6 4.5 6.3 4.6 4.6 3.6 3.8 4.5 4.9 4.6 3.4 4.3 3.9 5.7 4.7
##  [109] 3.9 3.9 4.7 4.1 3.8 5.6 3.2 4.1 4.1 4.4 5.0 5.0 5.3 3.2 3.2 4.5 5.6 4.3
##  [127] 3.1 4.6 4.5 2.8 4.4 5.4 4.2 5.2 4.4 5.1 4.1 4.5 3.5 4.2 4.8 4.5 5.5 5.8
##  [145] 3.9 4.0 6.3 3.8 4.3 4.7 4.6 5.0 3.0 5.2 4.1 3.9 5.0 4.2 5.1 5.3 3.6 3.7
##  [163] 4.0 5.9 3.5 5.2 5.4 3.2 3.5 4.0 4.7 3.3 4.4 4.0 5.4 4.6 5.5 6.4 4.1 2.7
##  [181] 4.6 4.2 3.2 4.9 3.4 4.3 6.6 5.5 3.6 3.8 4.4 3.8 5.2 5.5 5.6 4.7 6.0 2.9
##  [199] 3.1 4.1 4.5 2.8 6.1 5.8 3.5 5.6 4.2 5.5 6.6 5.0 5.0 5.1 3.3 4.7 3.9 4.9
##  [217] 5.9 4.3 4.6 5.1 3.6 5.3 4.0 3.5 3.6 4.4 4.2 3.6 5.2 5.4 6.4 2.7 3.5 6.1
##  [235] 2.9 4.9 4.4 6.3 3.3 3.3 3.3 4.8 5.6 4.2 4.7 5.5 3.2 5.8 3.6 4.0 4.5 5.0
##  [253] 4.8 5.9 4.2 3.9 5.3 4.0 3.6 2.5 4.6 5.0 5.3 4.2 4.2 5.5 4.6 5.5 3.6 4.3
##  [271] 4.5 4.5 4.3 4.9 3.7 4.5 5.8 5.1 4.1 5.3 3.1 4.7 5.5 4.1 3.8 3.5 4.7 5.0
##  [289] 5.2 3.7 4.2 5.3 5.6 3.6 4.5 3.9 3.3 4.4 3.8 4.7 4.8 4.1 3.0 3.9 4.4 3.9
##  [307] 5.6 4.9 4.9 5.3 5.3 3.4 4.4 3.4 4.6 4.6 3.9 4.4 4.5 5.0 4.6 5.2 4.8 4.4
##  [325] 3.9 5.7 4.5 4.9 4.9 5.2 5.7 3.7 1.8 2.9 4.7 4.0 3.6 3.8 5.1 3.9 3.6 3.4
##  [343] 5.6 5.5 3.9 3.5 5.4 3.5 4.2 3.6 3.7 4.7 4.4 3.7 3.5 4.0 5.2 5.9 3.7 4.7
##  [361] 5.0 4.4 3.4 4.7 6.5 2.9 4.3 5.1 6.1 5.2 4.6 5.0 4.5 5.4 4.4 4.8 6.6 5.0
##  [379] 4.2 5.0 3.8 3.5 4.5 2.4 4.0 4.3 5.3 3.3 5.1 4.8 4.6 2.7 5.5 4.2 3.7 4.0
##  [397] 5.2 3.5 5.4 4.2 4.9 4.2 5.1 4.2 4.9 5.2 5.8 5.2 4.4 5.4 7.0 4.7 3.0 5.6
##  [415] 5.1 6.3 5.0 6.4 4.6 4.7 3.6 4.5 5.3 4.4 5.2 3.2 3.9 5.1 6.3 5.1 4.5 4.2
##  [433] 4.4 5.5 4.5 4.6 4.4 3.1 3.9 3.6 6.3 3.3 3.2 5.4 2.5 3.8 3.4 4.3 4.1 3.9
##  [451] 4.1 5.9 5.7 3.5 4.2 2.6 4.2 4.7 5.7 3.9 4.3 4.3 3.3 3.8 4.7 4.2 2.7 4.4
##  [469] 6.1 2.5 3.7 5.6 4.7 3.6 4.5 5.3 3.6 5.3 4.4 5.6 5.9 4.9 5.7 4.6 5.0 4.2
##  [487] 5.0 4.1 3.8 3.4 4.5 4.3 3.9 5.1 3.2 3.5 6.0 3.6 5.8 3.8 4.5 3.4 4.1 3.9
##  [505] 3.1 6.1 5.7 4.5 3.7 5.2 5.0 3.9 3.0 4.9 5.9 5.2 3.9 4.5 4.8 3.8 5.4 5.5
##  [523] 5.1 4.8 3.2 4.8 4.5 4.8 4.9 4.5 5.5 4.8 4.5 5.3 4.6 5.1 2.8 5.0 2.8 4.3
##  [541] 4.2 3.9 4.7 5.6 4.6 4.2 4.1 5.8 5.4 5.1 3.6 6.6 4.1 3.9 3.0 5.4 5.3 2.9
##  [559] 5.7 4.2 6.2 4.3 5.0 4.2 4.8 5.2 5.1 3.0 4.9 5.4 3.6 3.8 4.5 5.2 4.3 5.2
##  [577] 2.7 2.8 6.1 4.5 4.2 6.0 4.8 5.8 5.3 5.2 4.0 4.1 4.4 3.7 5.5 6.3 5.8 4.6
##  [595] 3.9 3.8 5.0 3.6 3.5 3.7 4.7 4.2 5.5 4.3 5.2 4.8 4.8 4.3 3.8 5.0 4.5 3.9
##  [613] 4.4 6.0 4.8 3.7 5.8 5.8 5.0 3.6 5.0 2.9 4.4 4.6 4.9 2.9 5.1 4.0 4.4 4.1
##  [631] 3.6 4.5 4.2 5.0 5.5 3.0 4.9 6.1 4.5 3.4 4.9 2.7 3.8 4.0 5.3 4.5 3.8 3.0
##  [649] 4.6 6.2 4.6 4.5 4.2 5.0 4.4 5.3 5.1 3.6 5.9 5.1 5.7 3.6 4.4 4.0 5.4 4.0
##  [667] 5.0 2.4 4.0 4.3 4.9 6.2 4.0 4.7 4.8 4.6 4.0 2.9 3.0 4.6 4.2 3.3 4.9 4.7
##  [685] 5.1 4.3 5.2 6.2 4.0 3.7 4.4 5.8 6.1 4.2 5.8 4.1 5.0 5.0 5.2 5.1 3.4 6.2
##  [703] 5.4 4.3 4.2 4.9 5.8 3.5 5.0 4.2 5.6 4.9 6.4 3.8 4.6 4.0 4.7 3.8 1.5 3.8
##  [721] 3.1 5.1 4.0 4.9 2.7 5.0 4.6 5.7 3.1 3.8 3.9 3.6 5.6 3.7 5.0 3.6 4.7 4.2
##  [739] 4.5 4.5 4.8 6.0 4.5 4.4 5.7 4.4 3.9 5.1 4.2 3.9 4.2 4.7 4.5 4.6 4.9 3.5
##  [757] 3.4 3.0 3.2 4.4 3.1 4.5 5.6 5.6 4.6 3.7 4.8 4.0 4.3 4.2 4.3 4.2 6.0 5.2
##  [775] 4.6 4.8 4.8 4.4 6.1 5.0 4.7 3.3 4.2 4.4 2.0 5.0 5.3 5.6 5.7 3.7 1.9 4.2
##  [793] 6.0 4.3 4.2 4.7 4.8 5.0 4.9 6.3 3.1 3.6 4.9 4.5 3.8 4.7 4.2 4.6 6.5 4.1
##  [811] 4.6 5.3 5.1 2.5 4.1 4.8 4.1 4.0 4.6 6.0 4.8 3.9 5.1 4.5 5.9 3.4 5.8 4.2
##  [829] 5.1 5.0 3.0 4.9 4.7 3.3 3.8 5.3 4.8 3.2 4.2 4.4 5.1 4.1 4.4 5.0 4.0 5.2
##  [847] 5.3 4.6 4.8 5.5 5.1 5.4 3.4 5.7 5.5 5.0 4.0 4.6 5.0 3.1 4.4 5.3 1.6 5.2
##  [865] 4.0 4.8 5.2 4.7 5.4 2.8 4.9 4.9 4.5 3.4 3.9 3.4 3.6 4.6 3.4 5.2 4.2 5.3
##  [883] 4.2 5.5 5.2 4.9 5.3 3.7 1.9 4.5 4.5 5.9 4.4 4.0 5.3 5.0 3.7 5.4 5.4 3.7
##  [901] 3.9 4.4 5.1 6.6 3.3 5.0 6.0 2.4 3.2 4.4 6.1 4.4 3.0 4.7 4.2 4.4 5.5 4.2
##  [919] 5.1 4.8 3.9 5.3 4.3 5.8 3.5 4.9 3.8 4.1 4.7 4.4 6.2 5.4 4.2 5.5 5.6 5.0
##  [937] 3.3 3.8 6.2 4.9 5.1 4.1 2.7 4.2 3.9 4.2 4.4 5.5 3.8 5.3 4.7 5.0 6.0 5.3
##  [955] 4.5 5.0 5.7 3.8 4.3 5.1 4.9 5.5 5.8 5.7 5.2 6.3 3.8 3.6 4.9 5.0 4.7 5.2
##  [973] 4.4 4.7 3.3 4.7 3.5 3.2 5.4 3.8 5.1 4.6 4.0 5.0 5.3 4.6 2.9 4.6 5.1 5.3
##  [991] 5.2 4.5 4.5 3.8 4.5 5.3 6.0 3.8 5.3 4.6
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.5 5.3 5.3 5.2 5.1 5.0 4.8 4.8 4.7 4.5
## 
## $jack.boot.se
## [1] 0.8979978
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
## [1] 0.9096169
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
##   3.889770   5.965358 
##  (1.670328) (2.734362)
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
## [1]  0.5824394  0.6967325  0.4625187 -0.6851279  0.4961086  0.6166113
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
##    [1]  0.0524259718  1.2345155802  0.9222773655 -0.4115331616  1.2824720830
##    [6]  1.0655362661  0.3974272024  0.9031955501  0.4563883761  0.9878000559
##   [11]  0.6662660622  1.0193388693  0.5865380961  0.8461913097  1.7563761023
##   [16]  0.3359400895  0.6064785350  1.8029021762  1.0602221146  0.6203261138
##   [21]  0.5810461620  0.7153618220  0.4052254695  0.7963519871  0.6543833996
##   [26]  1.2935118638  0.9232213576  0.7508260774  1.4372578163  1.1802625328
##   [31]  1.2908433846  1.1482177484  1.2662404326 -0.0111967659 -0.1983185769
##   [36]  0.6823671726  0.8048910372  1.3471165481  0.4367832647 -1.2165831929
##   [41]  0.0550398423  0.1588279994  2.0168603889  0.6618933138  0.7831639959
##   [46]  0.9562896905  0.9328614318  0.8646560434  0.4976133122  0.3978045086
##   [51]  1.1817454692  0.7784061744  1.1954344417  1.4280471348  1.1512979508
##   [56]  1.0420537038  0.9113154458  1.7729111077  0.7984988639  0.5145647108
##   [61]  0.8425323069  1.6000130571  0.4542880396  0.5324656799  1.2067077072
##   [66]  0.1314477400 -0.1378068510  1.4375011970  0.6583242017  0.3189194367
##   [71]  0.2410740772  0.8225503653  0.8014090165  0.7795619145  1.0523820460
##   [76]  1.1052209088 -0.1931538708  0.7343347989  1.0243009945  0.2738103256
##   [81] -0.1584531431  1.7258002900  1.0901771403  0.8009877420  1.0494197959
##   [86]  0.9381868428  1.0037319246  0.9696677847  0.8092870876  0.3846978662
##   [91]  0.8256524391  1.7991030082  0.1503161597  0.2764259263 -0.5786044112
##   [96]  1.1907577432  1.7343533550  0.2216296911  0.3048983840 -0.9390982070
##  [101]  0.7523956738  0.3770966802  1.2913861093  0.5512944866  1.7606625093
##  [106]  0.4267027242  0.8518081962 -1.0394537190 -0.3035891436  0.0821227173
##  [111]  0.8322181978  1.0660766013  0.1438449462  0.3551257902  0.9191356850
##  [116]  0.3847858112  1.2549051394  1.5739676391  0.7363288958  1.3268971179
##  [121]  0.6585322954  0.0073621437  0.2775597601  0.6769994980 -0.2054070005
##  [126]  0.6798363191  0.4925723305  0.2557655763  0.8254438928  0.4114594992
##  [131] -0.6854445785  1.0080000966  0.9755053235  0.5632877974  1.1259085963
##  [136]  0.6255951293  1.3626866945  1.3566350405  0.4080140063  0.3755008547
##  [141]  0.2578577144  1.2711331850  1.3086949690  0.1069052347  0.9969580706
##  [146]  1.6405465649  0.1162966484  0.8697028099  0.3950698251  0.1538872826
##  [151]  0.6056624042  0.5863946777  1.5310948967  0.9094665958  1.0588437107
##  [156]  0.5303949654  0.9382112889  0.5370928950  1.3497435966 -0.6359338294
##  [161]  1.2897546258  0.4215953853  0.2338390494  0.4708617806  0.5916376996
##  [166]  0.8168202001  0.0584133519  1.8983093834  0.7828595823  1.1939365764
##  [171]  1.0027258024  0.1791547668  1.0664274596  1.0720272516  0.7411916861
##  [176]  0.5205305174  0.0715100069  0.3922215831  1.4052199142  0.8738016367
##  [181]  1.9639740688  0.9530468193  0.0545434838  0.5585272594  1.0079999928
##  [186]  0.2480771499  1.3627451371  0.5526235984  0.9483784388  0.4781047648
##  [191]  0.7920027400  0.6745498539 -0.5566553914  1.2185853638 -0.0428052133
##  [196]  0.8489183955  1.0824165124  1.0834716862  0.6163757128 -0.8803025408
##  [201]  0.4781887617  0.0718833175  0.7769055106 -0.7220635528  1.2642868149
##  [206]  0.5812473651  0.5947543691  1.6381465511  0.9578923395  1.1785717469
##  [211]  0.4317726357 -0.0744648947  1.0032470198 -0.2296282730  0.2223765132
##  [216]  0.4951260062  1.0245890296  0.9732743583  0.4280110321  1.6130713348
##  [221]  1.4669406916 -1.0493549030  1.2372933851 -0.7477985740  1.1867905841
##  [226]  1.0001593068  0.5097577421  0.4265983332  1.7563151444  0.6964067361
##  [231]  1.6814265818  0.2678438632  1.1051707654  1.2859658516  0.8985485418
##  [236]  0.2219165524  0.4304846985 -0.0001998190  0.6124997451  1.6035307006
##  [241]  0.5840732562  1.0191308213  0.7609802587  1.2240575777 -1.1518525089
##  [246]  1.1615669857  1.0761060809  1.5676441793  1.1557746604  0.8431209473
##  [251]  1.0140387614  0.4255831441 -0.6961368650  0.1808135050  0.0499548520
##  [256]  0.1836037774 -1.0872874221 -0.0918540590  0.8087303100  0.6210498451
##  [261]  1.0483227706  1.4849852447  0.5781329608  0.5428493706  0.4489239914
##  [266]  0.2620928307  0.2608922108  0.4833980653  0.5084619392  0.5245159285
##  [271]  1.2889139553  1.5838239772  1.2022356444  0.5589486856  1.1939679584
##  [276]  1.1122278564  0.3522067430  0.1820460529  0.1991373885 -0.6638986808
##  [281]  1.9813585542  0.7605253912  1.3630611797  0.8976838503  0.6633932268
##  [286]  0.0448205994 -0.0175106771  0.7977386674  0.0303187149  0.6291445336
##  [291]  1.2434964194  1.6361251720  0.5057444863  0.8575451700  0.4621008642
##  [296]  0.3169090351  1.1694046693  0.2149245487  0.4940081860  0.6633932268
##  [301] -0.0112414355  1.0731993874  0.1255688919  1.7592917816  0.1622179516
##  [306]  0.7423339475  0.9158117531  0.7238628627 -0.1473314990  0.2899837847
##  [311]  0.9660615699  0.7329890709  0.1782470343  0.1921779445  0.3875188568
##  [316]  0.8465748815 -0.5234203512  0.9788630913  0.1443988428  1.0107063466
##  [321]  0.7624834570  0.9470770777  0.0659660051  0.2386723816  0.9080988093
##  [326]  0.5698643941  0.8923063663  0.4103318175 -0.0192483764  0.5774482968
##  [331]  0.6442637005  0.4974035718  0.6082897231  0.5485180867  1.0430384353
##  [336]  1.4425028199  0.4301132611  0.1734104988  0.6560364976  0.9087184101
##  [341]  0.6067802256  0.8078560657 -0.4587296880  0.7030426186 -0.1551758585
##  [346]  0.7755229663  0.6670678328 -0.0095856060 -0.2983104755  0.2148697400
##  [351]  1.2811251202  0.4004050471 -0.5344873029  1.7512677740  1.7514828169
##  [356]  0.6926497122  0.7877366536  0.6121132345  0.4347768217  1.3110657030
##  [361] -0.1629152945  1.4874502626  1.7471540661  0.6744881614  0.3494044564
##  [366]  0.8122575332  1.0103467402  0.2147382645  0.6778241409  0.5321886156
##  [371]  0.9349079905 -0.0476569164  0.4243640594 -0.0066605167  1.0235012764
##  [376]  0.9233368643  0.0791368365 -0.2233185316  1.1350636282  0.3080823312
##  [381]  1.6437669412  0.6110198998  1.5609054327  0.0590703166  0.1707547384
##  [386]  0.8099502229  0.3395549812  0.5380241654  1.3808025355  0.4674042543
##  [391]  0.3642285125  0.8119866722  0.3445805388  0.9191652377  0.0544112310
##  [396]  0.3884640081  0.5222328050  1.0356000524  0.4045873630  0.1886311043
##  [401] -0.2226980754  1.4483178017  1.2664097487  0.1097622153  0.7391738313
##  [406]  0.7577984639  0.4532157446 -1.4300203656  0.9483559293  1.1737728633
##  [411] -0.1181917144  0.7341891074  0.9381868428  1.0499146337  0.9800960688
##  [416]  0.6211451810  0.8822216816  0.8632731894  0.0814314332  0.7134636662
##  [421]  1.6056130866  0.0726931785  0.9712219227  1.2803412718 -0.8450687637
##  [426]  0.5425269746  0.1271278557  1.0119586262  0.3471476798  1.4976441788
##  [431] -0.2600059913  0.4901646920 -0.2006847697  0.7167936378 -0.2831580077
##  [436]  0.7796578754  0.7881897464  1.2772229777  1.8229082176  0.8306017298
##  [441]  1.8829925157  0.6921446097  1.2212606628  1.0552905574  0.6556699517
##  [446]  1.0677802423  0.5987384921  0.9952088349  0.7148582693  1.1379391236
##  [451]  0.5100144177  0.2452756530  0.4381689915  1.2861757968  0.3837935167
##  [456]  1.5090542483  0.6176848640  0.5064091919  1.2936934381  0.8924760595
##  [461]  0.4764332327  0.9401916693  0.6874757089  0.9023152768  0.5711111696
##  [466]  0.3459163775  0.2067952159  0.9492963282  0.7342862647 -0.0065598437
##  [471]  0.4446015086 -0.1024763004  0.6034532889  0.5495557049  0.6226466206
##  [476]  0.3201855907  0.5049913826  0.2954546520  0.5582110722  0.9004200526
##  [481]  0.0301866633  1.1115392432  0.2472509240  1.2034509597 -0.2642019733
##  [486]  0.9475453856  0.5759036066  0.8688620376  1.8275306869  0.2977748855
##  [491]  0.3297479986  1.9107046260  0.1296955839  0.5144181510  0.6017142474
##  [496]  0.7323341891  0.3906087857  1.1605929837  0.3382053289  0.5001827574
##  [501] -0.0751237391 -0.1446643853  0.4985636186  1.3448144860  0.5636748730
##  [506]  0.3199573007 -1.1376203552  0.4247902757  0.5918297337  1.5588956408
##  [511]  0.3666097802  1.2596340544  1.4550657440  0.3549298893 -0.1537898815
##  [516]  0.9293821188 -0.3102954138  0.3909073743  0.8222994525  1.0044158744
##  [521]  0.6113582519  1.0217044840  0.0483608922 -0.0811469679 -0.0324505274
##  [526]  0.6149667131  0.7696052427  0.4059677109  0.3059442025  1.2935590944
##  [531]  0.4356192845 -0.1772290122  0.5243052547  1.3387371112  0.9015780997
##  [536]  0.9212351808  1.0483227706  1.2981336373  0.6436772234  0.2914923328
##  [541] -0.2454659549  1.1009455874 -0.5758045036  0.7645271071  0.7335113737
##  [546]  1.3053829307  0.8167763068  1.8099329170 -0.2215387216  0.6282725019
##  [551]  1.1826501141 -0.6268605851  0.4328361052  1.6563234462  0.0210719253
##  [556]  0.1891597312  0.2558211653  1.2144133946  1.1691836209  0.4004868683
##  [561]  0.9811367880  0.6859427070  0.2450458471  1.4848806811 -0.0275499073
##  [566]  0.6867738969  0.8093116662  1.1658860886  0.3430624009  0.3493010426
##  [571]  0.5131349464  1.0924891784  0.6156536318  1.5236697435  0.8798784069
##  [576]  0.5260850323  1.0623879665  0.4648181304  0.6936411612  0.4665066879
##  [581]  1.0046946032  1.3151272929  1.1445826661 -0.2149147038  0.4968023742
##  [586]  1.5009448903  0.6979077776 -0.0907806627  0.3793065487  1.1386178933
##  [591]  0.6717431810  1.4321156039  0.1054312264  1.6656036042  0.8645910375
##  [596]  0.2028684824  1.2886653873  1.7622012388  0.3572517605 -0.7050821276
##  [601]  0.8171592325 -0.4027948336  1.3408691909 -0.4272559337  0.2201256990
##  [606]  1.1596620158  0.7442537835  1.1405959082  0.8257794495 -0.0186059553
##  [611]  0.6584318188 -0.9086260124  0.6536830871  1.2596340544  0.8550102990
##  [616]  0.0275983527 -0.2422730602  0.7361683177 -0.0565313615  1.0536607601
##  [621] -1.1448692697  0.4990073095 -0.7368474858  1.0669806981  0.8171592325
##  [626]  0.1516105396 -0.5718597189  1.7551393204  0.2793502095  1.1744943687
##  [631]  0.4277890373  0.4721550557  1.1110637241  1.3002984412  1.2763956064
##  [636]  0.6508339365 -0.9353137377 -0.4775515511  1.0808656020  0.3775245716
##  [641]  0.5481645647 -0.0345451499  0.2149892772  1.1386178933  0.8600941942
##  [646]  0.7423332683  0.1196509639 -0.3410820492  1.3616395334  0.6339449066
##  [651]  1.6696279618 -0.4627921500  0.4046703138  0.3406219843  0.9419218417
##  [656]  1.1499705334 -0.4610585408 -0.0464157036  0.9639009044  0.8514178603
##  [661]  1.7540814572  0.5760207967  2.2838675803  0.0835871332  0.4587917727
##  [666] -0.8288758017  0.3392498470  1.1056744512  0.6201474515  0.4380653982
##  [671]  0.1806232648 -0.1887191184  0.1443988428  0.8990968317  0.6210498451
##  [676]  1.6687944347  1.1022116717  0.7174446553  0.4661536726 -0.4634798190
##  [681]  0.0290532202  0.3588961458  0.9822572437  1.0549441712  1.0547230933
##  [686]  0.7713896326  0.1187859731  1.0979423138  1.1641865489  0.3240843748
##  [691]  0.6953103864 -0.0870245019  0.0070662011  1.4573149146  0.7751795674
##  [696]  1.2272629049 -0.3300428264 -0.4134386800  0.9369477700  0.0414540653
##  [701]  0.7349981145  0.2330746467  0.6524422881  1.9758412448  1.3053829307
##  [706]  1.3195313594  0.1313382906  0.4368095822  0.6563817267  1.4321156039
##  [711] -0.0253150218  1.4758086349  0.6943496990  0.6996054678  1.6721484834
##  [716]  0.2437692535  0.5095804863  0.4146402173  0.8520484424  0.1803381889
##  [721]  0.9323754313  0.7915468562  0.8533141597  1.8718167524 -0.0262403197
##  [726]  1.2022045137  1.6626535149  1.6038226929  1.0319028737  1.6792295528
##  [731]  0.6662152989  0.0966045874  0.3937484948  0.4644941017  0.2965571687
##  [736]  0.9102429768  0.1848061254  0.2361007920  1.1628192198  1.2907596755
##  [741]  0.2347262428  1.4052199142  0.7320909639  0.5429837110  0.9015944679
##  [746]  1.2053113192  0.1272191055  1.5902826754  0.9279592119  0.8538341756
##  [751]  1.7368410659  0.3694470833  0.2494976633 -0.3544994819  0.7302372771
##  [756]  0.2916781519  0.5711835275  0.7309006063 -1.0916218259  1.4282674729
##  [761]  0.3800901862  1.8461880686  0.3382098414 -0.0442346253  0.1665678922
##  [766]  1.4219863771  0.4165064824  1.1405959082  0.1544469941 -0.2978298825
##  [771]  1.1164845875  0.7775211021  0.4668165089  0.2092395034  0.6824954118
##  [776] -0.9963782994  0.7871087543 -0.3646351112  0.8857493854  0.7089283352
##  [781]  1.2375987235  0.4724937401 -0.9417462715 -0.1843292896  0.7791979793
##  [786]  0.8926770217  0.3245918091  1.2486510869  0.3018759143  0.5792245095
##  [791]  1.1365571404 -0.1436921225  0.3935304809  0.0688946404  0.9005162141
##  [796] -0.0227257774  0.6012057242  0.4207400524  1.0041586234  0.5115967322
##  [801]  0.5065899253  1.4724122973  0.7628949680  1.9197638384  1.0385531042
##  [806]  0.2852196662  1.0790539745  0.5439401605  1.2127414521  0.5364767762
##  [811]  0.2163741584  0.8383554316  0.5997168747  1.1911089977  0.9744071683
##  [816]  1.4903605172  0.5697429393  0.0535912330 -0.1948755935  0.6847983711
##  [821]  0.9691008999  1.2889766071  0.4682351006  0.0704340063  1.1926743807
##  [826]  0.4360511880 -0.2974744073  0.7738780427  1.8822703229  0.7257445052
##  [831]  0.8511867985  0.1537978950  0.6881335037  0.3686072105  0.3681464059
##  [836]  0.8764021749  0.4652868814  0.5559571280  0.5749401479  0.5327203425
##  [841]  1.0685110542 -0.0180827961  0.1532585841  1.7488338887  1.1800369431
##  [846]  1.5446421930  0.8665698406  0.4920471159  0.5848996709 -0.2424891088
##  [851]  1.0846311394  0.2529161572 -0.0491795711  0.2164123917 -0.4725785021
##  [856]  0.0297398585  0.8953873172  1.1720861134 -0.7083952190  0.6339227183
##  [861]  0.7182246854  1.1547485872  1.0640455941  1.2829640820  0.8283194374
##  [866]  0.5063935905  1.1658715980  0.1340853499  0.8253286213 -0.0962091073
##  [871]  1.1165803806  1.2635750118  0.5299284380  0.9539348697  0.6109813751
##  [876]  0.6436350609  0.3355748144  1.5664567027  0.2064153289  0.5366468252
##  [881]  0.9961612173  0.0835549946  0.1350176051  1.2411029216  1.3495202463
##  [886]  0.6404629570  0.9460142898  1.0917711211  0.0364522983  0.8734658940
##  [891]  0.7168851489  0.7996105800  0.7277640633 -0.4213595954  0.1047294633
##  [896]  1.0880960498  1.2422664068  2.0513075234  0.9740765627  0.3927916189
##  [901]  0.9660769334  1.0179237793  1.3277148820  0.6532944552  0.9769448661
##  [906]  0.5001216129  1.4820897191  0.6124594758 -0.2741743591  0.7675836542
##  [911]  0.2989032075  0.4725947338 -0.7998443469  0.1724373271  0.3725539971
##  [916]  1.5086271902  1.0667765940  0.1185984218 -0.1991033678  0.5740072792
##  [921]  0.5159704499  0.2630953134  1.5057915803  0.7928095253 -0.0386572071
##  [926] -0.3293042207  1.4407000294  0.8815240353  1.5030445696  0.3104182435
##  [931]  1.1594838837  1.9077000583  1.0874919036  0.5421333982  0.2935262070
##  [936]  1.0983959383  0.8353573392  0.2370714911  0.6950616001  1.4750415604
##  [941]  1.9843903092  1.5468259728  1.7160388030  0.3062866073  0.3433228640
##  [946]  1.4300049213  1.1468758246  0.0265336088  1.0107143541  0.4326527150
##  [951]  0.7549307555 -0.1298047821 -0.4858539818  0.0009824766  0.6420111537
##  [956]  1.4465294096  1.8940336172  0.1079228622  0.8016259878  0.4055940534
##  [961]  0.3476507710  0.9468228215  0.4560280936  1.2465837614  1.2825023505
##  [966]  2.2520404815  0.6577602180  0.1975140323  0.2999854980  0.9094852613
##  [971] -0.4728875441  1.3771006052  0.8566635854  0.8333110511  1.0670984160
##  [976]  0.7604092754  0.6895796264  0.9209922508  0.3369946402  0.1264803245
##  [981]  1.1473717688  0.9957321115  1.1489234193  0.9707464634  1.1032542995
##  [986]  0.5183835573  0.8198538140  1.7442670826  1.2478107522  0.4143634378
##  [991]  0.9770181817  0.9624965629 -0.0756190674  0.2536653344  0.0591564154
##  [996]  0.6232033432  0.6156156035  2.1183536908  0.6872424655  1.0868483332
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
##   0.65205712   0.33725267 
##  (0.10664866) (0.07541187)
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
## [1] -0.03125328 -0.48922416  0.27800779 -0.59246829 -0.58184620 -0.43567869
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
## [1] 0.0158
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9056769
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
## t1*      4.5 0.01011011   0.9099692
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 1 4 6 7 
## 4 2 1 3
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
## [1] 0.0136
```

```r
se.boot
```

```
## [1] 0.914424
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

