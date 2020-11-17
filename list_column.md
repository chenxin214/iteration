list column
================
Chenxin Zhang
11/16/2020

## Lists

You can put anything in a list. an numeric vector a logical vector
matrix

``` r
l = list(
  vec_numeric = 5:8,
  vec_logical = c(TRUE, TRUE, FALSE, TRUE, FALSE, FALSE),
  mat = matrix(1:8, nrow = 2, ncol = 4),
  summary = summary(rnorm(100))
)
```

``` r
l
```

    ## $vec_numeric
    ## [1] 5 6 7 8
    ## 
    ## $vec_logical
    ## [1]  TRUE  TRUE FALSE  TRUE FALSE FALSE
    ## 
    ## $mat
    ##      [,1] [,2] [,3] [,4]
    ## [1,]    1    3    5    7
    ## [2,]    2    4    6    8
    ## 
    ## $summary
    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ## -2.2178 -0.4926  0.1313  0.1140  0.7353  3.0233

``` r
l$vec_numeric
```

    ## [1] 5 6 7 8

``` r
l[[1]] #give me the first list element
```

    ## [1] 5 6 7 8

``` r
l[["vec_numeric"]]
```

    ## [1] 5 6 7 8

``` r
mean(l[["vec_numeric"]])
```

    ## [1] 6.5

## `for` loop

Create a new list. abcd is input id name

``` r
list_norm = 
  list(
    a = rnorm(20, mean = 3, sd = 1),
    b = rnorm(30, mean = 0, sd = 5),
    c = rnorm(40, mean = 10, sd = .2),
    d = rnorm(20, mean = -3, sd = 1)
  )

list_norm$a
```

    ##  [1] 1.264955 2.444793 4.078050 2.605541 2.456255 2.330996 1.413943 2.394041
    ##  [9] 3.255746 3.765404 3.747477 2.153059 2.980089 3.332157 1.930500 2.066679
    ## [17] 3.741188 3.368075 1.749282 3.336358

``` r
list_norm[[1]]
```

    ##  [1] 1.264955 2.444793 4.078050 2.605541 2.456255 2.330996 1.413943 2.394041
    ##  [9] 3.255746 3.765404 3.747477 2.153059 2.980089 3.332157 1.930500 2.066679
    ## [17] 3.741188 3.368075 1.749282 3.336358

``` r
list_norm[["a"]]
```

    ##  [1] 1.264955 2.444793 4.078050 2.605541 2.456255 2.330996 1.413943 2.394041
    ##  [9] 3.255746 3.765404 3.747477 2.153059 2.980089 3.332157 1.930500 2.066679
    ## [17] 3.741188 3.368075 1.749282 3.336358

function.

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

**apply that function to each list element**

``` r
mean_and_sd(list_norm[[1]])
```

    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  2.72 0.831

``` r
mean_and_sd(list_norm[[2]])
```

    ## # A tibble: 1 x 2
    ##     mean    sd
    ##    <dbl> <dbl>
    ## 1 -0.347  4.88

``` r
mean_and_sd(list_norm[[3]])
```

    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  10.0 0.192

``` r
mean_and_sd(list_norm[[4]])
```

    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -2.92 0.825

**for loop**

``` r
output = vector("list", length = 4)
for (i in 1:4) {
  
  output[[i]] = mean_and_sd(list_norm[[i]])
}
```

**map** given all element in a list to a function

map():result is still a list

``` r
output = map(list_norm, mean_and_sd)
output = map(list_norm, median)#a different function:IQR,
```

map\_dbl(): result show up as numbers rather than as lists

``` r
output = map_dbl(list_norm, median)
output = map_dbl(list_norm, median, .id = "input")#no different from above, so we do not need a id = input in map_dbl
```

map\_df(): result is not a list of data frame but a combined data frame

``` r
#output = map_dbl(list_norm, mean_and_sd) is not correct, result must be a  single double, not a vector of class
output = map_df(list_norm, mean_and_sd)
output = map_df(list_norm, mean_and_sd, .id = "input")
#each output of mean and sd is a tibble
#put list name(abcd) from list_norm into a column named input
```

**List columns**

creat a data frame that has a list column in it

``` r
# put abcd in a column called 'name'
#'samp' column is the list of samples generated previously
#first row in 'name' has a and the first row in 'samp' has that first element of list_norm(it's a vector of class double of length 20)
#a more compact way of looking things
listcol_df = 
  tibble(
    name = c("a", "b", "c", "d"),
    samp = list_norm
  )
```

pull element in data frame

``` r
listcol_df %>% pull(name)#get the first column by pull()
```

    ## [1] "a" "b" "c" "d"

