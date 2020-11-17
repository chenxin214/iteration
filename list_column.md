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
    ##     Min.  1st Qu.   Median     Mean  3rd Qu.     Max. 
    ## -2.37286 -0.80385 -0.25804 -0.08383  0.60461  2.05035

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

    ##  [1] -0.008989595  2.655525600  3.127879618  3.309967931  3.674790969
    ##  [6]  3.066284292  4.112676405  2.290623195  2.719235760  3.131924611
    ## [11]  2.898907494  4.140796279  4.454536716  3.059394476  4.010836691
    ## [16]  2.116378624  2.864590143  2.963982727  2.176231029  0.442959806

``` r
list_norm[[1]]
```

    ##  [1] -0.008989595  2.655525600  3.127879618  3.309967931  3.674790969
    ##  [6]  3.066284292  4.112676405  2.290623195  2.719235760  3.131924611
    ## [11]  2.898907494  4.140796279  4.454536716  3.059394476  4.010836691
    ## [16]  2.116378624  2.864590143  2.963982727  2.176231029  0.442959806

``` r
list_norm[["a"]]
```

    ##  [1] -0.008989595  2.655525600  3.127879618  3.309967931  3.674790969
    ##  [6]  3.066284292  4.112676405  2.290623195  2.719235760  3.131924611
    ## [11]  2.898907494  4.140796279  4.454536716  3.059394476  4.010836691
    ## [16]  2.116378624  2.864590143  2.963982727  2.176231029  0.442959806

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
    ## 1  2.86  1.12

``` r
mean_and_sd(list_norm[[2]])
```

    ## # A tibble: 1 x 2
    ##     mean    sd
    ##    <dbl> <dbl>
    ## 1 -0.776  5.27

``` r
mean_and_sd(list_norm[[3]])
```

    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  10.1 0.202

``` r
mean_and_sd(list_norm[[4]])
```

    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -2.72 0.929

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
    ##  [1] -0.008989595  2.655525600  3.127879618  3.309967931  3.674790969
    ##  [6]  3.066284292  4.112676405  2.290623195  2.719235760  3.131924611
    ## [11]  2.898907494  4.140796279  4.454536716  3.059394476  4.010836691
    ## [16]  2.116378624  2.864590143  2.963982727  2.176231029  0.442959806
    ## 
    ## $b
    ##  [1]  -6.0411519   2.8822782   1.1392947   5.6656464   5.6446524  -5.7378660
    ##  [7]   9.2505424  -3.5986756  -0.7660272  -9.1975070  -3.6259115  -3.1758219
    ## [13]   4.1894631  -1.0853023 -10.3617500   8.5448995  -0.4673882  -1.8789099
    ## [19]  10.6806834   0.7700042  -7.3254604  -5.1041311  -1.5500894  -1.4231079
    ## [25]  -2.0926759  -1.9951532   3.8409260  -1.2282427  -3.3512432  -5.8965231
    ## 
    ## $c
    ##  [1] 10.099039 10.131440  9.965040 10.045448  9.941955 10.277089  9.998107
    ##  [8] 10.174415 10.306742 10.432841  9.916435  9.984614 10.146922 10.275817
    ## [15] 10.014235  9.776029  9.550998  9.584361 10.306789 10.039884  9.864266
    ## [22] 10.326013 10.150921 10.096931  9.895455 10.244064 10.185703 10.096582
    ## [29] 10.375095  9.816659  9.857476 10.160446 10.095875 10.002673 10.100888
    ## [36] 10.136917  9.805589  9.733696 10.107280  9.989078
    ## 
    ## $d
    ##  [1] -4.0653074 -3.2804242 -2.6473503 -2.3105032 -2.5026471 -3.1296857
    ##  [7] -2.2761939 -2.7096318 -2.5757946 -3.3401346 -1.8056229 -2.1983390
    ## [13] -2.5731572 -3.6280391 -1.5360746 -1.9438597 -4.2615574 -3.8027086
    ## [19] -0.4350522 -3.4653607

``` r
listcol_df$samp[[1]]#get the first element of the list
```

    ##  [1] -0.008989595  2.655525600  3.127879618  3.309967931  3.674790969
    ##  [6]  3.066284292  4.112676405  2.290623195  2.719235760  3.131924611
    ## [11]  2.898907494  4.140796279  4.454536716  3.059394476  4.010836691
    ## [16]  2.116378624  2.864590143  2.963982727  2.176231029  0.442959806

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

    ##  [1] -0.008989595  2.655525600  3.127879618  3.309967931  3.674790969
    ##  [6]  3.066284292  4.112676405  2.290623195  2.719235760  3.131924611
    ## [11]  2.898907494  4.140796279  4.454536716  3.059394476  4.010836691
    ## [16]  2.116378624  2.864590143  2.963982727  2.176231029  0.442959806

``` r
# put the first element into a function
mean_and_sd(listcol_df$samp[[1]])
```

    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  2.86  1.12

``` r
# put the first element into a function
mean_and_sd(list_norm[[1]])
```

    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  2.86  1.12

``` r
#map accross 'samp' column as an input list by mean_and_sd function
map(listcol_df$samp, mean_and_sd)
```

    ## $a
    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  2.86  1.12
    ## 
    ## $b
    ## # A tibble: 1 x 2
    ##     mean    sd
    ##    <dbl> <dbl>
    ## 1 -0.776  5.27
    ## 
    ## $c
    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  10.1 0.202
    ## 
    ## $d
    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -2.72 0.929

``` r
map(list_norm, mean_and_sd)
```

    ## $a
    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  2.86  1.12
    ## 
    ## $b
    ## # A tibble: 1 x 2
    ##     mean    sd
    ##    <dbl> <dbl>
    ## 1 -0.776  5.27
    ## 
    ## $c
    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  10.1 0.202
    ## 
    ## $d
    ## # A tibble: 1 x 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -2.72 0.929

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

## Weather Data

weather\_df = rnoaa::meteo\_pull\_monitors( c(“USW00094728”,
“USC00519397”, “USS0023B17S”), var = c(“PRCP”, “TMIN”, “TMAX”),
date\_min = “2017-01-01”, date\_max = “2017-12-31”) %\>% mutate( name =
recode( id, USW00094728 = “CentralPark\_NY”, USC00519397 =
“Waikiki\_HA”, USS0023B17S = “Waterhole\_WA”), tmin = tmin / 10,
tmax = tmax / 10) %\>% select(name, id, everything())

  - nest several columns to a new list column in a data frame:
    weather\_nest = weather\_df %\>% nest(data = date:tmin)

weather\_nest %\>% pull(name) weather\_nest %\>% pull(data) will show
the list

Suppose i want to regress `tmax` on `tmin` for each station.

lm(tmax \~ tmin, data = weather\_nest$data\[\[1\]\])

Let’s write a function: df means that it must be a data frame

weather\_lm = function(df) {

lm(tmax \~ tmin, data = df)

} output = vector(“list”, 3) \#vector is a type list with length 3 for
(i in 1:3) {

output\[\[i\]\] = weather\_lm(weather\_nest$data\[\[i\]\])

}

What about a map …\!?

map(weather\_nest$data, weather\_lm)

What about a map in a list column \!\!\!\!\!??

weather\_nest = weather\_nest %\>% mutate(models = map(data,
weather\_lm)) weather\_nest$models
