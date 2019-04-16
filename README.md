Internet Access and Inequality
================
Adam Shelton
4/12/2019

# Internet Access and Poverty

This is an R Markdown document. Markdown is a simple formatting syntax
for authoring HTML, PDF, and MS Word documents. For more details on
using R Markdown see <http://rmarkdown.rstudio.com>.

When you click the **Knit** button a document will be generated that
includes both content as well as the output of any embedded R code
chunks within the document. You can embed an R code chunk like this:

## The Internet is pretty important

## …but it isn’t spread equally

``` r
sum_us_int = sum(acs_17_mod$total_pop * acs_17_mod$no_inet)
avg_us_int = 1 - (sum_us_int / sum(sum(acs_17_mod$total_pop)))

states_sf %>% 
  ggplot() + 
  geom_sf(color = "grey90", fill = "white") +
  coord_sf(crs = st_crs(102003)) +
  geom_point(data = acs_17_mod, aes(x = X,
                     y = Y,
                     color = (1- no_inet),
                     size = pop_dens)) + 
  scale_color_gradientn(colors = color_pal(6, type = "cool"), labels = percent_format(accuracy = 1)) +
  scale_size(range = c(0.5, 15), breaks = c(50, 500, 5000, 50000)) +
  scale_alpha(range = c(1.0, 0.05)) +
  labs(title = paste(round(avg_us_int * 100), "% of America has Internet Access", sep = ""),
      color = "Percent with \nInternet",
       size = "Population Density \n(ppl/sq mi)",
      caption = "American Community Survey 2017") +
   theme_map() +
  theme(axis.text = element_blank())
```

![](README_files/figure-gfm/us-map-int-1.svg)<!-- -->

``` r
sum_us_bb = sum(acs_17_mod$total_pop) - sum(acs_17_mod$total_pop * acs_17_mod$broadband_any)
avg_us_bb = (sum_us_bb / sum(sum(acs_17_mod$total_pop)))

states_sf %>% 
  ggplot() + 
  geom_sf(color = "grey30", fill = "#323232") +
  coord_sf(crs = st_crs(102003)) +
  geom_point(data = acs_17_mod, aes(x = X,
                     y = Y,
                     color = broadband_any,
                     size = pop_dens)) + 
  scale_color_gradientn(colors = color_pal(5, type = "warm", reverse = TRUE), labels = percent_format(accuracy = 1)) +
  scale_size(range = c(0.5, 15), breaks = c(50, 500, 5000, 50000)) +
  scale_alpha(range = c(1.0, 0.05)) +
  labs(title = paste(round(avg_us_bb * 100), "% of America has Broadband Access", sep = ""),
      color = "Percent with \nBroadband",
       size = "Population Density \n(ppl/sq mi)",
      caption = "American Community Survey 2017") +
  theme_map() +
  theme(axis.text = element_blank())
```

![](README_files/figure-gfm/us-map-bb-1.svg)<!-- -->

## This has serious implications

``` r
ggplot(data = acs_17_mod, aes(x = no_inet * 100, y = below_poverty * 100)) + 
  geom_point(aes(fill = region, color = region), alpha = 0.6, size = 2) + 
  scale_fill_manual(values = color_pal(4)) +
  scale_color_manual(values = color_pal(4)) +
  labs(title = "Communities with Higher Poverty Rates Have Less Internet Access",
       x = "Percent with no Internet", 
       y = "Percent Below Poverty Line", 
       caption="American Community Survey 2017", 
       color = "Region") + 
  scale_x_log10(breaks = c(0, 5, 10, 15, 20, 25, 30, 35, 40, 45, 50)) +
  scale_y_log10(breaks = c(0, 5, 10, 15, 20, 25, 30)) +
  theme_master()  +
  theme(panel.grid.minor = element_blank())
```

    ## Warning: Transformation introduced infinite values in continuous y-axis

![](README_files/figure-gfm/point-int-pov-1.svg)<!-- -->

## …but there is some good news\!

``` r
ggplot(agg_urban_bb, aes(area = as.numeric(counts), fill = down_value, label = Group.1, subgroup = Technology)) + 
  geom_treemap() +
  geom_treemap_subgroup_border() +
  geom_treemap_subgroup_text(place = "centre", grow = T, alpha = 0.5, colour =
                             "black", fontface = "italic", min.size = 0) +
  geom_treemap_text(colour = "white", place = "centre",
                    grow = TRUE) +
  scale_fill_gradientn(trans = "log10", colors = color_pal(5, type = "continuous")) + 
  labs(title = "Cable and Fiber ISPs Provide Better Value for Internet Service",
       subtitle = "Small ISPs that use Fiber to the Home are the best value",
       fill = "Value \n(Megabits \nper USD)", 
       caption = "Urban Rate Broadband Survey") +
  theme_master()
```

![](README_files/figure-gfm/tree-map-1.svg)<!-- -->

``` r
chart_labels = tibble(text = c('FCC \nBroadband \nCutoff'), x = c(0.6), y = c(25))
ggplot(data = bind_rows(filter(urban_bb, Year == 2015), filter(urban_bb, Year == 2018)), aes(x = factor(`Year`), y = `Download Bandwidth Mbps`)) +
  geom_violin(aes(color = ordered(Year), fill = ordered(Year))) +
  scale_color_manual(values = color_pal(2)) +
  scale_fill_manual(values = color_pal(2)) +
  scale_y_continuous(trans = "log10", breaks = c(1, 5, 20, 50, 100, 300, 1000, 10000)) +
  geom_hline(yintercept = 25, linetype = 2,  color = "black", size = 1) + 
  geom_label(data = chart_labels[1,], aes(x = x, y = y, label = text), alpha = 0.95) + 
  labs(title = "Download Speeds Have Increased Significantly",
       x = "Year",
       fill = "Year",
       color = "Year",
       y = "Download Speed (Mbps)",
       caption = "Urban Rate Broadband Survey") +
  theme_master() +
  theme(legend.position="none") + 
  theme(panel.grid.minor = element_blank())
```

![](README_files/figure-gfm/speed-violin-plot-1.svg)<!-- -->
