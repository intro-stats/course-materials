Lab 8: Inference for Numerical Data
================

## Getting Started

### Load packages

In this lab, we will explore and visualize the data using the
**tidyverse** suite of packages, and perform statistical inference using
**infer**. The data can be found in the companion package for the
textbook, **openintro**.

Let’s load the packages.

``` r
library(tidyverse)
library(infer)
library(openintro)
```

### The data

Every two years, the Centers for Disease Control and Prevention conduct
the Youth Risk Behavior Surveillance System (YRBSS) survey, where it
takes data from high schoolers (9th through 12th grade), to analyze
health patterns. You will work with a selected group of variables from a
random sample of observations during one of the years the YRBSS was
conducted.

Load the `yrbss` data set into your workspace.

``` r
data(yrbss)
```

There are observations on 13 different variables, some categorical and
some numerical. The meaning of each variable can be found by bringing up
the help file:

``` r
?yrbss
```

1.  What are the cases in this data set? How many cases are there in our
    sample?

Remember that you can answer this question by viewing the data in the
data viewer or by using the following command:

``` r
glimpse(yrbss)
```

## Exploratory data analysis

You will first start with analyzing the weight of the participants in
kilograms: `weight`.

Using visualization and summary statistics, describe the distribution of
weights. The `summary` function can be useful.

``` r
summary(yrbss$weight)
```

2.  How many observations are we missing weights from?

Next, consider the possible relationship between a high schooler’s
weight and their physical activity. Plotting the data is a useful first
step because it helps us quickly visualize trends, identify strong
associations, and develop research questions.

First, let’s create a new variable `physical_3plus`, which will be coded
as either “yes” if they are physically active for at least 3 days a
week, and “no” if not.

``` r
yrbss <- yrbss %>% 
  mutate(physical_3plus = ifelse(physically_active_7d > 2, "yes", "no"))
```

3.  Make a side-by-side boxplot of `physical_3plus` and `weight`. Is
    there a relationship between these two variables? What did you
    expect and why?

The box plots show how the medians of the two distributions compare, but
we can also compare the means of the distributions using the following
to first group the data by the `physical_3plus` variable, and then
calculate the mean `weight` in these groups using the `mean` function
while ignoring missing values by setting the `na.rm` argument to `TRUE`.

``` r
yrbss %>%
  group_by(physical_3plus) %>%
  summarise(mean_weight = mean(weight, na.rm = TRUE))
```

There is an observed difference, but is this difference statistically
significant? In order to answer this question we will conduct a
hypothesis test.

## Inference Using the t-distribution

The classical method to address this question is to conduct a two-sample
t-test.

4.  Conduct a two-sample t-test that includes the following steps:

<!-- end list -->

  - Write the hypotheses for testing if the average weights are
    different for those who exercise at least times a week and those who
    don’t.
  - Compute the observed test statistic (you can `group_by()` and
    `summarize()` to compute things like group size (`n()`) and standard
    deviation (`sd()`)).
  - Find the degrees of freedom then use `pt()` to find the p-value
    associated with that observed statistic.
  - State your conclusions regarding the null hypothesis.

## Inference Using Permutation

An alternative technique is to skip the t-distribution and use a
computational technique.

Let’s start by calculating and saving the observed test statistic, which
we’ll call `obs_diff`.

``` r
obs_diff <- yrbss %>%
  specify(weight ~ physical_3plus) %>%
  calculate(stat = "diff in means", order = c("yes", "no"))
```

Notice how you have to specify the order of the difference in the same
manner that you did when looking at the difference in proportions.

Next you can use permutation to generate 500 data sets under the notion
that the `weight` is independent of `physical_3plus`.

``` r
null <- yrbss %>%
  specify(weight ~ physical_3plus) %>%
  hypothesize(null = "independence") %>%
  generate(reps = 500, type = "permute") %>%
  calculate(stat = "diff in means", order = c("yes", "no"))
```

We can visualize this null distribution with the following code:

``` r
null %>%
  visualize()
```

5.  How many of these `null` permutations have a difference of at least
    `obs_diff`? Shade those statistics in your null distribution plot
    and calculate the precise proportion (the p-value).

-----

## More Practice

6.  Calculate a 95% confidence interval for the average height in meters
    (`height`) and interpret it in context.

7.  Conduct a hypothesis test evaluating whether the average height is
    different for those who exercise at least three times a week and
    those who don’t.

8.  Now, a non-inference task: Determine the number of different options
    there are in the dataset for the `hours_tv_per_school_day` there
    are.

9.  Come up with a research question evaluating the relationship between
    height or weight and sleep. Formulate the question in a way that it
    can be answered using a hypothesis test and/or a confidence
    interval. Report the statistical results, and also provide an
    explanation in plain language. Be sure to check all assumptions,
    state your alpha level, and conclude in context.

<div id="license">

This is a product of OpenIntro that is released under a [Creative
Commons Attribution-ShareAlike 3.0
Unported](http://creativecommons.org/licenses/by-sa/3.0).

</div>

-----

# Problem Set

Please work through problems 5, 10, 14, 30, and 35 in the Exercises at
the end of Chapter 4.
