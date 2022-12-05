Class 5: Data Visualization with GGPLOT
================
Julia Ainsworth

- <a href="#our-first-plot" id="toc-our-first-plot">Our first plot</a>
- <a
  href="#new-graph-for-genes-upregulateddownregulated-with-drug-treatment"
  id="toc-new-graph-for-genes-upregulateddownregulated-with-drug-treatment">New
  graph for genes upregulated/downregulated with drug treatment</a>
- <a href="#gapminder-extension" id="toc-gapminder-extension">gapminder
  extension</a>
- <a href="#bar-charts" id="toc-bar-charts">Bar charts</a>
- <a href="#animation" id="toc-animation">Animation:</a>
- <a href="#combining-plots-for-10"
  id="toc-combining-plots-for-10">Combining plots for 10:</a>

# Our first plot

R has base graphics

Keyboard shortcut to insert a chunk of code: opt + cmd + i

``` r
plot(cars)
```

![](Class05_files/figure-gfm/unnamed-chunk-1-1.png)

How would I plot this with ggplot2?

If I hadn’t installed ggplot2 yet, this would be the first step:

``` r
# install.packages("ggplot2")
```

But before I use it, need to re-load (called a “library call”) in every
session. This is the case for every package ‘library(ggplot2)’

``` r
# library(ggplot2)
library(ggplot2)
```

Every ggplot needs at least 3 layers:

- **Data** (i.e. the data.frame, here cars)

- **Aes** (the aesthetic mapping of our data to what we want to plot)

- **Geoms** (How we want to plot this stuff!)

Simplest version of a ggplot:

``` r
ggplot(data=cars) +
  aes(x=speed, y=dist) + 
  geom_point()
```

![](Class05_files/figure-gfm/unnamed-chunk-4-1.png)

Adding a trend line to our data:

``` r
ggplot(data=cars) +
  aes(x=speed, y=dist) + 
  geom_point() +
  geom_smooth(method = lm, se = FALSE)+
  xlab("Speed") +
  ylab("Distance") +
  theme_bw()
```

    `geom_smooth()` using formula 'y ~ x'

![](Class05_files/figure-gfm/unnamed-chunk-5-1.png)

# New graph for genes upregulated/downregulated with drug treatment

``` r
url <- "https://bioboot.github.io/bimm143_S20/class-material/up_down_expression.txt"
genes <- read.delim(url)
head(genes)
```

            Gene Condition1 Condition2      State
    1      A4GNT -3.6808610 -3.4401355 unchanging
    2       AAAS  4.5479580  4.3864126 unchanging
    3      AASDH  3.7190695  3.4787276 unchanging
    4       AATF  5.0784720  5.0151916 unchanging
    5       AATK  0.4711421  0.5598642 unchanging
    6 AB015752.4 -3.6808610 -3.5921390 unchanging

``` r
nrow(genes)
```

    [1] 5196

``` r
colnames(genes)
```

    [1] "Gene"       "Condition1" "Condition2" "State"     

``` r
ncol(genes)
```

    [1] 4

``` r
table(genes$State)
```


          down unchanging         up 
            72       4997        127 

``` r
round( table(genes$State)/nrow(genes) * 100, 2 )
```


          down unchanging         up 
          1.39      96.17       2.44 

Answers to questions: Q: There are 5196 genes in this data set.

Q: There are `r`ncol(genes)\` columns in the genes data set. Graph of
the genes up and downregulated:

``` r
p <- ggplot(genes) + 
  aes(Condition1, Condition2, col=State) +
  geom_point()
p
```

![](Class05_files/figure-gfm/unnamed-chunk-7-1.png)

Graph showing the color changing: does p carry over? Answer: yes!

``` r
p + 
  scale_color_manual( values =c("blue", "gray", "red")) +
  labs(title = "Gene Expression Changes Upon Drug Treatment",
       x = "Control (no drug)",
       y = "Drug Treatment")
```

![](Class05_files/figure-gfm/unnamed-chunk-8-1.png)

# gapminder extension

``` r
library(gapminder)
library(dplyr)
```


    Attaching package: 'dplyr'

    The following objects are masked from 'package:stats':

        filter, lag

    The following objects are masked from 'package:base':

        intersect, setdiff, setequal, union

``` r
url <- "https://raw.githubusercontent.com/jennybc/gapminder/master/inst/extdata/gapminder.tsv"

gapminder <- read.delim(url)

gapminder_2007 <- gapminder %>% filter(year==2007)
```

First basic scatterplot of gapminder

``` r
ggplot(gapminder_2007) +
  aes(gdpPercap, lifeExp, size= pop) +
  geom_point(alpha = 0.5) +
  scale_size_area(max_size = 10)
```

![](Class05_files/figure-gfm/unnamed-chunk-10-1.png)

Redoing it for 1957

``` r
gapminder_1957 <- gapminder %>% filter(year==1957)

