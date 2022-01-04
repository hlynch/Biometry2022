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
## 2 4 5 6 7 8 9 
## 1 1 3 1 1 1 2
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
## [1] 0.0142
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
## [1] 2.754097
```

```r
UL.boot
```

```
## [1] 6.274303
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##  2.5% 97.5% 
##   2.7   6.3
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
##    [1] 5.4 3.9 5.4 3.9 3.2 3.4 4.7 4.3 2.8 4.0 4.3 4.4 2.9 5.2 4.7 5.3 3.9 5.3
##   [19] 3.5 5.4 6.1 4.3 5.6 3.8 5.1 4.1 3.7 4.5 4.2 3.6 4.4 4.5 5.0 3.7 3.6 7.0
##   [37] 6.0 4.3 5.9 4.6 4.1 5.0 4.1 3.4 4.0 4.5 4.9 3.7 5.7 3.5 3.6 3.6 3.0 2.6
##   [55] 5.3 4.9 3.0 4.9 3.7 5.4 4.9 5.5 4.4 3.4 5.3 4.7 5.5 4.6 4.6 5.4 4.2 5.9
##   [73] 4.4 3.7 4.1 4.2 5.2 3.3 3.7 3.6 5.7 3.7 4.8 4.1 3.5 4.7 4.5 5.5 5.4 5.1
##   [91] 4.7 4.7 2.8 5.5 5.3 5.2 3.2 5.5 3.8 5.5 4.3 4.5 2.9 5.2 4.5 4.4 5.1 5.2
##  [109] 4.1 4.6 5.8 3.1 3.6 4.9 4.9 4.6 5.4 2.5 4.4 5.0 4.5 4.5 3.8 2.9 6.4 3.8
##  [127] 3.8 4.3 4.0 5.0 3.1 4.1 4.3 4.2 5.6 4.4 4.7 3.8 4.0 4.3 5.7 3.7 3.7 5.3
##  [145] 3.9 5.1 4.7 5.0 4.5 4.0 5.4 3.8 4.8 3.0 4.6 4.3 3.5 4.2 4.2 4.7 5.8 4.6
##  [163] 5.6 5.2 3.9 5.0 3.7 4.5 4.5 6.0 3.4 4.6 6.4 4.7 5.2 4.3 4.1 5.1 4.0 3.6
##  [181] 3.6 6.0 6.0 3.5 3.6 4.4 4.4 2.1 3.9 4.4 4.9 5.0 4.1 4.9 3.8 5.0 5.9 5.1
##  [199] 4.6 4.2 5.0 5.4 5.5 6.3 5.0 5.0 4.0 2.9 4.7 4.2 3.6 4.2 4.3 4.5 6.5 4.5
##  [217] 5.2 2.7 4.9 5.0 4.0 3.0 5.6 6.1 4.9 4.9 4.7 5.2 5.0 5.6 5.1 4.4 4.2 4.3
##  [235] 5.6 3.3 4.7 3.0 4.3 4.8 4.2 4.7 4.2 4.3 4.2 4.3 4.7 4.8 5.4 3.5 3.7 4.1
##  [253] 4.9 6.0 4.6 4.9 3.6 5.2 4.6 5.5 4.0 2.7 4.1 3.3 5.4 4.8 5.2 5.7 3.7 3.7
##  [271] 4.5 6.0 4.5 3.6 3.7 4.8 4.7 4.7 4.9 4.9 3.7 4.7 6.8 4.0 5.9 3.4 3.5 5.1
##  [289] 3.9 3.5 3.9 3.6 4.9 3.8 5.5 3.1 4.5 4.4 4.2 3.2 2.7 4.9 5.5 5.6 6.3 5.5
##  [307] 4.5 5.8 4.5 4.3 4.2 4.6 3.0 4.2 4.7 3.3 5.6 3.7 2.6 4.5 5.3 5.1 4.7 4.2
##  [325] 5.6 5.8 3.3 4.3 4.2 5.7 5.0 4.1 5.6 5.6 2.7 3.6 4.0 4.9 4.2 5.5 4.7 4.4
##  [343] 3.8 3.8 5.7 3.5 4.5 3.7 5.0 3.8 3.5 3.3 3.6 4.6 4.5 5.6 5.2 3.4 4.7 5.5
##  [361] 5.2 3.6 4.6 4.9 3.9 4.2 3.5 3.2 2.9 4.0 4.5 5.1 4.0 4.5 5.1 6.7 4.4 5.3
##  [379] 6.2 5.4 4.5 4.5 5.0 4.5 5.3 4.5 5.2 4.7 4.4 6.4 4.5 3.6 5.1 6.2 4.9 4.6
##  [397] 3.0 3.2 3.8 3.4 4.2 4.7 5.4 4.7 3.4 3.1 4.3 4.7 4.7 4.3 4.1 3.4 3.3 4.9
##  [415] 5.0 3.9 5.0 3.2 3.5 4.4 4.0 3.9 4.6 5.3 4.8 4.7 5.2 3.9 4.6 1.9 5.4 5.5
##  [433] 4.2 5.5 4.4 5.8 4.2 5.5 4.3 4.4 5.9 5.6 4.4 3.2 3.7 6.0 3.7 3.9 5.6 4.0
##  [451] 5.3 5.1 3.9 4.2 4.5 2.5 4.7 6.0 4.5 4.8 4.2 5.0 3.9 5.2 5.1 5.6 5.8 5.4
##  [469] 5.4 2.8 4.5 3.6 4.3 4.4 5.1 3.8 5.2 4.7 4.5 4.0 3.5 3.3 5.7 5.1 4.5 6.0
##  [487] 3.7 3.8 3.8 4.3 6.0 3.7 5.2 4.3 4.0 5.1 6.2 4.7 3.9 4.1 5.2 4.9 6.3 3.4
##  [505] 3.4 5.6 6.4 5.8 4.2 4.0 4.5 3.6 4.0 3.7 5.6 3.7 3.8 3.2 4.7 4.2 5.9 5.4
##  [523] 3.7 4.3 6.0 4.2 4.9 3.9 5.1 4.5 3.4 3.7 5.2 3.8 5.7 3.7 5.2 5.4 5.5 3.5
##  [541] 5.1 4.6 4.1 4.4 3.1 5.8 4.1 3.5 5.1 4.8 3.8 4.2 4.5 3.7 6.3 2.8 2.4 5.8
##  [559] 5.1 5.7 2.9 3.3 3.7 3.4 2.8 4.4 4.4 4.2 4.4 3.7 3.3 4.1 2.6 3.5 2.8 2.8
##  [577] 5.3 2.8 6.1 5.1 4.7 4.6 5.9 5.5 4.6 4.0 2.8 4.6 4.4 4.0 4.0 5.5 5.1 5.4
##  [595] 3.9 3.9 3.4 5.7 4.7 4.8 6.0 3.8 4.6 5.2 5.6 4.8 4.0 4.4 4.5 4.3 5.1 3.7
##  [613] 5.3 4.6 3.9 5.2 3.0 3.2 5.6 3.5 4.2 3.6 5.1 4.6 4.8 3.6 4.2 5.1 4.9 4.6
##  [631] 4.7 6.2 4.4 4.3 6.2 5.0 4.0 2.7 5.3 4.4 3.6 3.9 3.3 3.1 3.4 4.4 3.4 3.7
##  [649] 6.5 3.6 5.2 5.5 5.1 6.9 4.1 4.3 4.0 3.3 4.9 4.1 4.7 4.4 4.8 4.3 5.0 4.4
##  [667] 4.1 2.7 4.3 4.8 5.7 5.9 4.5 5.0 4.0 5.6 3.3 6.5 3.0 6.4 4.3 5.5 4.6 3.1
##  [685] 4.9 3.9 3.4 2.5 3.1 4.6 5.0 4.6 3.3 6.6 4.6 4.8 3.8 4.2 3.9 4.6 4.5 4.2
##  [703] 2.8 4.3 3.8 4.1 3.9 3.8 3.9 4.8 3.9 5.7 4.7 5.3 5.8 4.1 6.2 3.5 5.4 4.5
##  [721] 3.1 3.5 4.9 5.5 3.2 3.0 3.5 5.4 4.3 5.7 4.7 5.9 2.8 4.0 4.6 5.0 3.6 4.9
##  [739] 4.2 5.4 3.7 4.6 4.7 4.7 4.1 4.8 4.7 3.8 4.9 3.0 4.7 4.7 4.5 2.2 3.2 5.7
##  [757] 5.0 5.7 4.8 5.5 4.7 4.9 3.4 5.5 4.0 4.3 2.9 4.5 4.6 3.6 4.5 4.5 3.6 5.0
##  [775] 2.7 5.7 5.0 4.1 4.5 5.2 5.0 5.4 3.5 4.9 6.4 4.2 5.5 4.6 4.1 5.3 4.4 4.0
##  [793] 2.7 4.8 4.3 3.1 3.7 4.5 3.7 3.7 4.7 3.7 5.5 4.2 5.1 3.8 4.7 3.9 2.2 4.6
##  [811] 4.0 4.0 5.3 7.0 4.6 4.9 4.8 2.7 4.7 5.5 6.8 3.8 4.5 4.9 5.9 5.6 4.0 5.0
##  [829] 4.4 3.6 4.3 5.8 5.4 4.9 5.7 4.2 3.4 4.5 3.6 5.6 5.5 4.0 4.8 3.9 4.1 4.5
##  [847] 3.7 4.8 5.0 5.1 4.9 3.8 4.5 4.1 4.6 3.5 3.7 3.8 4.4 6.3 4.1 4.9 2.7 3.9
##  [865] 3.4 3.7 3.2 4.7 6.1 3.8 4.2 3.3 4.4 4.0 3.3 4.0 5.9 3.1 6.2 5.4 3.8 3.6
##  [883] 5.2 4.5 4.6 5.6 4.5 5.1 3.9 5.6 5.1 3.8 4.3 4.2 4.1 3.7 5.6 3.2 3.2 3.6
##  [901] 3.0 6.0 4.8 3.5 4.4 4.6 4.9 5.3 4.3 5.1 3.8 3.2 5.4 5.4 5.2 4.2 4.0 4.2
##  [919] 3.7 4.0 4.9 5.2 4.9 4.4 3.5 4.0 4.2 4.4 4.1 5.3 3.8 4.9 4.4 4.0 2.5 6.0
##  [937] 4.8 2.9 5.1 3.7 5.0 4.5 5.3 2.9 3.2 4.2 3.4 4.9 4.7 4.2 6.1 5.5 5.4 5.6
##  [955] 3.6 5.3 5.6 4.6 5.7 4.5 3.9 5.7 5.1 4.7 2.8 3.9 5.0 3.4 5.4 4.9 3.9 4.4
##  [973] 3.6 5.3 4.3 3.3 4.1 4.3 3.4 4.8 6.0 2.6 5.1 3.0 2.8 4.3 3.2 3.4 4.2 4.6
##  [991] 5.7 3.0 4.9 5.8 3.8 4.6 4.8 5.6 4.8 3.7
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
##    [1] 4.2 3.9 4.3 5.5 5.8 3.1 4.4 3.5 4.2 3.5 4.9 5.2 5.3 4.7 3.9 3.8 3.3 3.4
##   [19] 3.7 4.7 4.6 4.0 4.9 3.7 3.7 4.9 4.4 6.0 6.2 4.4 5.8 4.8 5.2 3.8 4.2 5.2
##   [37] 3.8 5.6 6.0 3.6 4.2 4.4 5.0 4.2 3.2 6.8 3.5 4.8 4.6 3.7 5.3 2.8 5.0 3.8
##   [55] 5.0 5.6 5.2 4.7 3.2 3.9 4.5 5.1 4.3 4.3 5.0 5.7 4.8 3.2 4.8 4.5 3.3 4.0
##   [73] 3.8 5.5 4.9 3.4 5.1 4.6 5.1 3.6 3.8 4.4 4.5 5.0 5.8 6.0 3.9 4.9 4.7 2.1
##   [91] 3.3 4.0 3.5 5.3 4.0 4.7 4.3 4.3 4.5 4.3 2.9 5.1 4.6 5.2 4.8 5.6 3.9 4.7
##  [109] 4.4 4.3 5.5 4.3 3.8 4.1 4.4 5.4 4.5 4.6 3.7 3.0 4.2 4.2 3.3 4.2 4.5 5.4
##  [127] 4.2 5.6 6.8 4.5 5.2 4.3 3.5 6.2 4.5 3.4 4.0 5.9 4.5 4.5 4.1 4.4 4.0 4.9
##  [145] 5.1 2.7 5.5 5.3 5.1 4.8 4.2 2.8 3.9 5.4 3.8 3.9 3.7 3.6 4.5 4.7 5.0 2.7
##  [163] 3.9 4.5 3.8 4.5 4.6 2.9 5.1 3.4 4.4 4.7 3.5 4.1 4.3 3.6 4.5 3.3 4.1 4.1
##  [181] 5.0 4.2 5.4 4.5 5.0 5.5 4.8 6.6 5.6 4.4 3.6 6.5 5.0 5.6 4.0 4.2 4.3 4.5
##  [199] 5.0 4.2 3.0 4.3 4.3 3.5 4.2 3.3 6.7 4.6 4.6 4.7 4.9 3.6 5.8 4.8 6.0 5.9
##  [217] 5.2 3.9 4.3 3.8 4.9 3.9 5.0 3.6 4.1 4.5 2.7 4.1 3.5 4.4 5.1 3.6 4.4 3.7
##  [235] 4.8 5.8 5.2 6.7 4.5 3.8 7.3 4.6 4.7 4.5 4.3 3.9 5.3 4.3 5.2 5.7 4.4 3.6
##  [253] 5.1 3.9 3.1 5.0 5.3 3.9 4.7 4.3 4.1 4.3 5.2 2.7 5.2 4.4 4.3 4.9 5.2 4.1
##  [271] 5.4 4.2 4.0 4.9 4.5 4.5 4.9 5.4 3.5 3.0 3.1 6.1 4.0 5.9 2.6 3.3 5.3 4.1
##  [289] 3.3 3.9 4.2 4.1 6.2 4.3 3.9 4.8 5.1 4.8 2.7 5.2 5.4 5.4 3.6 4.1 5.4 5.0
##  [307] 4.5 5.3 4.6 4.1 4.0 4.0 4.9 3.0 4.4 4.5 4.8 5.6 4.0 4.7 5.5 4.3 3.5 4.6
##  [325] 5.7 4.6 3.2 4.1 3.1 4.6 3.7 5.3 4.8 6.1 5.5 3.6 4.8 4.4 3.5 4.9 3.3 4.6
##  [343] 4.8 3.3 5.3 5.0 3.6 4.5 5.0 5.7 3.2 6.8 4.3 4.0 4.7 4.0 4.9 3.6 5.3 4.5
##  [361] 4.7 4.2 5.0 3.2 4.5 4.9 4.1 3.9 5.6 5.3 4.6 3.0 4.7 4.2 3.9 4.1 5.0 4.5
##  [379] 5.2 5.4 4.5 4.4 5.2 3.7 6.5 4.4 5.5 5.5 2.6 5.4 3.1 4.4 5.3 3.9 3.2 4.3
##  [397] 2.3 4.2 4.4 6.6 4.3 5.2 4.4 4.0 5.2 4.3 5.1 5.0 3.1 5.0 4.8 4.7 3.6 5.0
##  [415] 6.2 4.4 3.8 4.1 4.6 3.8 4.6 4.3 3.7 5.0 5.0 2.9 6.7 5.1 3.9 3.3 5.5 4.6
##  [433] 5.9 6.5 3.7 5.3 6.3 4.4 3.9 4.1 2.8 5.5 3.5 3.9 5.5 3.9 3.0 5.2 4.1 4.2
##  [451] 4.3 5.6 5.1 4.0 3.8 4.7 4.7 4.2 3.4 5.6 4.2 5.6 6.2 5.5 5.2 5.6 5.0 5.8
##  [469] 5.0 3.6 3.7 3.4 4.7 2.7 3.8 4.8 5.0 5.3 5.3 4.3 4.4 4.2 5.1 5.6 4.4 5.1
##  [487] 3.6 5.0 4.4 2.8 5.1 3.4 2.6 5.1 4.4 6.2 4.6 5.0 4.1 4.2 4.5 3.2 4.7 5.0
##  [505] 4.3 4.6 4.6 4.6 2.7 5.3 5.7 4.5 3.9 4.2 3.8 5.1 6.4 4.2 4.5 5.0 5.1 3.7
##  [523] 4.6 5.9 5.6 4.1 5.6 4.6 4.8 4.7 3.2 5.1 4.4 4.3 4.5 5.4 5.3 4.5 5.4 5.8
##  [541] 5.3 5.5 4.1 4.0 5.4 4.0 5.6 6.1 4.3 4.4 4.6 5.0 5.8 5.1 5.9 4.9 4.3 3.5
##  [559] 5.2 3.4 4.2 4.4 4.0 5.9 4.8 4.2 4.0 4.3 5.5 3.7 5.2 4.8 4.5 5.0 6.2 3.7
##  [577] 5.6 4.8 5.3 4.9 5.0 5.7 4.8 5.5 3.9 5.4 4.5 6.5 5.2 5.6 6.8 2.7 3.5 5.5
##  [595] 4.0 3.5 5.7 4.9 4.0 5.0 4.3 4.2 3.1 5.9 5.7 5.2 6.0 5.0 3.5 3.6 3.4 5.6
##  [613] 2.5 5.0 5.1 4.6 5.6 4.8 4.1 4.1 6.1 3.5 4.1 4.1 4.2 4.0 3.7 5.9 3.2 3.3
##  [631] 4.6 5.9 5.7 3.8 4.9 5.7 2.8 5.1 5.1 5.6 5.4 3.9 4.0 3.6 2.9 4.3 4.0 5.4
##  [649] 4.0 4.4 5.4 4.8 4.9 4.4 5.0 3.3 3.2 5.8 5.2 6.0 4.7 5.9 4.1 5.1 5.7 5.0
##  [667] 6.2 3.4 3.3 3.7 4.7 4.8 5.9 5.6 3.4 4.0 6.1 3.6 4.2 5.0 6.2 4.3 5.1 4.1
##  [685] 4.4 3.8 5.3 5.1 2.7 3.4 4.4 4.7 4.9 5.0 4.5 3.9 2.7 4.7 4.2 3.6 6.0 5.3
##  [703] 4.6 5.2 3.4 5.0 2.9 2.2 6.2 5.7 5.6 5.0 5.2 3.7 3.7 5.3 4.3 3.7 3.8 3.1
##  [721] 5.3 5.1 4.6 4.4 3.5 5.5 4.5 5.0 3.8 3.7 4.3 3.6 5.0 4.6 5.7 3.9 4.2 4.1
##  [739] 4.2 3.9 4.3 3.4 3.4 2.7 5.0 4.3 6.4 4.2 3.7 4.4 3.4 4.3 4.3 5.2 3.7 3.4
##  [757] 4.2 4.7 4.4 6.4 4.3 4.0 3.2 3.5 3.9 3.8 4.8 3.2 5.0 4.0 5.1 5.6 5.0 5.1
##  [775] 3.5 6.7 2.7 3.9 4.0 5.5 5.6 4.6 4.9 3.8 3.2 3.0 3.7 6.0 5.3 4.5 3.5 5.3
##  [793] 4.2 5.2 4.4 3.1 2.7 5.1 5.5 4.3 3.6 4.6 3.4 4.3 2.6 4.9 3.4 4.3 5.4 5.0
##  [811] 6.2 4.4 4.0 4.1 4.9 4.8 3.3 4.8 5.7 2.7 5.6 3.8 5.2 5.9 5.3 3.1 4.6 4.6
##  [829] 5.6 3.5 4.4 4.3 3.5 3.8 3.4 3.7 6.2 4.6 5.8 4.5 5.6 4.8 4.5 5.6 5.0 2.2
##  [847] 5.1 5.0 5.2 5.6 4.3 4.7 4.7 5.9 4.7 5.0 4.0 2.9 6.3 5.8 3.5 4.0 5.0 6.1
##  [865] 2.8 3.3 4.2 6.7 4.6 3.9 4.6 3.8 5.5 2.7 4.4 4.3 5.0 3.0 4.1 2.4 5.4 4.7
##  [883] 4.0 3.7 5.7 5.4 4.9 5.2 4.1 4.3 5.5 6.6 5.4 4.1 3.1 3.2 3.5 3.0 6.1 5.2
##  [901] 5.1 6.1 4.8 4.7 5.8 3.8 4.3 3.6 4.3 3.7 4.7 5.3 5.0 5.2 4.4 3.4 3.6 4.3
##  [919] 3.1 3.3 3.9 4.9 4.3 5.2 5.2 4.0 5.1 5.4 3.8 6.4 3.6 2.7 4.2 5.2 4.6 3.9
##  [937] 4.9 6.1 5.5 4.8 4.5 4.6 6.4 4.5 4.2 6.3 4.7 6.1 3.4 6.2 2.4 5.4 3.8 3.6
##  [955] 3.5 4.1 3.4 3.7 4.5 3.5 4.4 3.4 5.1 5.1 3.7 4.7 4.3 3.7 4.5 3.7 5.0 3.8
##  [973] 5.7 3.8 4.7 5.4 4.8 5.7 5.3 2.9 4.2 3.0 4.0 3.9 4.3 4.8 4.3 2.7 5.0 4.8
##  [991] 4.4 5.6 4.3 4.4 4.4 3.7 6.0 4.5 4.2 4.8
## 
## $func.thetastar
## [1] 0.0178
## 
## $jack.boot.val
##  [1]  0.49543011  0.41849711  0.33605016  0.12036554  0.09056047 -0.07369863
##  [7] -0.13607038 -0.22753623 -0.38208955 -0.42127660
## 
## $jack.boot.se
## [1] 0.9249274
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
##    [1] 4.0 5.8 5.0 5.1 4.8 4.6 3.7 3.9 3.1 4.7 3.5 4.4 3.4 4.3 3.7 4.9 3.8 2.3
##   [19] 4.3 4.9 4.5 4.4 6.2 5.4 3.1 4.8 5.5 4.9 5.4 5.2 3.5 5.0 6.3 5.0 4.6 3.9
##   [37] 3.7 4.2 3.6 3.9 5.3 6.4 4.5 5.5 3.3 5.2 4.7 3.5 4.6 5.6 4.3 4.0 4.4 6.0
##   [55] 4.4 2.7 3.9 3.5 4.3 4.4 5.0 4.3 3.9 4.7 3.6 6.3 4.8 4.5 3.3 5.8 4.8 2.5
##   [73] 3.9 5.1 5.2 4.4 3.6 4.5 6.1 4.8 4.9 3.5 4.2 4.4 6.7 4.6 5.1 5.2 4.7 3.5
##   [91] 4.8 5.9 4.7 4.5 5.0 4.9 3.5 5.5 5.2 4.5 4.4 4.0 3.7 5.3 6.1 3.3 5.8 2.8
##  [109] 4.4 3.8 3.0 4.2 4.6 6.2 4.8 4.0 5.5 5.3 4.2 3.6 4.0 3.7 3.6 6.8 4.6 5.3
##  [127] 4.3 4.3 3.5 4.5 4.6 5.2 5.0 4.6 3.4 3.6 5.3 2.5 7.3 3.7 6.4 5.1 5.5 4.8
##  [145] 4.2 3.8 3.9 6.2 2.7 4.4 4.2 5.5 5.7 2.3 4.3 2.3 4.5 3.5 5.0 2.9 5.0 5.8
##  [163] 5.1 2.9 5.2 3.5 4.7 5.3 6.7 5.0 4.5 4.4 6.3 5.7 5.7 4.3 4.6 6.5 4.6 4.3
##  [181] 4.5 3.7 5.2 4.9 3.7 2.8 4.9 6.4 4.4 4.5 4.0 5.1 5.6 4.8 5.8 4.8 5.5 3.6
##  [199] 4.2 4.2 1.9 4.4 4.5 5.7 3.8 4.8 3.8 5.4 4.8 4.8 3.8 5.3 4.8 4.8 3.6 4.2
##  [217] 4.7 3.9 2.2 4.5 4.3 4.9 5.3 5.4 3.5 2.3 4.0 5.7 3.7 4.7 4.4 3.5 3.4 4.1
##  [235] 2.6 5.7 3.0 4.5 4.9 3.5 6.8 2.8 3.1 4.6 4.7 2.3 6.1 4.1 5.5 4.0 3.7 2.5
##  [253] 5.4 3.8 4.2 4.5 3.3 4.3 3.2 5.9 5.8 4.3 5.4 4.9 3.8 4.0 3.6 4.2 4.7 5.2
##  [271] 6.0 2.7 4.8 4.6 5.6 5.2 3.2 3.3 4.1 5.2 5.3 3.4 4.7 5.7 5.6 3.9 5.7 3.4
##  [289] 6.0 4.8 6.3 4.4 3.8 4.3 4.5 4.3 4.1 6.7 4.4 2.5 4.4 4.8 5.1 3.3 2.9 4.5
##  [307] 4.6 4.2 6.5 2.6 4.5 4.8 4.0 5.3 4.1 5.2 3.9 3.5 4.3 5.7 5.8 4.1 4.6 3.3
##  [325] 3.5 4.6 3.6 5.8 5.1 4.7 5.4 4.4 4.4 4.1 5.0 5.0 4.9 3.7 2.8 3.4 2.2 5.0
##  [343] 5.6 6.1 2.8 5.2 4.6 3.5 5.2 4.4 4.3 3.1 5.5 5.2 5.1 4.2 5.0 4.3 4.5 3.9
##  [361] 3.4 4.2 5.2 5.2 5.0 4.6 4.9 4.0 4.4 4.6 5.4 5.2 5.3 2.7 4.8 5.3 3.4 5.4
##  [379] 5.3 4.7 3.9 4.6 5.4 5.2 5.1 5.1 4.4 4.4 3.3 4.8 3.6 5.8 5.6 5.0 2.9 4.5
##  [397] 4.1 5.6 5.0 5.3 2.7 5.4 4.8 4.5 4.4 4.2 3.2 3.8 4.9 4.5 2.8 4.6 4.3 4.9
##  [415] 4.6 4.8 4.5 5.7 5.5 3.5 4.1 4.7 5.8 3.2 4.7 4.6 3.8 4.1 4.0 4.8 4.9 2.2
##  [433] 3.9 4.1 3.5 4.6 3.5 4.9 5.9 4.4 4.4 4.2 5.1 5.2 5.3 5.2 3.4 4.1 5.3 4.5
##  [451] 4.6 3.3 5.4 3.7 3.6 4.3 4.0 5.3 5.3 4.7 3.6 2.6 5.5 3.7 5.1 4.1 4.4 4.7
##  [469] 4.1 3.6 6.2 4.4 5.3 5.8 5.4 6.0 4.9 3.6 4.5 5.1 4.3 3.1 3.1 5.2 4.2 3.8
##  [487] 5.3 5.2 3.9 4.9 3.5 4.8 5.1 4.0 4.2 4.5 5.3 5.0 4.0 5.9 3.5 5.8 5.6 4.0
##  [505] 4.6 4.9 4.9 2.5 2.9 5.9 5.6 4.6 5.1 5.5 4.7 4.6 6.1 4.7 4.7 3.3 3.8 4.5
##  [523] 4.0 3.6 2.7 4.2 3.9 3.8 3.3 5.3 2.9 5.8 5.4 4.8 5.0 5.1 4.9 4.9 3.1 3.8
##  [541] 4.6 4.1 6.1 3.6 3.7 5.5 4.8 4.3 5.2 4.5 3.5 4.7 4.4 5.3 4.9 5.7 3.5 5.4
##  [559] 4.7 4.6 5.5 3.9 3.4 5.8 3.7 4.9 4.7 4.8 4.5 6.0 2.5 5.4 4.6 5.5 5.4 5.2
##  [577] 6.3 4.2 4.6 3.6 3.4 3.6 3.6 4.5 4.8 4.2 5.5 4.1 3.4 3.9 5.8 5.2 4.5 4.6
##  [595] 4.5 5.7 5.8 5.9 3.1 6.2 3.6 4.9 6.6 3.8 4.1 2.6 4.4 2.4 4.2 2.6 4.0 2.9
##  [613] 5.3 3.9 5.7 2.5 4.2 3.7 5.8 3.9 4.5 5.6 4.2 5.2 4.8 2.8 4.2 5.0 3.9 3.6
##  [631] 4.6 2.8 5.7 3.4 4.0 2.9 5.6 2.8 5.2 3.3 4.1 3.8 4.3 5.1 4.9 3.6 5.1 3.9
##  [649] 4.5 6.4 4.9 3.0 5.2 3.9 4.7 5.4 5.4 5.8 5.4 5.3 4.8 5.9 5.0 4.6 4.6 6.5
##  [667] 4.5 5.7 4.7 5.1 5.5 4.8 5.0 3.9 3.9 5.0 4.4 4.9 4.8 6.2 3.4 3.9 5.0 4.6
##  [685] 3.6 5.3 3.7 6.3 5.2 3.5 5.4 5.7 5.3 5.3 4.2 3.7 4.4 4.3 6.3 4.7 4.5 5.7
##  [703] 5.4 5.1 4.8 4.5 5.4 5.1 5.4 3.8 4.0 5.8 3.0 3.2 5.0 5.5 5.5 3.1 4.4 5.7
##  [721] 5.7 4.3 3.8 4.6 6.0 5.7 4.4 4.3 5.4 4.8 4.1 5.7 3.6 4.3 3.8 2.9 4.4 5.6
##  [739] 5.0 4.1 4.4 5.6 4.9 4.2 4.1 3.0 4.8 4.0 5.4 3.3 5.2 3.8 4.1 4.4 4.9 5.8
##  [757] 3.9 5.4 4.3 5.9 4.2 5.8 5.0 3.6 4.4 4.6 3.9 3.7 4.5 4.5 3.0 4.9 3.7 3.5
##  [775] 4.2 4.9 3.8 3.5 5.2 4.8 3.8 4.7 3.8 6.3 3.8 4.9 5.6 4.7 7.3 3.2 6.6 3.6
##  [793] 4.3 4.3 4.2 4.7 4.6 3.8 4.8 4.4 4.7 3.4 4.3 5.3 5.8 5.3 4.6 5.1 4.5 3.8
##  [811] 5.2 2.7 4.5 5.4 5.8 3.8 4.1 5.3 4.8 4.6 4.3 3.4 4.9 4.3 4.2 6.7 3.4 5.6
##  [829] 4.3 4.1 3.2 4.0 4.9 5.1 4.5 4.8 4.1 5.5 4.3 3.8 3.6 5.7 5.5 3.1 3.6 3.7
##  [847] 4.9 5.3 4.5 4.5 3.8 6.3 4.2 5.5 4.9 4.3 4.8 3.4 4.0 5.4 5.9 4.8 6.2 4.3
##  [865] 5.4 4.4 4.0 5.1 4.3 5.2 4.1 4.3 4.5 3.5 4.9 2.6 4.5 4.2 5.5 7.0 4.5 3.5
##  [883] 4.2 4.5 4.3 4.6 3.0 4.6 3.8 5.5 5.5 4.3 4.9 3.4 4.0 4.9 3.9 3.4 2.7 5.8
##  [901] 3.6 4.1 4.6 4.4 5.2 4.5 2.8 3.3 4.2 3.5 4.8 3.7 2.3 4.8 5.0 3.9 5.2 6.3
##  [919] 5.5 6.4 4.3 2.8 4.2 3.6 4.2 5.6 4.0 4.7 5.0 5.2 4.6 4.6 5.0 6.1 4.2 4.8
##  [937] 4.2 5.4 4.6 4.3 5.9 4.3 4.2 4.8 4.1 2.8 3.7 3.7 3.4 3.7 4.7 5.2 5.8 4.1
##  [955] 4.9 5.5 5.4 5.4 4.9 2.7 4.8 4.5 4.5 3.7 5.2 4.8 4.8 4.6 3.4 3.8 4.0 4.6
##  [973] 4.3 4.5 3.1 6.2 4.6 3.0 4.8 4.5 5.3 4.5 6.0 6.1 6.3 4.8 5.5 4.7 3.7 5.0
##  [991] 6.7 5.3 4.8 5.6 3.6 4.9 5.5 5.3 4.8 5.9
## 
## $func.thetastar
## 72% 
## 5.1 
## 
## $jack.boot.val
##  [1] 5.500 5.400 5.400 5.300 5.200 5.152 5.000 4.792 4.600 4.600
## 
## $jack.boot.se
## [1] 0.9473801
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
## [1] -0.1278785
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
##   3.095685   5.136920 
##  (1.316671) (2.371921)
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
## [1]  1.8679924 -0.1126573  0.8519071  0.6166065  0.9567046  1.0366195
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
##    [1]  1.935329e-01 -2.205587e-02 -2.603648e-01 -2.589946e-01  6.292652e-02
##    [6]  3.107780e-01  3.512676e-02 -4.045244e-01  2.391203e-01 -5.707987e-01
##   [11] -5.051752e-01 -8.506926e-01 -6.673880e-01  6.243257e-01  4.275011e-01
##   [16] -3.645137e-01 -5.713846e-01 -1.085368e+00  1.735945e+00  4.903653e-01
##   [21]  4.246847e-01 -2.942157e-01  1.258906e-01 -5.010240e-01  4.285415e-01
##   [26] -5.262527e-01  1.652327e-02 -4.442814e-01  2.900692e-01 -5.647948e-01
##   [31]  4.563159e-01  4.455897e-01 -5.171388e-01 -1.898721e-01  1.251400e-01
##   [36] -1.767702e-02  1.843509e-02  4.575628e-02  8.298875e-01 -3.290990e-01
##   [41]  1.118654e+00  4.113976e-01  1.626966e-01  5.428636e-02 -4.255000e-02
##   [46]  2.129487e-01 -5.011407e-01 -1.771427e-01 -3.777040e-01  4.237996e-01
##   [51] -1.018712e-01  2.020967e+00 -9.000628e-01  1.565078e-02 -8.207587e-01
##   [56] -3.688994e-01 -2.788217e-01 -2.813306e-01 -4.572812e-01 -1.517438e-01
##   [61]  5.064409e-02 -4.794816e-01 -9.318310e-01  5.606377e-02 -2.683587e-02
##   [66] -3.963339e-01 -7.891285e-02 -1.453913e-01  4.313007e-01 -2.349081e-01
##   [71] -2.212462e-02 -1.516588e-01 -2.165808e-01 -1.037729e+00 -6.868033e-01
##   [76] -2.380074e-01 -4.426647e-01 -1.277707e-01 -9.496314e-01  3.049781e-01
##   [81]  3.141648e-02 -3.033130e-01  8.877395e-01 -3.015399e-01  3.928000e-01
##   [86] -5.395255e-01  8.706237e-03 -7.706084e-02 -2.476697e-01 -2.922193e-01
##   [91]  6.127765e-01 -6.203412e-01 -1.967582e-01  4.735029e-02  4.230334e-01
##   [96] -1.114956e+00 -5.476801e-01 -6.433378e-01 -3.341475e-01 -1.761413e-02
##  [101]  5.963520e-02 -2.923164e-01  1.028124e-01  2.648553e-01 -2.668791e-01
##  [106] -2.558696e-01 -1.096314e-01  7.623820e-02 -2.100407e-01  2.905011e-01
##  [111] -6.605813e-02 -8.694334e-01 -7.641794e-01 -3.645699e-01 -1.270866e+00
##  [116] -5.858974e-01 -3.092640e-01 -4.576962e-01  1.114073e-01 -5.845192e-01
##  [121] -1.429049e-01 -6.665150e-01 -5.201678e-01 -2.873048e-01 -3.925727e-02
##  [126] -2.335720e-01 -2.959939e-01  8.594289e-02  1.551185e-01 -8.519733e-01
##  [131] -4.517916e-01 -3.739416e-02 -9.351101e-01  2.277147e-01  4.351424e-01
##  [136] -1.100482e-01 -3.524222e-01  4.626021e-01 -1.878795e-01  1.228645e-02
##  [141] -2.084768e-01 -4.318621e-02  1.654812e-01 -4.932925e-01 -3.444771e-01
##  [146]  1.000805e-01  2.481027e-01 -5.396248e-01  5.521094e-01 -1.232226e+00
##  [151] -9.380495e-01 -3.775733e-01  2.578750e-01  1.313024e-01 -2.079111e-02
##  [156] -1.917842e-01 -4.223249e-01  2.814133e-01  2.608335e-01 -6.849899e-01
##  [161]  2.343898e-01 -1.529954e+00 -1.128291e+00 -2.585862e-02  2.773619e-01
##  [166] -2.297957e-02 -8.810574e-01 -2.701871e-01 -6.504035e-01 -3.240694e-01
##  [171] -9.615695e-01 -3.269921e-01  4.021086e-01  7.333014e-02  3.081242e-01
##  [176] -1.230918e-01 -1.356322e-01  1.501007e+00 -7.865718e-02  3.161469e-01
##  [181]  9.758218e-01  6.878317e-01  2.506866e-01  3.566355e-01 -3.643109e-01
##  [186] -1.479276e-01 -9.915499e-02 -9.927038e-02 -2.866074e-01 -2.693903e-01
##  [191] -5.221040e-01  5.079318e-02 -4.970233e-01 -2.295399e-01  6.744053e-01
##  [196]  1.875363e-01  2.159151e-01  4.755332e-02 -1.351982e+00 -2.640175e-02
##  [201] -1.135340e-01 -4.545613e-01  2.358214e-01 -6.900568e-01 -4.771675e-01
##  [206]  3.539636e-01  3.808951e-01  3.502639e-01 -1.181233e+00 -1.127178e+00
##  [211] -7.624790e-01  2.548985e-02  3.655085e-01 -3.771980e-01  3.636997e-01
##  [216] -2.223540e-01 -7.661781e-03 -1.173550e+00  4.786714e-03 -2.439074e-01
##  [221]  9.415599e-02  5.634634e-01 -8.106394e-01 -2.890185e-01 -5.804937e-01
##  [226] -1.301189e+00 -9.374186e-01 -9.370397e-02 -2.973492e-01 -1.375872e-01
##  [231]  6.158918e-01 -3.486479e-01 -2.005329e-01  1.599851e-01  2.929415e-01
##  [236]  1.800648e-01  2.295351e-01 -7.574925e-02 -6.572223e-01  8.575381e-01
##  [241] -1.256757e+00 -7.593412e-01 -4.245630e-01  1.000926e+00 -5.529779e-01
##  [246] -7.526774e-01 -1.342965e-01 -5.201829e-02  2.791560e-01 -1.253346e-01
##  [251] -4.029747e-01 -6.524235e-01  6.169420e-01 -9.983292e-02 -7.098139e-01
##  [256] -8.698518e-01 -1.103996e+00 -7.143576e-01  3.853895e-01 -4.824641e-01
##  [261] -9.252594e-01 -1.854229e-01 -2.082547e-01 -1.896354e-01 -1.064054e-01
##  [266] -4.588951e-01 -3.968278e-01 -3.394422e-01  9.867204e-01 -2.039967e-01
##  [271]  4.591847e-01 -6.426852e-01 -3.113825e-01 -8.285497e-02 -9.348803e-01
##  [276]  1.062899e+00 -9.339782e-02  1.947782e-02  9.557621e-02  2.760617e-01
##  [281]  4.752383e-01 -1.073815e+00 -1.153476e+00 -2.833657e-01  8.155153e-02
##  [286]  4.484332e-01  1.023617e+00 -2.028376e-01 -5.371890e-02  6.032503e-02
##  [291]  2.555057e-01 -8.084376e-01  1.242653e-01 -1.730373e-01 -1.572919e-02
##  [296]  4.250847e-01  9.557621e-02 -8.921568e-01 -1.452887e-01  4.655393e-01
##  [301]  3.117018e-01 -3.812385e-01  2.448374e-01  3.853895e-01  2.686315e-01
##  [306]  7.146942e-01  9.929063e-02  1.387377e-01 -4.767231e-01 -8.237478e-01
##  [311] -7.013290e-01  1.327741e+00 -7.926794e-02 -4.356930e-01 -5.268930e-01
##  [316] -6.023132e-01 -8.060924e-01 -6.141226e-01  4.376612e-01 -3.743137e-01
##  [321]  6.348023e-01  4.507843e-04 -5.846595e-02  2.949963e-01 -5.224221e-01
##  [326]  2.319263e-02 -1.966401e-01 -2.976536e-01  5.124585e-01  9.214791e-01
##  [331]  1.132550e+00 -1.303277e-02 -9.434302e-02 -1.517122e-01  2.117248e-02
##  [336] -6.517860e-02 -1.440070e+00 -1.430049e-01  2.334441e-01  5.231764e-01
##  [341] -2.140165e-02  2.714830e-01 -5.146588e-01  3.883420e-01  8.711630e-02
##  [346] -7.782544e-01 -1.004630e-01  3.140491e-01  1.585668e-01 -4.851178e-01
##  [351] -7.826956e-01  1.079586e-01 -4.359194e-02  2.925155e-01 -3.786950e-01
##  [356]  2.896302e-01 -7.963120e-01 -1.098662e-01  2.658720e-01 -6.583791e-02
##  [361]  1.149399e-01  3.780759e-01  5.530313e-01  3.415656e-01  3.613470e-02
##  [366]  2.809466e-01 -1.050394e-01 -1.398155e+00  5.895183e-02 -9.795854e-01
##  [371]  1.742462e-01 -4.917838e-01  2.329905e-01 -3.497300e-02 -5.875047e-01
##  [376]  4.400655e-01 -6.553219e-01 -2.615140e-02  4.344550e-01 -1.973460e-01
##  [381] -2.512051e-01 -1.073245e+00 -9.960126e-02 -1.282712e+00  5.309114e-01
##  [386]  9.669572e-01  2.099373e-02 -1.399704e+00  6.649917e-02 -7.231751e-01
##  [391]  3.958978e-01  1.310123e-01  5.428872e-01  2.060729e-01  7.019679e-01
##  [396] -2.767957e-01  4.580880e-01  4.564997e-01  3.133664e-01 -4.135758e-01
##  [401]  7.065738e-01 -6.612705e-01  9.747502e-02 -2.054257e-01 -1.007496e+00
##  [406] -6.849899e-01 -6.309778e-01  3.978634e-01 -8.346681e-02  6.966086e-01
##  [411]  1.708291e-01 -7.224934e-01  5.840547e-01 -8.010197e-01  2.478924e-01
##  [416] -2.398379e-01  1.037548e-01 -4.935147e-01 -6.229223e-02  5.102505e-01
##  [421]  4.808537e-01 -1.425827e-01  9.124906e-02  5.176109e-01  8.298713e-01
##  [426] -3.674894e-01 -2.028600e+00 -2.828486e-01 -1.425558e+00 -4.201218e-01
##  [431] -5.500661e-01 -1.639744e-01 -2.013426e-01 -6.053477e-01 -6.588720e-01
##  [436] -1.109464e+00 -5.067726e-02 -2.832130e-01 -9.484919e-02  8.356325e-01
##  [441] -7.883973e-01  2.935116e-01  2.072439e-01 -6.842864e-01  5.685091e-01
##  [446]  1.277202e-01 -4.347539e-01  1.338100e-01 -8.909519e-03  5.139514e-01
##  [451] -4.210906e-01 -5.574812e-01  5.162145e-02 -4.685809e-01 -7.238142e-01
##  [456] -8.581012e-01 -3.116739e-01 -5.557515e-01 -4.696967e-01  9.780297e-02
##  [461] -5.874162e-01 -6.744265e-01 -6.856884e-02 -1.247700e-01 -5.784467e-01
##  [466] -3.856414e-01  4.968588e-01 -5.104743e-01 -9.852608e-01 -2.738283e-01
##  [471] -2.224918e-01 -1.141360e+00  4.381566e-01 -8.870260e-01 -1.892621e-01
##  [476]  7.021507e-01  3.140053e-01 -2.639344e-01  3.767675e-01  3.539636e-01
##  [481] -1.163347e+00 -6.838027e-01  2.349040e-01 -9.830192e-02 -8.478439e-02
##  [486] -7.332732e-01  7.145524e-01 -4.654622e-01 -8.083965e-01  3.166739e-01
##  [491]  3.655710e-03 -6.530807e-01  4.621329e-01 -2.851568e-01  2.704787e-03
##  [496]  6.242195e-01  6.608549e-01 -8.326150e-01  5.221745e-01  5.000017e-01
##  [501]  4.627048e-01  9.001813e-01 -1.271623e-01 -1.724874e-01 -5.973943e-01
##  [506] -2.185676e-01 -4.239009e-01 -5.141698e-01  2.409455e-01 -8.201901e-01
##  [511]  1.447620e-01  3.113504e-01  3.637362e-01  1.054186e+00 -1.879025e+00
##  [516] -4.766154e-01 -5.846050e-02  2.750319e-01 -4.222617e-01  1.507081e-02
##  [521] -7.429788e-01 -8.838602e-01  1.521932e+00 -3.722544e-01 -2.758026e-01
##  [526] -6.020236e-01  1.047396e-02 -5.991881e-01 -2.138816e-01 -8.059170e-01
##  [531] -2.872921e-01 -6.910055e-01 -7.478345e-02 -4.857855e-01 -9.394360e-01
##  [536] -1.135871e-01 -4.140709e-01  3.160006e-01 -2.866074e-01 -6.027803e-01
##  [541] -8.631447e-01  2.861114e-01  1.485907e-01 -4.007086e-02 -2.519363e-01
##  [546]  1.032731e+00 -1.880803e+00 -9.543918e-01 -6.517860e-02 -6.798970e-01
##  [551] -3.297857e-01 -4.336765e-02 -6.762834e-04  8.170924e-01  3.838405e-01
##  [556] -1.738520e-01  1.566873e-01  8.149295e-02 -9.138600e-01 -3.944062e-01
##  [561] -3.993901e-01 -6.324362e-02  4.294595e-01 -4.450219e-01 -3.484898e-01
##  [566]  9.587967e-02 -3.041872e-01 -7.588434e-02  1.007699e-01 -3.161443e-01
##  [571] -7.930851e-01  5.026904e-02  1.879725e-01  4.481833e-01  9.747357e-03
##  [576]  2.423693e-01  1.434850e-01 -9.438303e-01 -8.990540e-02  7.764273e-01
##  [581] -2.919234e-01 -8.353043e-02  3.644415e-02 -3.355160e-01  2.199623e-01
##  [586] -3.031983e-01 -3.481384e-01  3.084105e-01 -2.528353e-01 -2.895406e-01
##  [591] -1.370669e+00  5.664370e-02 -1.942778e-01 -6.060852e-01  1.243232e-01
##  [596] -1.405835e-01 -1.143330e+00 -6.879914e-01  3.352610e-02 -5.256808e-01
##  [601] -9.795854e-01  8.107355e-02  8.373889e-01 -1.991816e+00 -9.243155e-02
##  [606] -2.497948e-03  1.445762e-01 -1.259258e+00 -1.870806e-01 -8.565734e-01
##  [611]  2.144231e-01 -1.430210e-01 -6.736491e-01 -5.248638e-01 -5.303345e-01
##  [616] -8.426957e-01 -8.988439e-01 -2.742622e-01  1.544731e-01  4.664587e-01
##  [621]  5.992149e-01  1.333569e-01 -3.963287e-01 -4.891024e-01 -5.034982e-01
##  [626]  2.917779e-01 -1.428160e-01  2.050806e-01 -3.513550e-01  4.917988e-01
##  [631]  6.081768e-01  5.645554e-02 -1.207085e-01  3.904552e-01  1.363489e-02
##  [636] -8.538178e-01  2.105476e-01 -3.864378e-01 -1.724874e-01 -7.322666e-01
##  [641] -7.545856e-01  4.712549e-02  3.301350e-01  4.246847e-01 -6.764367e-01
##  [646] -5.034776e-01  1.901570e-01  4.830618e-01  2.081947e-01  3.472157e-01
##  [651] -1.570478e+00  4.385997e-01 -5.480574e-01 -4.430556e-01 -2.215698e-01
##  [656]  1.263644e-01  1.790609e-01 -6.223452e-01 -3.124973e-01 -1.902008e-01
##  [661] -3.966494e-01 -2.882108e-01  9.994255e-02  1.192495e-01  3.603313e-01
##  [666]  5.125896e-01 -1.613391e-01  3.638400e-01 -9.976137e-01  9.114822e-01
##  [671]  1.834466e-01  1.232291e-01 -2.296543e-01 -5.871338e-01 -1.517514e-01
##  [676]  6.761149e-01 -6.822460e-01  1.571544e-01  1.417776e-01  7.892475e-01
##  [681] -8.238601e-01 -6.241158e-01 -1.830374e+00  8.511044e-02 -1.138522e+00
##  [686] -7.186928e-01 -2.054841e-01  3.467504e-01  6.284903e-02  1.795608e-01
##  [691]  4.480649e-01  3.378273e-01 -5.051613e-01 -6.820925e-01 -7.173920e-03
##  [696]  2.753155e-01  2.076986e-01  2.476025e-01 -7.553499e-01 -3.106154e-01
##  [701]  3.023585e-01 -4.674828e-01  6.763350e-05 -5.855819e-01 -1.288727e-01
##  [706]  4.835353e-01 -1.173604e+00  1.125029e+00 -6.838027e-01  3.701815e-01
##  [711]  4.563159e-01  5.453987e-01  1.497313e-01 -1.990633e+00  4.282706e-01
##  [716] -6.621227e-01 -3.438751e-02 -1.030595e+00 -6.462473e-01 -6.031646e-02
##  [721]  4.194499e-01  6.905298e-02 -1.197924e+00  8.850692e-02  5.736319e-02
##  [726] -9.876179e-02  2.023271e-01 -8.434026e-01 -3.932896e-01 -4.905021e-02
##  [731]  2.154175e-01  2.457224e-05 -3.689057e-01 -7.452376e-01 -5.396248e-01
##  [736] -1.885559e-01 -1.761508e-01  4.787665e-01 -1.192107e+00 -7.712389e-02
##  [741] -2.978786e-01  3.423175e-01 -3.527549e-01 -7.491814e-01  5.098350e-01
##  [746] -2.547807e-01 -1.874826e-01 -6.482386e-02  1.540853e-01  8.823861e-02
##  [751] -6.503412e-01 -1.310308e+00  1.759957e-01 -1.696268e+00 -9.493227e-01
##  [756] -8.091873e-01  2.058762e+00  3.378283e-02 -1.923647e-01  2.991663e-01
##  [761] -2.000736e+00  4.776706e-01 -3.672461e-01 -3.740194e-01  5.463053e-01
##  [766]  2.501187e-01 -4.716205e-01  3.393699e-02 -6.524754e-01  2.918710e-01
##  [771] -1.706831e-01 -6.396998e-04 -3.443165e-01 -1.288727e-01 -5.529779e-01
##  [776]  1.107009e-04 -2.279592e-01 -9.653167e-01  4.954031e-02 -2.334224e-01
##  [781] -3.500781e-01 -2.315924e-03 -6.514103e-02 -1.448604e+00  7.114615e-02
##  [786] -4.499489e-01 -7.981178e-01 -5.306163e-01  3.090043e-01 -3.263602e-01
##  [791] -7.953274e-01 -4.102804e-01  6.515210e-01 -3.070850e-01 -6.629621e-01
##  [796] -3.060738e-01  5.153942e-01 -5.494904e-01  7.172904e-01 -8.040399e-02
##  [801]  4.495631e-01 -3.649215e-02 -4.962034e-02 -4.015783e-01 -9.158102e-01
##  [806] -3.683004e-01 -2.686077e-01 -1.166989e+00 -5.242307e-01 -4.659381e-02
##  [811]  7.262736e-01  2.705108e-01  8.470806e-02 -4.461465e-01 -1.986311e-01
##  [816]  4.981796e-01 -7.620886e-02 -1.462640e-01 -9.470834e-01  1.157592e-01
##  [821]  5.269930e-01  1.684052e-01 -1.414111e-01  5.905159e-01 -7.250184e-01
##  [826]  4.819066e-01  6.024889e-01 -1.452677e-01 -5.696056e-01 -6.896363e-01
##  [831]  6.615352e-01  1.327706e-02  1.972723e-02 -2.759136e-01  9.698987e-02
##  [836]  6.675705e-01 -7.056325e-02 -1.212922e+00  4.508307e-01 -5.197444e-01
##  [841]  3.140187e-01 -1.028748e+00 -8.091873e-01 -4.053452e-01  1.186487e-01
##  [846] -2.968052e-02  3.469943e-01  1.020688e+00 -1.642575e-01  1.382833e-01
##  [851]  1.080572e-01  4.941589e-01 -8.345619e-01  3.236662e-01 -2.976225e-01
##  [856] -2.591731e-01  1.983638e-01 -1.916225e-01  6.485471e-01  4.998351e-01
##  [861]  1.439803e-01 -6.712139e-01 -4.304055e-01  2.836247e-01  6.779132e-02
##  [866]  3.230339e-01 -3.613091e-01 -1.259258e+00 -1.302057e+00  4.512163e-01
##  [871]  4.975569e-02  1.106398e-01 -5.859090e-02  1.196407e-01 -7.367993e-01
##  [876]  1.187023e-01  1.717967e-01  4.610225e-02  4.713126e-02 -1.812459e-01
##  [881]  7.363951e-01 -8.276865e-01  3.872512e-01  2.936412e-01 -8.763019e-01
##  [886]  3.118750e-01 -3.522863e-01 -3.366699e-01  4.296538e-01 -7.558095e-01
##  [891] -1.976789e-01 -6.192658e-01 -6.765758e-01 -1.502151e-01 -9.652595e-01
##  [896] -1.838456e+00 -6.047668e-01 -3.195282e-01 -3.483133e-01 -6.064349e-02
##  [901] -4.415069e-01 -8.046566e-01 -6.694421e-01  1.531043e-01 -2.610036e-01
##  [906]  9.371391e-01  7.296185e-01 -9.143370e-01 -1.272914e-01 -1.625748e-02
##  [911] -9.798595e-01 -2.480922e-01 -4.740164e-01 -7.615320e-01  4.324431e-01
##  [916]  3.470250e-01 -4.354969e-01 -2.712084e-01 -6.804736e-02  5.540219e-02
##  [921]  6.338016e-02 -2.547483e-01  1.720624e-01  4.751619e-02 -6.086533e-01
##  [926]  5.954700e-02  8.406124e-01  4.422212e-01  3.311260e-01 -2.513206e-01
##  [931] -3.047573e-01 -3.943812e-01  6.085697e-02 -3.651327e-01 -2.282212e-01
##  [936]  3.281285e-01 -5.342439e-01  8.348287e-01 -7.208050e-01  1.216252e+00
##  [941] -5.183828e-02  7.321991e-01 -6.762221e-01  1.072182e+00 -2.420272e-02
##  [946] -2.881071e-01 -4.815728e-01  8.931523e-01 -1.608947e-01  5.083076e-01
##  [951] -1.567656e-01  8.075273e-01 -7.885826e-01 -3.955933e-01 -4.013018e-01
##  [956]  1.018193e+00 -3.928958e-01 -1.216368e-01  1.432312e-01 -3.359870e-01
##  [961] -1.252866e-01 -1.819655e-01 -8.947969e-02 -1.686601e-01 -6.154912e-01
##  [966] -8.456246e-01  6.005345e-01 -6.896363e-01 -2.141701e-01 -7.103548e-01
##  [971] -2.626714e-02  3.793788e-01 -2.021337e-01 -6.268824e-01 -5.526987e-01
##  [976] -9.496398e-01 -7.743711e-01 -6.802624e-01 -4.376107e-01 -1.504844e-01
##  [981] -3.838079e-01  4.567881e-02  7.195051e-01 -1.117486e-01 -5.852765e-01
##  [986] -5.693521e-01  5.360234e-01 -3.053524e-01 -2.478987e-01 -5.872242e-01
##  [991]  3.703069e-01 -1.150171e+00  5.142883e-02 -5.565075e-02 -3.217615e-01
##  [996] -9.007614e-01 -3.892076e-01  7.281528e-01  5.418155e-01 -1.884640e-01
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
##   0.60263266   0.30251130 
##  (0.09566247) (0.06763963)
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
## [1] -0.32610555 -0.33714825  0.14964962 -0.03124642 -0.06452778  1.20263382
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
## [1] -0.055
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9163158
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
## t1*      4.5 0.01181181   0.9347664
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 3 7 9 
## 2 4 1 3
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
## [1] 0.0539
```

```r
se.boot
```

```
## [1] 0.9126652
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

