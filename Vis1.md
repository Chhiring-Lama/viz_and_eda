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
  ggplot(aes(x = date, y = tmax)) +
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

``` r
weather_df |> 
  ggplot(aes(x = date, y = tmax, color = name, size = prcp)) +
  geom_point(alpha = 0.6) +
  geom_smooth(se = FALSE) +
  facet_grid(.~name)
```

    ## Warning: Using `size` aesthetic for lines was deprecated in ggplot2 3.4.0.
    ## ℹ Please use `linewidth` instead.
    ## This warning is displayed once every 8 hours.
    ## Call `lifecycle::last_lifecycle_warnings()` to see where this warning was
    ## generated.

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_smooth()`).

    ## Warning: The following aesthetics were dropped during statistical transformation: size.
    ## ℹ This can happen when ggplot fails to infer the correct grouping structure in
    ##   the data.
    ## ℹ Did you forget to specify a `group` aesthetic or to convert a numerical
    ##   variable into a factor?
    ## The following aesthetics were dropped during statistical transformation: size.
    ## ℹ This can happen when ggplot fails to infer the correct grouping structure in
    ##   the data.
    ## ℹ Did you forget to specify a `group` aesthetic or to convert a numerical
    ##   variable into a factor?
    ## The following aesthetics were dropped during statistical transformation: size.
    ## ℹ This can happen when ggplot fails to infer the correct grouping structure in
    ##   the data.
    ## ℹ Did you forget to specify a `group` aesthetic or to convert a numerical
    ##   variable into a factor?

    ## Warning: Removed 19 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](Vis1_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

## Learning Assessment

Write a code chain that starts with weather_df; focuses only on Central
Park, converts temperatures to Fahrenheit, makes a scatterplot of min
vs. max temperature, and overlays a linear regression line (using
options in geom_smooth())

### Solution

``` r
weather_df |> 
  filter(name == "CentralPark_NY") |> 
  mutate(tmax_fahrenheit = ((tmax * 9/5) + 32), 
         tmin_fahrenheit = ((tmin * 9/5) + 32)) |> 
  ggplot(aes(x = tmin_fahrenheit, y = tmax_fahrenheit, size = prcp)) +
  geom_point(alpha = 0.6) +
  geom_smooth(se = FALSE, method = "lm")
```

    ## `geom_smooth()` using formula = 'y ~ x'

    ## Warning: The following aesthetics were dropped during statistical transformation: size.
    ## ℹ This can happen when ggplot fails to infer the correct grouping structure in
    ##   the data.
    ## ℹ Did you forget to specify a `group` aesthetic or to convert a numerical
    ##   variable into a factor?

![](Vis1_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

We can look at the density at certain point in the graph too.

``` r
weather_df |> 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_hex()
```

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_binhex()`).

![](Vis1_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

## Learning Assessment

In the preceding, we set the alpha aesthetic “by hand” instead of
mapping it to a variable. This is possible for other aesthetics too. To
check your understanding of this point, try to explain why the two lines
below don’t produce the same result:

``` r
weather_df |> 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point(color = "blue")
```

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](Vis1_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

``` r
weather_df |> 
  ggplot(aes(x = tmin, y = tmax,color = "blue")) +
  geom_point()
```

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](Vis1_files/figure-gfm/unnamed-chunk-11-2.png)<!-- -->

### Solution:

In the second case, because we put the color in aesthetic which take
variables, it created a variable whose value is “blue” for all cases,
and ends up coloring with a single color. The primary chose for R is
red.

## Univariate plots

``` r
weather_df |> 
  ggplot(aes(x = tmin, fill = name)) +
  geom_histogram(position = "dodge", binwidth = 2)
```

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_bin()`).

![](Vis1_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

How we can fix this:

``` r
weather_df |> 
  ggplot(aes(x = tmin, fill = name)) +
  geom_histogram(position = "dodge", binwidth = 2) +
  facet_grid(.~name)
```

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_bin()`).

![](Vis1_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

We can use density plot instead

``` r
weather_df |> 
  ggplot(aes(x = tmin, fill = name)) +
  geom_density(alpha = .3)
```

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_density()`).

![](Vis1_files/figure-gfm/unnamed-chunk-14-1.png)<!-- -->

Using box plot

``` r
weather_df |> 
  ggplot(aes(x = name, y = tmin, fill = name)) +
  geom_boxplot(alpha = .3)
```

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_boxplot()`).