ggplot(gapminder_1957) +
  aes(gdpPercap, lifeExp, col = continent, size = pop) +
  geom_point(alpha = 0.7) +
  scale_size_area(max_size = 15)
```

![](Class05_files/figure-gfm/unnamed-chunk-11-1.png)

Facet wrapping: getting two graphs next to each other

``` r
gapminder_1957 <- gapminder %>% filter(year==1957 | year==2007)

ggplot(gapminder_1957) +
  aes(gdpPercap, lifeExp, color = continent, size = pop)+
  geom_point(alpha = 0.7) +
  scale_size_area(max_size = 10) +
  facet_wrap(~year)
```

![](Class05_files/figure-gfm/unnamed-chunk-12-1.png)

# Bar charts

``` r
gapminder_top5 <- gapminder %>%
  filter(year==2007) %>%
  arrange(desc(lifeExp)) %>%
  top_n(5, pop)

gapminder_top5
```

            country continent year lifeExp        pop gdpPercap
    1 United States  Americas 2007  78.242  301139947 42951.653
    2         China      Asia 2007  72.961 1318683096  4959.115
    3        Brazil  Americas 2007  72.390  190010647  9065.801
    4     Indonesia      Asia 2007  70.650  223547000  3540.652
    5         India      Asia 2007  64.698 1110396331  2452.210

Bar chart:

``` r
ggplot(gapminder_top5) +
  geom_col(aes(x = country, y = lifeExp))
```

![](Class05_files/figure-gfm/unnamed-chunk-14-1.png)

Putting colors in:

``` r
ggplot(gapminder_top5) +
  geom_col(aes(x = country, y = pop, fill = lifeExp))
```

![](Class05_files/figure-gfm/unnamed-chunk-15-1.png)

Plotting population size by country:

``` r
gapminder_top5pop <- gapminder %>%
  filter(year==2007) %>%
  arrange(desc(pop)) %>%
  top_n(5, pop)

gapminder_top5pop
```

            country continent year lifeExp        pop gdpPercap
    1         China      Asia 2007  72.961 1318683096  4959.115
    2         India      Asia 2007  64.698 1110396331  2452.210
    3 United States  Americas 2007  78.242  301139947 42951.653
    4     Indonesia      Asia 2007  70.650  223547000  3540.652
    5        Brazil  Americas 2007  72.390  190010647  9065.801

Barplot:

``` r
ggplot(gapminder_top5pop) +
  aes(x=reorder(country, -pop), y=pop, fill = country)+
  geom_col(col="gray30") +
  guides(fill="none")
```

![](Class05_files/figure-gfm/unnamed-chunk-17-1.png)

Flipping bar charts:

``` r
head(USArrests)
```

               Murder Assault UrbanPop Rape
    Alabama      13.2     236       58 21.2
    Alaska       10.0     263       48 44.5
    Arizona       8.1     294       80 31.0
    Arkansas      8.8     190       50 19.5
    California    9.0     276       91 40.6
    Colorado      7.9     204       78 38.7

``` r
USArrests$State <- rownames(USArrests)
ggplot(USArrests) +
  aes(x=reorder(State,Murder), y=Murder) +
  geom_col() +
  coord_flip()
```

![](Class05_files/figure-gfm/unnamed-chunk-19-1.png)

Segmented bar plot:

``` r
ggplot(USArrests) +
  aes(x=reorder(State,Murder), y=Murder) +
  geom_point() +
  geom_segment(aes(x=State,
                   xend=State,
                   y=0,
                   yend=Murder), color = "blue")+
  coord_flip()
```

![](Class05_files/figure-gfm/unnamed-chunk-20-1.png)

# Animation:

``` r
library(gapminder)
library(gganimate)

# Setup nice regular ggplot of the gapminder data
ggplot(gapminder, aes(gdpPercap, lifeExp, size = pop, colour = country)) +
  geom_point(alpha = 0.7, show.legend = FALSE) +
  scale_colour_manual(values = country_colors) +
  scale_size(range = c(2, 12)) +
  scale_x_log10() +
  # Facet by continent
  facet_wrap(~continent) +
  # Here comes the gganimate specific bits
  labs(title = 'Year: {frame_time}', x = 'GDP per capita', y = 'life expectancy') +
  transition_time(year) +
  shadow_wake(wake_length = 0.1, alpha = FALSE)
```

# Combining plots for 10:

``` r
library(patchwork)

p1 <- ggplot(mtcars) + geom_point(aes(mpg, disp))
p2 <- ggplot(mtcars) + geom_boxplot(aes(gear, disp, group = gear))
p3 <- ggplot(mtcars) + geom_smooth(aes(disp, qsec))
p4 <- ggplot(mtcars) +geom_bar(aes(carb))

(p1 | p2 |p3) / 
  p4
```

    `geom_smooth()` using method = 'loess' and formula 'y ~ x'

![](Class05_files/figure-gfm/unnamed-chunk-22-1.png)
