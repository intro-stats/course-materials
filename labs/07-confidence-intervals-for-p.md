Lab 7: Confidence Intervals for Proportions
================

In August of 2012, news outlets ranging from the [Washington
Post](http://www.washingtonpost.com/national/on-faith/poll-shows-atheism-on-the-rise-in-the-us/2012/08/13/90020fd6-e57d-11e1-9739-eef99c5fb285_story.html)
to the [Huffington
Post](http://www.huffingtonpost.com/2012/08/14/atheism-rise-religiosity-decline-in-america_n_1777031.html)
ran a story about the rise of atheism in America. The source for the
story was a poll that asked people, “Irrespective of whether you attend
a place of worship or not, would you say you are a religious person, not
a religious person or a convinced atheist?” This type of question, which
asks people to classify themselves in one way or another, is common in
polling and generates categorical data. In this lab we take a look at
the atheism survey and explore what’s at play when making inference
about population proportions using categorical data.

## Getting Started

### Load packages

In this lab we will explore the data using the `dplyr` package and
visualize it using the `ggplot2` package for data visualization. The
data can be found in the companion package for OpenIntro labs, `oilabs`.

Let’s load the packages.

``` r
library(dplyr)
library(ggplot2)
library(oilabs)
```

### The survey

The press release for the poll, conducted by WIN-Gallup International,
can be accessed
[here](https://sidmennt.is/wp-content/uploads/Gallup-International-um-tr%C3%BA-og-tr%C3%BAleysi-2012.pdf).

Take a moment to review the report then address the following questions.

1.  In the first paragraph, several key findings are reported. Do these
    percentages appear to be *sample statistics* (derived from the data
    sample) or *population parameters*? Explain your reasoning.

2.  The title of the report is “Global Index of Religiosity and
    Atheism”. To generalize the report’s findings to the global human
    population, what must we assume about the sampling method? Does that
    seem like a reasonable assumption?

### The data

Turn your attention to Table 6 (pages 14 and 15), which reports the
sample size and response percentages for all 57 countries. While this is
a useful format to summarize the data, we will base our analysis on the
original data set of individual responses to the survey. Load this data
set into R with the following command.

``` r
data(atheism)
```

3.  What does each row of Table 6 correspond to? What does each row of
    `atheism` correspond to?

To investigate the link between these two ways of organizing this data,
take a look at the estimated proportion of atheists in the United
States. Towards the bottom of Table 6, we see that this is 5%. We should
be able to come to the same number using the `atheism` data.

4.  Using the command below, create a new dataframe called `us12` that
    contains only the rows in `atheism` associated with respondents to
    the 2012 survey from the United States. Next, calculate the
    proportion of atheist responses. Does it agree with the percentage
    in Table 6? If not, why?

<!-- end list -->

``` r
us12 <- atheism %>%
  filter(nationality == "United States", year == "2012")
```

## Inference on proportions

As was hinted earlier, Table 6 provides *statistics*, that is,
calculations made from the sample of 51,927 people. What we’d like,
though, is insight into the population *parameters*. You answer the
question, “What proportion of people in your sample reported being
atheists?” with a statistic; while the question “What proportion of
people on earth would report being atheists” is answered with an
estimate of the parameter.

5.  Write out the conditions for inference using the Normal
    approximation to construct a 95% confidence interval for the
    proportion of atheists in the United States in 2012. Are you
    confident all conditions are met?

Although formal confidence intervals and hypothesis tests don’t show up
in the report, suggestions of inference appear at the bottom of page 6:
“In general, the error margin for surveys of this kind is \(\pm\) 3-5%
at 95% confidence”.

6.  What is the margin of error for the estimate of the proportion of
    the proportion of atheists in US in 2012 as estimated by the normal
    model? What about as estimated using the bootstrap standard error?

7.  Calculate confidence intervals for the proportion of atheists in
    2012 in two other countries of your choice, and report the
    associated margins of error. You are welcome to use either the
    normal approximation method or the bootstrap, but be sure to note
    whether the conditions for inference are met, and interpet the
    interval in context of the data.

## How does the proportion affect the margin of error?

Imagine you’ve set out to survey 1000 people on two questions: are you
female? and are you left-handed? Since both of these sample proportions
were calculated from the same sample size, they should have the same
margin of error, right? Wrong\! While the margin of error does change
with sample size, it is also affected by the proportion.

Think back to the formula for the standard error:
\(SE = \sqrt{p(1-p)/n}\). This is then used in the formula for the
margin of error for a 95% confidence interval:
\(ME = 1.96\times SE = 1.96\times\sqrt{p(1-p)/n}\). Since the population
proportion \(p\) is in this \(ME\) formula, it should make sense that
the margin of error is in some way dependent on the population
proportion. We can visualize this relationship by creating a plot of
\(ME\) vs. \(p\).

Since sample size is irrelevant to this discussion, let’s just set it to
some value (\(n = 1000\)) and use this value in the following
calculations:

``` r
n <- 1000
```

The first step is to make a variable `p` that is a sequence from 0 to 1
with each number incremented by 0.01. We can then create a vector of the
margin of error (`me`) associated with each of these values of `p` using
the familiar approximate formula (\(ME = 2 \times SE\)).

``` r
p <- seq(0, 1, 0.01)
me <- 2 * sqrt(p * (1 - p)/n)
```

Lastly, we combine these vectors into a dataframe so that we can
visualize it either using `geom_point()` or `geom_line()` (or both\!).

``` r
dd <- data.frame(p = p, me = me)
```

8.  Use ggplot to plot the relationship between `p` and `me` and
    describe it in words. For a given sample size, for which value of
    `p` is margin of error maximized?

## Success-failure condition

We emphasized that you must always check conditions before making
inference. For inference on proportions, the sample proportion can be
assumed to be nearly normal if it is based upon a random sample of
independent observations and if both \(np \geq 10\) and
\(n(1 - p) \geq 10\). This rule of thumb is easy enough to follow, but
it makes one wonder: what’s so special about the number 10?

The short answer is: nothing. You could argue that we would be fine with
9 or that we really should be using 11. What is the “best” value for
such a rule of thumb is, at least to some degree, arbitrary. However,
when \(np\) and \(n(1-p)\) reaches 10 the sampling distribution is
sufficiently normal to use confidence intervals and hypothesis tests
that are based on that approximation.

We can investigate the interplay between \(n\) and \(p\) and the shape
of the sampling distribution by using simulations. Play around with the
following app to investigate how the shape, center, and spread of the
distribution of \(\hat{p}\) changes as \(n\) and \(p\)
    changes.

    ## PhantomJS not found. You can install it with webshot::install_phantomjs(). If it is installed, please make sure the phantomjs executable can be found via the PATH variable.

<iframe src="https://ismay.shinyapps.io/shinyDist/?showcase=0" width="100%" height="600px">

</iframe>

9.  Describe the sampling distribution of sample proportions at
    \(n = 300\) and \(p = 0.1\). Be sure to note the center, spread, and
    shape.

10. Keep \(n\) constant and change \(p\). How does the shape, center,
    and spread of the sampling distribution vary as \(p\) changes. You
    might want to adjust min and max for the x-axis for a better view of
    the distribution.

11. Now also change \(n\).How does \(n\) appear to affect the
    distribution of \(\hat{p}\)?

12. If you refer to Table 6, you’ll find that Australia has a sample
    proportion of 0.1 on a sample size of 1040, and that Ecuador has a
    sample proportion of 0.02 on 400 subjects. Let’s suppose for this
    exercise that these point estimates are actually the truth.
    Construct their sampling distributions by using these values as
    inputs in the app. Do you think it is sensible to proceed with
    inference and report margin of errors, as the report does?

-----

## More Practice

The question of atheism was asked by WIN-Gallup International in a
similar survey that was conducted in 2005. (We assume here that sample
sizes have remained the same.) Table 4 on page 13 of the report
summarizes survey results from 2005 and 2012 for 39 countries.

13. Is there convincing evidence that the US has seen a change in its
    atheism index between 2005 and 2012? As always, write out the
    hypotheses for any tests you conduct and outline the status of the
    conditions for inference. If you find a significant difference, also
    quantify this difference with a confidence interval. You may use
    either a computational method (`permute` and `bootstrap`) or the
    normal approximation.

14. If in fact there has been no change in the atheism index in the
    countries listed in Table 4, in how many of those countries would
    you expect to detect a change (at a significance level of 0.05)
    simply by chance?  
    *Hint:* Review the definition of the Type 1 error.

15. Suppose you’re hired by the local government to estimate the
    proportion of residents that attend a religious service on a weekly
    basis. According to the guidelines, the estimate must have a margin
    of error no greater than 1% with 95% confidence. You have no idea
    what to expect for \(p\). How many people would you have to sample
    to ensure that you are within the guidelines?  
    *Hint:* Refer to your plot of the relationship between \(p\) and
    margin of error. This question does not require using the dataset.

-----

# Problem Set

Please work through problems 1, 35, 37, 39 and 42 in the Exercises at
the end of Chapter 3.
