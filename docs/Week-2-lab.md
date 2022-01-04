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
## 1 3 4 5 7 9 
## 1 1 3 2 1 2
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
## [1] -0.0038
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
## [1] 2.77111
```

```r
UL.boot
```

```
## [1] 6.22129
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##  2.5% 97.5% 
##   2.8   6.2
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
##    [1] 4.5 3.9 4.0 4.3 5.1 3.9 5.2 3.2 4.3 3.0 4.0 5.7 3.5 6.1 5.9 5.1 5.8 5.7
##   [19] 4.2 3.1 4.0 4.7 4.8 5.2 5.1 5.5 3.4 5.0 3.6 3.4 4.5 5.1 6.5 4.5 4.2 4.1
##   [37] 5.1 4.1 4.0 3.9 4.6 4.4 4.2 4.9 4.6 3.2 5.4 4.0 3.3 4.8 3.5 3.6 5.3 3.9
##   [55] 4.3 4.9 4.4 5.3 4.8 2.9 3.7 4.7 4.1 4.7 3.2 3.7 3.2 5.3 4.4 4.4 4.6 5.8
##   [73] 6.7 4.2 5.7 4.4 3.3 5.1 3.4 4.1 3.3 5.0 5.3 3.5 3.6 2.6 4.8 6.1 4.1 2.6
##   [91] 5.1 6.9 5.1 4.0 4.9 5.0 4.7 4.4 6.1 4.3 4.9 4.7 5.5 2.4 5.9 6.2 3.5 6.0
##  [109] 5.1 3.5 5.1 3.7 2.8 4.5 3.7 4.7 4.2 5.3 4.5 2.8 4.9 5.5 3.4 4.0 2.9 6.2
##  [127] 4.8 5.5 6.1 4.5 6.0 3.4 4.0 4.9 4.5 6.6 3.5 3.9 5.5 5.0 5.7 6.5 3.5 4.4
##  [145] 3.8 5.6 4.9 4.7 3.7 4.5 4.8 3.0 5.6 5.0 4.6 2.6 5.6 3.7 4.5 4.2 6.0 4.7
##  [163] 5.4 5.7 4.6 4.3 4.0 4.0 5.0 3.9 4.2 5.4 4.7 4.8 4.4 4.3 4.2 4.0 4.8 6.1
##  [181] 4.3 4.5 5.0 3.6 3.8 4.6 2.3 4.9 4.1 5.0 4.2 5.2 4.0 4.8 4.1 5.0 4.4 5.6
##  [199] 3.7 5.4 3.9 4.6 4.8 4.3 3.9 4.1 4.0 4.8 5.7 4.4 4.4 5.4 3.3 5.3 3.8 5.7
##  [217] 4.6 3.4 4.6 4.9 4.6 5.7 5.0 4.4 4.2 3.9 4.7 4.5 4.3 3.8 3.3 5.0 3.7 3.9
##  [235] 4.3 5.9 3.7 3.7 4.4 5.1 4.4 4.9 3.3 5.6 6.9 4.9 3.7 6.0 4.2 4.4 5.8 3.2
##  [253] 6.3 5.4 4.2 3.1 3.7 4.5 4.9 4.6 3.8 4.5 5.1 3.7 2.9 3.5 5.1 5.3 4.2 5.1
##  [271] 5.1 6.5 5.1 3.6 4.4 4.8 5.0 4.2 3.8 4.6 4.6 3.8 5.2 4.7 2.3 4.4 3.6 5.7
##  [289] 5.5 4.3 4.4 4.9 3.7 7.0 4.2 6.5 2.8 3.7 4.0 4.9 5.4 4.7 3.7 4.3 4.6 4.0
##  [307] 4.6 4.7 4.3 5.9 3.2 4.5 4.5 4.6 4.3 3.5 4.0 3.9 5.1 5.0 5.1 3.2 4.6 4.6
##  [325] 3.9 3.9 4.0 5.2 4.4 2.4 4.6 4.8 3.2 4.3 3.2 5.8 4.6 4.9 4.3 4.8 3.6 5.4
##  [343] 4.2 4.7 2.9 2.3 3.1 4.6 4.5 5.3 3.9 3.7 5.3 4.6 3.6 3.9 5.6 4.0 4.2 4.2
##  [361] 4.5 4.0 4.6 4.9 3.6 3.8 6.3 3.8 5.6 4.0 4.7 4.4 3.3 4.1 4.1 3.5 3.0 5.4
##  [379] 4.9 3.6 5.9 4.8 3.9 6.3 4.5 2.8 5.2 5.6 5.5 4.9 3.5 6.3 4.1 4.8 3.8 4.9
##  [397] 4.0 4.9 4.5 4.4 2.5 3.6 4.3 4.8 5.1 3.6 5.1 4.6 5.5 5.5 5.7 5.3 4.6 4.2
##  [415] 4.5 3.7 5.1 4.7 5.0 4.9 4.4 5.5 3.4 4.2 4.3 4.6 5.7 3.7 5.1 2.5 4.8 5.3
##  [433] 3.8 3.2 4.2 6.7 4.6 5.1 5.3 4.8 5.7 4.1 4.6 4.2 3.6 4.1 5.0 4.1 5.9 4.0
##  [451] 6.1 4.1 4.3 5.5 5.9 2.8 5.2 4.3 4.4 4.3 4.9 4.5 4.6 4.1 5.2 4.6 4.3 4.7
##  [469] 4.2 3.7 4.6 4.5 3.3 5.5 5.7 3.9 3.6 5.5 4.8 4.2 5.1 5.9 3.2 5.5 5.3 4.7
##  [487] 4.8 5.3 5.6 4.4 4.5 4.4 3.2 3.8 4.7 5.5 3.3 5.4 4.4 4.7 6.4 5.0 4.1 4.7
##  [505] 3.8 4.2 4.7 4.3 5.0 3.5 5.8 3.5 4.4 4.6 5.9 4.6 4.7 4.5 3.7 4.4 5.0 6.1
##  [523] 3.8 3.9 5.4 3.5 5.7 4.7 4.7 3.2 3.9 3.6 3.1 4.5 3.9 5.2 3.1 5.9 4.2 4.1
##  [541] 4.9 5.1 4.4 3.3 3.8 3.3 2.7 4.5 5.2 5.6 5.7 4.8 3.5 3.9 5.3 5.5 3.7 2.8
##  [559] 3.4 5.1 5.0 4.7 3.6 5.1 5.0 3.6 5.1 4.1 5.3 4.0 3.1 6.1 4.1 3.5 4.9 6.2
##  [577] 6.0 3.7 4.7 4.6 4.2 5.5 5.0 4.4 2.3 2.4 4.1 4.4 4.3 2.7 4.4 4.3 4.5 4.8
##  [595] 4.1 4.7 5.1 4.1 5.5 5.1 4.7 4.3 3.7 4.9 4.0 5.4 5.1 5.2 3.6 5.1 6.4 4.5
##  [613] 4.0 6.3 4.8 3.6 3.5 3.1 4.3 3.9 4.5 3.0 5.2 4.1 2.4 3.3 3.1 5.1 6.2 4.5
##  [631] 5.6 4.0 2.2 6.1 4.6 5.2 6.3 5.3 3.9 5.8 5.2 5.0 5.4 4.5 3.0 4.8 4.6 5.2
##  [649] 5.5 5.4 4.3 5.3 4.9 4.3 2.3 4.2 5.0 4.6 5.5 4.8 3.5 4.3 5.4 4.3 3.4 5.2
##  [667] 3.9 3.9 4.5 5.1 5.1 6.1 6.0 5.9 3.9 4.3 4.9 3.5 3.5 4.9 4.3 4.7 5.2 5.3
##  [685] 4.2 3.9 3.6 3.4 5.1 4.4 3.5 3.0 5.1 5.3 3.7 3.3 5.4 4.1 4.7 3.1 4.0 5.5
##  [703] 4.8 3.2 4.7 4.9 5.4 6.4 5.3 4.6 5.0 5.6 4.5 5.5 5.8 5.5 5.0 5.4 4.4 4.5
##  [721] 2.9 6.1 2.3 6.0 3.0 5.7 3.4 3.2 5.4 3.8 4.8 3.7 4.5 5.0 5.1 5.3 3.2 3.8
##  [739] 4.8 5.0 5.8 3.8 4.5 3.8 4.3 5.4 5.0 5.3 4.4 3.1 3.8 4.9 5.0 5.2 7.0 4.1
##  [757] 3.9 4.6 6.0 5.6 4.6 2.8 4.3 4.7 5.1 4.7 4.5 5.6 5.9 3.7 5.6 5.4 5.5 4.0
##  [775] 5.3 4.1 4.2 4.9 5.9 4.6 5.2 3.9 4.2 5.7 3.7 4.5 3.8 3.7 3.0 4.4 5.2 3.7
##  [793] 3.7 3.7 4.6 4.2 2.9 4.6 5.9 5.8 4.2 4.0 4.5 4.9 3.1 5.9 3.4 5.0 5.3 4.1
##  [811] 2.4 4.6 5.3 2.7 6.4 4.7 4.8 6.0 5.5 4.1 4.8 4.4 5.0 4.2 3.0 3.3 4.7 4.8
##  [829] 4.8 5.2 5.3 5.5 4.4 5.0 3.2 6.0 3.9 4.1 4.9 5.4 2.8 5.3 3.7 5.8 4.8 4.1
##  [847] 3.6 5.3 4.2 4.7 4.0 4.2 5.1 7.1 4.0 5.9 3.8 3.4 3.1 5.3 3.9 6.4 4.1 3.6
##  [865] 3.8 3.7 3.9 2.7 3.8 5.1 4.6 4.5 4.8 4.2 4.3 5.9 5.6 3.9 4.1 4.0 4.3 5.4
##  [883] 4.4 3.5 5.3 5.2 5.5 5.2 5.0 3.4 3.7 5.4 4.7 3.3 4.0 5.0 5.3 4.2 4.0 4.1
##  [901] 3.8 5.4 4.4 5.0 6.4 3.1 5.6 5.4 5.3 3.9 6.0 4.8 5.2 4.1 6.6 6.3 4.0 4.5
##  [919] 4.3 4.2 4.5 4.9 4.4 4.8 3.9 4.2 5.1 5.3 4.2 3.8 2.9 3.9 4.2 3.9 3.9 3.3
##  [937] 5.8 4.2 6.0 4.0 5.5 4.3 3.3 4.5 5.5 6.2 4.5 2.7 4.6 4.1 5.7 4.0 5.9 5.2
##  [955] 2.5 4.5 3.1 3.6 3.7 5.3 5.5 3.5 4.7 3.9 2.9 4.6 5.5 4.9 3.9 6.1 5.1 4.6
##  [973] 3.6 5.1 4.4 3.9 4.3 4.4 4.5 4.2 5.7 4.3 5.8 5.7 4.6 5.2 4.2 4.8 2.4 4.7
##  [991] 4.7 3.0 6.5 4.4 4.5 4.0 5.0 5.3 3.4 3.7
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
##    [1] 5.8 5.1 3.4 4.1 4.7 4.5 3.5 5.1 3.6 4.7 5.8 3.6 3.3 3.4 5.7 3.7 3.7 5.8
##   [19] 5.8 5.8 4.8 4.2 2.5 5.8 4.8 2.7 4.0 5.1 4.3 5.6 4.7 3.9 4.3 4.3 4.9 3.2
##   [37] 4.4 5.6 3.3 4.7 4.8 3.4 3.9 5.5 3.5 3.9 4.7 4.4 4.1 5.6 4.2 5.0 4.5 4.7
##   [55] 3.3 5.2 5.2 5.1 6.2 4.0 4.1 4.0 3.8 4.3 6.5 3.8 5.5 4.1 3.8 4.2 5.4 3.5
##   [73] 4.6 5.2 3.9 5.7 5.1 4.1 4.6 4.6 4.6 5.6 4.0 5.1 5.4 6.3 3.6 4.4 4.9 6.4
##   [91] 4.8 3.5 3.7 6.4 5.2 3.8 4.1 4.8 6.1 6.0 4.5 4.2 6.5 3.7 3.9 3.1 3.6 3.7
##  [109] 5.7 4.6 5.3 3.4 4.9 5.0 4.3 4.4 4.9 2.9 4.6 4.4 5.1 4.0 5.3 4.5 5.5 5.8
##  [127] 5.3 5.7 4.5 5.0 2.7 3.8 3.9 5.4 3.3 3.2 4.5 4.0 5.4 3.5 5.4 3.5 3.9 2.1
##  [145] 4.9 4.9 4.6 3.4 4.0 3.3 3.7 4.1 6.1 3.5 4.8 4.3 3.5 4.6 4.0 4.2 3.7 4.2
##  [163] 4.0 6.1 5.3 3.9 4.9 3.5 5.7 4.7 4.9 4.7 2.5 4.5 4.8 4.6 3.3 4.4 4.9 4.8
##  [181] 5.0 4.7 3.2 3.3 5.1 5.0 4.0 3.5 3.7 4.0 7.0 4.7 4.1 4.3 5.2 4.2 4.7 4.8
##  [199] 4.8 4.1 4.1 4.2 6.2 3.7 4.3 2.9 4.7 6.5 5.4 4.9 5.0 3.8 4.9 4.5 3.6 5.6
##  [217] 2.8 5.2 3.7 6.4 4.6 4.3 6.0 4.3 3.9 5.3 5.4 4.1 5.3 4.4 5.1 4.9 4.5 4.3
##  [235] 3.8 4.5 4.2 3.9 5.6 4.9 4.7 3.7 4.7 5.5 3.9 5.6 4.0 4.3 5.1 5.5 6.7 4.0
##  [253] 3.8 3.8 4.3 5.1 2.0 3.7 4.7 5.3 5.5 5.7 4.8 4.5 5.2 3.2 3.5 5.5 5.3 3.3
##  [271] 4.3 3.6 4.1 3.9 3.9 2.9 3.3 3.1 4.1 4.8 4.1 5.3 4.7 4.3 4.4 3.9 3.6 3.8
##  [289] 4.2 6.0 4.0 3.1 4.9 3.5 5.2 6.0 4.4 4.5 3.7 4.7 3.7 4.9 5.1 3.3 5.3 4.8
##  [307] 4.6 6.0 5.2 5.1 5.2 2.7 5.6 2.5 3.0 4.6 3.3 4.3 4.3 5.2 3.5 5.4 4.7 5.1
##  [325] 3.3 5.2 4.2 5.7 4.7 4.0 3.8 4.1 3.3 4.4 3.1 3.5 5.0 3.5 2.5 5.7 4.8 3.6
##  [343] 4.9 4.6 4.4 4.1 4.9 3.6 3.4 6.7 5.3 4.8 3.8 3.7 6.0 4.5 2.8 5.9 4.1 4.8
##  [361] 6.8 5.2 3.5 5.0 4.5 3.9 4.8 4.9 3.1 3.8 4.8 4.4 5.3 5.0 4.1 6.0 4.1 3.9
##  [379] 5.1 4.0 4.3 4.5 2.9 5.2 5.6 5.0 3.9 6.0 3.9 5.1 4.6 3.4 6.1 3.2 4.3 3.8
##  [397] 4.8 3.6 6.6 6.4 6.0 4.1 5.8 6.0 3.4 3.8 3.1 2.8 5.6 3.8 5.5 3.4 3.7 4.2
##  [415] 3.4 4.3 4.9 4.0 3.2 3.9 7.0 2.8 4.8 3.6 3.2 3.1 4.0 3.6 4.4 4.9 5.7 7.2
##  [433] 6.2 4.7 4.6 4.0 5.8 3.1 6.2 3.3 5.7 4.2 4.4 4.5 3.5 3.8 4.9 4.4 4.1 4.5
##  [451] 3.8 4.2 5.0 5.6 5.7 3.7 4.6 5.3 4.8 3.8 5.2 5.9 5.0 4.7 4.1 5.3 4.1 5.5
##  [469] 5.5 4.6 4.6 3.8 5.2 4.2 3.8 4.7 4.5 3.2 5.1 4.8 3.6 3.8 5.7 5.2 3.5 2.1
##  [487] 3.5 5.2 5.2 3.4 3.4 4.6 4.3 4.4 4.0 4.7 4.0 3.3 4.0 5.4 4.3 4.0 3.1 4.9
##  [505] 5.8 3.2 4.6 2.7 5.5 3.8 3.7 4.3 4.7 4.3 4.0 4.4 3.6 4.3 4.3 4.7 5.0 6.0
##  [523] 5.0 5.6 3.3 3.9 4.9 3.9 4.7 4.5 4.4 5.5 3.6 4.1 3.5 6.1 5.1 3.7 4.2 4.7
##  [541] 3.6 5.5 3.8 3.4 5.3 4.8 2.8 3.7 4.1 4.2 5.1 5.7 3.4 5.9 4.1 3.1 6.0 4.3
##  [559] 4.7 5.5 4.9 3.9 3.7 5.9 3.7 5.4 4.9 4.4 3.4 4.4 3.8 4.9 4.6 6.2 4.6 4.0
##  [577] 4.6 4.9 4.2 4.5 4.1 4.8 5.4 3.4 6.0 3.1 6.2 3.1 3.4 6.4 5.7 4.8 3.3 4.2
##  [595] 5.0 5.4 4.5 4.5 3.6 6.0 5.8 6.5 6.2 3.7 4.8 4.5 2.5 3.1 5.9 5.4 2.6 4.3
##  [613] 4.5 5.2 5.6 4.7 3.5 3.5 3.6 4.1 5.3 3.0 3.7 5.2 3.7 3.8 6.9 5.2 6.4 5.3
##  [631] 4.8 5.4 4.8 3.8 4.0 4.0 4.8 3.0 5.6 4.7 4.6 3.7 4.2 4.0 5.3 2.5 6.4 4.5
##  [649] 4.0 4.7 5.1 4.4 4.1 4.7 4.2 5.1 4.5 5.0 4.8 4.5 2.5 4.2 3.4 3.5 5.0 4.3
##  [667] 4.2 5.1 4.5 6.4 4.6 3.3 2.9 4.7 3.6 4.4 4.3 4.8 3.6 5.3 3.4 3.9 4.2 5.6
##  [685] 3.3 4.4 4.0 4.2 5.7 3.7 2.9 5.4 2.8 4.7 5.1 4.4 6.3 5.7 4.8 4.5 6.4 4.1
##  [703] 3.8 5.3 3.5 3.1 5.6 3.2 5.0 4.1 3.9 4.8 3.8 4.6 3.8 3.8 3.7 4.3 5.0 4.0
##  [721] 4.3 4.3 4.7 4.8 4.0 4.1 4.2 5.4 4.7 5.9 4.2 3.7 5.4 5.2 5.2 4.4 5.3 4.8
##  [739] 5.0 5.2 4.9 3.7 2.8 4.2 3.1 4.5 3.5 4.9 4.6 5.6 5.8 3.9 5.5 4.3 5.0 5.0
##  [757] 5.3 5.4 4.0 4.4 5.4 6.0 3.1 4.7 3.6 5.1 5.0 5.4 4.5 4.5 3.8 3.5 3.8 3.6
##  [775] 4.3 3.4 5.5 4.3 4.7 3.0 5.0 3.6 5.2 2.9 4.2 4.5 4.6 5.2 5.0 4.8 4.6 4.2
##  [793] 4.8 2.5 5.1 4.3 5.1 6.3 4.0 4.1 4.5 2.7 5.2 5.4 4.3 4.8 4.3 6.0 3.9 4.5
##  [811] 3.6 4.4 4.3 4.7 4.2 5.0 4.1 2.9 3.7 5.7 6.1 3.8 5.1 3.9 4.1 4.5 3.5 4.4
##  [829] 4.0 5.7 5.3 5.1 5.3 4.6 4.8 4.1 4.1 6.0 3.8 4.6 4.5 3.6 4.0 4.1 5.3 5.0
##  [847] 4.8 4.9 4.4 4.4 5.0 3.0 5.2 4.9 4.4 5.4 4.4 5.4 5.1 5.5 6.4 4.5 5.4 5.3
##  [865] 4.1 3.1 5.5 3.6 4.9 4.4 4.8 4.3 5.4 6.7 4.9 3.6 3.8 5.3 3.5 5.5 4.1 4.2
##  [883] 3.4 4.3 4.9 3.7 4.6 4.0 5.9 4.4 4.8 4.0 3.8 4.1 4.6 6.9 4.8 3.8 5.2 4.1
##  [901] 3.4 2.8 5.9 4.5 5.5 5.2 4.0 5.0 5.6 3.2 5.1 4.4 2.6 5.6 5.4 5.9 4.5 4.9
##  [919] 4.1 4.2 3.1 5.1 3.9 4.4 3.6 4.2 6.1 4.4 4.0 6.0 4.1 4.7 4.9 4.3 2.4 4.8
##  [937] 5.6 6.0 2.0 6.1 3.5 4.8 3.5 4.4 4.6 6.7 5.7 7.8 5.6 5.2 4.8 4.4 6.2 4.5
##  [955] 4.3 4.4 3.8 4.8 5.5 5.1 5.0 5.3 6.7 7.3 4.7 5.0 5.7 4.5 4.1 3.6 4.3 4.6
##  [973] 4.9 4.8 4.0 3.7 4.5 4.0 3.0 2.4 4.5 3.3 5.4 4.6 6.0 3.6 4.4 5.9 4.7 3.1
##  [991] 4.0 4.8 4.3 4.1 4.7 4.7 4.7 3.6 5.8 3.8
## 
## $func.thetastar
## [1] 0.0079
## 
## $jack.boot.val
##  [1]  0.49583333  0.44924925  0.33795014  0.17900552  0.06291209 -0.01661808
##  [7] -0.14943182 -0.18699422 -0.34396783 -0.51238390
## 
## $jack.boot.se
## [1] 0.9611025
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
##    [1] 4.3 4.9 6.1 4.6 5.2 5.6 3.7 3.3 4.2 4.8 4.0 4.7 4.1 5.1 5.7 5.3 3.6 4.8
##   [19] 3.2 4.9 4.9 4.4 4.4 3.7 4.1 3.8 5.4 4.0 3.6 5.4 4.0 4.0 6.5 4.2 4.8 4.5
##   [37] 5.7 5.3 5.4 4.5 4.1 4.4 4.0 5.1 5.1 4.6 4.8 6.4 4.2 5.4 4.8 4.0 4.2 5.8
##   [55] 4.4 4.4 3.1 4.8 4.4 3.5 3.0 5.4 4.1 7.2 4.9 3.3 4.7 5.6 4.8 5.0 5.3 4.8
##   [73] 2.2 4.4 4.9 3.8 5.4 3.6 5.5 4.7 4.2 3.9 3.7 3.3 4.4 4.3 5.1 3.4 6.2 4.5
##   [91] 4.3 5.6 6.6 4.0 3.9 2.6 4.4 4.2 5.6 3.7 4.6 3.0 5.6 3.3 3.4 5.4 3.0 5.2
##  [109] 4.4 4.1 4.3 4.4 5.5 6.0 5.4 4.1 5.9 5.3 6.3 4.6 4.5 4.5 2.9 4.5 2.6 4.8
##  [127] 4.6 3.2 3.3 1.9 4.1 5.8 5.2 6.0 5.3 4.2 5.5 5.2 4.3 4.7 5.3 3.8 3.8 4.8
##  [145] 3.8 5.7 4.2 5.6 4.9 3.3 4.1 6.1 3.4 4.4 4.4 6.2 4.7 4.2 4.2 5.4 3.8 4.6
##  [163] 3.7 5.4 5.1 5.3 5.0 4.2 3.7 3.4 2.7 4.8 4.2 4.1 2.7 4.2 5.5 2.5 3.5 5.6
##  [181] 4.3 4.0 4.2 3.9 4.3 5.8 4.7 5.0 5.2 4.3 3.7 4.0 6.2 3.8 4.2 2.7 4.3 4.2
##  [199] 4.3 3.6 6.7 2.8 5.5 3.0 5.0 4.0 4.7 3.8 5.5 5.1 3.0 5.2 4.9 3.9 3.5 5.0
##  [217] 7.1 4.5 2.4 3.6 5.2 4.2 5.5 4.0 5.2 5.5 5.3 4.0 3.5 4.6 4.7 4.2 5.3 5.2
##  [235] 4.9 4.0 3.2 3.4 4.5 3.8 4.2 3.5 4.9 4.8 4.9 4.6 3.5 4.0 4.0 5.1 4.1 3.2
##  [253] 4.0 4.9 3.9 4.9 4.8 4.8 5.0 5.1 4.5 4.7 5.1 4.8 5.6 4.9 4.5 3.9 2.2 4.1
##  [271] 4.7 5.0 6.7 4.5 4.9 4.5 5.0 4.2 3.6 5.3 5.6 4.9 4.7 3.3 5.3 5.2 4.7 5.6
##  [289] 4.5 4.3 5.0 4.7 2.9 3.5 4.9 3.5 3.8 6.2 4.5 5.1 3.9 4.3 3.3 4.1 4.2 3.1
##  [307] 3.7 5.0 5.1 5.9 4.6 6.8 4.5 2.9 4.0 5.6 4.8 4.0 2.9 5.4 2.9 3.7 4.5 3.4
##  [325] 6.1 4.6 4.8 5.4 4.2 3.7 4.6 4.5 5.0 4.2 4.7 4.8 4.1 4.9 3.4 4.9 3.9 3.7
##  [343] 4.3 3.7 2.9 5.4 4.0 4.2 3.1 6.3 4.5 5.1 4.0 4.3 4.4 2.8 5.1 4.3 3.7 6.5
##  [361] 4.8 4.9 7.0 5.8 6.0 5.1 3.3 4.9 3.9 5.7 4.4 4.6 4.7 4.8 3.8 5.4 6.1 2.7
##  [379] 5.0 5.0 6.3 3.8 4.9 4.9 3.2 4.6 5.0 4.8 6.3 4.7 4.6 4.3 3.4 4.5 4.2 6.1
##  [397] 4.4 4.4 5.5 3.7 4.1 3.9 3.8 4.4 5.0 3.2 3.7 4.1 4.8 4.6 4.8 5.2 5.6 5.2
##  [415] 5.3 4.8 4.8 5.5 4.3 5.1 5.2 5.3 5.7 2.7 4.8 6.1 3.6 5.7 4.3 5.1 4.6 3.5
##  [433] 5.5 4.3 4.0 5.8 5.3 4.6 5.0 3.9 3.0 4.6 5.4 5.4 2.1 4.4 6.5 5.4 3.8 4.7
##  [451] 4.8 5.1 4.5 4.5 5.7 4.1 5.5 3.9 4.5 3.4 4.5 3.7 4.5 2.2 4.5 4.6 5.2 4.7
##  [469] 5.1 4.7 4.2 4.2 3.4 2.3 5.3 4.8 4.6 4.2 5.1 3.0 4.3 3.9 3.6 4.0 3.5 3.6
##  [487] 2.7 5.6 4.7 4.5 4.3 5.0 4.2 4.0 3.9 4.4 4.5 5.8 4.1 4.9 3.4 5.3 4.4 4.2
##  [505] 4.8 4.7 5.0 5.0 3.9 5.1 3.4 4.9 4.9 3.4 5.0 4.8 4.6 3.9 4.8 4.1 4.8 4.3
##  [523] 4.3 4.8 4.1 5.0 4.2 4.5 4.5 3.4 5.1 3.7 5.2 6.1 4.0 4.7 3.1 6.7 6.1 3.6
##  [541] 4.7 5.5 2.9 3.3 5.3 6.0 3.8 5.4 4.4 3.3 4.9 4.2 4.4 4.5 4.0 4.1 5.3 5.6
##  [559] 4.9 5.7 6.0 3.8 4.9 4.7 4.0 4.6 5.7 4.3 4.1 6.0 3.9 5.1 5.8 4.5 4.4 4.7
##  [577] 4.2 3.0 4.2 5.1 3.8 3.6 5.1 6.2 5.0 5.3 6.9 6.1 5.4 5.1 5.6 4.3 5.2 3.7
##  [595] 4.0 4.2 3.4 5.6 5.1 3.5 5.0 5.3 4.8 4.5 4.7 4.4 4.7 3.4 3.9 4.0 4.7 4.0
##  [613] 4.6 4.2 3.9 4.4 4.8 4.8 5.3 3.9 4.6 4.8 2.7 3.6 5.4 4.9 4.2 4.7 3.8 3.2
##  [631] 4.0 4.9 3.4 3.3 4.0 2.3 3.5 3.3 3.5 5.5 4.6 2.7 5.6 6.1 4.7 6.3 3.6 6.7
##  [649] 4.8 3.1 5.3 4.3 4.1 5.6 4.1 4.1 5.9 3.9 4.1 6.0 3.7 5.6 4.4 4.6 4.8 5.3
##  [667] 5.4 3.1 5.1 4.2 5.0 4.2 4.4 6.6 4.5 3.3 5.2 2.8 4.9 5.9 3.4 3.4 4.4 4.5
##  [685] 6.7 3.3 5.2 4.8 4.7 5.5 5.0 3.7 5.9 5.1 4.3 4.4 5.3 4.6 5.3 5.1 3.5 4.3
##  [703] 4.5 3.1 4.4 3.6 4.5 5.0 5.6 3.1 5.1 4.5 4.6 3.6 5.1 4.5 2.6 5.8 2.7 5.6
##  [721] 5.0 4.9 4.0 3.5 6.8 4.3 5.1 6.1 5.0 5.8 3.7 3.3 5.9 2.9 3.4 3.6 5.1 3.6
##  [739] 4.1 4.8 5.5 4.1 4.5 4.4 6.3 3.6 4.1 5.2 3.2 4.6 3.7 3.6 3.7 4.7 3.8 5.0
##  [757] 5.3 4.9 3.8 4.2 5.1 4.5 5.2 6.4 4.8 5.1 4.5 3.7 4.2 5.6 3.3 3.6 4.6 6.4
##  [775] 3.7 4.6 5.9 5.3 3.6 4.5 3.3 4.6 4.1 3.9 4.4 4.0 5.5 4.7 4.2 5.2 4.1 3.2
##  [793] 4.3 4.1 4.3 5.2 4.3 4.7 4.2 3.7 5.8 3.4 6.4 5.6 4.0 4.0 3.6 2.7 5.2 4.3
##  [811] 5.6 3.4 3.7 3.7 4.7 3.6 3.1 4.0 3.1 6.0 5.5 4.7 3.8 3.9 4.6 3.2 4.6 3.5
##  [829] 3.7 3.1 5.6 3.1 5.4 3.9 3.9 6.4 5.0 5.0 5.1 3.7 4.7 4.3 4.4 5.0 4.8 4.9
##  [847] 5.5 5.1 5.1 4.5 5.5 5.5 5.5 2.3 4.7 3.8 6.0 5.1 4.5 4.5 5.2 3.8 3.9 3.3
##  [865] 4.5 4.1 4.5 4.2 5.8 3.8 2.8 3.8 5.7 2.7 5.1 4.4 4.0 5.9 5.2 4.5 4.7 4.0
##  [883] 4.5 6.7 2.6 5.8 4.6 5.1 4.5 3.3 4.5 5.0 5.4 4.6 3.7 5.4 4.0 3.4 3.3 4.9
##  [901] 3.3 4.5 4.1 3.0 4.0 3.4 4.4 6.2 5.9 5.7 4.1 5.0 4.8 4.0 3.9 4.2 4.2 4.5
##  [919] 3.7 4.8 4.1 5.0 2.6 2.4 5.1 3.5 5.4 5.0 2.9 4.2 5.3 3.5 3.8 4.8 3.9 6.0
##  [937] 3.1 4.9 3.6 5.3 3.5 4.3 4.8 5.0 4.3 4.9 4.8 4.5 4.7 3.7 4.3 5.0 4.4 6.6
##  [955] 3.9 4.1 5.4 3.5 3.8 4.6 3.8 3.9 4.4 5.1 5.2 4.1 4.2 4.4 4.7 5.3 5.5 4.0
##  [973] 6.4 5.3 3.4 3.1 3.9 4.7 3.7 5.1 4.3 4.2 5.5 4.8 3.4 6.1 6.2 2.5 5.8 4.3
##  [991] 3.8 4.4 5.2 4.5 4.4 4.3 5.3 4.1 5.0 5.3
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.5 5.4 5.4 5.1 5.1 5.0 4.8 4.7 4.7 4.6
## 
## $jack.boot.se
## [1] 0.93
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
## [1] 0.303105
```

What is skew? Skew describes how assymetric a distribution is. A distribution with a positive skew is a distribution that is "slumped over" to the right, with a right tail that is longer than the left tail. Alternatively, a distribution with negative skew has a longer left tail. Here we are just using it for illustration, as a property of a distribution that you may want to estimate using your data.

Lets use 'fitdistr' to fit a gamma distribution to these data. This function is an extremely handy function that takes in your data, the name of the distribution you are fitting, and some starting values (for the estimation optimizer under the hood), and it will return the parameter values (and their standard errors). We will learn in a couple weeks how R is doing this, but for now we will just use it out of the box. (Because we generated the data, we happen to know that the data are gamma distributed. In general we wouldn't know that, and we will see in a second that our assumption about the shape of the data really does make a difference.)


```r
library(MASS)
fit<-fitdistr(original.data,dgamma,list(shape=1,rate=1))
```

```
## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced
```

```r
# fit<-fitdistr(original.data,"gamma")
# The second version would also work.
fit
```

```
##     shape       rate  
##   3.298386   5.675397 
##  (1.406877) (2.614689)
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
## [1]  0.05076717  0.57476865  1.10099392  0.69825309  1.34412610 -0.58639573
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
##    [1]  0.3766817487 -0.1069016135  0.2760732806  1.9842248607  0.4784813814
##    [6]  0.5654296229  0.4388496065  0.2067330675  0.7987940400  1.0911773452
##   [11]  0.6243950580  0.9276937053  0.2182038738  0.2224416966  0.5739619861
##   [16]  0.5277899999  0.7193670415  0.5804215255 -0.0005702775 -0.1519744652
##   [21]  0.5222859929  0.5067129506  0.1852592013  0.6834527215  0.3717495684
##   [26]  0.2173444582  0.0156041529  0.0292000684  0.5995285917  0.7191706266
##   [31]  1.5796341351 -0.1138523772  0.0888603192  0.8336217620 -0.2319608837
##   [36]  0.1480816117 -0.0459423358  0.0204539877  0.4413458550 -0.3479040315
##   [41]  0.6816091234  0.4555485148 -0.0115052856 -0.8374746547 -0.4023113938
##   [46] -0.7173214221  0.8287811476  0.8044355915 -0.0056197948  0.5783859707
##   [51]  0.3583293774  0.2675589512 -0.1904513748  0.5256817463  0.4266209673
##   [56]  0.2812210581  0.9206916225 -0.3805330673  0.3800949199  0.9467092835
##   [61] -0.4739395921  0.0205820830  0.9760193560  0.9142255353  0.3779959475
##   [66]  0.2416508660  0.9326226812  0.3883543213 -0.5038146453  1.4569073451
##   [71]  1.3768219275  0.8139632503 -0.3617832225  0.8122157291  0.8274237052
##   [76] -0.1351038225  1.3945621339  0.3049176713  0.8848843202  0.1940196423
##   [81]  0.1552114021  0.1742077512  0.5117068403  2.5187202301 -0.0833286074
##   [86]  0.6519456485  0.0168987242  1.4728791046  0.1913360229  0.5918587448
##   [91]  0.2860112950  0.2849052115 -0.1140943403  0.4242776995  1.3225489724
##   [96]  0.4434383283  0.1964700092  0.6238097735 -0.2801447646  0.4743360768
##  [101] -0.3646595614 -0.1667197099 -0.3409225416  1.7862297088 -0.0732413802
##  [106]  2.3737901300 -1.9644342237  0.3495345492  1.4674704392  1.4424147934
##  [111]  0.1154077732 -0.2178476981  0.8320921393  0.3090709537  0.8542400495
##  [116]  0.2726546666 -0.0577026068  0.1255529801  0.2365627324  0.0929319143
##  [121]  0.8726753612  0.4847336714  0.3877543077 -1.2271707026 -0.0451697622
##  [126] -0.1897398716  0.4040679248 -0.0024517977 -0.7300392673  0.0196326734
##  [131]  0.1620564761  0.4812300180  0.5843890767  0.3532852002  0.1872029530
##  [136]  0.5677127611  0.5922056434 -0.1122537237 -0.2564915751  0.6418841431
##  [141]  0.7656571352  0.0961571699  1.0461456055  2.2206948612  1.3539486734
##  [146]  0.1150585433  0.4715497723 -1.8269422961  0.3750272679  0.5095523240
##  [151]  1.3461868068  0.2544436454  0.0219879817 -0.4023113938  0.2606185630
##  [156]  0.4620716819  1.3556611464 -0.1458392958 -0.0423874914 -0.6868431353
##  [161]  0.1403366277  0.9965281132  0.1348706126  0.2640294784  0.2933626310
##  [166]  0.4039457748  0.2737717814  0.4460247764  0.4464799805  1.2811084475
##  [171]  0.8307634798  0.5831417221  0.0746885240  0.4703855410  0.4075801473
##  [176]  0.1009491867  0.1539731808  0.0785520325  0.0130354169  0.9106430958
##  [181]  1.9337786915  0.4017972965  0.4221969611  0.4210134078  0.1093269839
##  [186]  1.5177712775 -0.2434661404 -0.1555509830 -0.0975834696 -0.0279513552
##  [191] -0.3201185177  0.9990411170  0.5974512683  0.5010207801  0.7897966058
##  [196]  0.5505780986  0.8367285121  0.9865166965  0.2909439490  0.9674305187
##  [201]  0.3583293774  1.9901949436  0.5811355181  0.5444867093  0.2077477524
##  [206] -0.0947134282 -0.5610816147 -0.2182605453  0.3836027499 -0.0874436596
##  [211]  0.8428727222  0.7318007123  0.9158579924  0.1047248516 -0.0904496378
##  [216]  0.2716457683  0.2110328371  0.6225399586  0.8328233994  0.2522773014
##  [221]  1.4556870005  0.1468626769  0.7931112989  0.7226899348  1.3666574374
##  [226]  0.1699614059  0.4177196807  1.5493927035  0.4094077160  0.2701137270
##  [231]  0.7490011461  1.0459592769  0.2075825473 -0.2646417899  0.1567770227
##  [236]  1.3101441556  0.2075351370 -0.2674291084  0.1888952017  0.4672442282
##  [241]  0.8202886311  0.7935027845  0.8853051769  0.4931995641 -0.3834575856
##  [246]  0.4943818623 -0.0766518376  0.6976626351  0.3579179427  0.7779526188
##  [251] -0.5598141044 -0.4080696768  0.8615725610  1.4013417657  0.0119733894
##  [256] -0.0223484289  0.4633880839  0.5588837799 -0.2422887662  0.0146835283
##  [261] -0.3820165972  0.6466174628  0.2625925830 -0.1279481925  1.5114879179
##  [266] -0.6477590354  0.2431941036  0.4765376620  0.8118497746  2.4108589718
##  [271]  0.0179473353  2.0937413427 -0.1425894571  0.2289676841 -0.1142737054
##  [276]  0.4850952222  0.3656898589  1.2861266052  0.1054927397  1.1078199037
##  [281]  0.8839607965  0.4751918167  0.8047048887 -0.0205062142 -0.0764633790
##  [286]  0.4074490096  0.1730999778  0.0054259714  0.5026766063 -0.0537824892
##  [291]  0.3464969761  0.3747463996  0.8939648262  0.2553346496 -0.1629908867
##  [296] -0.0933642375  0.3301174967  0.1670428610  0.9638491336 -0.0482004063
##  [301] -0.3610370858  0.1967685633  0.8675016494 -0.4101963400 -0.2570074799
##  [306]  0.0035540233 -0.3747224227  1.4786824465  0.4527095271  0.2622246954
##  [311]  0.1292065640  0.0595347995 -0.3753222441 -0.0964386916  0.8310784070
##  [316]  0.3494734847 -0.6873375881 -0.3420532426  0.4249799891  0.4210134078
##  [321]  0.5068058135  0.8748387450  0.4439211705  0.2038392449  0.7408892665
##  [326] -0.0039468485  1.6397428022  0.4699864603  1.1966100896 -0.3418939064
##  [331]  0.4665944728  0.8930552779  0.9564822530  0.3829912178  0.5262331912
##  [336]  0.9559081022 -0.3359495955  1.9670942488 -0.5094091587  1.3139207104
##  [341]  0.1732715504  0.1378760834  0.2217780913  0.9713994452 -0.0563501520
##  [346] -0.2260177235  0.1033130088  0.4185896719 -0.3152589462  1.4564566766
##  [351]  0.0330739754  0.2241936441 -0.0175900166  0.6049582692 -0.2058103306
##  [356]  0.1585220736 -0.4301537061  0.7659439352  0.5741369277  0.1930440470
##  [361]  0.3684955175  1.0301808396  0.7546860330  0.1952268407  0.4375737857
##  [366]  1.5159017560  0.5853099252  0.2051194293  0.2085827874  0.5191400361
##  [371]  0.9390135698  0.7920810308  0.9492712653  0.6184458618  0.1729034348
##  [376] -1.9950581565  0.0399192180  0.2197464882  1.4234794684  0.5988240813
##  [381]  0.0036563955  0.2076932555  0.1763959517  0.4124344191  1.5406260578
##  [386]  0.8046912286  1.0203216714  0.3148390085 -0.2012608567  0.3497586169
##  [391]  1.3799549132  0.7427252073  0.2576147128  0.3833246805  0.1083325976
##  [396] -0.0040466093  1.0876159707  0.4252661380  0.4782670613  0.4274911335
##  [401]  0.5266753002  0.8573366666  0.2862988853  1.2815878828  0.0023903842
##  [406]  0.0818614567  0.6640146195  0.2989714008 -0.0123434300  0.5087251659
##  [411]  1.3217553166  0.5661407597  0.5394983076  1.3930632318  0.9004055841
##  [416]  0.1892936083  0.2140919009  0.6645196240 -0.2991096086  0.4682813466
##  [421]  0.0074935505  0.3645843125  0.5479409624 -0.1210871498  0.7994708790
##  [426]  0.4345578936  0.9087217843  0.1251399393 -0.0422995877 -0.1452980656
##  [431] -0.6952747910 -0.1053098812  1.3507648894  0.7946979782  0.5541046624
##  [436]  0.0424525239  0.0174382781  0.3618102299  0.2050851096  1.0850005142
##  [441]  0.5253286645  1.2114332697  0.0326079122 -0.2591124260  0.4452947032
##  [446]  0.9126270449  1.3797587050 -0.8617833915  0.2460805273  0.0957158514
##  [451]  0.8539565685  0.1717384253  0.2364533572 -0.1816952041  0.1239737853
##  [456] -0.3685245749  0.6597616040  2.4474372901  0.6074241668  0.8069409786
##  [461]  1.4567533291  0.2970716416  0.2441667524  0.2289485874  0.4553810006
##  [466] -0.0358067474  0.3272564761  0.0668454025  0.9022576554  2.2415524664
##  [471]  0.1336143160  0.6874656590  0.0780655875  0.4452737036  1.5963119971
##  [476]  2.0908913110 -0.1714335169  0.3284272931  0.2110328371  2.2913567709
##  [481] -0.0245808455  0.1736645648  0.5240665933  2.2080848812  0.5701552089
##  [486]  0.0258830214 -0.3002762831  0.7443601806 -0.2653996145 -0.1591057836
##  [491]  0.1902834609  0.1254690133  1.4412228226 -0.2712149073  0.7622537858
##  [496] -0.2469035285 -0.2561125883 -2.1744770535  1.3904780025  0.6635918852
##  [501]  0.6730305558  0.5065140364 -0.3748165034  0.2217005877  0.7825517137
##  [506]  0.5800945025  1.4162189844  0.3197165672  0.5743554128 -0.1841803178
##  [511]  2.0203377593  2.4247171666  0.1891974941  0.6450952802  0.0490865547
##  [516] -0.7951854126  0.8491385937  0.1701717751  0.7172737313  0.4658853340
##  [521]  0.4797110414 -0.0916136882  1.3762331816 -0.1372640669 -0.0563460960
##  [526]  0.7939891927  0.2869980055  0.8585521916 -0.1902742497  0.6214892945
##  [531] -0.4341245493  0.4702924143  0.5283893665 -1.3308089682 -0.6621411621
##  [536]  0.4001250867 -0.2577198108  2.4106179229  0.4988163785  0.2021639892
##  [541] -0.6185784326  0.8293189127 -0.2288429332 -0.1038794781  0.2852896439
##  [546]  1.4165457281 -0.2172222929  0.4258897204  0.8008358715  0.4514109632
##  [551]  0.2277383515  0.5672307363 -0.3866515934  0.5147077662  0.8735833393
##  [556] -0.2446333149  0.1562834200  0.7154115141  0.2543615269  0.3755315275
##  [561]  0.6193268183 -0.0106161181 -0.1277453654  0.1330650893  0.9645349300
##  [566]  0.0597478219 -0.2933249607  2.1670082102 -0.0770864114  1.1472993428
##  [571]  0.3606689194 -0.0677589991  0.7490011461  0.0909615167 -0.2127173998
##  [576]  0.2698935875  0.1119716735 -0.1729393254  2.4584052856  0.9231055505
##  [581]  0.1691692506  0.4243138640  0.1186974428  0.5368039647 -0.5181174341
##  [586]  0.9142152907  1.3620017764 -2.2587728388  0.2392517740  0.3155822442
##  [591]  0.4241363567  2.2984023022  0.4837668572 -0.1832024425  0.1656392450
##  [596]  0.3056303780  0.6424773491  1.1675377664  0.9125783275  0.0838676390
##  [601]  0.4800693920  0.8151537592  0.8569531631  0.0372521679  0.3625117343
##  [606]  0.3877800079  0.2811334991 -0.4027026544 -0.2588056928  0.4439211705
##  [611]  0.5597922599  0.4274911335  0.2383295561 -0.1662609171  0.9802772222
##  [616]  0.1444302708  0.6156857854  0.5355584422  0.4673353963  0.9118524737
##  [621] -0.5087165192 -0.0264363353  0.9596926159  0.1285844588  0.2928875109
##  [626]  0.5064292169  0.3708269786  1.3993476720 -0.0208501805  0.4894324146
##  [631]  0.1332462260  0.7381407790  0.1196294490 -0.0915219846  0.3908205289
##  [636]  0.9222560320  0.1432070397  0.0951661017  1.5514272738  0.3772454187
##  [641]  0.9274642977  0.8771800759  0.4184208192 -0.1761104531  0.3858405018
##  [646]  0.3691891674  1.0303173581  0.3273322970  0.4265133822  0.6603647313
##  [651]  0.8012752500 -0.1919520259  0.8398513230  0.5802214260  0.0365751722
##  [656]  0.3225098842 -0.6661868248  1.4100257030 -0.1717728411  2.1286664279
##  [661] -0.0988721648  2.2700744451  0.2234132287  0.8369666459  0.3282735350
##  [666]  0.0978060834  0.9642911857  0.7565884348  0.6751250582 -0.0001378733
##  [671] -0.0174816038  0.0785520325  0.4377278437  0.5959378546  0.3135249107
##  [676]  0.2977407950  0.5336515058 -0.1453880295  0.4721137055  0.5108979337
##  [681]  0.4419201927  0.0404179649  0.8089560188 -0.5820077553 -0.1000264162
##  [686] -0.1994590295  1.4919874244 -0.1904513748  0.3813889058  0.7681956693
##  [691]  0.1983354338 -0.0271962754  0.6711669482 -0.3073196272  0.8473597161
##  [696] -0.2618492391  0.1697169491  0.6067003977  0.3325009920  0.3772890615
##  [701] -1.9989406076  0.4220603103 -0.2919515212  0.6934149429  0.8321690015
##  [706]  0.0625338727  0.5853609732 -0.0162603300  0.7541498908  2.4636889541
##  [711]  0.1815035709  0.6978212280 -0.3968511842 -0.2784629355 -0.7670107589
##  [716]  0.2297859337  0.0283466998  0.6382459960 -1.2707618411  0.9907152558
##  [721]  0.1503023298  0.1794673290 -0.0968979971  0.6751250582  0.3335377376
##  [726]  0.1486937900  0.0292268367  0.5345335897  0.3848893462 -0.2796794139
##  [731]  0.1294651838  1.4752029439  0.3482853780  0.4145088699  0.6002280413
##  [736]  0.9548501873 -0.6530100906  0.2473619306  0.2092550483  0.4469066180
##  [741]  0.0636687561  0.9506551848  0.2925186252 -0.4763277095 -0.1246350952
##  [746]  1.4875225361  0.6058418443  0.4790841294  0.4386861483  0.7488370535
##  [751]  0.8319661744  0.0241304993  1.0239076137 -0.2869266338  0.4686780459
##  [756] -0.0111050686  2.0301714728 -0.1690376630 -0.2574543944  0.3656184473
##  [761]  0.0732705534  0.8235127829  1.3818706291  0.2169720758  0.4112249982
##  [766]  1.4255590861  0.4662612063 -0.3988680196  1.3378669234  1.3255177278
##  [771]  0.2555717400  0.1284806023  0.3738211868  0.3824322903  0.9630751618
##  [776]  0.4620716819  1.5600721586  0.2274410660  0.0666878611  0.2906834592
##  [781]  0.9084607098  0.3666126508  0.9869806808  0.5559980341 -0.3523222153
##  [786]  0.8230068748 -0.6228147390  0.0855086516  2.1289748428  0.1044978989
##  [791] -0.4008763957  0.8585355699  0.8233666958  0.6253134709  0.4659164382
##  [796]  0.2204004195  1.5037398340  0.4937634034 -0.0585443067  0.4989942807
##  [801]  0.4907829307  0.0252351178  1.0407760976  0.7621340730 -0.2765034085
##  [806]  0.5032919517  0.0497652293 -0.3116195156  0.0663240781 -0.4890797959
##  [811] -0.3123526106  0.0137034585  0.3078542705 -0.0931593319 -0.3393843296
##  [816] -0.0447980595  1.4163030761 -0.3594261637  0.1819245155 -0.0794685493
##  [821] -0.0740571765  0.5168511233  0.7248587605  0.3580315755  0.2555717400
##  [826]  0.0385903154  0.6520642423 -0.4146960520  0.4424637750  0.5279180614
##  [831]  0.6029035519 -0.1919892892 -0.1069016135 -0.3784255681  0.8897554059
##  [836]  0.6894269759  0.1166382501  0.5094396034 -0.1216753076  0.4429554746
##  [841] -0.3213177518 -1.3057453064 -0.0537782706 -0.2935116044 -0.1836570734
##  [846]  0.3079258314  0.3507972950  0.0876419755  0.3861195994  0.2451948925
##  [851] -1.9840715219  0.7219095538  0.4608314193  0.6570147132  0.0189130227
##  [856]  0.3704404293  0.8871118590  0.2294061179  0.0319394689  0.0637099682
##  [861]  0.0830787106  0.9965281132  0.4078011688  0.5065087105  2.4603176083
##  [866]  0.3536141023  0.5007595576  1.0357057393  0.3451924522  0.6789525662
##  [871] -0.0206520181 -0.2816433948  0.0080143083  0.0469812046  2.3676565755
##  [876] -0.4517325150  1.4013423562 -0.0005702775  1.4481630147  0.4530251259
##  [881]  0.5139109787  0.3892862351 -0.0086117375  0.9475020060 -0.1081051399
##  [886] -0.4355012119 -0.3453651258  1.4372595321  0.1170012876 -0.0833027611
##  [891]  0.6629481168  0.3512421080 -0.3929675213  0.2909439490  0.6293568931
##  [896] -0.0490444282  0.3090709537 -0.0412400871  0.0675599084  0.6502768339
##  [901] -0.1334471464 -0.8264533022 -0.0331214306 -0.1564008787  1.5272887875
##  [906]  0.4610175131  0.4843444108 -0.6679640601  2.3029078990 -0.0841135965
##  [911] -0.2680533386  0.2383394079  0.1999823943  0.8072620024 -0.1168211249
##  [916]  0.6000286407 -0.0480189532  0.0206273529  0.4273913729 -0.0612408731
##  [921]  0.4388344966 -0.2762146829  0.9326226812 -0.3700006056  0.2419022330
##  [926] -0.0249403039 -0.0539146599  0.0786557553  0.0921393216 -0.0916136882
##  [931]  0.9432759075  0.1380393619  0.0321111391  0.2186642570 -0.0418100190
##  [936] -0.1844269130  0.7621569400  0.8893752693  0.5803564184  0.9709897541
##  [941]  0.8656246224  0.5011366696  0.4149636292 -0.4844362538  2.3247292205
##  [946]  1.4618079514  0.7955094421  0.3909365191  0.1182381095  1.0654122885
##  [951] -0.8451568889  0.0935868052  0.1597191097  0.3245788123  0.6308865215
##  [956] -0.1901992221  0.7449260526  0.7955094421  0.8762690297  0.8057395144
##  [961]  1.5202006877  0.3332704756  0.3621185939  0.1496300080  2.1264958737
##  [966]  0.4546611447  0.1083325976  0.5333742625  0.7995531152  2.2191088186
##  [971]  0.2118494175  1.4338696039  1.5441624713  0.2386779267  0.6433496878
##  [976] -0.1849228252 -0.4239615751  0.8479935334  0.4433441566 -0.0723002102
##  [981]  0.0222705701  0.8554673326 -0.3948042924  1.1256110565  0.3874595741
##  [986]  0.2120508717  0.0911253207  0.5047716975  0.0206700860 -0.1564737850
##  [991]  1.2708750094 -0.2541014419  0.1171857421  0.6908767583 -0.0368253737
##  [996]  0.4503249152 -0.2161485990  0.2029462873  0.1740752117  1.5789446382
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
##   0.58117191   0.28015289 
##  (0.08859212) (0.06264089)
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
## [1]  0.07371685 -0.23570062 -0.08403179  0.32126493  0.97503829  1.41800858
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
## [1] 0.0176
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9107468
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
## t1*      4.5 -0.02392392   0.9018564
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 2 3 7 9 
## 2 1 2 2 3
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
## [1] -0.0069
```

```r
se.boot
```

```
## [1] 0.9249854
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

