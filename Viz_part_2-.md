Viz part 2
================
Sarah Haberlack
2023-10-03

## Settings

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.2     ✔ readr     2.1.4
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.0
    ## ✔ ggplot2   3.4.3     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.2     ✔ tidyr     1.3.0
    ## ✔ purrr     1.0.2     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(patchwork)

theme_set(theme(legend.position = "bottom"))

options(
  ggplot2.continuous.colour = "viridis",
  ggplot2.continuous.fill = "viridis"
)

scale_colour_discrete = scale_colour_viridis_d
scale_fill_discrete = scale_fill_viridis_d
```

Get the data for plotting today.

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
    tmax = tmax / 10) |>
  select(name, id, everything())
```

    ## using cached file: /Users/sarahhaberlack/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USW00094728.dly

    ## date created (size, mb): 2023-09-28 10:18:28.029499 (8.524)

    ## file min/max dates: 1869-01-01 / 2023-09-30

    ## using cached file: /Users/sarahhaberlack/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USW00022534.dly

    ## date created (size, mb): 2023-09-28 10:18:34.386055 (3.83)

    ## file min/max dates: 1949-10-01 / 2023-09-30

    ## using cached file: /Users/sarahhaberlack/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USS0023B17S.dly

    ## date created (size, mb): 2023-09-28 10:18:37.975504 (0.994)

    ## file min/max dates: 1999-09-01 / 2023-09-30

This results in a dataframe with 2190 observations on 6 variables.

## Same plot from last time

Adding labels (“labs”)

``` r
weather_df |> 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = .5) +
  labs(
    title = "Temperature plot",
    x = "Min daily temp (°C)",
    y = "Max daily temp",
    color = "Location",
    caption = "Max vs min daily temp in three locations, data from rnoaa"
  )
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

![](Viz_part_2-_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

Now, let’s play with scale. Default is good, but there are other options
that you might sometimes want to use instead. Scales modify the default
mapping between the variable and the aesthetic it’s mapped onto. Note:
We can also “sqrt” transform the data using `trans = "sqrt"` within the
`scale_y_continuous` command. It is sometimes easier to look at some
data if it’s transformed. You could also do this in the mutate function
if you want.

``` r
weather_df |> 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = .5) +
  labs(
    title = "Temperature plot",
    x = "Min daily temp (°C)",
    y = "Max daily temp",
    color = "Location",
    caption = "Max vs min daily temp in three locations, data from rnoaa"
  ) +
  scale_x_continuous(
    breaks = c(-15, 0, 15),
    labels = c("-15 C", "0", "15")
  ) +
  scale_y_continuous(
    position = "right",
    limits = c(0,30)
  )
```

    ## Warning: Removed 302 rows containing missing values (`geom_point()`).

![](Viz_part_2-_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

what about colors … for colors, continuous color options and discrete
color options. for categorical variables (not ordinal), `hue` works. but
it’s gross!

``` r
weather_df |> 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = .5) +
  labs(
    title = "Temperature plot",
    x = "Min daily temp (°C)",
    y = "Max daily temp",
    color = "Location",
    caption = "Max vs min daily temp in three locations, data from rnoaa"
  ) + 
  viridis::scale_color_viridis(discrete = TRUE)
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

