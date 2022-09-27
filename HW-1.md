Stat 433 HW 1
================
2022-09-21

\##Problem 1 How many flights have a missing dep_time? What other
variables are missing? What might these rows represent?

``` r
flights %>% filter(is.na(dep_time)) %>% count()
```

    ## # A tibble: 1 x 1
    ##       n
    ##   <int>
    ## 1  8255

``` r
dep_time_na = flights %>% filter(is.na(dep_time))

view(dep_time_na)
```

There are 8255 flights without a dep_time.

Using view(dep_time_na) we can see that dep_delay, arr_time, arr_delay,
and airtime are all also NA.

This suggests that these were cancelled flights.

\##Problem 2

Currently dep_time and sched_dep_time are convenient to look at, but
hard to compute with because they’re not really continuous numbers.
Convert them to a more convenient representation of number of minutes
since midnight.

``` r
dep_min_since_midnight = ((flights$dep_time %/% 100) * 60) + (flights$dep_time %% 100)

sched_dep_min_since_midnight = ((flights$sched_dep_time %/% 100) * 60) + (flights$sched_dep_time %% 100)

flights %>% transmute(dep_min_since_midnight = dep_min_since_midnight, sched_dep_min_since_midnight = sched_dep_min_since_midnight)
```

    ## # A tibble: 336,776 x 2
    ##    dep_min_since_midnight sched_dep_min_since_midnight
    ##                     <dbl>                        <dbl>
    ##  1                    317                          315
    ##  2                    333                          329
    ##  3                    342                          340
    ##  4                    344                          345
    ##  5                    354                          360
    ##  6                    354                          358
    ##  7                    355                          360
    ##  8                    357                          360
    ##  9                    357                          360
    ## 10                    358                          360
    ## # ... with 336,766 more rows

\##Problem 3 Look at the number of canceled flights per day. Is there a
pattern? Is the proportion of canceled flights related to the average
delay? Use multiple dyplr operations, all on one line, concluding with
ggplot(aes(x= ,y=)) + geom_point()

``` r
plot = flights %>% group_by(month, day) %>% mutate(porp_cancel = sum(is.na(dep_time) / n())) %>% filter(is.na(dep_time) == FALSE) %>% mutate(avg_dep_delay = mean(dep_delay)) %>% ggplot(aes(x=avg_dep_delay ,y=porp_cancel)) + geom_point()
```
![image](https://user-images.githubusercontent.com/113466747/192576256-87d36293-651e-425b-adce-4249db51c12c.png)

![](HW-1_files/figure-gfm/avg_delay%20v%20porp_cancel-1.png)<!-- -->

Yes, as the average departure delay increases, so does the porportion of
cancelled flights. One hypothesis is that on days with bad weather, it
is more likely for flights to be both cancelled or delayed which results
in the correllation seen above.
