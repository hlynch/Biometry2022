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
## 0 1 2 3 4 5 6 7 
## 1 1 2 1 2 1 1 1
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
## [1] 0.0466
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
## [1] 2.733707
```

```r
UL.boot
```

```
## [1] 6.359493
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##   2.5%  97.5% 
## 2.7975 6.4000
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
##    [1] 5.6 5.0 4.9 3.5 4.8 3.7 5.4 3.7 3.5 5.5 4.4 4.7 4.6 3.1 3.1 5.1 5.3 5.0
##   [19] 4.0 3.6 6.1 5.1 5.1 3.4 4.2 3.0 3.0 5.3 4.7 4.0 4.6 5.0 4.7 6.9 6.1 3.2
##   [37] 4.2 5.2 3.7 6.5 5.7 5.2 4.7 4.2 5.5 4.1 5.1 4.6 3.2 4.7 4.7 3.4 5.5 2.9
##   [55] 4.2 5.8 5.4 3.6 4.1 5.3 4.7 4.1 3.8 5.3 2.7 3.5 4.1 5.5 5.1 2.8 4.2 5.5
##   [73] 4.1 4.9 5.0 3.6 5.3 4.8 4.4 5.2 3.6 3.6 3.6 3.8 4.0 4.1 3.9 4.1 5.5 3.2
##   [91] 3.8 4.5 5.0 5.7 5.6 4.2 4.3 4.2 4.6 3.1 3.0 3.3 3.5 5.2 6.1 4.7 5.2 4.1
##  [109] 3.3 5.4 2.7 3.9 5.7 4.6 3.7 4.8 3.9 5.5 3.7 4.5 5.0 5.9 4.0 6.4 5.0 5.7
##  [127] 3.9 5.0 4.2 6.5 4.2 4.7 4.8 5.0 5.1 3.0 4.4 4.1 4.4 5.3 4.6 4.7 3.0 3.9
##  [145] 4.9 4.4 4.8 3.6 5.8 3.8 2.7 4.3 5.6 5.8 6.1 5.4 4.3 4.5 5.8 3.7 4.8 2.9
##  [163] 3.0 3.6 5.2 4.5 3.6 3.8 4.8 3.5 4.7 5.4 4.6 5.2 5.8 5.3 3.2 3.6 4.0 4.2
##  [181] 5.5 3.7 4.7 3.6 4.0 3.3 6.0 4.5 4.7 4.5 4.9 4.0 5.0 4.3 5.6 3.6 4.0 5.7
##  [199] 4.6 4.7 4.7 3.6 4.1 5.6 3.4 5.3 1.6 2.9 4.2 4.4 5.6 5.1 4.4 3.9 5.2 7.1
##  [217] 5.4 4.0 3.2 2.7 2.9 3.9 5.6 4.8 5.6 5.0 3.1 5.9 3.9 4.8 4.1 2.5 6.0 4.6
##  [235] 4.1 5.2 3.5 4.6 4.3 3.8 5.7 5.1 4.0 4.1 4.9 3.7 3.9 3.3 5.2 3.9 4.9 4.8
##  [253] 4.4 3.7 5.1 5.8 4.4 6.0 4.7 5.7 4.2 3.6 4.7 4.2 4.8 5.4 4.6 4.3 6.9 4.7
##  [271] 3.2 3.9 4.3 4.4 4.7 4.5 3.0 4.5 3.9 4.7 4.8 3.6 5.5 3.8 4.9 5.6 4.9 2.4
##  [289] 4.5 4.4 4.5 5.9 4.4 3.8 6.0 4.4 5.3 3.6 4.3 3.5 4.7 4.4 5.0 4.4 3.5 6.2
##  [307] 6.6 4.9 5.7 5.3 4.0 3.5 4.1 4.1 6.7 2.8 4.7 5.5 4.8 3.6 4.7 3.6 4.7 2.8
##  [325] 5.4 3.1 4.0 4.5 4.4 4.5 3.9 4.2 5.4 3.0 3.8 4.9 5.0 4.8 3.4 4.4 3.5 4.7
##  [343] 4.5 5.8 4.7 2.6 5.3 4.2 4.2 4.4 3.6 5.5 5.4 5.0 4.3 4.6 3.1 5.0 5.2 4.4
##  [361] 3.8 4.1 3.3 3.1 4.9 4.4 4.3 5.2 5.0 4.1 5.2 4.3 4.0 5.6 5.6 5.4 4.4 4.8
##  [379] 6.0 4.1 3.6 2.8 3.3 5.6 4.7 3.0 4.5 5.1 4.1 4.3 4.1 4.8 4.9 5.2 4.8 3.4
##  [397] 5.2 4.7 5.0 4.2 3.7 6.2 4.8 4.6 5.5 4.6 5.3 3.1 4.9 4.6 4.8 5.0 5.3 3.6
##  [415] 3.7 4.8 3.4 4.6 3.5 5.8 5.3 5.3 4.1 4.7 4.2 3.8 3.4 4.7 3.4 5.5 4.1 5.5
##  [433] 5.7 3.4 2.7 3.0 4.8 5.1 3.1 5.0 3.2 4.1 4.1 4.3 4.2 3.5 4.7 4.2 4.2 5.9
##  [451] 3.2 4.1 5.7 4.3 2.6 4.3 5.3 3.7 5.2 3.5 3.4 4.6 4.8 2.5 6.3 4.5 5.3 3.9
##  [469] 5.4 5.0 4.0 4.3 3.9 4.6 3.8 5.2 3.1 4.2 3.9 5.5 5.5 3.5 4.3 4.4 4.1 2.9
##  [487] 6.2 3.1 4.2 2.7 5.1 4.1 3.4 5.9 3.7 4.8 3.5 4.5 5.3 5.3 4.7 4.3 5.1 4.9
##  [505] 5.8 3.1 5.0 6.0 3.9 3.4 2.9 4.7 4.5 2.9 4.8 5.2 3.3 4.3 5.1 4.0 4.5 3.4
##  [523] 5.4 3.2 5.7 4.3 5.4 3.1 5.2 4.8 3.5 5.5 5.1 4.6 4.0 4.3 5.4 3.7 4.1 4.4
##  [541] 6.4 5.5 4.6 2.2 5.3 4.7 3.5 4.6 3.7 4.1 4.6 3.6 5.3 4.6 2.5 5.2 2.8 3.4
##  [559] 3.9 2.9 5.8 4.5 4.7 5.0 5.2 3.9 4.4 4.5 4.3 3.5 4.0 3.6 4.5 3.6 6.0 5.7
##  [577] 4.5 5.3 5.3 5.6 3.9 5.7 5.0 5.0 5.9 6.2 4.8 4.5 4.4 5.2 4.0 3.5 4.7 4.2
##  [595] 2.7 4.8 4.0 5.4 4.1 3.9 5.0 6.5 4.1 5.5 4.3 3.0 6.1 4.3 4.3 3.7 3.3 4.9
##  [613] 3.9 5.7 4.7 4.3 4.5 5.4 3.8 4.5 3.1 5.6 4.0 4.1 4.5 2.1 3.4 4.4 4.4 3.7
##  [631] 4.3 4.5 4.2 5.5 3.3 4.9 3.7 4.5 5.2 4.9 4.6 5.0 3.7 3.5 4.7 4.4 4.1 3.9
##  [649] 3.0 4.4 2.8 4.5 3.7 4.4 4.1 5.2 5.3 6.7 5.6 4.5 3.8 4.9 5.0 4.8 4.4 3.4
##  [667] 6.2 3.6 3.9 3.7 5.1 5.7 4.0 4.9 3.2 4.2 5.4 4.8 3.5 5.2 4.3 4.0 5.3 4.6
##  [685] 4.2 5.7 4.7 4.0 4.8 4.6 4.0 5.1 6.1 4.9 5.4 3.4 3.7 4.6 4.6 3.9 4.1 6.0
##  [703] 3.3 4.8 4.6 4.8 3.7 6.0 4.9 4.9 5.3 4.6 5.0 5.8 4.9 5.4 4.0 3.4 5.7 3.4
##  [721] 3.0 2.8 4.5 5.1 4.0 5.1 4.6 3.7 5.9 5.5 4.0 4.8 5.8 4.4 4.9 3.7 3.8 5.2
##  [739] 4.0 4.4 3.3 2.8 5.1 3.3 4.5 5.3 5.9 4.4 5.1 5.5 5.0 5.1 5.7 4.2 3.8 4.5
##  [757] 4.2 3.7 4.8 4.2 5.4 4.4 4.4 5.7 4.2 4.0 4.1 4.1 4.3 5.4 3.1 5.4 4.7 5.0
##  [775] 3.9 4.4 2.9 3.3 4.0 3.3 2.8 2.7 4.7 2.9 5.2 4.3 4.4 4.8 5.2 5.2 4.3 4.9
##  [793] 5.7 3.1 3.3 5.3 5.2 3.7 3.6 4.1 4.4 4.8 3.9 4.7 4.0 5.3 3.8 3.3 4.7 5.3
##  [811] 3.8 3.8 4.3 4.5 3.1 3.0 4.4 4.5 4.2 5.1 3.9 4.4 2.9 4.6 4.8 5.7 4.3 4.7
##  [829] 4.4 5.9 5.2 4.8 5.0 3.6 4.0 3.6 5.7 5.3 4.8 4.7 3.8 5.1 4.8 4.4 5.8 2.0
##  [847] 4.1 4.6 4.1 4.4 3.4 5.4 4.0 3.4 4.1 4.8 4.6 4.6 2.1 5.4 4.2 5.2 4.3 4.3
##  [865] 4.5 6.4 3.8 3.3 2.8 4.7 5.3 4.5 5.8 3.8 2.8 3.5 5.6 4.1 3.3 4.2 4.6 5.5
##  [883] 5.2 5.6 4.9 3.5 4.9 4.3 5.7 5.1 5.3 4.9 3.2 4.7 4.8 5.4 5.3 4.2 4.8 4.9
##  [901] 3.2 4.7 3.3 4.3 4.5 3.0 4.1 5.6 5.5 6.4 5.8 4.3 4.5 4.6 4.5 3.9 5.2 3.0
##  [919] 6.0 5.5 4.2 4.8 5.8 5.5 5.0 3.9 4.8 5.6 4.5 4.8 4.2 3.6 3.4 4.0 3.1 3.5
##  [937] 3.7 3.2 3.4 5.3 4.5 4.7 5.3 3.7 4.4 3.4 5.6 5.7 5.0 4.5 4.4 3.5 6.0 5.3
##  [955] 4.2 3.4 5.5 4.0 4.0 3.0 4.0 5.4 6.8 4.0 4.3 2.9 4.6 4.0 5.4 4.3 4.6 3.7
##  [973] 5.4 3.9 4.5 3.9 4.3 5.9 4.5 5.5 4.0 5.6 5.5 3.5 3.9 4.2 5.6 5.4 4.4 4.1
##  [991] 5.7 5.4 3.1 4.7 3.7 3.6 5.3 7.7 5.3 5.3
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
##   2.8   6.1
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
##    [1] 4.3 4.0 5.1 4.2 4.9 4.9 4.1 5.7 5.4 5.6 4.0 3.9 4.8 5.5 5.0 3.9 4.1 4.2
##   [19] 4.8 3.5 4.6 3.7 4.9 3.4 4.7 4.1 3.8 4.7 5.0 4.7 6.3 2.8 3.8 5.1 4.0 4.5
##   [37] 4.5 4.7 3.3 4.4 3.6 5.4 5.7 3.9 3.8 3.4 4.9 4.9 4.9 5.0 5.7 3.3 4.1 4.0
##   [55] 5.5 4.8 5.9 3.3 5.0 4.1 4.7 6.0 4.0 4.7 3.1 5.1 4.1 4.0 5.0 2.7 4.3 5.3
##   [73] 5.1 3.6 4.2 5.5 5.1 6.3 6.5 2.3 3.8 2.3 5.2 5.4 3.9 4.6 5.0 4.3 2.9 4.1
##   [91] 3.8 4.6 3.7 4.0 5.1 4.7 5.0 5.0 6.3 5.5 5.9 3.6 4.9 4.3 4.3 5.2 5.0 5.4
##  [109] 4.0 5.2 5.5 4.7 3.7 5.6 5.3 4.0 4.4 3.7 3.5 5.6 4.9 4.3 4.6 4.7 3.3 3.4
##  [127] 5.3 3.9 4.4 4.1 5.2 7.1 6.1 3.7 4.5 6.4 4.2 4.6 4.0 3.0 3.6 4.3 4.2 4.7
##  [145] 4.5 5.0 5.1 3.9 5.0 4.4 3.9 5.5 3.1 6.2 5.0 3.1 4.8 4.1 4.0 3.9 3.1 4.7
##  [163] 4.9 4.0 5.9 4.4 3.9 4.3 3.2 4.3 4.5 4.6 3.9 4.6 4.7 4.5 3.6 3.5 4.4 4.5
##  [181] 5.5 5.0 3.5 4.5 5.3 3.9 5.5 4.4 3.2 4.1 3.9 4.5 3.4 4.2 2.9 4.7 4.7 4.3
##  [199] 5.7 5.6 5.3 5.6 4.8 4.9 3.7 3.2 3.8 4.8 3.2 5.3 4.5 3.5 5.5 4.0 4.2 4.7
##  [217] 5.0 5.6 4.0 4.4 4.0 3.1 5.3 5.6 3.8 4.1 4.8 4.4 5.1 3.9 4.6 4.9 4.9 3.9
##  [235] 4.7 4.6 3.3 1.6 4.7 3.0 4.4 4.7 6.3 5.4 4.3 4.1 4.9 3.7 4.4 5.2 4.0 5.5
##  [253] 5.4 3.7 5.2 3.3 4.2 4.3 4.9 5.5 5.0 4.1 2.5 6.8 4.8 4.1 4.2 3.9 4.4 5.1
##  [271] 4.0 4.4 3.5 5.0 5.7 4.2 5.1 3.8 4.5 4.8 3.4 4.2 4.2 3.1 3.9 6.7 4.6 4.4
##  [289] 6.1 5.7 4.6 1.9 4.1 5.0 4.5 2.9 4.5 1.7 5.7 4.2 3.7 6.4 4.6 3.7 6.2 4.0
##  [307] 3.4 4.3 5.1 4.5 3.4 5.2 3.4 3.2 5.2 5.0 3.6 3.9 4.6 5.3 3.6 3.6 5.7 5.1
##  [325] 5.5 4.7 5.1 6.0 4.2 3.4 4.2 6.4 4.4 5.1 5.2 4.2 7.2 4.9 4.0 4.6 3.6 4.6
##  [343] 2.7 4.5 6.4 3.8 4.3 5.1 4.6 6.2 4.8 2.7 5.1 5.3 4.5 6.3 5.1 3.7 4.4 4.4
##  [361] 4.1 4.2 5.9 5.6 6.6 2.3 3.3 3.2 3.3 4.0 5.0 4.9 3.2 4.5 3.3 4.8 3.7 3.7
##  [379] 3.9 4.8 5.3 4.1 5.5 4.2 4.5 6.6 2.9 5.3 5.5 4.1 3.2 3.9 4.9 4.0 4.6 4.7
##  [397] 4.8 4.4 3.7 3.6 3.7 5.7 4.4 4.4 6.1 5.4 3.6 3.4 3.8 4.9 3.4 4.9 4.0 6.1
##  [415] 6.0 6.0 3.4 3.6 5.3 5.0 4.5 4.9 5.3 5.1 4.8 2.5 6.2 5.8 3.8 5.8 4.5 5.2
##  [433] 5.0 4.5 4.3 5.9 5.5 5.0 4.3 5.9 5.6 2.6 3.7 3.3 5.0 6.2 3.9 4.0 4.8 5.5
##  [451] 4.8 4.6 3.4 2.9 5.7 4.6 6.0 5.1 4.5 5.4 4.9 4.0 2.9 3.3 3.9 5.2 3.0 6.0
##  [469] 4.7 4.5 4.8 4.3 5.2 6.2 3.4 5.2 3.7 4.7 3.9 4.7 5.3 5.5 4.7 3.4 3.0 3.8
##  [487] 4.1 4.8 3.2 3.7 4.6 4.1 4.5 5.3 3.3 3.3 5.8 4.2 5.3 4.0 5.7 4.1 4.8 5.3
##  [505] 5.1 5.7 4.8 2.6 4.1 2.9 3.4 4.5 5.3 5.0 5.8 3.6 3.7 3.1 4.8 3.7 5.7 4.2
##  [523] 4.6 3.8 4.9 4.4 4.2 4.4 3.9 6.2 4.5 3.2 4.0 6.6 5.1 4.6 4.9 4.6 4.0 2.8
##  [541] 5.2 4.7 4.9 4.8 6.4 4.1 5.6 4.9 4.3 4.4 5.1 5.4 3.0 3.6 6.1 4.8 4.7 3.3
##  [559] 4.8 4.7 4.5 3.7 4.8 5.9 4.7 3.8 4.5 3.2 4.3 6.1 5.1 4.0 4.6 4.8 3.6 4.3
##  [577] 4.6 5.4 5.1 3.4 3.1 4.3 7.0 4.2 4.9 4.7 4.9 4.9 3.8 5.5 4.4 4.9 5.8 4.9
##  [595] 4.0 4.0 4.6 5.1 5.4 5.1 3.8 3.4 5.1 2.4 5.0 3.9 5.1 4.8 3.5 3.5 5.9 5.0
##  [613] 3.7 3.9 6.7 4.1 4.1 2.9 5.3 4.2 5.2 3.3 5.3 4.3 3.1 4.1 3.1 5.0 4.5 2.9
##  [631] 4.0 3.8 6.2 4.6 4.6 3.7 6.2 5.1 6.7 5.0 2.7 3.0 3.3 5.2 4.4 6.5 4.2 3.8
##  [649] 5.4 5.4 5.0 3.6 5.7 4.3 3.7 6.1 5.3 3.9 5.6 3.8 3.7 5.3 4.2 3.2 5.4 3.4
##  [667] 4.3 5.1 6.3 5.6 4.9 4.4 4.2 4.6 1.8 5.8 4.5 3.9 4.7 4.1 4.2 3.9 4.1 4.2
##  [685] 3.7 4.9 5.5 3.6 4.2 4.7 5.4 4.6 4.4 5.7 3.7 3.1 2.9 4.0 3.6 4.9 4.1 3.2
##  [703] 5.7 5.7 4.4 4.2 5.0 4.6 3.4 4.0 3.8 3.5 4.4 5.5 3.1 3.1 4.4 3.8 4.0 5.7
##  [721] 4.0 5.5 5.5 3.9 5.1 4.8 4.4 3.8 5.2 3.9 4.8 3.1 3.0 6.7 3.3 3.9 3.4 5.5
##  [739] 4.0 5.5 3.0 2.7 5.9 4.3 4.8 5.0 3.1 4.2 4.9 4.3 4.1 4.3 3.1 4.7 3.8 4.7
##  [757] 5.4 4.6 4.6 5.2 4.1 5.5 4.1 3.9 3.3 3.3 4.4 4.6 4.4 4.5 2.9 5.1 4.8 4.2
##  [775] 4.2 5.7 4.9 3.0 4.3 4.1 5.6 3.8 4.3 4.6 6.1 3.6 4.3 4.2 3.9 3.0 3.4 4.0
##  [793] 4.7 4.4 4.9 5.7 3.9 6.0 3.9 4.5 5.2 5.1 4.7 4.3 6.2 5.9 5.3 4.6 4.6 3.6
##  [811] 4.6 5.5 4.9 3.4 4.9 2.9 4.7 4.6 6.0 4.8 3.7 4.0 4.5 5.2 4.8 4.3 5.0 5.0
##  [829] 4.6 4.7 3.3 5.7 4.1 4.6 4.5 4.7 3.3 3.1 3.5 4.1 4.0 5.9 4.1 2.4 6.4 4.5
##  [847] 3.6 4.6 3.7 6.3 4.2 5.3 4.3 4.0 2.5 4.8 5.3 4.8 3.2 5.0 3.5 3.8 3.8 4.6
##  [865] 4.4 5.4 3.4 4.1 4.1 5.0 5.9 4.0 4.5 3.3 4.6 4.2 3.8 5.2 4.5 6.5 2.7 4.7
##  [883] 5.8 3.3 5.2 3.1 4.7 4.0 4.4 3.2 3.1 4.1 6.0 6.3 3.6 5.4 3.8 3.0 4.3 6.8
##  [901] 5.5 3.9 3.2 4.7 5.5 4.7 5.3 5.1 4.9 3.6 3.9 4.0 4.3 5.4 5.5 3.5 5.8 4.4
##  [919] 5.4 4.3 3.1 3.0 6.0 6.3 3.4 3.1 3.6 4.5 5.1 4.3 6.7 2.9 3.6 5.5 6.2 4.7
##  [937] 4.0 4.5 5.4 5.5 4.5 6.0 3.9 6.0 4.9 5.1 4.8 2.4 5.5 4.4 5.3 5.4 3.7 2.6
##  [955] 5.1 5.8 3.3 4.7 4.8 3.9 5.5 3.7 3.0 3.7 4.8 4.3 3.4 5.3 5.1 5.4 5.3 3.1
##  [973] 3.8 4.7 5.1 3.5 4.5 4.7 3.0 4.5 4.8 4.9 2.4 4.2 4.7 3.5 5.3 4.5 4.2 3.4
##  [991] 4.6 2.1 2.0 4.7 5.6 5.1 5.1 3.4 3.8 4.3
## 
## $func.thetastar
## [1] -0.0176
## 
## $jack.boot.val
##  [1]  0.53976608  0.39069069  0.24672619  0.16647230  0.06626506 -0.13027778
##  [7] -0.18926554 -0.24048913 -0.46403785 -0.53819629
## 
## $jack.boot.se
## [1] 1.017464
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
##    [1] 5.2 5.1 7.2 5.7 5.5 3.4 4.7 5.6 4.5 4.1 4.7 4.6 5.5 4.3 3.5 4.5 5.8 6.7
##   [19] 4.6 6.2 4.3 3.6 2.5 5.0 3.6 6.2 6.0 5.6 5.2 5.7 4.2 3.1 3.5 6.3 4.6 4.8
##   [37] 3.9 4.8 2.8 3.0 3.4 5.8 3.5 4.1 4.4 4.2 3.6 5.3 5.5 4.2 3.0 5.1 4.6 5.7
##   [55] 3.8 3.5 3.5 5.0 4.5 3.8 4.6 4.7 5.1 4.9 3.6 5.6 3.1 5.3 2.9 3.8 4.6 4.6
##   [73] 5.8 5.0 6.6 5.7 3.0 5.1 4.1 5.6 4.7 3.4 4.7 4.9 4.2 4.8 4.2 5.3 4.5 5.3
##   [91] 5.1 4.9 5.4 5.3 5.3 3.6 5.3 5.0 4.1 3.2 4.1 3.5 5.5 4.3 3.7 5.4 3.8 4.1
##  [109] 4.9 4.9 6.6 3.6 5.6 2.6 4.6 2.2 4.1 4.5 4.6 5.5 3.8 4.4 5.0 3.3 4.8 5.0
##  [127] 6.2 5.9 4.6 5.1 5.6 6.2 4.5 5.0 3.8 2.6 5.1 4.5 4.6 4.7 4.3 4.6 3.8 5.9
##  [145] 5.1 5.4 4.7 2.5 3.3 5.6 4.7 3.4 6.0 3.2 4.1 4.6 4.2 5.4 4.9 4.8 3.9 3.9
##  [163] 5.2 5.4 4.8 5.2 5.0 5.1 4.1 4.5 3.7 4.5 2.9 4.5 4.8 4.8 6.1 5.8 5.7 4.2
##  [181] 5.9 4.7 4.5 4.6 3.4 3.1 5.1 4.2 3.8 4.5 5.1 4.2 4.9 4.8 3.2 4.3 4.0 5.2
##  [199] 4.5 3.9 4.4 5.0 5.0 4.0 4.6 3.7 3.8 3.9 5.9 4.4 5.3 4.1 4.0 5.1 5.6 3.9
##  [217] 3.5 4.4 4.4 4.3 4.1 4.5 4.9 4.1 3.5 4.3 3.9 3.9 3.0 4.2 4.5 4.1 6.1 5.8
##  [235] 6.1 4.2 4.1 4.5 4.2 3.2 3.4 5.2 3.3 3.3 4.7 2.7 4.4 3.0 3.9 4.0 6.0 4.0
##  [253] 3.2 4.1 2.3 4.7 3.2 4.7 4.4 3.7 5.6 4.3 4.2 3.2 4.3 4.5 5.2 3.6 3.2 2.7
##  [271] 4.3 5.7 5.4 4.1 5.3 4.6 4.7 4.3 4.4 4.8 5.0 3.8 4.7 4.7 4.4 4.7 3.6 3.8
##  [289] 3.4 4.0 3.7 3.1 4.2 2.8 4.8 4.9 4.9 5.2 6.2 6.0 3.1 4.3 5.3 3.8 6.3 4.7
##  [307] 4.6 3.6 5.1 4.6 5.9 4.0 3.9 4.4 4.3 4.2 4.4 3.7 4.3 4.4 5.7 4.6 5.1 5.3
##  [325] 5.7 4.9 3.5 3.5 4.3 4.4 4.9 4.0 5.4 3.9 4.7 4.1 5.7 4.0 4.5 3.8 4.1 3.8
##  [343] 4.7 3.8 5.2 3.2 4.5 3.6 4.5 4.8 4.8 5.9 4.1 5.8 4.2 4.3 3.8 5.3 3.4 3.6
##  [361] 4.1 4.2 6.3 4.6 4.7 3.7 4.5 4.0 3.9 2.8 3.3 4.9 4.5 4.4 5.2 3.7 4.5 5.3
##  [379] 4.1 3.5 6.4 5.0 5.2 4.3 4.1 5.7 4.2 4.5 5.6 4.2 5.3 5.7 6.0 4.7 4.4 3.0
##  [397] 5.3 4.2 4.6 3.4 4.6 4.7 4.6 4.5 4.9 2.2 5.1 4.3 2.8 4.5 5.2 2.5 5.7 4.8
##  [415] 4.8 5.1 5.1 5.0 6.2 5.2 5.4 3.6 6.3 5.2 4.5 5.7 4.0 6.7 3.0 4.0 5.2 4.2
##  [433] 4.4 5.5 4.1 3.1 4.6 5.3 3.0 3.5 5.4 4.2 3.9 3.5 3.6 4.9 3.6 3.8 5.5 4.6
##  [451] 4.3 4.2 4.6 5.3 5.1 4.3 2.8 5.0 3.2 4.4 2.6 3.7 5.2 2.9 3.0 3.5 3.7 3.8
##  [469] 4.9 4.9 4.3 2.7 4.9 5.9 5.1 4.3 2.6 3.9 4.4 5.6 6.2 4.9 4.7 4.2 3.2 4.2
##  [487] 4.9 4.5 5.1 4.8 3.9 5.5 3.6 5.3 5.3 3.7 4.8 4.5 5.0 3.6 3.8 4.7 4.3 5.2
##  [505] 6.1 5.4 4.2 3.2 4.9 2.1 4.2 5.3 5.4 4.1 4.3 5.6 4.4 5.3 2.9 5.2 3.1 4.1
##  [523] 4.9 4.4 4.7 3.3 3.9 4.8 4.1 6.4 3.7 4.7 4.2 4.9 6.8 5.8 4.6 4.9 5.1 4.6
##  [541] 4.7 2.8 2.9 2.9 3.4 3.5 5.3 4.8 5.2 4.6 4.4 5.2 3.9 3.5 4.6 3.1 4.4 4.8
##  [559] 5.1 4.5 4.2 4.4 3.0 5.2 5.8 4.0 5.9 3.6 3.0 5.0 4.9 5.0 5.5 4.9 3.2 4.8
##  [577] 3.6 4.5 4.8 5.4 4.3 4.7 5.9 4.9 4.0 4.3 4.7 3.6 6.1 5.2 4.1 3.7 4.7 4.6
##  [595] 4.4 5.1 4.7 4.3 5.5 3.7 3.8 3.6 4.9 5.0 4.7 3.9 5.4 4.5 4.2 4.0 4.2 3.1
##  [613] 5.8 4.2 3.3 4.6 5.0 5.6 4.6 4.0 6.3 3.5 3.4 4.5 4.0 4.5 5.3 6.6 5.3 4.0
##  [631] 5.7 4.5 5.1 1.8 6.0 3.6 3.6 5.2 5.4 5.8 5.2 2.7 4.9 6.2 5.3 6.1 3.5 4.4
##  [649] 5.0 5.8 4.2 3.6 3.2 5.9 5.6 4.7 3.9 4.0 4.0 5.0 3.7 5.1 2.7 3.2 5.1 2.9
##  [667] 3.4 4.7 3.9 5.3 5.3 4.7 4.6 3.6 2.1 4.3 5.6 4.5 3.7 4.4 3.5 4.0 3.0 3.5
##  [685] 6.1 4.3 4.3 4.9 5.3 4.7 5.9 4.9 5.0 2.3 4.4 4.5 4.4 4.8 5.1 4.4 5.2 4.8
##  [703] 4.9 5.0 4.6 3.0 4.0 5.0 2.3 5.0 5.5 4.5 5.1 4.9 3.6 3.3 5.0 4.2 4.1 3.4
##  [721] 3.1 5.0 4.4 5.0 4.4 2.7 4.1 3.3 5.1 4.4 4.9 6.7 3.9 5.5 4.3 4.0 5.6 5.1
##  [739] 4.7 3.8 4.7 4.3 6.2 5.3 3.9 2.9 5.0 2.8 3.9 5.3 3.9 4.0 3.2 5.3 4.0 6.0
##  [757] 4.6 4.9 3.8 4.5 6.2 4.8 5.4 5.5 4.5 5.6 5.6 4.0 4.1 3.4 5.7 4.4 4.6 3.7
##  [775] 4.4 3.9 4.3 5.6 3.4 4.3 4.3 4.6 5.5 5.0 4.6 4.8 4.9 3.9 3.4 4.9 5.4 3.8
##  [793] 5.0 5.6 5.6 3.9 4.4 4.5 5.5 5.2 4.5 4.6 3.7 4.4 3.0 3.9 4.5 5.4 5.5 5.0
##  [811] 3.3 3.1 3.8 4.0 4.6 4.9 4.5 4.4 6.9 3.8 2.5 4.5 4.2 3.8 6.4 4.0 4.3 2.9
##  [829] 3.5 4.6 4.8 3.2 4.6 4.7 3.0 4.4 5.0 5.0 5.0 4.1 4.2 4.0 4.4 5.6 6.3 5.3
##  [847] 3.2 4.2 3.9 5.0 5.1 4.0 3.5 3.8 5.5 5.0 5.9 5.1 2.6 4.5 3.5 3.8 3.3 4.7
##  [865] 4.7 3.7 3.7 3.5 4.1 4.8 5.8 6.3 3.5 4.3 4.3 3.5 7.0 6.2 5.1 4.6 6.0 4.8
##  [883] 5.0 5.4 3.6 6.6 3.2 5.3 4.3 4.7 4.5 4.9 5.2 4.7 5.0 4.6 2.9 5.3 3.5 4.2
##  [901] 5.0 3.8 4.0 5.0 5.7 3.3 4.2 4.7 6.6 5.2 3.8 5.8 4.3 3.6 3.6 4.2 5.9 3.7
##  [919] 4.7 5.9 2.7 3.9 3.9 3.9 4.5 5.6 5.5 4.6 4.8 5.1 5.3 5.5 4.9 5.2 4.1 5.9
##  [937] 5.3 4.9 5.3 4.7 3.7 3.6 3.8 4.9 3.1 2.8 5.5 3.9 4.8 6.4 6.3 4.9 4.4 5.1
##  [955] 4.7 3.6 5.1 3.4 3.5 5.1 5.6 3.6 4.4 4.6 5.2 4.7 5.6 3.9 3.2 5.3 4.9 3.1
##  [973] 4.6 2.3 4.4 4.3 4.6 5.1 4.0 3.9 4.2 3.9 3.9 3.8 6.4 4.3 3.6 5.1 4.1 3.9
##  [991] 4.5 5.5 5.1 7.8 4.2 3.8 5.4 4.1 5.2 6.8
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.50 5.30 5.24 5.20 5.10 5.00 4.80 4.70 4.60 4.50
## 
## $jack.boot.se
## [1] 0.9426113
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
## [1] -0.5273072
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
##   2.832077   6.427804 
##  (1.199457) (2.978256)
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
## [1]  0.9739167  0.6697346 -0.1554426  1.5974021  0.7662570  0.7678546
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
##    [1] -0.6167251524 -0.2543104326 -1.0962917315 -0.3860113095 -0.3552974122
##    [6] -0.2528669556 -0.6559128963 -0.9455710992  0.5946937782 -0.1687620624
##   [11]  0.4038948230  0.0194995260 -0.9282982192 -0.0427345201 -0.1907727359
##   [16] -0.6764908764  0.5220484170 -0.7510877527 -0.6673924225 -0.8800578832
##   [21] -1.6270822794 -0.0120631677 -1.1529326887 -0.0115517987  0.0046996752
##   [26]  0.1095575980 -0.7355825936 -1.1642216524 -0.5017389474 -1.0464329479
##   [31] -0.1965778331 -0.2461896961  0.0783554099 -1.0318203110 -0.5564109238
##   [36]  0.1777597579 -0.5768656166 -0.5590906659 -0.6271462229 -1.5013128229
##   [41]  0.0093706225  0.0180618534 -0.8398586137 -0.6891614268 -0.3738464845
##   [46] -0.7070457342 -1.0423168761 -0.1568630000 -1.1004313529 -0.0064706707
##   [51] -0.7961132438 -1.0139683501 -0.9107128004 -0.6008885202  0.2720337774
##   [56]  0.6989375821 -0.5427673451 -0.6138224675 -0.0804843339 -0.3967709542
##   [61] -0.1973843815  0.4632309633 -1.5771857724 -1.2403275131  0.3484498312
##   [66] -0.8111516977 -0.4241068868 -0.1029869461 -0.2202682491 -0.4702019200
##   [71] -1.4512214225 -0.8255233766 -0.7177582291 -0.1734533263 -0.4418856025
##   [76]  0.4378392363 -1.0110976023 -1.1760253323 -0.0835409808  0.0884824534
##   [81] -1.1482533539 -0.3385770487 -0.4316451639 -0.1167209733  0.0606568655
##   [86]  0.1013889282 -0.6927494873 -0.3866606285 -0.4243771042  0.1416749690
##   [91]  0.1787992662 -1.1335867463 -0.5281969649 -0.5319474525 -0.8079739205
##   [96] -0.7954591422 -0.2582374660 -0.4086034301 -0.1571474158 -1.0500740623
##  [101] -0.0982558810  0.3206716420 -0.3108721823 -0.9280538964 -0.9930328209
##  [106] -0.2183446636 -0.7757328300 -0.2624523798 -0.5133775011 -0.4954600349
##  [111] -0.5455506495 -0.1383746180 -0.9421275436 -0.9495859149 -0.7731902467
##  [116] -0.0976306582 -0.1575054408 -0.7846020566 -0.3851729634 -0.6478410228
##  [121] -0.8739295095  0.0083344672 -0.7666843930 -0.2142899427  0.4630081717
##  [126]  0.3408019135 -0.8528228849  0.5935631744 -0.2484332262 -1.3754491333
##  [131]  0.3114316979 -0.6499987458 -0.5814058697 -0.6443117591 -0.3005207122
##  [136] -1.9112607542  0.1574154042 -0.1245019589 -0.1082384810 -0.2786494896
##  [141] -0.5332596612 -1.3069197357 -1.1619145692 -0.7476642786 -0.8327172885
##  [146] -0.4466453593 -0.6859782414  0.2404608058 -0.3206071199 -1.0301135575
##  [151] -0.6617707033 -0.0555267719 -1.1359909731  0.6801891914 -0.7584494975
##  [156] -1.3855878747 -0.2201171140 -0.6927258040 -1.8565416873  0.0865091051
##  [161] -1.0151213832 -0.3993213624 -0.6795950280 -0.6511272058 -1.1660824084
##  [166]  0.5536634668  0.2131286036 -0.8383646076 -0.1639950511  0.0911099921
##  [171] -0.3263212730  0.2625091888 -0.3618002007 -0.3938013424 -0.4307708067
##  [176] -1.0310182082 -0.2192425105 -0.2706223327  0.0243758235 -0.7901833459
##  [181] -0.1334144991 -0.2467748646 -0.5322439427 -0.2798153324 -0.6327145135
##  [186] -0.5255819026 -1.3363142021 -0.3378783834 -0.5375041545 -0.3582092775
##  [191] -0.5322439427  0.0583351149 -0.3627141787  0.3509899491 -0.5761525102
##  [196]  0.1711392485 -0.4554027500 -0.5143876911 -0.7988458364 -0.1440860919
##  [201] -0.6077527760 -0.7555360629 -0.9241209621 -1.4741253857 -1.1296592920
##  [206] -1.6884152670 -0.2572000070 -0.9042152477 -0.5744293790 -0.7301142653
##  [211] -0.1253816318 -0.2122634459 -0.8468556449 -0.6984807798 -1.4088368843
##  [216] -0.2813402025 -0.5460589970  0.1570356136 -1.0744681329 -0.9609054013
##  [221] -1.5586531263 -1.7592948865 -0.5327610843 -0.4011048045 -0.6167236475
##  [226] -0.6682309730 -0.9490237861 -1.1899994228  0.0008808092 -0.0433052468
##  [231] -0.4038319766 -1.8667723840 -0.3614021884 -0.9686669977 -0.0170458316
##  [236] -0.6698146174 -0.2092438087 -0.2455843031 -0.1713024328 -0.2153640033
##  [241] -0.1108768880  0.0024843067 -0.7183630835 -1.0410697291 -0.3371871949
##  [246]  0.0799417419 -0.2949723080 -0.5965257498  0.1318008654 -0.4087621399
##  [251]  0.3491972084 -0.7430689763 -0.6375264991 -0.0850008266 -1.5157341443
##  [256] -0.7575051468 -1.0651015160 -1.0118932586 -0.3766598440 -0.7060968100
##  [261] -0.5410040902 -0.7104104656  0.4652431730 -0.4334446364 -0.6102553967
##  [266] -0.3702807822  0.2112064359 -0.4470824487 -0.6769131202 -0.4321847979
##  [271] -0.5111514877 -0.0026519784 -0.6560581200 -0.3639390154 -0.4695770985
##  [276] -0.6506084557 -0.2766783489 -0.4466816615 -0.1669943370 -1.5258120022
##  [281] -0.1371503918 -1.0485361814  0.0356359963  0.1380055047 -0.1739946765
##  [286] -0.7288128508 -0.9691727122 -0.5279546583 -0.1920462611 -0.0195196103
##  [291] -0.3501122127  0.1181204351 -0.2729665148 -1.6751720417 -1.0469009282
##  [296] -0.4867413399 -0.8161926099 -0.5620134932 -0.4397402417 -0.6558038512
##  [301] -0.7359419698  0.7251326136 -0.5656778978 -1.4039523508 -0.4548639653
##  [306]  0.2898420212 -0.2630056815 -0.2463308284  0.0123838673 -1.2572395534
##  [311] -0.3369720079 -0.5431044380 -0.6086538152 -0.5981072368 -1.0685516405
##  [316] -0.7079270874 -0.6905616011 -0.7294424186 -1.1754695797 -0.8155570333
##  [321] -1.3151031891 -0.9618689590 -0.3505746289  0.8407159480 -0.2476646813
##  [326] -1.1562754874 -0.1823224693 -0.6437082751 -0.3795114513 -0.8448282134
##  [331] -1.0035694060  0.5192257312 -1.3013537649 -0.6576020264  0.5074800247
##  [336] -0.1716754562 -0.1513372283 -0.3669323541 -0.8310121444 -0.2685127912
##  [341] -1.6323793692 -0.9913049053 -0.2009859504 -0.8985630066 -0.2541726466
##  [346] -1.5175950206  0.1601767360 -1.5358345069 -0.7819199268 -0.8970995573
##  [351] -0.9934731637 -0.2961020519 -0.8038927867 -0.3197543380 -0.5849758668
##  [356] -0.7946172197 -0.7740911537 -0.9809542572  0.1797545749 -0.6453667300
##  [361] -0.7351007495 -0.2096625638 -0.2107019225 -0.7682698343 -1.0185993222
##  [366]  0.2244968268 -1.0341474543 -0.9706853566 -1.4809920924 -1.2988421814
##  [371] -0.9472799374 -1.6390422295 -0.8301489182 -0.3230626459 -0.9573147707
##  [376] -0.1629778070 -0.2482716011 -0.0331225517 -0.5845227067 -0.4712295674
##  [381] -0.8263426692 -1.0322947135 -1.3740087712 -0.1647862393 -0.5224323971
##  [386] -1.5340350720  0.6130321515 -0.3702807822 -0.9356377434 -0.2824393540
##  [391] -1.3393815530 -1.4301706730 -0.2099718815 -1.0208105543 -0.7980288812
##  [396] -0.1069886691 -0.5509466116  0.0206316788 -0.4591967562  0.1107324872
##  [401] -0.8662519457  0.1847987061 -0.2908943598 -0.7189151926 -0.2300664321
##  [406] -0.5528742796 -0.5460342956 -0.7649834468  0.0345285476 -0.1205998105
##  [411] -1.7026355248  0.1315418190  0.1054806366 -1.0466620804 -0.1799804956
##  [416] -0.1025380826 -0.6009804091 -0.7790223967 -0.4776136576  0.4549990670
##  [421]  0.7545338337  0.1274363160 -0.1454614287 -0.6096228136 -0.9762934297
##  [426] -0.1699981237 -0.2427778930 -1.0228582189 -0.7854074431  0.3157362936
##  [431] -0.9106942645 -0.9328348288 -0.1323020828  0.1420364856  0.8501728978
##  [436] -0.9882684125  0.1800936491 -0.8365221747 -0.1722895900 -1.0960254709
##  [441]  0.5314857634 -0.2510137682 -1.2868192350  0.2859352853 -0.7338787849
##  [446]  0.1686365024  0.2180557543 -1.3739911962 -0.1220637478 -1.2232836651
##  [451] -0.8611218448  0.4380212996 -0.4602489478 -0.4435386376 -0.3384920581
##  [456]  0.3012851091 -0.7840687099 -0.0711296758 -0.9644836582 -0.9996309152
##  [461] -0.5434466066 -0.6380494685 -0.6556293946  0.2741524133 -1.3588745231
##  [466] -0.8210316307 -1.1531653069 -1.1410300350  0.0240181569 -0.7312964406
##  [471] -0.9995503110 -0.6571669650 -1.4135027638 -1.0524382673  0.1832386027
##  [476]  0.0099201451 -0.7332586487 -0.6905834061 -0.3915497162 -0.8013713223
##  [481] -0.5340814055 -0.4453368192 -0.6463606930 -0.3673345222 -0.2551232052
##  [486] -0.5071425826 -0.7353178359 -0.4471322650 -0.8538622594 -0.3727474305
##  [491] -0.5479365543 -0.3643760036 -0.2080703308 -1.5194539562 -0.5341104011
##  [496]  0.6580825125 -1.4233088448 -0.2754220494 -0.4754213655 -0.4453935545
##  [501]  0.1696487794 -0.8925521824 -0.0199073404 -0.6845069460  0.1435252110
##  [506] -0.9600912083 -1.2049847961 -0.6610327956  0.0853498616 -0.2310584180
##  [511] -0.5482319234 -0.7532440455 -1.6276184519 -1.6624761242 -0.5303324386
##  [516] -1.1177982596 -1.2513610755 -0.5654118249 -0.5928795487 -0.2506276880
##  [521] -1.1189040341  0.0134744631 -0.1718875714  0.0798232093 -1.1455537519
##  [526] -0.9150379731 -0.2055388186 -0.7368949293 -0.6245307790 -0.1252555142
##  [531] -0.6175153367 -0.3560021976 -0.0269363319 -0.1483654849 -0.7956547363
##  [536] -0.1167209733  0.0582996636 -0.8687137053 -1.0783299422  0.0359080474
##  [541] -1.7878922491 -0.5202291031 -0.7513859196 -0.2429287462 -0.4032906805
##  [546] -0.3139510833 -1.3320196528 -0.2579447100  0.1964312570 -0.6366326434
##  [551] -0.0477026011 -0.6258241397 -1.2218383759 -0.8631520600 -0.5751810999
##  [556] -1.6509148550 -0.7416442492 -0.6650368371 -0.0066884227 -0.3546307348
##  [561] -0.4067633344 -0.1587590261  0.0498110084 -0.0255968010  0.1047748345
##  [566] -0.9056697051 -1.2516883482 -0.4692108382 -0.0691861650 -1.2719223527
##  [571] -1.3468979996 -1.0485361814 -0.2232822958 -0.1403889822 -0.6243810394
##  [576] -0.1221641101 -0.6577037078 -0.9888927644 -0.5302015300 -1.1362677761
##  [581]  0.0187268400 -0.5485672525 -0.8407170419 -0.8427217480 -0.5520086598
##  [586] -0.9272584608 -0.5589351362 -0.3293752922 -0.2572291855 -0.4882519396
##  [591] -0.0742679346 -0.4608432013 -0.6353991706 -0.6397510500 -1.6360275322
##  [596] -0.6259027785 -1.4053207485 -0.6677729880 -0.6536714907 -0.2634522552
##  [601] -0.6507956778 -0.6889991627 -0.4201133291 -0.4662514099 -0.5965257498
##  [606] -0.2278861663 -0.4409655074 -0.6575133395  0.0965994534 -1.0231437870
##  [611] -1.0645753488 -0.4518409915 -0.7320812420 -0.2375906745 -0.2423036644
##  [616] -0.4685554665 -0.1748638746 -1.1226954274 -0.2705070613 -0.5616988144
##  [621] -1.6456606285 -0.4813153342  0.3573595722 -0.4277921250  0.0316948241
##  [626] -0.8467303763 -0.7021668315 -0.1788414435 -0.2062014278  0.1380321121
##  [631] -1.1219213265 -0.7961945300 -1.5066292539  0.5875144763 -0.9081443666
##  [636] -0.2845913796 -0.5805650692 -0.5430927942  0.3189752847 -1.0996653193
##  [641] -0.9216841039  0.1396632773 -0.9754129026 -0.2229583803 -0.6354456300
##  [646] -1.0369384111 -0.3207795337  0.1452839591 -0.1550063722 -0.4523755786
##  [651] -0.6137095314 -0.8159936624  0.0246311212 -0.3401223254  0.0922303498
##  [656] -0.5696954729  0.3192307851 -0.2498506009 -0.4365903873 -0.3421713634
##  [661] -1.2239052607 -0.1253139772  0.2305296299 -0.6538478026 -1.5844571320
##  [666]  0.0029017855 -0.7433033477 -0.4060657266 -0.1932587953 -0.7764873854
##  [671] -0.1658000113 -0.6360843774 -0.2549899765 -0.7102926434 -1.2512654967
##  [676] -0.0747262145 -1.4130083867 -0.6362175279 -1.2169897278 -1.1442855194
##  [681] -1.4373178112 -1.2076742144 -0.9049300554  0.6119623706 -1.3450908477
##  [686] -0.6099268457 -1.6851259756  0.3543183677 -0.2862807222 -0.2967802866
##  [691] -0.2375111599 -0.8011739118 -0.8741781414 -0.0956752615  0.6052001586
##  [696] -1.6776076232 -0.5626251057  0.1773116917 -0.9694039952 -0.6570681727
##  [701] -0.7262840850 -1.0696921197  0.0512259248 -1.1620791854 -0.4135068996
##  [706] -1.1494627572 -0.1623130185 -0.6780701385 -1.4074576529 -1.1926033908
##  [711] -0.2497117367 -0.7903960687 -0.9471495352 -1.1248789177 -0.7958536566
##  [716] -0.2277254408 -0.5197715950 -0.3294396106 -1.6468372328 -0.5687057205
##  [721] -1.5358345069  0.0734654724 -1.3491682656 -1.6635723537 -1.1390546683
##  [726] -1.0886311953  0.0350744005 -0.6463606930 -0.0165624370 -0.3601148233
##  [731] -0.2124253145 -1.3831712422 -0.2516573920 -0.5083776044 -0.7901833459
##  [736] -0.2461896961 -0.0837478256 -0.3457403247 -0.4939323906 -0.4996837605
##  [741] -0.0767792187 -0.1407977786 -0.1423933201 -0.0371984084 -0.7931293644
##  [746] -1.9012934934 -0.7753909124 -0.0648530870  0.6326318849 -0.5535269001
##  [751] -0.1828888995 -0.8237045341 -1.3174282401 -0.2792839238  0.1628572660
##  [756]  0.5565241152 -0.1664716144  0.7083377674 -1.3483809802 -0.7373221308
##  [761] -0.8338364597 -0.7402974785 -0.6064086548 -1.0751962809 -1.4692665810
##  [766]  0.6254495152 -1.0966486225  1.0566366539 -0.2429854327 -0.0495104840
##  [771] -1.1613226039 -0.1106101563 -0.8762434240 -0.5341736337 -0.3867461031
##  [776] -0.2736709314 -0.7401810901 -0.5370582254 -0.1328049495 -0.7157539947
##  [781]  0.3156921234 -0.3122300620 -0.3294713185 -0.4465439510  0.1302977343
##  [786] -0.2421006041 -0.1755299675 -0.7314873432 -1.3456735842 -1.0049700839
##  [791] -0.6141621649 -1.4592628674 -0.6404384710 -0.3283953436 -0.0182834830
##  [796] -0.2656722927 -0.7176665786 -2.0951037163 -0.4749582402 -1.1314978394
##  [801] -0.7406718075 -0.5308862132 -0.2960536353 -0.5162203199 -0.3738129711
##  [806] -0.1354049254 -0.4354710233 -0.4715195579 -1.1105401898  0.0265816842
##  [811] -0.3933117786 -1.0370884707 -0.8127130228 -0.1371503918 -0.2338571189
##  [816] -0.3819131449 -0.3856626442  0.1724379359 -0.7366029552 -0.8510552831
##  [821]  0.0010905154 -0.2322297851 -1.0942672398 -1.0565332340 -0.5397138410
##  [826] -0.3470599872 -1.1631209988 -0.4388449269 -0.2634156016 -0.9706853566
##  [831] -0.3347435101  0.1243892892 -0.7838468698  0.0800319509 -0.5302840965
##  [836] -0.1628918720 -0.3241750589 -0.7243512069 -0.6909108305 -0.3423147417
##  [841] -0.5542389946 -1.0004209205  0.4082788193 -1.1506369363 -1.1494627572
##  [846] -0.9614495149 -1.3141678503 -0.1430761673  0.1514427426 -0.3178743559
##  [851] -0.3243616024 -0.5066486447 -0.6797714805 -0.6423516331 -0.1949938019
##  [856]  0.0047159780 -0.8100272933 -0.5931226866 -0.7640936149 -0.5057443565
##  [861] -0.1717629668 -1.5785906398  0.1909356086 -1.0138384634  0.8544768734
##  [866] -0.9070764358 -0.6788691864 -0.4106832549 -0.9079562118 -1.7183825477
##  [871] -0.9745754546 -0.6457010024  0.2889750833 -1.8323647892 -0.6828934548
##  [876]  0.3407264996  0.3335095786 -1.3310670679 -0.5016025586 -0.8833454762
##  [881] -0.6905495826 -0.5187232915 -0.2814874623 -0.6824481611 -0.7986899785
##  [886] -1.0696921197 -1.5127368034 -0.0185302063 -0.3006049308 -0.9168384231
##  [891] -0.6517929834 -2.0257439306 -1.0536648681 -0.4481321334 -0.2667293952
##  [896] -0.1913185907 -0.7045108383 -1.5512600361 -0.7082783816 -1.3572647594
##  [901] -0.1913363204 -0.8161926099 -0.1125494105 -0.2897781672 -1.0628168563
##  [906] -0.0792949206 -0.5291798898 -1.2115720228 -0.2361868950 -1.1354644893
##  [911] -0.5839647216 -0.7299717091  0.0665786781 -1.0353910372 -0.5806436355
##  [916] -1.2320021781 -1.3433876140 -0.4742470350 -1.3053725302 -0.2541726466
##  [921] -1.4955905605 -0.8967683565 -1.3359529279 -1.1316597569 -0.6034777450
##  [926] -0.8857802585 -1.0671935794 -0.9960392113  0.3430322071 -1.2598352463
##  [931]  0.0811608375  0.1561282693 -0.5293651761 -0.6633708628 -0.7884224801
##  [936] -0.7014104719 -1.0353910372  0.3013898425 -1.4400930263 -1.1638458276
##  [941] -0.3361698135 -0.8276456225 -0.7776017940 -0.6056690797 -0.2881953171
##  [946]  0.6141599507 -0.4338000945 -0.6848287065 -0.6379308413 -1.2680848191
##  [951] -0.2650633869 -0.3171581164 -0.8212104863 -0.4915290326 -0.7419259225
##  [956] -0.1437323353 -0.9376638331  0.5412144171 -0.5835943052 -0.2934029683
##  [961] -0.9196575984 -0.6841099321 -0.7285544582 -0.1120101122 -0.6428952480
##  [966]  0.2997124970 -0.7767703455 -1.8073048764 -0.3278788608 -0.6423014395
##  [971]  0.1615399127  0.1085274777 -0.5356172029 -0.5998976408 -0.0858580375
##  [976] -0.9171644902 -0.5400756305 -0.8552370512  0.1882213985 -1.1742887580
##  [981] -0.6246073133 -0.4245464818  0.1512757043 -0.3553401299 -0.1686299148
##  [986]  0.0332275461 -0.6952163679 -0.1328354049  0.3898826337  0.2374853680
##  [991] -0.5567312347 -0.2724219557 -1.0310182082 -0.3160500197 -0.7737664584
##  [996] -0.5495408604  0.2950950635 -0.3275505308 -0.3440702803 -0.4821452512
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

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced
```

```r
fit2
```

```
##       mean          sd    
##   0.44059677   0.20532881 
##  (0.06493067) (0.04590779)
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
## [1]  0.38583296 -0.07535055  0.17063905 -0.68287511  0.44973872 -0.14987234
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
## [1] -0.001
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9351757
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
## t1*      4.5 0.01111111   0.8959644
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 1 3 5 7 9 
## 1 2 1 2 2 2
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
## [1] 0.0621
```

```r
se.boot
```

```
## [1] 0.9439728
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

