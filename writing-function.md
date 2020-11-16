Writing functions
================

## Do something simple

``` r
x_vec = rnorm(30, mean = 5, sd = 3)
(x_vec - mean(x_vec)) / sd(x_vec)
```

    ##  [1] -1.12110577 -0.91934111 -0.81019385 -0.98190727  0.35495586 -0.08792861
    ##  [7]  1.41705544  0.37242216  0.80426391  1.49785597 -0.86919083  0.68381547
    ## [13]  0.31197964  0.77351255 -0.31558242 -1.31758245  0.87957952  0.69755915
    ## [19]  1.45962004  0.12524110  0.48014735 -0.22538704 -0.93345336 -0.75707545
    ## [25] -0.83443251 -0.19759322  0.43610327  0.28401834  1.57403174 -2.78138763

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

    ##  [1] -1.12110577 -0.91934111 -0.81019385 -0.98190727  0.35495586 -0.08792861
    ##  [7]  1.41705544  0.37242216  0.80426391  1.49785597 -0.86919083  0.68381547
    ## [13]  0.31197964  0.77351255 -0.31558242 -1.31758245  0.87957952  0.69755915
    ## [19]  1.45962004  0.12524110  0.48014735 -0.22538704 -0.93345336 -0.75707545
    ## [25] -0.83443251 -0.19759322  0.43610327  0.28401834  1.57403174 -2.78138763

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
    ## 1  3.19  3.80

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
    ## 1  5.77  2.89

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
    ## 1  5.87  2.89

``` r
sim_mean_sd(samp_size = 100, mu = 6, sigma = 3)#recomand use name to match in function
```

    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  6.23  2.71

``` r
sim_mean_sd(mu = 6, samp_size = 100, sigma = 3)#can shif things with name defined
```

    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  6.20  3.18

when the defult value is defined: sim\_mean\_sd\_d =
function(samp\_size, mu = 3,sigma = 4) you can replace the defult with
our prefer

## Let’s review Napoleon Dynamite

**gather amazon review as data**

``` r
url = "https://www.amazon.com/product-reviews/B00005JNBQ/ref=cm_cr_arp_d_viewopt_rvwer?ie=UTF8&reviewerType=avp_only_reviews&sortBy=recent&pageNumber=1" #this is only page 1 in the website

dynamite_html = read_html(url)

review_titles =
  dynamite_html %>% 
  html_nodes(".a-text-bold span") %>% 
  html_text() # convert to text

review_stars = 
  dynamite_html %>%
  html_nodes("#cm_cr-review_list .review-rating") %>%
  html_text() %>%
  str_extract("^\\d") %>% #get the first(^) digits(d),^is a special charactor which require \, and \ itself is a charactor which need \
  as.numeric()

review_text = 
  dynamite_html %>%
  html_nodes(".review-text-content span") %>%
  html_text() %>% #until this step you get \n in the begining and end of each review
  str_replace_all("\n", "") %>% #replace \n with nothing
  str_trim() # trimming off the two white spaces in the beginning


reviews = tibble(
  title = review_titles,
  stars = review_stars,
  text = review_text
)
```

**if you want to get more pages** given url and read url,you can give x
and read x

``` r
read_page_reviews = function(url) {
  
  html = read_html(url)
  
  review_titles = 
    html %>%
    html_nodes(".a-text-bold span") %>%
    html_text()
  
  review_stars = 
    html %>%
    html_nodes("#cm_cr-review_list .review-rating") %>%
    html_text()
  
  review_text = 
    html %>%
    html_nodes(".review-text-content span") %>%
    html_text() %>% 
    str_replace_all("\n", "") %>% 
    str_trim()
  
  reviews = 
    tibble(
      title = review_titles,
      stars = review_stars,
      text = review_text
    )
  
  reviews
  
}
```

Let me try my function.

``` r
dynamite_url = "https://www.amazon.com/product-reviews/B00005JNBQ/ref=cm_cr_arp_d_viewopt_rvwer?ie=UTF8&reviewerType=avp_only_reviews&sortBy=recent&pageNumber=2"
read_page_reviews(dynamite_url)
```

    ## # A tibble: 10 x 3
    ##    title                            stars      text                             
    ##    <chr>                            <chr>      <chr>                            
    ##  1 Entertaining, limited quality    4.0 out o~ "Entertainment level gets a 5 st~
    ##  2 Boo                              1.0 out o~ "We rented this movie because ou~
    ##  3 Movie is still silly fun....ama~ 1.0 out o~ "We are getting really frustrate~
    ##  4 Brilliant and awkwardly funny.   5.0 out o~ "I've watched this movie repeate~
    ##  5 Great purchase price for great ~ 5.0 out o~ "Great movie and real good digit~
    ##  6 Movie for memories               5.0 out o~ "I've been looking for this movi~
    ##  7 Love!                            5.0 out o~ "Love this movie. Great quality" 
    ##  8 Hilarious!                       5.0 out o~ "Such a funny movie, definitely ~
    ##  9 napoleon dynamite                5.0 out o~ "cool movie"                     
    ## 10 Top 5                            5.0 out o~ "Best MOVIE ever! Funny one line~

**Let’s read a few pages of reviews**

``` r
dynamite_url_base = "https://www.amazon.com/product-reviews/B00005JNBQ/ref=cm_cr_arp_d_viewopt_rvwer?ie=UTF8&reviewerType=avp_only_reviews&sortBy=recent&pageNumber=" #get a url base the number is not specified
dynamite_urls = str_c(dynamite_url_base, 1:20) #combine url base with number 1-5

all_reviews = 
  bind_rows(
    read_page_reviews(dynamite_urls[1]),#read page reviews on the first page
    read_page_reviews(dynamite_urls[2]),
    read_page_reviews(dynamite_urls[3]),
    read_page_reviews(dynamite_urls[4]),
    read_page_reviews(dynamite_urls[5])
  )
```

## Mean scoping example

``` r
f = function(x) {
  z = x + y
  z
}
x = 1
y = 2
f(x = y)#the input x=2, so the result will be altually 4
```

    ## [1] 4

## Functions as arguments

my\_summary is a function, pass in two argument’x’ and ‘summ\_fuc’ get a
function and pass in a vector’summ\_func’

``` r
my_summary = function(x, summ_func) {
  
  summ_func(x)
  
}
x_vec = rnorm(100, 3, 7)
mean(x_vec)
```

    ## [1] 3.290138

``` r
median(x_vec)
```

    ## [1] 3.439474

``` r
IQR(x_vec)
```

    ## [1] 9.540965

``` r
my_summary(x_vec, mean)
```

    ## [1] 3.290138
