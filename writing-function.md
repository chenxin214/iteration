Writing functions
================

## Do something simple

``` r
x_vec = rnorm(30, mean = 5, sd = 3)
(x_vec - mean(x_vec)) / sd(x_vec)
```

    ##  [1]  0.73199519 -0.52015554  1.39569976  0.69316121 -0.05878609 -0.63393923
    ##  [7]  0.83614539  1.80722413 -0.27439423 -0.61575972  0.52756186 -0.87723002
    ## [13] -0.39198551  0.38184181  0.83887346  0.69498094 -2.13381254  0.63449708
    ## [19]  1.69124139  0.05790747 -0.51000153  0.30362227 -0.89275540 -0.78010314
    ## [25] -2.50609385  1.05163632 -1.16505067  0.06549516 -0.07857060 -0.27324535

I want a function to compute z-scores

``` r
z_scores = function(x) {
  
  if (!is.numeric(x)) {
    stop("Input must be numeric")
  }
  
  if (length(x) < 3) {
    stop("Input must have at least three numbers")
  }
  
  z = (x - mean(x)) / sd(x)
  
  return(z)
  
}
z_scores(x_vec)
```

    ##  [1]  0.73199519 -0.52015554  1.39569976  0.69316121 -0.05878609 -0.63393923
    ##  [7]  0.83614539  1.80722413 -0.27439423 -0.61575972  0.52756186 -0.87723002
    ## [13] -0.39198551  0.38184181  0.83887346  0.69498094 -2.13381254  0.63449708
    ## [19]  1.69124139  0.05790747 -0.51000153  0.30362227 -0.89275540 -0.78010314
    ## [25] -2.50609385  1.05163632 -1.16505067  0.06549516 -0.07857060 -0.27324535

Try my function on some other things. These should give errors.

``` r
z_scores(3) #
```

    ## Error in z_scores(3): Input must have at least three numbers

``` r
z_scores("my name is jeff")#cannot be a character
```

    ## Error in z_scores("my name is jeff"): Input must be numeric

``` r
z_scores(mtcars)
```

    ## Error in z_scores(mtcars): Input must be numeric

``` r
z_scores(c(TRUE, TRUE, FALSE, TRUE))
```

    ## Error in z_scores(c(TRUE, TRUE, FALSE, TRUE)): Input must be numeric

## Multiple outputs

two things need to return, so we do not use return() but tibble/list

``` r
mean_and_sd = function(x) {
  
  if (!is.numeric(x)) {
    stop("Input must be numeric")
  }
  
  if (length(x) < 3) {
    stop("Input must have at least three numbers")
  }
  
  mean_x = mean(x)
  sd_x = sd(x)
  
  tibble(
    mean = mean_x,
    sd = sd_x
  )
}
```

Check that the function works.

``` r
x_vec = rnorm(100, mean = 3, sd = 4)
mean_and_sd(x_vec)
```

    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  3.04  4.23

## Multiple inputs

**usuall way**

``` r
sim_data =
  tibble(
    x = rnorm(100, mean = 6, sd = 3)
  )

sim_data %>% 
  summarize(
    mean = mean(x),
    sd = sd(x)
  )
```

    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  6.14  2.55

**I’d like to do this with a function. ** use mu but not mean because we
use true mean sim\_mean\_sd = function(n, mean,sd) the imput mu will bw
drop into mean = mean(x) run this function, each time you will get
different mean and sd

``` r
sim_mean_sd = function(samp_size, mu, sigma){
  
  sim_data =
  tibble(
    x = rnorm(n = samp_size, mean = mu, sd = sigma)
  )
  
  sim_data %>% 
    summarize(
      mean = mean(x),
      sd = sd(x)
    )
}

sim_mean_sd(100,6,3)#can not shif name
```

    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  6.09  2.84

``` r
sim_mean_sd(samp_size = 100, mu = 6, sigma = 3)#recomand use name to match in function
```

    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  6.50  3.21

``` r
sim_mean_sd(mu = 6, samp_size = 100, sigma = 3)#can shif things with name defined
```

    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  6.45  2.94

when the defult value is defined: sim\_mean\_sd\_d =
function(samp\_size, mu = 3,sigma = 4) you can replace the defult with
our prefer
