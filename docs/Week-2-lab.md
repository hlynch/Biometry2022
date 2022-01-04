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
## 0 3 5 6 8 9 
## 1 1 1 1 5 1
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
## [1] 0.0098
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
## [1] 2.615688
```

```r
UL.boot
```

```
## [1] 6.403912
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##   2.5%  97.5% 
## 2.5975 6.4000
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
##    [1] 5.5 2.6 4.4 5.4 6.9 3.2 5.3 5.0 5.8 4.4 4.5 4.3 4.3 4.2 4.6 3.6 2.8 2.9
##   [19] 4.8 3.4 5.2 6.9 3.5 5.0 4.2 3.1 3.7 2.7 5.3 5.4 4.3 4.3 4.4 3.7 4.4 5.0
##   [37] 4.0 5.1 4.4 4.5 5.9 3.3 5.6 4.7 4.0 4.4 4.2 5.0 5.1 4.5 5.2 5.6 6.4 4.4
##   [55] 5.5 4.1 4.8 5.4 5.2 2.3 5.5 4.2 4.6 6.1 2.6 4.2 2.9 4.8 4.8 3.9 3.4 4.3
##   [73] 4.3 5.8 3.1 4.2 4.5 3.7 2.7 3.2 4.3 5.4 2.9 3.0 6.2 4.6 5.4 4.2 4.6 4.9
##   [91] 6.1 5.2 4.8 3.1 4.9 5.8 5.3 5.3 5.2 4.3 4.3 4.4 5.8 5.0 4.2 4.3 5.8 4.7
##  [109] 4.4 5.9 4.7 4.5 4.3 5.3 3.9 5.8 5.8 4.8 4.3 3.2 5.1 5.5 4.1 4.0 5.9 6.4
##  [127] 4.1 5.2 3.3 5.3 6.3 4.0 4.2 4.1 5.0 4.4 5.5 5.0 3.3 3.9 5.8 4.1 6.6 5.7
##  [145] 2.4 4.1 4.7 4.2 4.4 4.1 4.4 6.7 5.8 5.4 4.4 5.7 4.5 4.6 5.1 3.9 3.9 5.0
##  [163] 4.8 5.4 4.2 4.5 5.7 4.8 4.2 4.8 5.7 5.7 4.4 6.1 2.8 4.2 6.2 5.0 2.8 5.1
##  [181] 4.7 3.9 6.1 5.7 4.3 6.2 5.5 4.3 4.7 3.0 4.3 6.0 2.5 4.1 4.9 3.9 4.0 5.1
##  [199] 4.9 4.6 4.7 3.8 5.0 4.3 4.5 3.6 5.0 5.3 5.2 4.0 5.5 5.3 4.0 5.8 3.9 4.2
##  [217] 3.7 5.1 5.4 4.8 4.1 4.9 4.6 5.2 5.3 4.0 4.3 3.7 6.8 4.8 3.6 5.3 5.4 2.7
##  [235] 3.5 2.4 4.7 5.2 4.0 4.4 3.2 6.1 4.9 6.5 2.8 3.7 4.5 5.5 3.4 4.3 4.1 3.6
##  [253] 4.9 5.3 4.5 4.6 2.8 5.9 3.2 3.0 4.7 6.6 3.8 4.6 5.1 4.0 5.0 5.7 4.6 5.7
##  [271] 4.9 4.4 3.0 4.0 5.3 5.0 5.7 4.2 4.7 3.8 4.4 5.6 3.8 4.2 5.6 4.3 2.9 4.5
##  [289] 5.0 6.2 5.6 5.4 4.8 4.7 4.9 4.1 4.6 3.1 4.1 4.6 3.6 5.0 5.0 4.4 4.2 4.5
##  [307] 5.6 3.9 4.6 6.6 4.9 4.1 5.8 4.3 6.2 4.3 3.6 1.9 4.1 4.6 5.1 4.1 4.8 3.0
##  [325] 3.1 4.4 4.8 3.4 3.9 5.2 3.9 4.0 3.3 3.9 5.1 4.1 5.1 4.8 3.4 2.6 3.3 5.3
##  [343] 4.4 3.5 6.5 3.6 4.3 5.3 5.3 4.5 3.0 4.4 5.5 6.0 4.7 3.5 3.5 5.5 4.1 5.1
##  [361] 4.3 3.8 4.2 6.1 5.4 3.2 3.7 5.0 3.2 4.6 3.8 4.4 4.1 5.0 4.8 3.9 4.4 5.0
##  [379] 6.0 4.8 3.9 5.6 4.2 5.1 3.5 3.4 3.5 3.6 4.4 4.0 5.0 3.7 5.0 4.8 5.6 4.7
##  [397] 3.9 4.4 2.9 3.7 5.1 3.4 4.0 3.7 5.5 5.1 4.6 3.6 5.5 5.1 6.1 2.4 4.1 3.2
##  [415] 3.7 5.3 5.9 4.5 3.7 5.8 3.9 3.9 4.6 5.0 4.1 4.9 4.1 4.7 6.0 4.6 5.6 3.8
##  [433] 6.6 5.0 4.2 5.8 5.6 6.6 3.0 4.5 3.3 4.7 4.8 4.4 4.8 4.4 4.0 4.5 4.4 4.3
##  [451] 4.4 5.5 5.2 4.5 3.9 4.0 6.6 3.0 6.1 4.5 4.7 4.8 5.0 3.8 5.2 4.1 6.4 5.1
##  [469] 4.9 4.8 4.3 4.5 5.3 3.6 3.6 4.5 4.9 5.1 2.8 3.2 3.5 4.3 2.8 3.7 4.2 3.7
##  [487] 5.1 2.4 5.0 4.7 5.0 3.1 5.5 4.3 4.6 2.3 4.6 3.5 5.3 4.8 3.8 3.9 4.2 4.1
##  [505] 5.2 5.2 5.6 4.6 5.4 3.3 3.8 4.8 2.7 4.6 3.7 4.1 4.5 5.2 3.2 3.6 5.9 3.7
##  [523] 5.5 3.7 4.1 6.0 4.2 5.3 4.6 3.9 4.1 5.0 4.9 5.0 2.9 4.5 4.4 6.9 4.0 3.3
##  [541] 4.4 6.1 2.9 3.0 3.9 5.0 5.5 4.0 3.9 6.7 5.3 5.7 5.6 3.7 3.4 5.0 5.4 4.5
##  [559] 4.2 5.3 4.5 5.5 4.0 2.9 3.8 4.2 3.3 5.6 6.5 4.0 3.3 4.3 4.7 5.2 4.3 1.8
##  [577] 5.4 4.6 3.5 5.3 3.8 6.0 4.2 4.6 4.0 5.1 5.3 5.4 3.8 3.7 3.8 5.6 4.8 4.5
##  [595] 4.3 4.4 5.2 4.2 4.9 6.0 3.1 5.6 3.9 4.9 5.7 4.2 4.2 4.1 3.6 2.9 6.0 5.5
##  [613] 4.5 3.2 5.6 4.0 5.6 4.2 4.4 4.4 4.5 5.1 4.8 5.7 5.1 4.5 4.6 3.8 3.5 4.3
##  [631] 5.0 3.9 4.4 2.9 4.1 3.8 4.5 4.3 5.7 5.4 4.5 3.2 3.6 5.1 4.4 5.2 4.5 6.3
##  [649] 3.6 3.0 4.2 3.1 4.4 4.5 4.3 4.3 3.2 5.8 4.4 3.4 3.9 3.9 5.7 6.1 3.5 4.4
##  [667] 4.4 5.2 7.0 4.7 6.4 3.4 6.6 3.9 3.9 3.6 3.5 4.2 3.9 2.8 5.7 5.6 4.7 3.6
##  [685] 4.7 4.0 5.7 4.2 3.6 4.8 4.4 4.4 4.7 5.5 4.4 4.6 3.5 6.1 4.4 5.1 5.7 4.4
##  [703] 4.1 4.9 5.1 4.6 4.4 4.5 4.2 4.7 5.0 2.6 4.5 3.9 2.6 4.3 4.6 2.8 6.1 4.2
##  [721] 3.2 2.4 3.9 5.4 4.8 4.6 6.0 3.7 5.0 4.9 3.3 4.5 2.9 5.1 3.9 4.9 5.5 2.9
##  [739] 4.0 4.3 5.3 5.1 3.9 3.8 3.6 4.5 4.1 3.7 5.3 4.7 5.2 4.4 3.5 5.1 3.3 4.0
##  [757] 4.6 5.0 4.4 3.7 6.6 5.2 3.9 4.6 4.5 4.2 4.5 4.4 6.2 4.7 3.4 4.0 4.5 5.6
##  [775] 4.7 3.8 5.0 3.6 3.7 3.6 5.2 5.4 4.6 4.6 3.1 4.8 2.7 5.2 3.5 5.9 4.9 3.8
##  [793] 4.4 4.0 3.8 5.3 5.3 4.2 4.0 4.1 3.7 3.7 5.3 3.6 4.3 5.1 5.5 5.5 7.1 4.1
##  [811] 4.2 2.8 5.1 3.8 3.8 3.1 2.8 3.9 4.5 3.8 3.4 5.4 5.5 3.2 6.2 4.8 4.4 4.8
##  [829] 4.3 3.2 4.1 5.3 4.9 3.5 5.7 3.0 3.9 2.1 3.6 2.6 3.3 5.8 2.5 4.9 4.4 3.3
##  [847] 3.2 4.4 4.4 4.0 5.0 4.2 2.7 5.3 5.3 4.5 5.0 4.5 3.1 3.5 4.9 5.8 5.9 4.5
##  [865] 5.2 3.8 4.5 3.7 4.6 3.9 4.8 6.0 5.2 4.1 5.3 5.0 4.7 4.5 3.4 4.4 5.9 4.0
##  [883] 5.2 4.0 3.8 3.8 4.7 4.0 5.1 4.1 4.7 3.3 3.4 4.2 4.0 4.0 5.4 2.9 3.8 4.9
##  [901] 4.1 3.6 4.5 2.8 4.8 6.0 3.9 4.7 3.3 4.0 2.8 5.8 4.9 5.1 3.6 5.6 6.0 5.7
##  [919] 4.0 3.7 6.4 4.0 6.8 4.2 4.5 5.4 4.9 5.4 4.7 3.7 5.2 3.5 4.8 6.7 3.8 3.1
##  [937] 5.1 5.2 5.2 4.4 5.0 4.1 4.6 4.4 5.2 4.4 4.8 2.5 4.4 3.1 3.5 7.1 3.8 5.2
##  [955] 3.9 4.3 4.6 4.1 4.3 4.5 5.1 5.4 4.2 3.1 3.1 4.6 5.3 2.9 5.2 5.6 3.2 5.1
##  [973] 4.2 5.9 4.5 3.8 4.9 4.1 3.4 2.9 4.4 3.8 4.8 4.3 4.9 4.0 3.1 5.4 3.6 4.7
##  [991] 5.5 4.5 4.2 5.4 4.5 3.7 4.0 3.4 3.0 4.5
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
## 2.7975 6.4000
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
##    [1] 5.6 3.2 3.7 5.4 5.3 5.4 5.1 5.4 4.8 5.3 4.2 3.7 5.8 5.0 4.8 5.6 4.6 4.7
##   [19] 4.8 5.9 3.3 4.8 3.9 5.0 3.4 3.1 5.4 3.2 4.7 4.4 5.6 4.6 4.9 3.5 2.9 5.1
##   [37] 5.4 5.0 3.8 3.9 4.2 5.7 6.1 5.1 3.3 4.3 4.7 3.4 5.7 4.0 5.1 3.6 5.4 4.0
##   [55] 4.5 3.8 4.8 3.9 4.3 4.7 4.0 5.4 3.8 2.4 4.2 3.7 4.4 4.9 4.8 5.0 4.2 4.9
##   [73] 4.9 4.7 4.1 3.7 5.5 4.4 4.3 4.6 2.4 5.4 3.8 3.3 4.1 5.8 3.3 5.7 6.1 5.7
##   [91] 6.5 4.8 4.2 4.6 5.2 3.8 3.6 4.7 4.5 5.9 6.1 3.8 4.8 3.6 5.3 6.0 2.9 6.0
##  [109] 5.7 4.2 3.9 5.2 5.4 4.3 4.5 5.7 4.6 5.1 5.3 4.1 5.4 3.7 4.9 4.7 3.7 4.5
##  [127] 6.1 2.8 3.2 3.2 6.7 4.2 4.0 5.4 5.4 2.6 3.8 4.8 3.4 4.9 3.6 6.5 4.3 4.5
##  [145] 2.9 4.1 5.4 4.3 2.9 4.7 6.7 4.4 3.4 4.0 4.4 6.2 2.6 7.0 5.4 4.6 4.6 4.3
##  [163] 6.9 6.0 4.3 4.2 5.3 5.0 3.4 5.9 4.5 4.3 3.7 5.6 5.5 5.6 4.7 5.3 4.2 4.0
##  [181] 3.3 3.6 4.4 4.7 3.8 3.2 5.5 4.5 4.9 3.4 5.0 4.9 6.2 3.3 4.4 4.4 6.1 3.7
##  [199] 3.8 6.2 4.4 4.4 3.9 5.3 3.5 5.8 3.4 4.2 3.5 4.9 4.1 3.3 5.0 5.7 4.0 5.9
##  [217] 5.9 4.7 2.9 5.2 4.5 2.8 4.6 4.1 4.7 3.3 3.6 5.2 4.7 3.1 4.0 4.5 3.4 4.8
##  [235] 3.6 5.4 4.3 3.8 3.7 3.2 3.9 6.2 4.4 4.4 2.8 2.7 4.5 4.9 4.9 5.0 5.4 6.6
##  [253] 4.4 3.8 4.9 2.7 3.1 4.6 3.9 3.2 4.7 5.2 4.4 5.2 5.3 4.6 4.3 4.0 3.2 4.8
##  [271] 4.5 4.7 5.4 5.5 5.1 5.9 4.1 4.1 5.3 5.3 3.1 4.9 4.6 4.2 3.9 3.5 4.9 6.2
##  [289] 4.4 4.4 3.7 4.4 4.2 3.5 4.2 4.0 3.2 3.9 3.8 5.8 3.9 4.4 4.4 4.0 5.2 4.4
##  [307] 4.4 4.0 4.6 3.3 4.1 6.1 4.7 3.3 4.1 5.5 5.8 5.2 5.8 4.4 5.4 3.7 3.2 5.3
##  [325] 2.6 4.5 5.5 4.3 4.7 3.0 4.8 6.1 2.9 3.1 3.7 4.6 4.8 4.5 5.5 4.6 4.9 3.7
##  [343] 3.3 4.9 2.3 4.4 5.2 5.1 2.9 2.7 4.6 4.3 5.8 5.7 3.9 3.7 5.0 5.6 5.6 5.3
##  [361] 3.5 5.1 4.0 5.0 4.4 5.4 4.0 4.6 4.3 4.6 3.1 4.3 4.7 5.1 4.3 4.4 3.7 5.0
##  [379] 6.0 3.9 5.6 5.6 5.3 4.8 2.8 6.1 3.8 4.4 4.1 5.3 4.9 3.9 5.1 4.5 4.6 4.9
##  [397] 5.0 4.2 5.9 5.2 3.5 5.1 6.2 4.7 6.1 4.9 5.3 5.9 4.0 4.1 4.0 4.1 4.9 4.9
##  [415] 4.4 6.5 5.5 4.1 5.0 4.8 4.0 4.6 5.3 5.4 4.5 3.8 4.2 4.0 4.7 4.0 3.1 2.9
##  [433] 5.8 4.1 5.9 4.0 5.9 5.2 4.2 3.4 3.3 3.8 6.4 4.4 5.1 4.9 3.0 3.0 4.1 5.7
##  [451] 4.4 2.5 3.5 3.1 6.0 4.5 4.1 4.6 5.3 5.2 4.7 6.3 4.4 3.3 4.1 2.9 6.3 4.8
##  [469] 6.3 5.6 3.4 4.1 5.4 3.6 3.5 5.9 4.4 4.2 4.7 4.4 4.6 5.5 4.3 5.6 4.3 5.7
##  [487] 4.3 4.8 3.3 5.5 4.9 5.3 2.1 6.2 4.9 4.4 4.4 4.7 4.6 4.8 4.4 3.1 4.7 4.2
##  [505] 4.7 4.8 4.7 4.2 4.6 3.1 4.7 4.2 4.7 4.1 5.4 4.2 3.7 5.5 4.5 4.5 4.3 4.9
##  [523] 4.1 4.5 3.7 5.3 2.7 5.4 3.1 4.7 5.5 5.3 4.2 4.0 3.8 5.1 4.2 6.0 3.5 4.9
##  [541] 4.1 5.3 3.9 4.4 5.2 2.6 5.4 5.0 4.1 4.4 4.3 4.0 6.4 3.9 4.4 4.7 4.6 4.3
##  [559] 4.5 3.9 4.5 3.6 3.6 5.4 4.9 5.3 4.7 4.0 5.0 5.9 4.0 4.1 6.3 4.1 3.9 4.6
##  [577] 4.6 5.3 5.2 4.9 4.3 2.8 3.4 6.2 5.0 5.0 5.2 4.9 3.6 4.9 5.0 4.6 3.5 5.1
##  [595] 3.1 4.6 4.4 4.8 5.5 5.9 3.9 4.8 3.4 3.4 5.1 5.4 5.1 5.3 6.0 4.4 3.8 4.4
##  [613] 3.7 4.1 4.0 2.7 4.3 3.5 5.8 4.0 5.0 5.5 5.0 6.0 5.2 3.8 5.2 5.4 3.0 4.9
##  [631] 5.2 5.1 3.6 2.6 4.6 6.5 3.2 4.5 4.1 4.0 4.7 4.5 5.2 3.8 4.1 5.0 4.0 4.0
##  [649] 4.1 3.3 4.7 2.9 4.7 4.9 4.8 5.6 2.7 5.8 4.3 4.5 4.5 3.6 4.2 4.6 5.2 4.7
##  [667] 5.3 3.8 4.5 4.3 2.5 5.5 4.6 5.9 3.7 3.5 3.9 3.5 1.4 4.8 5.6 3.6 4.3 3.3
##  [685] 4.4 4.7 5.2 3.4 5.9 4.3 6.3 4.8 5.5 4.0 5.4 4.0 4.4 3.7 4.9 5.8 4.9 3.9
##  [703] 4.8 5.6 3.0 4.2 6.0 4.3 4.5 2.8 4.6 5.2 4.4 6.6 5.3 5.0 5.5 4.0 5.0 5.4
##  [721] 6.7 5.2 5.4 5.1 5.6 4.8 6.2 4.3 2.8 5.4 3.3 4.1 6.1 5.4 5.8 5.0 5.1 4.4
##  [739] 2.8 3.1 4.4 3.7 4.2 4.3 3.9 3.9 3.1 4.6 4.9 5.4 3.5 3.0 4.8 5.2 4.0 5.8
##  [757] 4.0 3.7 3.6 2.8 5.6 4.8 2.7 3.0 5.1 3.0 4.4 3.4 4.4 4.4 5.2 4.0 4.8 4.4
##  [775] 5.6 4.7 3.5 3.6 4.4 4.7 5.4 3.7 5.0 5.1 4.9 4.4 5.7 5.4 5.6 4.6 4.6 5.1
##  [793] 4.9 3.2 3.6 3.7 5.1 4.2 5.4 4.2 6.3 4.8 5.3 5.5 3.3 3.9 4.0 3.7 5.1 5.4
##  [811] 3.6 4.5 4.4 5.1 4.2 3.8 4.9 3.3 3.7 4.0 6.3 5.7 4.5 3.9 4.9 2.8 5.0 7.1
##  [829] 4.6 4.7 3.1 2.6 4.6 4.5 3.0 4.2 4.3 5.0 4.5 6.2 4.4 4.7 3.8 4.9 3.0 3.9
##  [847] 3.8 4.1 5.8 3.7 3.9 4.7 4.5 5.1 4.9 3.9 3.7 4.6 3.7 4.2 4.2 5.2 4.3 3.7
##  [865] 2.5 5.4 4.9 4.3 4.7 3.7 4.1 4.0 5.3 5.4 5.4 5.7 4.5 5.3 4.7 3.9 4.1 6.6
##  [883] 5.5 4.6 6.1 6.0 5.1 6.1 3.9 6.1 5.3 4.9 4.9 4.0 4.6 4.5 4.6 4.1 4.7 5.2
##  [901] 2.4 4.4 3.7 5.3 2.8 5.4 4.7 7.0 3.9 5.9 4.0 4.5 2.6 4.1 3.3 5.7 3.7 4.4
##  [919] 7.0 3.8 4.1 6.7 5.3 3.4 4.5 5.1 4.1 3.9 2.5 4.1 5.1 5.5 5.5 4.9 3.2 4.4
##  [937] 4.3 5.6 4.8 3.6 3.3 5.1 4.8 6.8 6.1 4.9 3.4 4.6 4.8 4.1 4.7 4.4 4.0 2.5
##  [955] 4.3 3.3 5.4 3.8 4.7 4.5 3.1 6.1 3.4 5.2 3.2 4.2 4.8 4.5 5.6 4.4 5.1 4.6
##  [973] 3.5 3.6 4.2 4.2 3.4 5.3 4.6 2.5 3.7 5.8 4.7 3.7 4.0 4.7 5.2 5.9 4.7 3.1
##  [991] 5.4 5.5 5.6 6.3 3.2 3.6 5.0 3.6 5.4 4.6
## 
## $func.thetastar
## [1] 0.026
## 
## $jack.boot.val
##  [1]  0.49587629  0.37526882  0.34969325  0.17146739  0.12668622 -0.04391691
##  [7] -0.13160920 -0.27168675 -0.35718391 -0.39786096
## 
## $jack.boot.se
## [1] 0.9116056
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
##    [1] 3.8 5.9 2.5 6.0 3.8 4.7 4.3 5.2 3.7 4.4 4.1 5.7 5.4 6.4 3.0 4.6 5.1 4.0
##   [19] 4.0 4.2 3.2 5.5 3.4 5.9 5.3 4.7 5.4 4.1 5.0 5.0 5.5 4.8 3.0 5.6 3.9 4.8
##   [37] 5.1 4.6 4.9 3.0 3.7 4.8 5.0 4.4 5.1 4.8 3.6 4.5 3.3 5.8 4.3 2.7 5.3 5.1
##   [55] 4.0 4.2 5.1 5.7 5.2 4.5 3.8 3.7 4.6 4.3 5.1 3.7 6.0 4.6 5.4 3.6 5.6 4.6
##   [73] 5.1 5.7 4.3 2.8 5.2 4.2 4.3 4.5 5.0 3.9 5.4 4.3 6.4 5.2 5.0 3.6 4.4 3.7
##   [91] 5.6 4.3 3.4 3.4 3.2 3.5 5.1 4.0 4.8 4.8 3.8 6.2 3.1 4.8 2.5 4.9 3.8 4.1
##  [109] 5.0 3.6 5.7 3.5 5.5 3.6 4.1 3.2 4.2 5.0 3.9 4.4 3.5 4.5 4.8 5.4 5.4 4.5
##  [127] 4.8 4.0 1.9 4.0 3.6 3.2 4.7 4.1 3.4 3.7 4.5 5.0 4.1 3.9 4.8 4.5 3.6 3.9
##  [145] 5.7 5.5 4.8 8.2 4.9 3.7 4.1 6.4 5.0 4.5 4.3 4.7 4.3 4.8 6.1 4.5 4.3 3.7
##  [163] 4.9 4.6 5.3 4.9 4.2 3.0 2.6 4.6 2.1 4.4 4.8 2.4 5.1 5.0 5.0 4.4 4.7 4.8
##  [181] 6.2 3.3 5.4 3.5 5.4 4.2 6.9 5.4 4.1 4.6 4.9 6.1 2.7 4.3 3.3 5.0 3.8 4.7
##  [199] 3.6 3.6 2.8 5.0 4.5 3.8 4.8 4.3 3.5 3.5 4.1 7.1 4.6 4.4 4.0 4.2 5.6 5.5
##  [217] 3.9 3.7 5.1 3.6 3.9 4.6 5.1 5.2 3.0 4.8 5.0 3.9 4.7 4.8 3.4 6.4 4.5 5.4
##  [235] 5.4 5.3 4.2 6.7 3.8 3.8 3.4 4.1 4.6 4.1 3.7 5.0 2.6 6.1 3.3 4.0 5.5 7.0
##  [253] 3.6 5.0 2.8 5.3 5.0 4.1 4.3 3.7 5.6 3.9 5.2 4.2 4.9 5.8 4.9 5.0 5.9 3.8
##  [271] 2.8 5.0 4.7 4.8 4.8 4.1 5.5 5.9 4.5 5.7 6.2 5.6 3.9 4.8 3.8 5.8 4.4 6.0
##  [289] 5.6 2.5 4.7 5.1 4.8 4.7 4.1 4.7 4.5 4.1 5.0 4.1 6.3 4.0 4.4 3.7 4.0 4.3
##  [307] 3.9 4.7 5.0 4.4 3.4 5.0 3.0 4.7 5.6 6.1 4.0 4.7 4.1 3.6 3.4 3.3 5.0 4.4
##  [325] 5.8 3.7 3.9 3.6 4.4 3.5 3.5 6.3 2.7 4.5 5.5 3.6 6.0 4.2 4.1 4.7 3.4 5.9
##  [343] 5.1 5.4 4.4 4.2 4.3 4.9 2.9 4.7 3.5 5.4 5.5 4.9 4.9 6.5 5.4 3.6 3.9 5.0
##  [361] 3.2 4.3 5.1 3.3 4.6 4.8 5.1 5.1 4.2 3.6 5.0 5.2 4.0 4.2 5.2 4.2 3.5 5.3
##  [379] 6.0 4.9 4.0 5.8 4.3 2.7 5.1 6.7 2.9 5.1 6.1 4.9 4.9 4.2 4.7 3.1 4.6 3.5
##  [397] 4.3 4.1 5.3 4.5 4.2 3.9 6.3 5.6 3.9 3.1 6.1 4.1 4.5 5.0 6.9 3.2 4.5 5.1
##  [415] 4.2 5.4 4.2 3.5 5.5 2.7 4.6 2.7 4.6 4.3 5.0 5.6 5.8 4.6 5.5 3.7 3.9 4.8
##  [433] 4.6 6.9 5.3 5.4 5.2 4.8 3.4 4.2 4.5 4.9 4.2 4.7 4.2 4.2 3.9 5.2 4.1 4.9
##  [451] 3.5 6.5 4.7 4.5 3.9 4.9 4.8 3.5 4.1 3.8 3.6 2.3 4.6 3.2 5.6 3.8 6.2 4.8
##  [469] 4.1 3.8 6.0 2.6 3.6 3.1 3.7 3.5 4.8 3.3 3.4 5.7 5.4 5.0 3.9 4.4 4.7 5.3
##  [487] 4.2 5.3 3.7 3.6 5.3 4.3 6.3 5.8 5.3 3.8 5.7 4.0 4.3 4.9 4.8 5.3 5.2 3.8
##  [505] 5.7 4.5 4.3 5.0 5.5 3.3 5.6 4.4 4.4 4.5 5.2 4.7 5.3 3.7 3.7 3.2 4.2 4.5
##  [523] 4.5 2.3 4.3 5.1 3.8 6.2 6.6 4.6 4.6 4.5 4.6 3.5 5.4 3.0 4.3 4.4 4.8 5.1
##  [541] 5.6 4.1 4.6 5.0 5.0 4.2 4.2 2.0 4.9 4.6 4.1 5.0 3.6 3.6 4.9 4.8 4.8 4.1
##  [559] 3.8 3.0 3.1 5.2 4.1 5.6 4.8 4.3 3.5 4.9 4.0 4.9 3.3 5.1 4.3 4.5 4.4 3.4
##  [577] 3.0 5.1 4.8 3.7 5.0 3.0 4.3 4.4 3.5 3.2 4.2 4.8 4.4 5.0 3.8 4.2 5.0 2.7
##  [595] 5.0 6.1 4.0 4.4 3.4 5.6 5.5 4.8 5.1 4.2 4.2 2.7 5.3 4.9 3.7 4.3 4.4 5.5
##  [613] 3.6 6.2 4.6 5.5 4.6 3.9 4.4 4.9 4.4 5.9 5.4 3.0 3.8 4.0 4.7 4.2 5.2 5.9
##  [631] 4.4 3.8 3.9 4.2 3.9 5.0 4.4 4.4 4.5 5.9 4.5 5.2 2.7 4.8 4.2 4.6 4.8 4.5
##  [649] 3.5 4.2 4.1 6.2 3.9 5.0 5.0 6.0 6.3 3.9 6.0 3.3 4.8 4.9 3.2 5.1 4.4 5.5
##  [667] 6.0 4.4 4.5 4.9 4.9 3.6 3.6 4.8 6.5 5.3 5.9 4.8 4.6 5.3 4.7 5.4 5.8 4.8
##  [685] 5.7 4.4 4.0 4.0 5.0 2.9 4.0 3.2 4.7 5.1 5.0 4.7 5.0 5.4 5.1 5.4 2.3 4.4
##  [703] 3.3 4.6 3.7 4.2 5.0 4.1 4.5 4.2 5.8 3.8 5.5 4.6 4.8 4.8 2.6 4.3 5.3 4.2
##  [721] 3.8 4.9 3.0 3.3 3.7 4.5 4.3 5.4 3.2 3.2 3.6 4.8 4.1 3.2 4.7 4.3 4.8 4.6
##  [739] 4.5 3.4 4.3 4.4 4.4 4.0 3.6 6.1 3.8 3.0 4.3 4.2 5.0 4.9 4.6 6.3 3.1 4.5
##  [757] 4.7 4.3 5.0 3.3 3.7 5.8 4.5 4.5 4.2 3.0 4.7 6.5 4.5 5.1 4.7 4.5 4.1 4.8
##  [775] 3.8 3.9 6.1 3.3 6.1 3.0 3.7 3.7 3.6 4.6 4.9 5.5 4.9 4.8 3.5 4.7 3.9 3.4
##  [793] 5.8 3.6 4.8 5.6 7.6 3.8 4.5 5.3 3.7 6.0 4.1 3.8 5.6 4.7 5.5 3.2 4.1 4.2
##  [811] 5.0 5.4 4.4 5.3 4.3 4.6 4.2 3.1 6.7 5.2 5.1 2.8 4.5 4.0 4.2 5.8 4.0 3.9
##  [829] 2.9 4.3 3.4 3.8 4.5 3.4 3.6 4.4 4.6 3.0 4.4 4.7 6.1 5.9 5.4 4.1 3.8 3.4
##  [847] 2.7 4.5 4.6 6.6 3.3 4.7 4.3 4.4 5.3 5.3 5.0 5.9 5.9 4.9 5.5 4.8 5.8 3.7
##  [865] 4.6 3.9 2.6 5.1 5.2 5.7 4.3 2.6 3.4 4.1 4.3 5.1 6.5 4.6 4.8 3.3 3.4 3.3
##  [883] 5.7 4.7 3.1 3.9 4.5 4.9 4.3 4.5 3.2 3.1 3.9 3.7 3.7 3.5 3.7 5.8 4.6 4.8
##  [901] 5.6 4.4 4.9 5.2 4.8 5.8 3.4 3.8 5.0 5.3 5.0 4.5 2.7 5.8 4.3 5.2 5.2 3.8
##  [919] 5.7 3.9 4.8 5.1 3.8 4.3 5.5 3.6 5.3 3.5 4.7 4.1 5.9 4.5 4.6 4.5 4.3 3.6
##  [937] 5.9 2.9 4.4 4.4 3.4 4.8 4.7 5.3 3.8 4.4 5.2 4.8 5.5 5.4 3.5 6.2 4.8 5.1
##  [955] 4.2 5.1 3.8 2.8 5.3 4.9 3.7 4.9 5.6 3.0 3.4 3.2 5.5 6.0 4.7 3.9 4.5 6.0
##  [973] 5.3 4.7 4.1 5.1 5.8 5.2 5.5 6.2 5.2 7.4 4.8 3.5 3.8 4.5 3.0 4.3 5.9 5.5
##  [991] 3.6 5.4 5.1 5.3 4.6 5.0 4.7 2.7 5.0 4.3
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.50 5.40 5.30 5.30 5.10 5.00 4.96 4.70 4.60 4.40
## 
## $jack.boot.se
## [1] 1.041324
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
## [1] 1.348963
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
##   5.416663   6.742604 
##  (2.351596) (3.067255)
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
## [1] 0.59886876 1.11191859 0.21614467 0.07959045 0.46336118 0.72966080
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
##    [1]  0.638717683  1.925160051  1.541921096  0.942439820 -0.222831758
##    [6]  1.133655060  1.604304348  1.450035430  0.595478747  0.580927022
##   [11]  0.474797651  2.062737624  0.534449786  1.278324085  0.346047413
##   [16]  0.920994606  0.394676225 -0.696463664  0.483578113 -0.064387685
##   [21]  0.153725048  1.047862301  1.155771831  1.199460421 -0.516470890
##   [26]  0.785106696  1.891387736  1.240165459  1.868767867  0.881726991
##   [31]  0.322815206  1.270574663  0.321126593  1.253046831  1.013152957
##   [36]  0.700801761 -0.069180524  1.009758855  0.780211952  1.335488093
##   [41]  1.616022541  0.885189397  1.594376131  0.211349346  0.716523504
##   [46] -0.418754156  2.121641817  1.163051053  1.605324553 -0.230596887
##   [51]  1.449047778  1.520193657  1.115920247  1.515067608  2.033773938
##   [56]  0.045735307  2.071864716  1.097888752  0.983383164  1.246920654
##   [61]  1.057233163  1.350947809  0.952181085  0.575854704  0.687823356
##   [66]  1.033053288  0.450302058  0.912825500  0.080419390  0.649707407
##   [71]  1.219990783  0.318448347  2.259758167  0.686344760  1.584953068
##   [76]  0.694716438  0.879570717  1.412732628  1.015211832  0.883161207
##   [81]  2.010343103  0.735955349  0.657618875  1.492377884  0.744443141
##   [86]  1.313779419  0.470621636  1.070161912 -0.229298435  1.403119329
##   [91]  0.237002268  0.542388199  0.311002530  1.173346348  0.575922250
##   [96]  0.956721857  1.759046332  1.968019422  1.094604237  0.240317673
##  [101]  0.521661818  0.367399283  0.959571193  1.211584734  1.297491652
##  [106] -0.939113041  1.245434627  1.930237695  0.360841336  0.834723749
##  [111]  1.082612487  0.619740208  1.032317921  1.452575598  0.658300030
##  [116]  0.290440581  0.944871210  0.532300337  0.847063026  0.456560914
##  [121]  0.006437957 -0.706231290  1.092524716 -0.016852513  1.385408264
##  [126]  0.659938584  1.112204571  0.261778973  1.676708497  0.764591472
##  [131]  0.764262433  0.829324061  1.790160361  1.260205307  1.404398431
##  [136]  1.322083877  1.646214902  1.922047878  1.075816740  1.137381842
##  [141]  0.391619141  0.167877897  0.773623578  1.000254901  1.138731751
##  [146]  0.738409751  0.963978978  2.077956696  1.444306870  0.343093633
##  [151]  0.239565210 -1.118475637  0.749869975  1.304576279  1.362780065
##  [156]  1.700113920  0.713936798  1.116111874  1.266545452  0.716656604
##  [161]  1.297491652  0.440726021  1.135706398  1.076675946  1.388544614
##  [166]  0.937400217  0.814981325  0.940076996  1.024170619  1.568046932
##  [171]  0.728793778  1.268202244 -0.043745575  0.309613449  0.620691239
##  [176]  0.606159769  1.326095591  1.664417948  0.778413363  0.351336182
##  [181]  0.387582640  0.164336264  2.196016914  0.657602455  0.884143412
##  [186]  0.801402170  1.525152849  1.786972390  1.073428593  0.505972777
##  [191]  0.400062116  1.391953283  1.102460954  0.930618868  1.764492418
##  [196]  1.453336909  0.787006914 -1.060766845  1.133273228  0.707029609
##  [201]  1.732049748 -0.275642986  0.586216036  0.003081579 -0.101654564
##  [206]  0.707648457  1.213397610  0.018669531  1.383404113  0.069369898
##  [211]  1.304576279  0.655565873  0.361033085  0.285771669 -0.566422579
##  [216]  0.942091820  1.400643956  0.863679502  1.070586785  0.744622247
##  [221]  2.149286521 -0.483590926  1.166339403  1.033765503  0.995573565
##  [226]  1.953792373  0.764195273 -0.014397906  1.179244947  1.143000312
##  [231]  0.688837841  1.058715676  1.105892741  0.230294860 -0.232939537
##  [236]  0.733219206  1.273558631  0.871641557  0.286270409  0.056020276
##  [241]  0.952673706  2.161018570  0.790315759  1.547764691 -0.014351485
##  [246]  0.897645489  1.688820639 -0.441583866  2.338832580  1.199979469
##  [251] -0.335568769  0.807599030  0.518441227  0.974805664  1.479789094
##  [256]  0.009362381  0.544064692  1.042409232  0.872535169  1.348963423
##  [261]  0.950143556  1.157258411  1.635588518  1.185818978  0.653804490
##  [266]  2.079333688  1.887413262  0.323415861  1.976995910  0.952468871
##  [271]  1.253861583 -0.267470662  1.294380007  0.795139243  2.241558911
##  [276]  0.605101434  1.163408633  1.300804804  0.597994032  1.003883877
##  [281]  1.135597613  1.553148938  0.536833376 -0.638807310  0.575061836
##  [286]  0.610659308  0.317060780  1.578171046  0.898781066  1.073909278
##  [291]  0.540143622  0.075710497  1.265991911  0.718199209  1.188770071
##  [296]  0.588201670  2.371944603  1.705481700  1.165282251  0.640090845
##  [301]  0.682850890  0.818556047  0.928843139  2.020453000  0.009325461
##  [306] -0.007979840  0.537636411  0.029897118  1.343849169  0.759849200
##  [311]  0.456864522  1.602029680  0.362679272  1.169776729  1.397516757
##  [316]  0.691784740  1.277708766  0.916700573  1.612950089  0.847063026
##  [321]  0.997325975  0.807445433  0.048090029  0.891669255  1.318416999
##  [326] -0.606592784 -0.434059073  1.571268250  0.999831119  0.324070630
##  [331]  0.173843238  1.537540209  0.809653218  0.953936034  0.364140820
##  [336]  0.262603397  0.622170712  1.108969081  1.130967201  1.273909106
##  [341]  0.541041212  1.031276043  1.022125474  1.791416845  2.151152329
##  [346]  1.417113821  1.171225450 -0.691133627  1.282553879 -0.472158262
##  [351]  0.716842204  2.191662960  1.036317189  1.543189201  1.474056856
##  [356]  1.774600471  1.138975779  0.875896427  2.543408866  0.273630892
##  [361]  1.600829145  0.983993604  0.059273353 -0.091668465  1.346250438
##  [366]  0.898131159  2.329671147  2.442555864  1.060152176  1.778004142
##  [371]  0.589937541  1.177784992  0.435982418  1.135597613  0.837630994
##  [376]  1.944249379 -0.363833749  0.784278648 -0.137241153  1.183107395
##  [381]  1.620929603  0.770994171 -0.103781310  1.549591179  1.416495212
##  [386]  1.188013744  1.501977049  0.586791326  2.343598572  0.842755636
##  [391]  1.100067046  1.563477474  1.358559637  2.277252072  1.260753155
##  [396]  0.852414802  0.455876670 -1.243715157  1.566034300  0.643438379
##  [401] -0.346894808  1.026192815  1.421936755  1.962861902  1.355183081
##  [406]  1.105892741  1.767298902  1.490766341  1.883906250 -0.678697011
##  [411]  0.744954326  0.719491695  1.018550776  0.807932330 -0.257646851
##  [416] -0.176760991  2.272438903  1.335709595  0.629626913  0.441832121
##  [421]  1.315228089  1.060247997 -0.128872332  2.010513406  1.400189697
##  [426]  0.535304723  0.332735040  0.657812302  1.140020853  0.782060792
##  [431] -0.058198491  1.189642454  0.061878133  0.296429370  2.234701784
##  [436]  1.666198914  0.937130715 -0.166911489  1.052275891  2.215353040
##  [441]  1.118029423  1.998767001  0.010308561  1.458173791  0.093806599
##  [446]  0.510896704  0.918157544  0.483733076  0.861420349  0.747687221
##  [451] -0.036463317  0.997674873  0.111457540  2.066222631  1.208981787
##  [456]  1.548665367  1.284948494  1.231940195  1.706852897  1.636030401
##  [461]  0.468245980  1.004416712  0.223105447  1.168726939  1.644984098
##  [466]  0.699545544  0.352279510  1.020968290  1.735256022  1.324184764
##  [471]  1.743107111  1.828688952  0.620421814  1.438167561  1.043465762
##  [476]  1.448698692  0.849456077 -0.674156636  1.793591776  0.801609312
##  [481]  1.199333515  0.942686760  1.268913419  0.105993845  1.212049351
##  [486]  1.150423594  1.564667756  0.951885996  1.081922530  0.924883052
##  [491]  0.364140820  0.867239166  0.090696480  0.998570296  1.231491495
##  [496]  1.142508366  1.283854185  1.403993520  0.989631049  1.330148345
##  [501]  0.701674967  1.218685416  0.492431410  0.804558463  1.279452186
##  [506]  1.356324226  1.672084280  0.968582408  1.609344953  1.344654624
##  [511]  0.935508065  1.084163841  2.139783957  0.691211624  0.227490523
##  [516] -0.151078569  1.363684768  2.218491631  0.528566365  1.379013536
##  [521]  2.116497819  1.282550533  0.402438638  1.833765007  1.208267936
##  [526]  0.698652054  0.396827985  1.047992723  0.549924031  1.126570423
##  [531]  0.624176017  1.388387908  0.639249034  0.761149333  2.153432318
##  [536]  0.831864869  0.527241648  1.240039525  1.698771228  0.746677929
##  [541]  1.314883262  0.970248344  1.322333056  0.826057459  0.907792918
##  [546] -0.315298947  0.547899479  0.367197541  1.628654701  1.558475379
##  [551]  0.466989136  0.889447983  1.352405479  0.833284283  0.551122257
##  [556]  0.983978995  1.353228821 -0.108212503  0.712998497  0.023327541
##  [561]  0.544962952  1.619567040  1.443193230  0.831530655  1.373651258
##  [566]  2.322286690  0.282220661  1.697655229  1.646506139  0.271659391
##  [571]  1.375168653  1.685894494  0.308616463  1.056543862  0.006724167
##  [576] -0.604765103  0.364140820  0.929134544  1.834061665  1.782643743
##  [581]  0.096100346  1.618926306  1.009470713  2.166001914 -0.056972252
##  [586] -0.317649215 -0.402889658  1.063210887  1.744990686  0.894971388
##  [591]  0.886248252  1.662983423  1.527322978  1.523945111  1.093454901
##  [596]  0.218775425  1.485400369  0.998118370  1.333137857  1.886828317
##  [601] -0.063414670  0.965495809  1.394077974  0.698652054  1.370474641
##  [606]  0.879469549  1.501532346  1.114679033  1.287345652  0.908135806
##  [611]  0.635491433  1.402503978  0.437749911  1.679554510  0.501509865
##  [616]  1.243032505  1.927920020  1.583420788  1.056629317  1.404230335
##  [621]  1.579479552  1.315206220  1.097121334  0.941612447  0.156287946
##  [626]  0.384544596  0.908196763  0.760039172  1.490466243 -0.230914015
##  [631]  0.534194798  0.105993845  0.942893126  0.244026961  1.642736603
##  [636]  0.911248665  0.473888721  0.971782917  1.137055275  1.154092577
##  [641]  0.068352996  1.173462750 -0.222817331  0.889462206 -0.053907605
##  [646] -0.185738317  0.721499647  0.669836444  2.114607310  0.271753019
##  [651]  1.707927686  0.871483321  1.804861627  0.422721719  0.734332833
##  [656]  1.013475955  1.613860667  1.280570406  1.620300124  0.685308913
##  [661]  0.618759509  1.391953283  0.834266697  1.925492772  1.620300124
##  [666]  1.176879182  0.762688556  2.222024260  1.269072581  1.424671890
##  [671]  1.179272077  1.168324904  0.442688793  0.911640323  2.053982274
##  [676]  1.293920810 -0.914831756  0.796267153  0.630827912  0.376028231
##  [681]  0.849884996  1.260093609  1.340369212  1.501977049  0.954313544
##  [686]  1.396408844  1.156912077  0.969480436  0.540351215  0.673451160
##  [691]  0.749942242 -0.298395339  1.657722013  0.970073872  0.763397962
##  [696]  2.287787203  0.802604045  1.021666569  0.528463639  1.531770718
##  [701]  1.100341238  0.288071356  1.699915529  1.089441440  0.734626749
##  [706]  1.397516757  1.661542060  0.921619890  1.375729956  0.793463050
##  [711]  0.347849482 -0.274855846  2.211611527  0.002471077  0.037170966
##  [716]  1.229886105  0.768513712  0.998000863  1.254660209  0.561724447
##  [721]  0.925983795  1.307177616  0.653334708  1.243091663  1.845685144
##  [726]  0.476170547  0.380774779  1.673104817  1.176690804  0.795726229
##  [731]  0.850617160  0.502647935  0.948689692  1.662968577  0.410361423
##  [736]  0.888933518  0.547124436  0.324673421  0.608478154  0.711427936
##  [741]  1.463003114  1.828454616  0.471267151  0.452002477  0.918601007
##  [746] -0.380289226  1.448138037  1.007632010  1.745869548  1.015842947
##  [751]  0.226183201 -0.849583650  0.751266530  1.740674451  1.107696028
##  [756]  0.770994171  1.916162652  0.800861604  0.688013563  1.265991911
##  [761]  0.804737553  0.579244915  0.583472633  2.050659621  1.304335352
##  [766]  0.446190184 -0.273542644  0.952504612  2.116497819  0.320580651
##  [771]  0.085693824  0.432928836  1.371776873  0.558050818  0.685833237
##  [776]  1.840781907  1.287896068  1.299978460  2.236050405  0.756536229
##  [781]  0.036172777  1.177068705  1.178987623  1.566839614  1.211857576
##  [786] -0.022728820  0.527359519  0.548952553  0.864655010  0.497020121
##  [791]  1.203570064  1.365759930  0.953936034  1.516250872  2.105128972
##  [796]  0.606777722  1.008549156  0.316424525  1.099193062  0.849454788
##  [801] -0.579090979  0.465230716  1.337953343  1.814396152  0.960840760
##  [806]  0.786639744  0.297376026  0.962713307  0.983127302  0.687143359
##  [811]  0.330174421  0.371611790  1.146014219  1.437263178  2.456510808
##  [816]  0.867176400 -1.057264772  1.281005973  1.075309543  2.108055962
##  [821]  0.746047856  0.952181085  0.736259648  0.247854039  0.821300114
##  [826]  2.343598572  1.645392286  0.608797130  0.189845307  0.533513641
##  [831]  0.398454981  0.329940818  0.812924084  1.107474264  0.511067285
##  [836]  1.305645594  1.569329082  1.318191665  0.596622891  0.340346829
##  [841]  1.973959365  1.328400528  1.286258054  0.647826754  1.480026870
##  [846]  0.964145752  0.990372853  1.093458985  0.596478464  0.590685452
##  [851] -0.530265814  0.777618416  1.803158137  1.169934900  1.467055469
##  [856]  1.214496477  0.808448834  0.813933826  1.065454258  1.067568825
##  [861]  0.344684013  0.165895112  1.685467477  1.666968029 -0.647727922
##  [866]  1.346083331  0.819162340  0.790772560  0.364093102  2.108386023
##  [871]  0.759649734  1.707077838  1.178510019  1.282770724 -0.266767251
##  [876]  1.005503004  0.495390918  0.967751577  0.153725048  1.588158323
##  [881]  0.978293051  1.374098224  1.010684022  1.159135713  0.304229485
##  [886]  0.033338147  1.574932722  1.370957121  0.548952553  0.887299928
##  [891]  0.330668060  1.413639344  1.132933252  2.354580306  1.283152678
##  [896]  2.096380106  0.819377555  1.258991414  0.703200696  0.160440729
##  [901]  1.464586237  1.758413119  1.293823009  1.624469575  1.514928213
##  [906]  0.712499240  1.453336909  0.203694092  1.072017292  0.716192210
##  [911]  1.616564996  1.363684768  0.871109581  0.839106918  1.243241808
##  [916]  1.160552722  0.883286801  1.776551912  1.327221729  0.691211624
##  [921]  0.397930836  0.963455481  0.812881611  1.670065683 -0.602828482
##  [926]  0.501554433  0.492431410 -0.320800799  1.160174374  1.998721787
##  [931]  1.214673188  1.549683774  1.232623955  0.036172777  0.760841015
##  [936]  0.477120154 -0.163051194  0.823212679  1.078989236  1.460417321
##  [941] -0.161125874  1.315733436  0.849625159  1.068734885  0.524009138
##  [946]  0.918807272  1.092396907  1.993123358  0.795059287  0.808500011
##  [951]  1.325340932  2.182189588  1.250952373  0.634603006  1.076479158
##  [956]  0.243031690  1.538328606 -0.440882475 -0.456330513  1.641631279
##  [961]  0.331980489  1.453612961  1.973882540  1.009470713  2.136359345
##  [966]  2.232048532  0.227642333  1.383707412  0.559753700  1.378355750
##  [971]  0.571032220  0.693344017 -0.544056547  0.849217393  2.296732431
##  [976]  1.073666760  1.702582113 -0.162423041 -0.194409983  0.279674992
##  [981]  0.522107934  0.210688551  0.527356166  0.658257002  0.756121777
##  [986]  0.598404847  0.228730313  1.879641203  0.617215034  1.199466926
##  [991]  1.348963423  0.206205955  1.637904204  0.705004832  1.007568134
##  [996]  0.023811972  0.965790500  1.645332945  2.126804393  0.753407453
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
##   0.80334860   0.38029503 
##  (0.12025985) (0.08503464)
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
## [1]  0.19260925  0.09477533  0.48781520  0.30013440 -0.90267802  0.18645288
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
## [1] -0.031
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.90287
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
## t1*      4.5 0.003703704   0.9208106
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 1 3 4 5 6 7 9 
## 1 1 1 1 2 1 2 1
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
## [1] 0.036
```

```r
se.boot
```

```
## [1] 0.9106005
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