![](Vis1_files/figure-gfm/unnamed-chunk-15-1.png)<!-- -->

A variation of box plot: violin plot (this helps identify difference
between multiple groups, and also identify bimodal data distribution)

``` r
weather_df |> 
  ggplot(aes(x = name, y = tmin, fill = name)) +
  geom_violin(alpha = 0.3) + 
  stat_summary(fun = "median", color = "blue")
```

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_ydensity()`).

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_summary()`).

    ## Warning: Removed 3 rows containing missing values or values outside the scale range
    ## (`geom_segment()`).

![](Vis1_files/figure-gfm/unnamed-chunk-16-1.png)<!-- -->

Another variation: ridge plot

``` r
weather_df |> 
  ggplot(aes(x = tmax, y = name)) + 
  geom_density_ridges(scale = .85)
```

    ## Picking joint bandwidth of 1.54

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_density_ridges()`).

![](Vis1_files/figure-gfm/unnamed-chunk-17-1.png)<!-- -->

## Learning Assessment:

Make plots that compare precipitation across locations. Try a histogram,
a density plot, a boxplot, a violin plot, and a ridgeplot; use aesthetic
mappings to make your figure readable

### Solution

``` r
ggplot(weather_df, aes(y = prcp, fill = name)) +
  geom_histogram(position = "dodge", alpha = 0.8, binwidth = 50) +
  facet_grid(name~.)
```

    ## Warning: Removed 15 rows containing non-finite outside the scale range
    ## (`stat_bin()`).

![](Vis1_files/figure-gfm/unnamed-chunk-18-1.png)<!-- -->

``` r
ggplot(weather_df, aes(y = prcp, fill = name)) +
  geom_density(alpha = 0.8)
```

    ## Warning: Removed 15 rows containing non-finite outside the scale range
    ## (`stat_density()`).

![](Vis1_files/figure-gfm/unnamed-chunk-19-1.png)<!-- -->

can log-transform the density plot

``` r
ggplot(weather_df, aes(y = prcp, fill = name)) +
  geom_density(alpha = 0.8) +
  scale_y_log10()
```

    ## Warning in scale_y_log10(): log-10 transformation introduced infinite values.

    ## Warning: Removed 1490 rows containing non-finite outside the scale range
    ## (`stat_density()`).

![](Vis1_files/figure-gfm/unnamed-chunk-20-1.png)<!-- -->

``` r
ggplot(weather_df, aes(x = name, y = prcp, fill = name)) +
  geom_boxplot(alpha = 0.8) 
```

    ## Warning: Removed 15 rows containing non-finite outside the scale range
    ## (`stat_boxplot()`).

![](Vis1_files/figure-gfm/unnamed-chunk-21-1.png)<!-- -->

``` r
ggplot(weather_df, aes(x = name, y = prcp, fill = name)) +
  geom_violin(alpha = 0.8) + 
  stat_summary(fun = "median", color = "blue")
```

    ## Warning: Removed 15 rows containing non-finite outside the scale range
    ## (`stat_ydensity()`).

    ## Warning: Removed 15 rows containing non-finite outside the scale range
    ## (`stat_summary()`).

    ## Warning: Removed 3 rows containing missing values or values outside the scale range
    ## (`geom_segment()`).

![](Vis1_files/figure-gfm/unnamed-chunk-22-1.png)<!-- -->

``` r
ggplot(weather_df, aes(y = name, x = prcp, fill = name)) +
  geom_density_ridges(scale = .85) 
```

    ## Picking joint bandwidth of 9.22

    ## Warning: Removed 15 rows containing non-finite outside the scale range
    ## (`stat_density_ridges()`).

![](Vis1_files/figure-gfm/unnamed-chunk-23-1.png)<!-- -->

So far, the box plot lets us look at the distribution and identify the
outliers.

## Saving and embedding plots

``` r
gg_weather = 
  weather_df |> 
  ggplot(aes(x = date, y = tmax, color = name)) +
  geom_point()

ggsave("plots/ggp_weather.pdf", gg_weather, height = 5, width = 6)
```

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

embedding plots

``` r
weather_df |> 
  ggplot(aes(x = date, y = tmax, color = name)) +
  geom_point()
```

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

<img src="Vis1_files/figure-gfm/unnamed-chunk-25-1.png" width="90%" />
