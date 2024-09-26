Vis 1
================
Chhiring Lama
2024-09-26

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(
    c("USW00094728", "USW00022534", "USS0023B17S"),
    var = c("PRCP", "TMIN", "TMAX"), 
    date_min = "2021-01-01",
    date_max = "2022-12-31") |>
  mutate(
    name = recode(
      id, 
      USW00094728 = "CentralPark_NY", 
      USW00022534 = "Molokai_HI",
      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10,
    month = lubridate::floor_date(date, unit = "month")) |>
  select(name, id, everything())
```

    ## using cached file: /Users/chhiringlama2/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USW00094728.dly

    ## date created (size, mb): 2024-09-26 10:18:03.970907 (8.651)

    ## file min/max dates: 1869-01-01 / 2024-09-30

    ## using cached file: /Users/chhiringlama2/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USW00022534.dly

    ## date created (size, mb): 2024-09-26 10:18:15.614742 (3.932)

    ## file min/max dates: 1949-10-01 / 2024-09-30

    ## using cached file: /Users/chhiringlama2/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USS0023B17S.dly

    ## date created (size, mb): 2024-09-26 10:18:19.186102 (1.036)

    ## file min/max dates: 1999-09-01 / 2024-09-30

Making our first plot

``` r
ggplot(weather_df, aes(x = tmin, y = tmax)) +
  geom_point()
```

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](Vis1_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

We can save and print it later

``` r
gg_weather_scatterplot <- ggplot(weather_df, aes(x = tmin, y = tmax)) +
  geom_point()

gg_weather_scatterplot
```

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](Vis1_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

Check why some rows are missing

## fancier scatterplots

``` r
weather_df |> 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = 0.6, size = 0.8) +
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_smooth()`).

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](Vis1_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

Where we define the `aes` matters. The smoothing looks different when
used in `geom` where we only define the color grouping only for the
scatterplot, the grouping is not global so `geom_smooth` does not
recognize this.

``` r
weather_df |> 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point(alpha = 0.6, size = 0.8, aes(color = name)) +
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'gam' and formula = 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_smooth()`).

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](Vis1_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

Use facet

``` r
weather_df |> 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point(alpha = 0.6, size = 0.8, aes(color = name)) +
  geom_smooth(se = FALSE) +
  facet_grid(.~name)
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_smooth()`).

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](Vis1_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->
