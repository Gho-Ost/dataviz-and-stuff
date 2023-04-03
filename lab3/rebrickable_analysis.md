---
title: "Rebrickable exploration"
author: "Bartek"
date: "2023-04-03"
output: 
  html_document: 
    css: custom.css
    theme: null
    keep_md: yes
---

Prerequisite libraries:


```r
#install.packages('R.utils', repos = "http://cran.us.r-project.org")
#install.packages("waffle", repos = "http://cran.us.r-project.org")
#install.packages("treemapify", repos = "http://cran.us.r-project.org")
#install.packages("shinythemes", repos = "http://cran.us.r-project.org")
library(data.table)
library(tidyr)
library(ggplot2)
library(dplyr)
library(waffle)
library(treemapify)
library(shiny)
library(shinythemes)
```

***

## Colors

***

In the next section we will explore the ***colors*** data of the ***rebrickable*** dataset.



Here are the ***top 20*** most frequent colors based on the ***inventory_parts*** table quantities:

```r
colors <- fread("rebrickable/colors.csv.gz")
inv_parts <- fread("rebrickable/inventory_parts.csv.gz")

color_id_sums <- inv_parts %>% group_by(color_id) %>% summarise(sum = sum(quantity))

color_sums <- inner_join(x=colors, y=color_id_sums, by=join_by(id==color_id))

color_sums <- color_sums %>% mutate(
  r = paste("0x", substr(rgb, start=1, stop=2), sep=''),
  g = paste("0x", substr(rgb, start=3, stop=4), sep=''),
  b = paste("0x", substr(rgb, start=5, stop=6), sep='')
)
```


```r
top_colors <- color_sums %>% arrange(sum) %>% tail(top_n) %>% mutate(order = seq.int(top_n))

top_colors %>% ggplot(aes(area=sum, fill=factor(order), label=rgb)) + 
  geom_treemap() +
  geom_treemap_text(min.size = 8, size=20, color=c(rep("#ffffff", nrow(top_colors)-3), 1, rep("#ffffff", 2))) +
  scale_fill_manual(values=rgb(top_colors$r, top_colors$g, top_colors$b, maxColorValue=255)) +
  ggtitle("Most frequent colors") +
  theme(legend.position="none", plot.background = element_rect(fill="#2d2d2d"),
        plot.title=element_text(hjust=.5, size=25, colour = "#d1d1d1"))
```

![](rebrickable_analysis_files/figure-html/topncolors-1.png)<!-- -->


On the figure we can observe that the most common colors used are different shades of grey. A characteristic tone of yellow, blue and red are also present in the treemap.

















