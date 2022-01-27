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
## 1 3 4 6 7 9 
## 2 1 1 1 3 2
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
## [1] 0.0329
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
## [1] 2.804356
```

```r
UL.boot
```

```
## [1] 6.261444
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
##    [1] 6.0 4.1 3.7 4.4 5.5 4.1 5.5 3.7 4.5 5.6 4.5 5.2 5.2 5.3 4.4 5.0 4.7 4.8
##   [19] 5.1 4.7 4.9 4.8 5.6 3.2 4.0 4.2 5.1 4.4 3.7 6.5 4.7 5.8 5.0 4.1 6.4 3.7
##   [37] 3.5 4.8 4.3 4.6 3.7 3.8 4.1 4.5 4.6 4.8 3.4 3.1 4.1 4.6 3.3 5.4 4.8 5.0
##   [55] 3.9 3.4 4.3 5.4 4.2 4.1 5.5 3.6 5.8 5.6 3.7 3.6 5.5 4.3 5.9 4.0 4.3 5.4
##   [73] 4.8 5.0 4.2 5.1 4.7 4.7 3.4 4.0 4.1 4.9 3.7 5.7 3.6 2.4 4.5 3.9 4.3 5.2
##   [91] 5.0 5.3 5.9 3.8 5.1 5.0 4.2 4.5 3.7 3.5 4.7 6.4 3.4 5.6 4.7 3.9 4.0 5.3
##  [109] 3.8 3.2 5.0 5.4 2.4 4.2 4.8 3.0 5.7 4.4 4.4 3.7 3.4 5.0 4.0 5.1 5.1 3.1
##  [127] 5.1 4.0 5.1 5.0 4.1 6.5 3.9 4.9 5.8 3.5 5.3 3.2 4.0 4.4 5.9 4.6 5.6 4.2
##  [145] 3.7 3.9 4.7 3.5 4.3 5.5 4.8 4.7 3.7 5.2 4.8 3.3 4.8 5.2 4.3 3.9 4.3 4.6
##  [163] 4.1 3.3 5.2 4.1 2.5 4.4 4.7 3.9 2.7 5.9 5.9 6.1 5.6 3.6 4.8 4.7 4.7 3.1
##  [181] 4.2 3.6 5.6 4.7 4.9 5.0 3.4 3.9 3.9 4.9 4.1 5.3 3.5 3.9 5.4 2.3 5.5 4.5
##  [199] 3.2 5.0 5.2 4.5 3.9 3.9 3.6 5.8 4.5 5.1 4.8 5.5 3.6 5.5 5.4 5.3 5.4 3.7
##  [217] 3.8 3.8 4.5 4.5 5.2 3.7 4.4 6.1 3.0 5.5 4.4 4.7 4.3 4.7 5.2 5.4 5.0 3.6
##  [235] 4.5 3.8 5.5 4.8 4.0 3.7 4.5 6.4 5.1 4.7 5.8 4.5 4.3 4.6 4.6 5.5 5.7 3.7
##  [253] 5.4 5.7 4.8 4.9 4.1 4.5 4.7 5.1 5.6 6.2 5.0 3.5 4.8 3.5 5.7 4.2 4.2 5.6
##  [271] 3.4 2.9 4.4 5.6 3.9 3.3 4.0 5.5 3.6 5.3 5.4 4.2 4.8 2.8 3.4 4.7 4.9 4.4
##  [289] 3.8 4.9 5.1 3.3 5.1 3.0 4.3 5.1 3.8 5.0 5.2 3.0 4.0 4.4 6.1 3.7 5.7 6.1
##  [307] 2.7 5.3 5.6 6.4 4.7 5.0 6.7 6.1 4.6 3.0 4.0 4.1 3.3 4.9 3.8 3.2 5.9 4.9
##  [325] 4.0 3.4 5.5 4.6 2.7 4.5 5.3 4.0 3.6 4.4 4.0 5.4 5.2 6.4 4.7 4.2 4.4 4.6
##  [343] 3.5 6.2 3.9 5.4 6.3 5.5 4.7 3.3 5.1 3.7 4.6 5.5 3.4 3.9 5.3 4.0 4.4 3.8
##  [361] 3.8 5.8 3.6 5.9 4.9 3.9 4.1 4.4 4.8 4.6 3.8 4.6 3.8 5.0 4.9 5.3 5.7 4.9
##  [379] 5.9 6.1 3.4 4.6 3.1 6.1 5.2 3.7 4.2 4.9 6.0 3.6 3.5 5.0 3.8 3.7 5.3 3.9
##  [397] 3.9 3.8 5.5 4.7 5.0 3.1 4.0 4.6 4.5 5.2 4.2 5.4 4.1 3.9 3.4 5.3 5.0 3.9
##  [415] 4.8 5.7 4.0 3.9 3.5 5.1 5.0 4.4 3.9 4.6 5.6 5.9 4.0 4.7 3.6 4.3 4.0 5.5
##  [433] 5.0 4.3 3.8 3.5 2.9 5.7 4.4 3.7 4.7 4.0 3.0 3.2 4.3 6.6 3.9 6.1 3.6 5.1
##  [451] 4.8 4.6 4.9 4.2 3.6 4.0 4.7 5.3 3.9 4.5 2.8 5.3 5.0 5.9 4.4 3.5 4.0 5.2
##  [469] 3.5 5.3 2.5 5.1 5.1 3.7 5.6 5.7 5.3 4.4 3.9 5.1 4.4 5.1 3.9 3.0 4.4 3.3
##  [487] 5.2 6.0 3.0 3.8 3.6 5.0 5.7 5.1 5.4 5.2 4.1 4.7 4.3 4.1 4.3 5.5 3.9 4.8
##  [505] 4.4 4.4 3.3 4.2 3.3 4.5 4.1 4.3 6.4 5.5 5.2 4.2 4.4 3.8 4.8 5.3 3.3 5.1
##  [523] 6.0 3.7 3.9 4.8 4.6 5.1 3.1 6.0 3.9 6.2 3.4 4.3 4.9 4.5 5.5 4.8 3.8 5.8
##  [541] 4.1 5.9 3.6 4.1 4.1 4.2 4.5 5.6 4.6 5.8 4.2 3.7 5.0 3.6 3.2 5.4 4.5 5.6
##  [559] 5.9 5.5 3.4 4.3 6.1 6.1 5.2 3.1 5.9 4.1 4.1 6.2 4.2 5.1 4.7 5.3 4.4 5.4
##  [577] 4.7 4.8 4.9 3.5 5.0 4.0 4.7 5.9 4.0 4.3 3.7 6.4 5.4 5.0 5.0 4.4 4.2 5.3
##  [595] 4.2 5.3 4.9 6.1 5.8 2.1 4.9 5.1 4.2 3.8 3.6 5.2 3.6 3.8 2.4 3.7 5.1 3.2
##  [613] 5.1 3.7 4.6 4.7 5.6 3.2 4.2 4.0 3.3 4.7 5.4 6.0 5.2 5.4 3.2 5.4 4.6 4.4
##  [631] 4.8 5.8 4.7 5.5 5.2 4.7 4.2 6.2 4.7 3.1 4.2 3.7 4.9 4.6 4.9 4.4 4.4 5.7
##  [649] 4.5 4.6 4.3 5.1 4.7 5.3 4.3 4.4 4.3 4.4 3.8 6.3 5.0 3.8 3.2 3.8 5.2 6.4
##  [667] 4.6 3.9 4.8 5.0 3.1 5.0 4.3 6.0 4.2 5.8 5.5 3.5 3.6 3.3 2.8 4.5 5.9 4.7
##  [685] 5.6 5.1 5.2 3.6 3.6 4.9 4.4 3.8 4.8 3.3 5.1 3.3 5.0 4.6 4.8 4.8 5.3 6.1
##  [703] 4.4 5.1 6.5 4.8 3.3 3.6 4.2 4.6 4.1 4.8 4.3 2.8 4.9 3.5 3.9 3.8 4.6 5.9
##  [721] 3.9 4.4 4.6 3.5 3.5 4.2 4.6 4.3 4.4 4.8 5.3 4.8 3.7 3.7 4.1 2.6 4.5 5.1
##  [739] 4.6 4.1 5.2 3.8 4.4 4.2 5.0 5.0 4.3 4.4 5.2 3.7 4.8 5.9 4.0 4.0 5.6 2.1
##  [757] 4.1 5.2 5.5 3.5 3.7 4.6 4.5 4.3 5.4 3.5 6.2 4.9 3.4 3.2 4.7 5.4 5.2 3.7
##  [775] 4.3 3.9 3.7 3.8 5.6 4.7 4.7 4.7 4.2 5.4 4.4 4.8 6.0 3.2 5.9 5.1 4.6 3.6
##  [793] 3.7 6.3 2.3 2.6 4.2 3.7 4.7 4.3 3.4 4.9 2.0 3.6 3.4 3.5 5.0 3.3 4.8 6.0
##  [811] 4.0 4.5 4.2 2.8 3.6 5.5 3.2 5.4 3.7 4.8 3.3 2.9 4.1 6.2 3.4 4.6 4.5 3.8
##  [829] 5.2 3.8 6.4 4.4 4.5 3.7 4.8 4.7 2.7 4.6 3.7 4.3 4.7 4.6 5.0 5.2 4.4 4.3
##  [847] 4.6 4.6 4.4 4.8 4.5 3.5 3.7 4.6 3.9 3.5 6.0 4.7 6.6 4.6 5.2 2.8 3.7 4.5
##  [865] 3.3 5.7 5.1 4.8 6.2 4.8 4.6 3.5 3.7 3.6 4.5 2.2 4.9 4.3 6.5 4.0 3.7 4.6
##  [883] 6.2 3.7 4.2 3.7 4.0 6.4 5.5 5.7 5.2 6.1 4.4 3.3 5.9 4.2 3.5 4.6 3.5 4.0
##  [901] 4.7 2.3 6.3 4.4 3.3 4.6 5.9 6.0 5.3 5.1 4.2 4.9 2.3 5.4 4.5 5.2 4.5 4.3
##  [919] 4.2 4.4 2.5 3.6 5.9 2.5 4.1 4.8 5.2 3.6 5.4 4.2 4.5 5.6 4.5 5.6 3.5 5.3
##  [937] 5.1 4.5 5.2 4.3 4.2 3.8 3.4 5.4 4.4 6.1 6.5 3.5 3.8 4.8 3.8 4.9 5.5 4.9
##  [955] 4.5 3.9 4.5 3.5 5.0 3.9 4.2 4.2 4.2 4.0 4.6 5.5 4.9 4.9 3.9 5.1 4.9 4.1
##  [973] 3.7 5.6 4.6 4.7 5.3 3.9 3.8 5.7 5.7 5.0 3.4 3.8 3.6 4.8 3.7 5.2 4.6 3.8
##  [991] 4.9 3.8 3.8 3.3 4.6 4.9 4.5 5.9 4.9 3.0
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
##   2.8   6.2
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
##    [1] 4.8 6.2 5.4 2.9 3.2 4.4 3.0 5.1 5.3 4.9 5.3 3.4 4.0 5.0 3.8 4.5 3.1 4.8
##   [19] 5.6 5.9 4.0 6.2 4.8 3.8 3.6 3.9 4.6 4.0 4.5 3.8 4.8 4.4 4.6 5.5 3.2 5.2
##   [37] 4.0 4.9 4.5 5.6 5.3 4.7 6.1 4.3 4.5 4.9 5.1 4.5 3.8 4.7 4.5 4.5 4.0 3.3
##   [55] 4.5 4.7 3.6 4.1 3.4 4.6 6.1 3.8 4.8 4.7 4.4 4.7 4.8 4.5 4.0 4.5 4.8 5.6
##   [73] 3.2 5.2 6.2 4.5 4.4 4.7 3.0 5.1 6.1 5.3 3.9 4.2 4.9 4.0 3.5 3.0 5.1 4.4
##   [91] 5.2 4.3 3.8 4.2 5.0 5.5 3.4 4.6 4.6 4.8 5.0 4.7 4.7 3.9 5.4 4.3 6.3 5.1
##  [109] 4.3 5.3 4.8 4.7 4.6 3.1 3.5 4.6 3.7 5.1 5.3 5.4 4.2 2.4 4.5 4.6 5.0 4.7
##  [127] 4.2 3.1 4.4 5.1 4.4 5.1 5.2 5.6 3.0 3.7 3.1 6.1 4.3 4.9 4.3 3.2 3.1 4.0
##  [145] 5.7 4.6 4.0 4.9 5.8 4.0 4.4 3.7 4.9 5.0 3.6 3.9 5.0 4.9 4.7 3.0 4.9 4.1
##  [163] 7.5 3.9 3.3 3.6 4.2 4.0 2.4 5.1 4.0 3.8 5.7 6.1 4.5 4.3 5.4 4.8 4.3 4.4
##  [181] 4.9 6.0 6.3 6.5 3.3 4.1 5.0 3.9 3.7 3.5 4.7 4.7 3.7 4.6 4.6 4.9 3.3 3.9
##  [199] 5.7 4.5 4.1 4.5 5.0 5.1 5.8 4.0 3.5 4.0 3.5 5.2 3.9 2.8 5.1 3.4 5.0 2.5
##  [217] 3.6 4.4 2.9 4.2 4.6 5.4 3.7 4.7 4.2 5.0 3.9 4.9 3.8 4.3 4.7 5.2 4.0 5.0
##  [235] 4.9 4.1 5.2 3.5 3.0 3.5 5.6 6.1 3.9 2.4 3.9 4.2 5.1 2.1 3.1 2.6 3.7 4.8
##  [253] 4.9 5.9 4.5 4.6 4.0 5.4 4.6 3.3 4.0 4.2 5.6 4.3 3.8 3.1 5.2 5.5 4.1 3.8
##  [271] 4.7 4.2 2.7 4.5 4.7 4.5 4.3 4.8 5.4 4.3 4.1 5.0 3.4 4.7 4.9 4.3 4.8 3.1
##  [289] 5.0 4.0 4.8 4.9 5.6 3.0 5.3 5.0 3.5 4.7 3.7 2.7 6.2 3.8 3.0 4.5 6.3 5.4
##  [307] 6.4 4.2 2.3 4.1 3.8 6.2 4.5 5.7 5.1 5.0 4.0 3.8 3.6 4.2 4.7 4.8 5.0 2.9
##  [325] 2.8 3.1 5.0 4.6 5.3 5.1 5.1 4.2 3.5 4.3 3.6 4.6 4.4 4.3 4.0 4.9 5.4 5.4
##  [343] 4.4 4.0 4.7 4.4 3.8 3.8 4.8 3.2 6.0 4.5 3.9 4.1 4.9 3.6 5.9 4.8 4.5 4.4
##  [361] 3.5 5.0 4.4 4.6 4.6 3.7 5.9 5.6 3.1 3.1 4.6 2.4 3.3 2.9 4.8 5.7 3.9 4.8
##  [379] 4.3 4.2 5.1 4.8 5.4 4.5 4.7 5.4 3.1 4.1 5.1 3.3 3.9 5.4 4.5 3.2 2.9 4.2
##  [397] 4.9 5.8 4.1 3.3 4.0 4.9 3.6 2.5 6.0 5.2 4.8 4.3 3.9 5.3 3.9 4.1 5.2 4.8
##  [415] 4.4 4.1 4.9 5.1 3.5 4.9 4.8 3.6 4.6 4.8 4.6 3.3 4.6 3.9 4.1 6.3 5.6 3.7
##  [433] 4.7 4.5 6.1 5.4 5.9 5.0 4.8 4.8 3.6 4.2 4.9 4.5 5.4 4.8 3.7 4.0 3.7 4.3
##  [451] 4.6 4.3 4.6 3.4 5.4 4.3 4.8 3.9 4.2 5.4 6.0 1.9 4.3 3.4 5.3 3.9 4.9 5.3
##  [469] 4.2 3.3 4.2 3.6 4.1 5.4 4.5 5.2 4.1 5.9 5.6 3.5 4.0 2.9 6.3 4.5 3.9 3.7
##  [487] 4.4 5.9 4.2 4.7 4.4 5.8 3.8 6.8 4.7 4.4 2.7 4.3 4.4 2.9 6.0 4.7 6.0 4.9
##  [505] 4.8 5.1 4.7 3.2 5.1 5.6 5.1 5.5 4.6 3.3 5.4 4.1 4.9 3.3 4.7 4.0 5.0 4.4
##  [523] 3.7 5.0 4.3 4.7 5.1 3.1 5.1 4.1 4.5 4.1 4.5 5.1 2.9 4.9 4.5 4.6 5.2 5.6
##  [541] 4.4 4.8 4.2 3.8 5.0 5.6 4.9 3.9 3.8 5.9 5.3 4.1 4.3 4.6 4.0 3.3 4.7 4.8
##  [559] 5.4 4.3 3.0 4.4 4.3 4.2 4.7 4.1 4.5 5.4 4.3 4.6 4.4 4.2 5.5 4.7 4.1 5.2
##  [577] 3.4 4.8 3.3 4.5 5.6 2.7 2.8 3.7 4.9 4.2 6.0 4.4 4.8 6.1 4.3 4.1 4.4 3.1
##  [595] 5.2 3.6 5.8 5.2 4.5 5.2 4.4 4.9 5.4 5.3 4.5 5.7 4.2 4.3 5.0 5.0 4.6 4.3
##  [613] 3.0 4.8 3.6 4.6 5.0 4.0 5.9 5.5 5.3 4.1 4.3 4.7 6.1 5.1 4.8 4.8 4.1 4.3
##  [631] 5.3 4.0 4.2 4.9 4.1 3.2 3.6 4.5 3.6 6.2 5.6 5.2 2.9 4.0 4.2 5.0 4.1 4.6
##  [649] 5.5 3.6 3.1 4.7 6.6 4.6 5.0 4.5 3.5 3.6 3.8 5.6 4.1 4.1 4.3 3.8 6.2 5.9
##  [667] 4.1 5.3 4.9 5.5 3.7 3.0 5.7 3.3 5.3 4.2 4.4 4.7 3.7 5.5 4.3 4.6 5.5 4.6
##  [685] 7.1 4.3 3.6 4.9 3.5 5.0 1.9 4.9 4.5 5.8 3.0 5.7 6.0 4.3 4.4 3.9 4.2 4.5
##  [703] 3.7 5.3 5.9 6.7 5.6 4.0 5.7 4.9 4.0 5.2 5.8 2.7 6.1 4.2 4.6 6.5 5.7 3.1
##  [721] 4.8 4.9 4.1 2.5 4.4 3.2 3.5 3.0 3.7 3.8 5.4 6.9 4.6 5.3 3.9 3.0 4.6 3.6
##  [739] 5.5 5.0 4.2 6.7 4.3 4.0 4.0 3.6 2.9 4.8 3.6 4.1 4.3 5.3 4.4 3.0 5.0 4.2
##  [757] 2.3 5.4 4.1 2.9 5.8 3.6 4.5 6.3 6.0 4.2 4.8 4.4 4.7 4.7 5.1 4.0 6.0 3.4
##  [775] 4.6 4.7 5.9 4.0 4.9 3.5 6.0 4.1 4.0 5.2 4.5 4.1 6.0 5.9 4.1 4.6 3.6 2.5
##  [793] 5.4 4.2 4.5 5.1 5.0 6.8 5.6 4.7 4.0 4.1 5.0 5.3 4.9 4.7 4.9 4.7 4.3 5.5
##  [811] 3.7 4.7 5.4 5.3 5.0 3.6 2.6 5.7 3.4 4.5 2.9 5.6 5.3 5.1 5.3 4.0 4.8 5.2
##  [829] 3.2 5.0 3.8 5.0 3.8 5.7 5.2 2.3 5.1 5.5 3.1 5.6 4.1 4.7 4.5 4.1 2.9 4.5
##  [847] 3.3 3.5 4.0 4.2 3.1 4.1 3.5 5.5 5.9 3.9 5.3 5.0 4.3 4.9 5.8 3.5 3.2 4.7
##  [865] 3.9 4.4 3.9 3.4 3.4 3.7 3.8 4.1 5.4 4.2 4.0 3.5 3.0 3.9 5.0 5.3 4.9 5.0
##  [883] 4.4 4.7 4.5 4.5 6.8 3.7 3.9 5.2 3.8 4.2 2.9 4.6 4.2 4.0 4.3 5.3 5.2 4.7
##  [901] 5.0 3.9 4.0 3.4 5.8 5.6 4.6 4.2 2.9 3.5 4.9 5.6 3.3 3.5 6.1 4.6 4.5 4.6
##  [919] 4.1 5.4 6.4 5.3 5.0 5.1 4.6 4.3 6.2 5.7 4.1 3.9 4.7 5.7 3.8 4.9 4.6 4.0
##  [937] 5.3 4.9 3.4 5.0 6.0 4.4 5.1 5.1 3.4 4.8 5.6 4.1 4.9 4.6 3.5 4.3 4.3 5.3
##  [955] 5.0 5.6 4.0 3.9 3.9 2.7 4.4 4.3 4.1 4.1 4.9 5.7 4.4 5.4 4.5 5.3 5.5 4.9
##  [973] 4.4 5.0 4.5 3.9 3.5 4.1 4.8 3.5 3.9 4.5 5.6 3.3 5.7 2.5 3.6 4.7 4.3 4.9
##  [991] 3.5 4.7 3.7 5.3 5.5 5.3 5.3 6.1 5.1 5.9
## 
## $func.thetastar
## [1] -0.0039
## 
## $jack.boot.val
##  [1]  0.479827089  0.357101449  0.298853868  0.133152174  0.003380282
##  [6] -0.109770115 -0.111141304 -0.262162162 -0.345896657 -0.504587156
## 
## $jack.boot.se
## [1] 0.9157711
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
##    [1] 4.7 5.8 5.7 2.5 4.0 3.6 5.2 4.9 4.5 4.4 3.7 4.7 4.3 4.1 5.6 3.3 3.9 5.3
##   [19] 5.9 5.0 4.1 5.9 4.2 3.1 4.3 4.9 5.4 4.8 5.8 4.9 4.9 3.7 3.9 4.3 5.5 5.2
##   [37] 4.9 4.1 4.7 4.9 5.5 4.0 4.9 6.0 4.9 5.4 5.3 4.0 4.0 3.3 5.4 3.3 5.8 4.0
##   [55] 4.8 3.9 2.2 4.0 4.6 4.0 4.1 3.7 2.6 6.2 3.2 5.0 4.8 4.8 3.9 3.8 4.1 5.2
##   [73] 3.8 5.0 4.6 4.4 5.0 5.9 3.4 3.9 5.2 5.9 4.7 4.3 4.1 3.5 3.8 7.6 4.2 2.9
##   [91] 3.9 4.6 4.9 5.8 4.0 5.3 4.6 2.2 5.2 3.9 3.9 6.1 4.0 5.1 3.9 5.3 4.1 4.5
##  [109] 4.4 5.2 4.1 3.9 5.6 4.8 4.0 2.9 5.2 2.7 3.3 4.6 4.0 4.1 6.4 4.0 5.7 3.4
##  [127] 3.6 5.1 4.0 3.4 3.3 4.0 3.6 3.4 3.9 4.3 3.7 4.6 4.4 3.9 4.6 4.7 4.1 4.1
##  [145] 4.7 5.7 6.3 4.4 4.9 4.8 5.8 5.7 4.0 3.3 5.6 5.7 3.0 5.1 4.7 4.3 5.5 5.0
##  [163] 6.0 5.7 6.1 3.9 4.3 3.7 5.1 2.9 3.3 4.0 4.2 5.4 4.2 3.8 4.2 3.3 4.8 3.9
##  [181] 3.8 4.6 4.7 2.7 4.3 5.0 4.9 2.8 4.9 3.6 3.5 4.7 5.1 4.9 4.5 3.5 5.6 4.4
##  [199] 2.7 3.6 4.0 6.3 4.6 3.7 5.0 2.8 4.5 3.9 5.5 3.6 3.2 4.2 5.3 4.7 4.7 4.6
##  [217] 5.5 4.2 4.2 3.6 5.2 3.1 4.5 4.1 5.6 3.5 5.0 5.4 3.5 4.9 5.6 3.8 4.5 4.8
##  [235] 5.0 4.3 5.9 4.6 3.9 4.9 3.6 4.2 3.2 3.8 3.5 5.1 4.8 5.7 5.6 5.0 4.6 4.6
##  [253] 5.5 6.2 4.1 3.4 3.5 3.5 5.0 4.7 4.0 4.5 3.7 3.7 4.8 3.8 5.0 5.2 5.1 4.2
##  [271] 4.8 6.0 4.8 5.7 3.6 5.1 5.1 3.4 5.1 4.1 4.5 3.3 5.9 3.4 5.5 2.8 3.9 4.6
##  [289] 5.2 4.8 4.6 3.7 6.2 5.6 3.9 3.9 4.8 5.1 5.6 6.4 4.1 4.8 4.7 5.2 6.8 4.9
##  [307] 4.7 4.1 6.1 3.4 4.7 4.1 3.8 4.9 3.5 4.0 4.2 5.9 5.4 4.2 4.0 2.7 4.6 4.5
##  [325] 3.8 3.9 4.2 4.9 4.6 5.6 4.9 5.4 4.6 3.5 3.9 4.7 3.6 3.3 5.2 5.3 4.1 4.8
##  [343] 4.1 4.3 5.2 5.0 5.0 4.4 3.4 3.3 4.4 4.7 3.3 3.8 5.0 6.1 3.0 4.7 6.9 4.0
##  [361] 3.2 4.0 2.6 3.9 3.7 4.1 5.0 5.7 5.5 5.8 5.9 6.1 3.5 4.4 5.1 4.1 5.3 4.5
##  [379] 6.7 5.6 5.7 3.1 4.2 3.0 5.6 3.8 4.4 4.9 3.3 4.6 5.7 4.5 4.3 5.0 4.0 5.1
##  [397] 5.0 6.7 5.3 3.4 3.6 4.6 3.8 7.7 5.4 6.0 2.4 3.7 4.3 4.1 3.4 5.5 5.1 2.6
##  [415] 4.5 3.7 4.7 4.5 2.4 3.9 4.6 4.9 4.6 5.3 4.7 4.7 4.0 4.3 5.1 4.3 3.2 4.0
##  [433] 5.7 5.7 3.2 5.9 4.0 5.2 5.4 4.2 4.9 5.3 4.0 4.2 5.0 4.7 4.9 4.4 3.4 4.6
##  [451] 4.4 4.7 4.6 3.8 2.9 3.8 4.4 2.7 2.3 4.9 4.1 5.5 4.2 4.6 3.7 2.6 4.7 3.4
##  [469] 4.7 4.6 4.0 5.3 4.3 3.4 4.5 4.5 3.9 4.5 3.9 5.0 4.6 4.6 4.0 4.3 3.4 4.7
##  [487] 3.8 5.0 5.0 4.8 5.2 4.8 4.3 5.6 5.2 4.7 4.1 3.8 4.8 6.0 4.6 6.6 4.7 3.1
##  [505] 3.8 4.0 3.2 6.8 4.9 4.9 3.9 4.3 4.6 3.4 5.1 5.4 3.0 4.9 3.7 3.9 4.2 3.1
##  [523] 2.9 5.5 4.7 4.8 5.4 4.1 4.8 4.6 3.8 3.2 5.2 3.4 3.7 5.0 4.2 4.1 5.5 4.8
##  [541] 4.2 5.8 4.6 4.5 5.4 4.9 5.5 4.2 4.0 4.4 4.6 3.8 4.9 5.6 4.7 3.5 4.1 4.5
##  [559] 4.0 5.7 3.9 5.3 5.4 3.4 4.4 4.3 5.2 3.4 4.4 3.9 5.5 5.8 5.7 4.5 3.5 3.8
##  [577] 3.4 4.3 3.0 5.0 4.1 4.9 4.2 4.2 4.6 4.2 3.0 3.2 4.4 4.3 5.3 5.5 2.8 3.6
##  [595] 4.2 4.7 3.2 2.6 6.3 3.7 3.4 3.5 3.5 3.6 4.7 4.7 4.4 5.5 2.8 5.9 6.9 4.6
##  [613] 4.3 3.9 5.6 4.6 4.2 2.5 3.4 4.0 8.0 5.5 3.2 3.7 2.4 4.3 5.4 5.2 6.2 4.7
##  [631] 4.1 3.8 4.8 3.5 4.6 7.1 5.4 4.7 4.7 4.2 3.2 4.1 6.4 5.0 4.2 4.4 5.0 4.1
##  [649] 2.9 3.6 3.4 4.4 5.3 4.0 4.2 5.7 4.1 5.4 3.8 4.1 4.1 5.3 3.6 4.6 4.5 5.2
##  [667] 3.8 2.9 5.6 4.7 3.7 3.1 3.0 4.3 4.8 3.8 3.5 3.8 4.4 3.3 5.1 4.7 4.2 4.2
##  [685] 5.0 3.3 6.1 3.6 3.5 4.2 3.1 3.4 3.7 5.1 5.1 3.4 4.0 4.3 5.1 4.4 5.9 4.2
##  [703] 5.5 5.5 4.5 5.4 4.4 6.5 3.4 5.4 4.1 4.9 5.2 2.7 3.3 3.5 3.1 5.4 4.6 3.4
##  [721] 3.8 4.9 4.3 4.8 3.5 5.2 3.5 3.5 3.7 5.9 4.5 4.7 4.0 4.3 4.4 5.2 3.0 3.1
##  [739] 5.1 4.8 5.3 3.4 6.2 4.0 6.0 5.4 4.1 3.7 4.4 3.8 1.9 5.0 3.5 4.8 5.2 3.8
##  [757] 3.6 6.1 3.9 4.3 2.6 5.2 2.9 5.4 4.8 5.2 4.3 3.3 4.2 5.6 4.5 6.6 4.6 5.6
##  [775] 4.1 5.5 4.7 6.2 2.6 4.0 4.8 3.8 4.9 4.4 4.9 3.9 4.4 5.5 5.9 3.8 5.3 3.2
##  [793] 5.0 4.5 4.5 6.0 3.2 3.8 3.6 4.0 4.7 5.3 5.4 3.8 4.0 3.3 4.1 3.5 4.8 4.0
##  [811] 3.9 4.4 6.3 3.9 5.1 4.3 4.8 6.8 5.3 4.4 5.2 3.6 4.1 4.3 4.0 4.4 4.0 3.9
##  [829] 3.9 3.2 4.3 3.3 2.7 5.7 4.8 3.7 4.4 4.4 5.6 3.5 4.3 6.1 5.2 3.5 3.5 2.8
##  [847] 3.7 5.2 4.3 3.2 3.6 5.1 4.3 4.7 3.4 4.4 3.8 5.7 5.6 4.1 3.0 5.2 6.1 4.4
##  [865] 3.3 2.9 3.8 3.4 4.6 5.1 4.2 2.2 5.2 5.2 3.8 4.3 3.4 4.8 4.3 5.3 3.9 3.8
##  [883] 4.4 3.7 3.2 4.4 5.1 5.2 5.0 3.2 4.5 6.0 4.8 4.8 4.7 3.8 3.8 4.0 4.7 5.1
##  [901] 4.8 2.4 3.5 4.8 4.2 5.0 5.0 4.3 4.5 5.8 3.4 4.7 5.2 4.8 5.3 2.7 4.6 4.3
##  [919] 4.0 3.4 5.2 4.6 4.0 3.9 5.5 4.2 5.8 4.2 2.7 4.9 6.2 4.1 5.7 6.0 4.1 5.5
##  [937] 4.3 4.5 4.1 4.2 5.5 4.3 3.6 4.2 3.7 5.3 2.6 4.4 6.3 2.8 5.3 4.1 3.6 2.7
##  [955] 3.4 5.2 6.0 4.8 5.4 2.8 5.3 5.2 6.5 3.9 5.2 4.6 4.3 3.8 3.6 5.4 4.3 3.6
##  [973] 2.6 4.7 2.8 4.9 4.9 4.3 5.2 4.5 5.6 4.9 4.8 4.5 4.7 6.1 3.4 4.2 5.4 4.6
##  [991] 3.5 3.7 4.3 4.6 5.6 4.7 4.3 3.9 5.0 5.1
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.400 5.400 5.200 5.200 5.136 5.000 4.800 4.700 4.500 4.400
## 
## $jack.boot.se
## [1] 1.018024
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
## [1] 0.1262594
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
##   3.342570   5.038076 
##  (1.426547) (2.320030)
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
## [1]  1.8071349 -0.6868457  0.0511126  0.1988442  1.0472204  1.0557098
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
##    [1]  0.0973131347  0.4382485811 -0.1312954003  0.4216971756  0.5322772413
##    [6] -0.7687096756  0.2447304098  0.5046016411 -1.0695026007 -0.7648301120
##   [11] -0.0924761213 -1.2735712908  0.5797910132 -0.1462353978  0.2035391150
##   [16]  0.1924751359 -1.0062904087 -0.0259350797  0.6586480210 -0.1743067750
##   [21] -0.6098916959 -0.0573166883 -2.3033858315  0.2703280432 -0.8629743472
##   [26] -0.4989635726 -0.2511014553 -1.5365249061  0.4267097693 -0.3045693035
##   [31]  0.1675204227  0.2991616134  0.5813429362 -0.7443232370 -1.9606718041
##   [36]  0.1149282687  0.1738632827  0.2155899725 -0.7333488175 -0.8658238857
##   [41]  0.1734689409 -0.5534368698  0.1088485320  0.3168834626  0.2438012184
##   [46] -0.5599496572 -0.0643984525 -0.4470011172  0.4188718287  0.0106981295
##   [51]  0.1833024469 -0.0037192900 -0.7066851959  0.3386891870 -0.1874594797
##   [56] -0.5839531343 -0.3031065217  0.0908241699 -0.0665569682 -0.0009197423
##   [61]  0.1748095410 -0.0140348237 -0.5923150685  0.5751902438 -0.1662329040
##   [66] -0.0776287265 -0.6552338677  0.0560469194  0.2436276061  0.9450150650
##   [71]  0.3493073726 -1.1194816331 -0.3254604735 -0.3149261167 -0.7197333840
##   [76]  0.2953613138 -0.1994334234  0.1682369448 -0.1757629169 -0.2346137617
##   [81]  0.7558549103 -0.0025841820 -0.1298711074 -0.3490749063 -0.9186127431
##   [86]  0.4201852831 -0.2333436052 -0.4147186032 -0.2716661667 -0.5949604079
##   [91]  0.9939552023 -1.4643393432 -0.2364356210 -0.0443426270  0.2566137778
##   [96] -0.0473459009 -0.1988616466  0.0127989620 -0.7072507324  0.2508031409
##  [101]  0.1049667017  0.9660713758  0.6271064121  0.5550455922  0.5020579874
##  [106] -0.1085853129 -0.0811546128  0.0497000317  0.3525717354 -0.9099306699
##  [111]  1.2100965248 -1.1241287792 -0.2617670523 -0.4850534624 -0.5905517379
##  [116]  0.8248670228  0.2642063878 -0.4603018147 -0.6802932944  0.5716972393
##  [121]  0.7350344597 -1.2379314396 -1.0797103136 -2.3905947273  0.2446301528
##  [126] -0.5878125530  0.0295737368  0.3251694184  1.3943921741 -0.4222813592
##  [131]  1.0153922947  0.0407388888  0.6814908572  0.1661182767  0.1370644157
##  [136]  0.5304630996  0.2211682188  0.0879941976 -0.3397040431 -0.4880622225
##  [141]  0.1623137711  0.3548251972  0.1043048725  0.4909220984 -0.0445066943
##  [146] -0.2057790107  0.0373350921  0.2403017901 -1.5300554754  0.0846739308
##  [151] -1.7821376929  0.4139715229 -0.6006734337  0.2494910206 -0.0857359958
##  [156] -0.2102605121 -0.3932876321  0.4140758629 -0.0741637087  0.1237142320
##  [161] -0.3226996349 -0.2814115012  0.3470973687  0.2017984069 -0.2298250685
##  [166] -0.1147288539  0.7158707130  0.0889710513 -0.1103303558  0.3634289083
##  [171] -0.6741007459  0.6014821655 -0.5273837966  0.7722830078 -0.3420907575
##  [176]  0.0340554356 -0.1815854026 -0.0821286169  0.4740707163  0.5895601381
##  [181]  1.4648269169  0.0498381361  0.7703853535 -0.0643432161 -0.6019819562
##  [186] -0.3690873687  0.1703949384  0.2090400140 -0.6242737588  0.1161781651
##  [191]  0.3903513859  0.0218911079  0.1104008609  0.2699911865 -1.0306110003
##  [196] -0.2683915084  0.4608400972  0.1300124078  0.2009275549 -0.5357374067
##  [201] -0.3168406710 -0.9229607180  0.1682369448  0.1104903147 -0.0809822607
##  [206]  0.2915383823  0.2655504827 -0.3806179890  0.6070922321 -0.4564128958
##  [211] -0.5115898216  0.5257632038 -0.0187286442 -0.2739495080 -1.0916459853
##  [216] -0.7043145165 -0.3922827769  0.6848817945  0.4118143765  0.0094071710
##  [221]  0.5029802390  0.5105139206 -0.8404062285 -0.3255107526 -0.0950085435
##  [226]  0.3756725541  0.3497063692  0.2856765288 -0.5062036682 -0.4638463192
##  [231]  0.2215894313  0.1769474826  0.0460879845 -0.2749560957 -0.9012859630
##  [236]  0.0746804842  0.5150456797 -0.2771071079  0.2953613138  0.4348406444
##  [241] -1.4965161907  0.3369151689  0.4817347235  0.9024661331 -0.8604820573
##  [246] -0.5180745784 -0.2940202566 -0.4456424516  0.2799164092  0.2178175571
##  [251] -1.0785847944 -0.0635053443  0.8857867578  0.0210116144  0.0553343763
##  [256]  0.4205916223  0.4101946455  0.1456374981  0.1195988919 -0.3060425685
##  [261] -1.4984449925 -0.2697355873  0.3251556673  0.8728715609 -0.1497619270
##  [266] -0.1640094653 -0.4695791491 -1.2899275477  0.6783015141  1.5709034583
##  [271] -0.2986582943 -0.6591869638  0.2729998289 -0.0451434987 -0.0447816148
##  [276]  0.4222153952 -0.3456925449  0.3514307037  0.2761953671  0.1117312364
##  [281]  0.0738492277  0.2968712375 -0.0408299793 -0.3818279593 -0.6031924832
##  [286]  0.2791967949 -0.5640841688  0.2165677387  0.3588110565  0.1983252722
##  [291]  0.0567866265  0.4872364349 -0.3886976791  0.2388619659 -0.3797731028
##  [296]  0.4755870600 -0.2743593260  0.2613177080 -0.0320079135  0.4722023669
##  [301]  0.6295370976  0.2793983665 -0.0216756437 -0.1439790250 -0.0425998053
##  [306] -0.6246234041 -0.8076433997 -0.4703920981 -0.9145126547  0.0766433653
##  [311] -0.6526317576 -1.8061357620  0.7887929712  0.1262593782 -0.4689459931
##  [316] -0.8057401721 -0.1584764720 -0.3434262075 -0.6366065917  0.3418568477
##  [321]  0.2361179526 -0.0395522553 -0.2776604486  0.6007864906  0.2918580776
##  [326]  0.0656770580 -0.1485518413 -0.5838898843  0.3555365462 -0.4182945395
##  [331]  0.8264411605  0.0017289518 -0.6378765584  0.0899369873  0.4584110989
##  [336] -0.0417342648 -0.0406063542 -0.3097635506  0.7841219020 -0.0425074191
##  [341] -0.0441011414  0.3186522380  1.3690398665 -0.2617670523  0.3013246178
##  [346]  0.5944049164  0.1007835074  0.9561354044  0.1329784152 -0.5385046242
##  [351] -0.0466770230  0.2517365609 -0.4114477735  0.2164075240 -0.6591448300
##  [356]  1.4532387938 -0.7692470678  0.7140353068  0.2378893548 -0.8280188582
##  [361]  0.0917140939 -0.4726609516 -0.4784213162  0.1726914709  0.5505733129
##  [366]  0.4267097693  0.8875294638  0.2519874497  0.4200870819  0.4024461849
##  [371]  0.6263336954  0.3732787223  0.0422453777  0.2512351432 -0.0310832535
##  [376]  0.3369426086 -0.3553780317  0.4412868922  0.2166124482  0.1882251827
##  [381] -0.0614083358  0.0124279209 -0.1566616419 -1.7700443327 -0.7297689471
##  [386]  0.2367794531 -0.4110489172  0.2141708614  0.0122723376 -0.0518822019
##  [391] -0.2385010427  0.0643512905 -0.5116819082 -1.1002809591  0.0406791509
##  [396] -0.1777627358  0.4075992116 -0.8665982907 -0.4667284486 -0.5258248611
##  [401]  0.6897235145  0.2786408248 -0.0888058158 -0.3869722861  0.1496635982
##  [406]  0.3836523250 -0.3858247307  0.1255701629 -0.1757004066  0.1287325379
##  [411]  0.0094071710 -0.4638732571 -0.6354780990 -0.0950085435  0.7703824603
##  [416]  0.2179926002  0.0991383152 -0.1306856371  1.0733400322 -0.0400755947
##  [421]  0.6241728101  0.2345545526  0.8076172807 -0.1194719435 -0.1319174270
##  [426]  0.7522459655 -0.4372607583 -0.1454179970  0.0603745191  0.3618962055
##  [431]  0.6885128260 -1.1278197708 -0.5906452230  0.1682369448  0.0768440410
##  [436] -0.4171243775  0.2104371696  0.1282092965 -0.5958260905 -0.6530368340
##  [441]  0.1663550437  0.0263930765  0.2657120792  0.5235830741 -0.8998838340
##  [446] -0.7579211593  0.3975212132  0.0628149418  0.5102365718  0.5812379148
##  [451] -1.0984136294  0.0469476231  0.1577781946  0.8500205627 -0.1307300585
##  [456]  0.4888315555  0.6799817185 -0.2671731515  0.5521314100 -0.3171907794
##  [461] -0.1951856147 -0.2263562080 -0.4378488361 -0.2634304820 -0.5094816107
##  [466] -0.1533161928 -0.8881139175 -0.1641995596  0.5219535436 -0.0850441038
##  [471]  0.4018485865  0.2342130045 -0.1195350782  0.1382549205  0.2417758489
##  [476]  0.4319899465 -0.3419557265 -0.5315030825  0.1642569903 -0.1547411475
##  [481] -0.4229029290  0.3021264065 -0.0321041484 -0.0381378123 -0.9477601143
##  [486] -0.3718558709  0.0242909099  0.3174428712 -0.1893277112 -0.1155105874
##  [491]  0.0539776541 -0.1457394581 -0.0490905983  0.2312563105 -0.0995341561
##  [496]  0.3330686575  0.5768260904 -0.1265151004 -0.4687804202  0.7694632637
##  [501]  0.1327716954 -0.8006546972 -0.2993442909  0.2446301528  0.6913703754
##  [506]  0.4764213383  0.4832233775  0.0306694157  0.9273306583  0.6963014647
##  [511]  0.0359796378  0.1709528212  0.2180820355  0.4161612168  0.1934492864
##  [516]  0.7598569475  0.2551864327  0.5406755237 -0.4784228694 -0.1576408747
##  [521]  0.1218187057  0.1776731410  0.1997158002 -0.7572229872  0.0930605237
##  [526] -0.7728547971  0.7172446161  0.1593177196 -0.1819589810  0.1744367063
##  [531] -0.2536561518  0.1344091183  0.5102953348 -0.6588442736  0.1471503371
##  [536]  0.2944122313 -0.2747996684 -0.0519083854  0.8348914568 -0.1697524598
##  [541]  0.5122055162  0.0819665451  0.1859783875 -0.2010381160  0.3969273926
##  [546]  0.0051969386 -0.2114196641  0.4827192757  0.5144920181  0.2457382221
##  [551]  0.0742208031  0.3248672254 -0.3944628589 -0.1268837671 -0.0475978805
##  [556] -0.0936757325  1.2512058994  0.0609938169  1.1357663269  0.0466714097
##  [561] -0.2266738492  0.0262399168 -0.1935072194  0.9333976046  0.0093198802
##  [566] -0.2387868815  0.8538993148 -0.1878799966 -0.1320963691  0.4329391875
##  [571] -0.1151801932 -0.3539653496  0.4754477319 -0.2010766725 -0.2765418544
##  [576] -0.2797504814 -0.1631468259  0.1207483036 -1.2242447173  0.1913534265
##  [581] -1.6646105331  0.6703383601 -0.6174697959 -0.0947631169  0.2719619125
##  [586] -0.9876962502  0.0730587918  0.2101693847  0.5658846480  0.1174163921
##  [591] -0.2645183281  0.7992633406  0.4930373533 -0.0255134238 -0.2348751337
##  [596]  0.8929743266 -0.0707180796 -0.0757950853 -0.2294278433 -0.2188738460
##  [601] -0.9015490651  0.6017594207  0.0480133407 -0.2310826505 -0.2180774098
##  [606]  0.7847087486 -0.0381895786 -1.5248686363 -0.4858713901  0.5882805466
##  [611] -0.2093907729 -0.3431251854 -0.2359776830 -0.1033149256 -0.7593783321
##  [616] -0.4864642497  0.8283026168  0.4067147752 -0.5388664099 -0.2607925349
##  [621] -0.1404968658  0.0814798551 -0.5414678770 -0.3193124847  0.0562785985
##  [626]  0.3674965810 -0.1812201545  0.3977349538 -0.5231650312 -0.0992492969
##  [631] -1.0559706659 -0.5590319353 -0.4362950192  0.7381434014 -0.1448753235
##  [636] -0.7660878475 -0.4438903232  0.5589601703 -0.2258681673  0.3251854029
##  [641] -0.2324516646 -0.3127868954 -0.3870252739  0.8410879299  0.2184853952
##  [646]  0.9041276208 -0.4680271619  0.5459076327 -1.1167977941  0.8899327840
##  [651]  0.5836891068  0.4332865653  0.4563329310  0.4281040022 -0.6785214558
##  [656]  0.3010493982  0.3188207870  0.5001351236  0.1296568752 -0.1697709322
##  [661] -0.0910600424  0.5367378516 -0.0248124736 -0.8962550204  0.1697601925
##  [666] -0.4959370087  0.1278122946  0.6936210002 -0.6572747090  0.5876101230
##  [671]  0.2448833747 -0.0694726184 -0.2122065899  0.8705511936  0.4762793125
##  [676]  0.5155824169 -0.5753884874 -1.0297149552 -0.3963199438  0.5449680086
##  [681] -0.3301952383 -0.4044975872 -0.3593950496  0.4768414047 -0.4438373791
##  [686] -0.0430724779 -0.8902695251 -0.1776169743 -0.0618644302 -0.0425145135
##  [691] -0.2708029887  0.5425355681 -0.2903026477  0.9236700752 -0.1528896326
##  [696]  0.1092878030 -0.0654897514  0.6326614167 -0.6293524916 -0.0973726049
##  [701]  0.6531509072 -0.0386869008 -0.0533128047 -0.7711441098  0.6638930382
##  [706]  0.3530892235 -0.2501787001  0.1698112783 -1.3040669109 -0.2499150813
##  [711]  0.1145338453 -0.2746829457  0.1325267582  1.1367771191 -0.2466174872
##  [716] -0.0210432327 -0.5392780276 -0.7408451626  0.3558021081  0.2635776268
##  [721] -0.0443886237  0.9383469691 -1.0517479788  0.2574668816 -0.0451411577
##  [726] -0.2200988979  0.2829401908  0.2838556384  0.5381800397  0.3886382296
##  [731]  0.4381616634  0.1105880393  0.6634638286 -0.4094007864 -0.4328712499
##  [736]  0.5641178104 -0.0650552837  0.7180356763 -0.6304312180 -0.6408912981
##  [741] -0.5502529857  0.0624021531  0.4439978878  0.5663311480  0.4806578770
##  [746] -0.0813107366  0.7177457352 -0.6177495132 -0.0134494906 -0.1531255585
##  [751]  0.4875005235 -0.6505912438 -0.4529965840  0.1930426652 -0.0254199622
##  [756] -0.0030444695 -0.5308018595  0.7656364879 -0.7802479709  0.1236046012
##  [761]  0.4559612597  0.6336884536  0.1392677252  0.1754259843 -0.4273174100
##  [766] -0.6089698306  0.5422346552  0.3880341251  0.1255701629  0.3800813274
##  [771] -0.0214784035  0.4700975850  0.1863671562  0.0741179442 -0.0976133478
##  [776] -0.0538922309 -0.3177864815 -0.4883455582  0.1427750046  0.2595815362
##  [781] -0.2402614591  0.2769435676  0.5137388101  0.9337456890 -0.3130941739
##  [786]  0.3635777855  0.0121243386  0.3751148822 -0.3534044263 -0.2989479151
##  [791] -0.2952871837  0.3780581222 -0.0420411307 -0.0577255738  0.2749317876
##  [796]  0.1802572030  0.5956179006  0.2729031224 -0.3361925832  0.1654590909
##  [801] -0.0043707566  0.0016599366  0.1450318284 -0.0122941409  0.7619714258
##  [806]  0.6457458564 -0.2639493779  0.5141763341  0.1574610609  0.2302913977
##  [811] -0.7713630058  0.0817294747 -0.7336174387  0.6737314079 -0.4574462754
##  [816] -0.0150287083  0.2791331312 -0.8143579797  0.5904393433  0.1457334633
##  [821] -0.9686074799  0.0199913807  0.4740707163 -0.4522803240  0.5445342726
##  [826] -0.5070816891  0.4219204561  0.5550455922  0.0814329895  0.2953613138
##  [831] -0.1417643966  0.0521390825 -0.1574098289 -0.2766802876  0.5888360898
##  [836]  0.3379753671  1.4978765737  0.2386966966  0.2413847738 -0.2718646875
##  [841] -0.1206040705  0.7246767574  0.1808840917 -0.3413454376  0.1733668016
##  [846]  0.8888126941  0.1017119380 -0.1242206364 -0.0010199999  0.0906904253
##  [851] -0.0707682548 -0.8342965613  0.0049115317  0.5807557925 -0.4502961911
##  [856] -0.2922467276 -0.3225694515  0.4113353072 -0.2953980257  0.4835030167
##  [861]  0.3775636872 -0.3431165035 -0.6275493358  0.0438808099  0.0320137792
##  [866] -0.2655669823 -0.1915534221  0.4334186705  0.0385472775 -0.0821286169
##  [871]  0.3304415934  0.3121259498  1.1945774589  0.4089401657  0.0693114270
##  [876] -1.3040669109 -0.0435629127 -0.3663472765  0.8294242663 -0.3037431086
##  [881] -0.9427269265 -0.0661670720 -0.1914644982  0.0240660608  0.7263792823
##  [886] -0.6189308806  0.0499196615  0.0196521553  0.0025492633 -0.7575313064
##  [891] -0.1757004066 -0.2877914320 -0.3929697057 -0.9088454366 -0.2928607321
##  [896]  0.2451717027  0.4813905001 -0.4083924408 -0.9337652878 -1.0986795936
##  [901] -0.0267117360  0.3699302415 -0.3865908398 -0.7562178680  0.0460452897
##  [906]  0.5260238697  0.8476148691  0.5385135105 -0.3693797739  0.2567373737
##  [911]  0.0750345226 -0.0422028953 -0.4897108261 -0.0196927453 -0.1492843813
##  [916]  0.8286100282  0.4977671869 -0.4494423728  0.6794774820  0.7292381470
##  [921] -0.1572426477 -0.9113820455  0.1160083549 -0.0099783101  0.0629221175
##  [926]  0.2487018464 -0.1213073295  0.1860237700  0.0872494040  0.2987896679
##  [931]  0.1053074090 -0.9174078774 -0.9332730348 -0.9353731124  0.3221932198
##  [936] -0.9107464848  0.4913367127 -1.5236054507  0.1777835763 -1.5241598414
##  [941] -0.3360245753  0.6283977814 -0.7343379698  0.2754340916 -0.4357543746
##  [946]  0.4538493617 -0.1677775187  0.0381457336 -0.2743593260 -0.1004443738
##  [951]  0.2983320282 -1.4692693447 -1.5162515491  0.2615957121  0.5184752466
##  [956] -0.2392552105 -0.7428771832  1.1275879159  0.5422275456 -0.2115481829
##  [961] -1.2128308497  1.0706874687  0.5990999598  0.0678536314  0.0210116144
##  [966]  0.3167277294  0.2302913977  0.3681734719  1.0541396490  0.1296146225
##  [971] -1.7229991524 -0.6647666103 -0.4222713477 -1.2899223785  0.0600031122
##  [976]  0.0072955563 -0.1055901997  0.5454502776 -0.5381688748  0.0570835777
##  [981] -0.1384608725  0.5367086814 -0.2281110619  0.6467490149  0.6019430291
##  [986]  0.0265777510 -0.2948345309  0.7602664775  0.3512348776  0.7114317656
##  [991] -0.2116097913 -0.2403906849 -0.5313674442  0.6733010412  0.5130814202
##  [996]  0.3251556673 -0.3671212034  0.4052520851  0.0143067899  0.8261956168
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
##   0.66347086   0.32617482 
##  (0.10314553) (0.07293199)
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
## [1]  1.2224858  0.5199743 -0.3531594  0.4445434 -0.2657403  0.4696928
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
## [1] 0.0345
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.8725716
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
## t1*      4.5 0.05705706   0.8910869
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 1 2 3 4 5 6 7 8 
## 1 1 1 2 1 1 1 2
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
## [1] -0.0233
```

```r
se.boot
```

```
## [1] 0.9144306
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