``` r
listcol_df %>% pull(samp)#get the second column by pull(), the list
```

    ## $a
    ##  [1] 1.264955 2.444793 4.078050 2.605541 2.456255 2.330996 1.413943 2.394041
    ##  [9] 3.255746 3.765404 3.747477 2.153059 2.980089 3.332157 1.930500 2.066679
    ## [17] 3.741188 3.368075 1.749282 3.336358
    ## 
    ## $b
    ##  [1]  -2.4933349  -0.7427001   4.6800147   8.7108361  -4.0328539  -3.7324090
    ##  [7] -10.6097621  -1.9863219  -4.0016550   7.6761275   0.9966155  -1.9832682
    ## [13]  -0.4391826   6.2296965  -4.4581150  10.7279560  -0.4147240   1.2659011
    ## [19]  -1.4705159  -2.2471418   0.5506985   3.5636773  -0.4644600  -1.0880717
    ## [25]  -2.4802122  -1.6524774   2.5659325  -3.0410323 -11.8026086   1.7659217
    ## 
    ## $c
    ##  [1] 10.177033  9.765418 10.083816 10.137755 10.291294  9.744960 10.255361
    ##  [8] 10.177920  9.482568 10.009718 10.134645 10.175235 10.276515  9.987193
    ## [15] 10.124767  9.989886  9.710858 10.135887  9.794016 10.161184  9.714875
    ## [22]  9.961318  9.818488  9.970259 10.146998  9.862693  9.841821  9.866847
    ## [29] 10.111631 10.031146  9.972572  9.924255 10.245983  9.768340 10.050681
    ## [36]  9.978721  9.990114 10.301227 10.003567  9.739118
    ## 
    ## $d
    ##  [1] -2.907473 -2.144823 -4.869367 -2.373893 -2.649373 -3.491027 -3.909901
    ##  [8] -1.977069 -3.893196 -2.648778 -2.271089 -2.373800 -3.754431 -3.458225
    ## [15] -2.495964 -2.091033 -2.041576 -3.883679 -2.115906 -3.016132

``` r
listcol_df$samp[[1]]#get the first element of the list
```

    ##  [1] 1.264955 2.444793 4.078050 2.605541 2.456255 2.330996 1.413943 2.394041
    ##  [9] 3.255746 3.765404 3.747477 2.153059 2.980089 3.332157 1.930500 2.066679
    ## [17] 3.741188 3.368075 1.749282 3.336358

``` r
listcol_df %>% 
  filter(name == "a")#get the first row of the data frame as a tibble 
```

    ## # A tibble: 1 x 2
    ##   name  samp        
    ##   <chr> <named list>
    ## 1 a     <dbl [20]>

some operations: dataframe-map, function

``` r
#get the first element in the list
listcol_df$samp[[1]]
```

    ##  [1] 1.264955 2.444793 4.078050 2.605541 2.456255 2.330996 1.413943 2.394041
    ##  [9] 3.255746 3.765404 3.747477 2.153059 2.980089 3.332157 1.930500 2.066679
    ## [17] 3.741188 3.368075 1.749282 3.336358

``` r
# put the first element into a function
mean_and_sd(listcol_df$samp[[1]])
```

    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  2.72 0.831

``` r
# put the first element into a function
mean_and_sd(list_norm[[1]])
```

    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  2.72 0.831

``` r
#map accross 'samp' column as an input list by mean_and_sd function
map(listcol_df$samp, mean_and_sd)
```

    ## $a
    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  2.72 0.831
    ## 
    ## $b
    ## # A tibble: 1 x 2
    ##     mean    sd
    ##    <dbl> <dbl>
    ## 1 -0.347  4.88
    ## 
    ## $c
    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  10.0 0.192
    ## 
    ## $d
    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -2.92 0.825

``` r
map(list_norm, mean_and_sd)
```

    ## $a
    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  2.72 0.831
    ## 
    ## $b
    ## # A tibble: 1 x 2
    ##     mean    sd
    ##    <dbl> <dbl>
    ## 1 -0.347  4.88
    ## 
    ## $c
    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  10.0 0.192
    ## 
    ## $d
    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -2.92 0.825

**add a list column**

``` r
#map_df() result as data frame
#map() result as list
listcol_df1 = 
  listcol_df %>% 
  mutate(summary = map_df(samp, mean_and_sd),
         medians = map(samp, median))
```

``` r
#apply this function to each element of the list and save that output in the same data frame
#mutate function working inside the listcol_df, so we do not need listcol_df$samp
#creat a 'summary' column by map mean_and_sd across the 'samp' column in this data set
#
listcol_df2 = 
  listcol_df %>% 
  mutate(
    summary = map(samp, mean_and_sd),#result as list
    medians = map_dbl(samp, median))# result as single number
```
