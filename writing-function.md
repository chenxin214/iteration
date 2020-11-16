Writing functions
================

## Do something simple

``` r
x_vec = rnorm(30, mean = 5, sd = 3)
(x_vec - mean(x_vec)) / sd(x_vec)
```

    ##  [1] -0.4182537  1.2381032  1.5670918 -0.5988195 -1.9419116 -0.5190990
    ##  [7] -0.3889982 -0.4024085 -0.3601213  0.1555636  1.5595173 -0.9556157
    ## [13] -0.1829264  0.6099766  2.2366831 -0.4810322  1.1439798 -0.4625779
    ## [19]  0.5898312 -0.9859304 -0.5748583 -0.4700404 -1.1281597  0.2957120
    ## [25]  0.3794324  0.9241753  0.5348715  0.8626142 -0.1851187 -2.0416806

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

    ##  [1] -0.4182537  1.2381032  1.5670918 -0.5988195 -1.9419116 -0.5190990
    ##  [7] -0.3889982 -0.4024085 -0.3601213  0.1555636  1.5595173 -0.9556157
    ## [13] -0.1829264  0.6099766  2.2366831 -0.4810322  1.1439798 -0.4625779
    ## [19]  0.5898312 -0.9859304 -0.5748583 -0.4700404 -1.1281597  0.2957120
    ## [25]  0.3794324  0.9241753  0.5348715  0.8626142 -0.1851187 -2.0416806

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
    ## 1  2.08  4.31

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
    ## 1  6.62  3.06

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
    ## 1  6.02  2.89

``` r
sim_mean_sd(samp_size = 100, mu = 6, sigma = 3)#recomand use name to match in function
```

    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  5.63  3.00

``` r
sim_mean_sd(mu = 6, samp_size = 100, sigma = 3)#can shif things with name defined
```

    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  5.82  2.94

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
    ##    title                             stars      text                            
    ##    <chr>                             <chr>      <chr>                           
    ##  1 "Boo"                             1.0 out o~ "We rented this movie because o~
    ##  2 "Movie is still silly fun....ama~ 1.0 out o~ "We are getting really frustrat~
    ##  3 "Brilliant and awkwardly funny."  5.0 out o~ "I've watched this movie repeat~
    ##  4 "Great purchase price for great ~ 5.0 out o~ "Great movie and real good digi~
    ##  5 "Movie for memories"              5.0 out o~ "I've been looking for this mov~
    ##  6 "Love!"                           5.0 out o~ "Love this movie. Great quality"
    ##  7 "Hilarious!"                      5.0 out o~ "Such a funny movie, definitely~
    ##  8 "napoleon dynamite"               5.0 out o~ "cool movie"                    
    ##  9 "Top 5"                           5.0 out o~ "Best MOVIE ever! Funny one lin~
    ## 10 "\U0001f44d"                      5.0 out o~ "Exactly as described and came ~

**Let’s read a few pages of reviews**

``` r
dynamite_url_base = "https://www.amazon.com/product-reviews/B00005JNBQ/ref=cm_cr_arp_d_viewopt_rvwer?ie=UTF8&reviewerType=avp_only_reviews&sortBy=recent&pageNumber=" #get a url base the number is not specified
dynamite_urls = str_c(dynamite_url_base, 1:5) #combine url base with number 1-5

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

f = function(x) { z = x + y z } x = 1 y = 2 f(x = y)

## Functions as arguments

my\_summary = function(x, summ\_func) {

summ\_func(x)

} x\_vec = rnorm(100, 3, 7) mean(x\_vec) median(x\_vec)
my\_summary(x\_vec, IQR)