![](Viz_part_2-_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

## Themes

`theme_bw()` inverts the background grid colors. theme_bw is a RESET
theme command. a global preference. order matters here, theme_bw has to
be first.

``` r
weather_df |> 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = .5) +
  labs(
    title = "Temperature plot",
    x = "Min daily temp (°C)",
    y = "Max daily temp",
    color = "Location",
    caption = "Max vs min daily temp in three locations, data from rnoaa"
  ) + 
  viridis::scale_color_viridis(discrete = TRUE) +
  theme_bw() +
theme(legend.position = "bottom")
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

![](Viz_part_2-_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

## data argument…

``` r
weather_df |> 
  ggplot(aes(x = date, y = tmax,)) +
  geom_point(aes(color = name)) +
  geom_smooth()
```

    ## `geom_smooth()` using method = 'gam' and formula = 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

![](Viz_part_2-_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

``` r
nyc_weather_df = 
  weather_df |> 
  filter(name == "CentralPark_NY")

hawaii_weather_df =
  weather_df |> 
  filter(name == "Molokai_HI")

ggplot(nyc_weather_df, aes(x = date, y = tmax, color = name)) +
  geom_point() +
  geom_line(data = hawaii_weather_df)
```

![](Viz_part_2-_files/figure-gfm/unnamed-chunk-7-2.png)<!-- -->

## `patchwork`

``` r
weather_df |>
  ggplot(aes(x = date, y = tmax, color = name)) +
  geom_point() +
  facet_grid(. ~ name)
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

![](Viz_part_2-_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

``` r
ggp_temp_scatter =
weather_df |> 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = 0.5) +
  theme(legend.position = "none")

ggp_prcp_density =
  weather_df |> 
  filter(prcp > 25) |> 
  ggplot(aes(x = prcp, fill = name)) +
  geom_density(alpha = 0.5) +
    theme(legend.position = "bottom")

ggp_temp_scatter + ggp_prcp_density
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

![](Viz_part_2-_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

``` r
ggp_tmax_date =
  weather_df |> 
  ggplot(aes(x = date, y = tmax, color = name)) +
  geom_point() +
  geom_smooth(se = FALSE) +
  theme(legend.position = "bottom")

(ggp_temp_scatter + ggp_prcp_density)/ggp_tmax_date
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).
    ## Removed 17 rows containing missing values (`geom_point()`).

![](Viz_part_2-_files/figure-gfm/unnamed-chunk-9-2.png)<!-- -->

## data manipulation

``` r
weather_df |> 
  mutate(
    name = fct_relevel(name, c("Molokai_HI", "CentralPark_NY", "Waterhole_WA"))
  ) |> 
  ggplot(aes(x = name, y = tmax)) +
  geom_boxplot()
```

    ## Warning: Removed 17 rows containing non-finite values (`stat_boxplot()`).

![](Viz_part_2-_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

Factor variables are ordinal. When ggplot thinks about orders, every
time you need to put sth that was a character variable into a plot, in
the background R categorizes them as factor variables by alphabetical
order. If you want these things in a different order, you need to do
data manipulation.

What if you want to order a variable in the order of a certain other
variable? Let’s order `name` by `tmax`.

``` r
weather_df |> 
  mutate(
    name = fct_reorder(name, tmax)
  ) |> 
  ggplot(aes(x = name, y = tmax, fill = name)) +
  geom_violin()
```

    ## Warning: There was 1 warning in `mutate()`.
    ## ℹ In argument: `name = fct_reorder(name, tmax)`.
    ## Caused by warning:
    ## ! `fct_reorder()` removing 17 missing values.
    ## ℹ Use `.na_rm = TRUE` to silence this message.
    ## ℹ Use `.na_rm = FALSE` to preserve NAs.

    ## Warning: Removed 17 rows containing non-finite values (`stat_ydensity()`).

![](Viz_part_2-_files/figure-gfm/unnamed-chunk-11-1.png)<!-- --> So now
this is a somewhat more natural ordering, we can see how the data stacks
up.

## complicated FAS plot

``` r
litters_df =
  read_csv("data/FAS_litters.csv") |> 
  janitor::clean_names() |> 
  separate(group, into = c("dose", "day_of_tx"), se = 3)
```

    ## Rows: 49 Columns: 8
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (2): Group, Litter Number
    ## dbl (6): GD0 weight, GD18 weight, GD of Birth, Pups born alive, Pups dead @ ...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
pups_df =
  read_csv("data/FAS_pups.csv") |> 
  janitor::clean_names()
```

    ## Rows: 313 Columns: 6
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): Litter Number
    ## dbl (5): Sex, PD ears, PD eyes, PD pivot, PD walk
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
fas_df =
  left_join(pups_df, litters_df, by = "litter_number")

fas_df |> 
  select(dose, day_of_tx, starts_with("pd")) |> 
  pivot_longer(
  pd_ears:pd_walk,
  names_to = "outcome",
  values_to = "pn_day"
) |> 
drop_na() |> 
mutate(outcome = fct_reorder(outcome, pn_day)) |> 
ggplot(aes(x = dose, y = pn_day)) +
geom_violin() +
facet_grid(day_of_tx ~ outcome)
```

![](Viz_part_2-_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->
