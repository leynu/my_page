---
title: "For #TidyTuesday - Chocolate Ratings"
subtitle: "A Tile plot"
author: "LN"
summary: " "
date: 2022-02-23T21:13:14-05:00
categories: ["R"]
tags: ["R Markdown", "plot", "TidyTuesday"]
---




The data comes from [Flavors of Cacao](http://flavorsofcacao.com/chocolate_database.html) by way of [Georgios and Kelsey](https://github.com/rfordatascience/tidytuesday/issues/408).
The code can also be found [here:](https://github.com/leynu/Jan2022_cacao) 



```r
## ---------------------------
## TidyTuesday 2022-01-18 (Week 3)
##
## Author: Leyla Nunez
## Date Created: 2022-01-23
## ---------------------------

# Libraries
library(here)
library(tidyverse)
library(camcorder)
library(MetBrewer) #Met Palette Generator
library(ggplot2)
library(hrbrthemes)
library(viridis)
library(showtext)
library(sysfonts)
library(ggstream)
library(cowplot)
library(patchwork)

# Adds
showtext_auto()
font_add_google("Pacifico")
font_add_google("Josefin Sans")
font_add_google("Lobster Two")

font1 <- "Pacifico"
font2 <- "Josefin Sans"
font3 <- "Lobster Two"

#color palettes
pal <- met.brewer("Homer2")
pal1 <- met.brewer("Greek", n=5)

# Chocolate Ratings
# The data this week comes from Flavors of Cacao by way of Georgios and Kelsey.
# Some analysis from 2017 on this data at Kaggle.
chocolate <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2022/2022-01-18/chocolate.csv')
summary(chocolate)

South_America <- c("Argentina", "Bolivia", 
                   "Brazil",
                   "Chile",
                   "Colombia",
                   "Ecuador",
                   "French Guiana",
                   "Guyana",
                   "Paraguay",
                   "Peru",
                   "Suriname",
                   "Uruguay", "Venezuela")

a <- chocolate %>% 
  mutate(cocoa_num = readr::parse_number(cocoa_percent)) %>% 
  filter(country_of_bean_origin %in% South_America & cocoa_num >= 75) %>% 
  mutate(cocoa_cat = cut(cocoa_num,
                         breaks=c(0, 75, 85, 100),
                         labels=c('>= 75', '76-85', '86-100'))) %>% 
  group_by(country_of_bean_origin, review_date, cocoa_cat) %>% 
  mutate(rating_mean = mean(rating)) %>% 
  ungroup() %>% 
  select(cocoa_cat, country_of_bean_origin, review_date, rating_mean) %>% 
  unique()

list_countries <- unique(a$country_of_bean_origin)
list_cocoa_cat <- unique(a$cocoa_cat)


# Global theme
theme_for_all<-   theme(plot.margin = margin(0.15, 0.75, 0.75, 0.75, "cm"),
                        panel.grid.major = element_blank(),
                        panel.grid.minor = element_blank(),
                        axis.title.x = element_blank(),
                        axis.title.y = element_blank(),
                        legend.position = "none",
                        plot.subtitle = element_text(size = 22,
                                                     hjust = 1,
                                                     family = font2,
                                                     face = "bold"
                        ),
                        axis.text.x = element_blank(),
                        axis.text.y = element_text(color="#4f2217")) 

p1 <- a %>% 
  filter(cocoa_cat == list_cocoa_cat[1]) %>% 
  ggplot(aes(review_date, country_of_bean_origin, fill=rating_mean)) +
  geom_tile(colour = "white", size = 0.2) + 
  labs(title = "Chocolate from South America",
       subtitle = "\nChocolate that has less than 75% cacao") +
  theme_ipsum() + 
  theme_for_all + 
  theme(plot.title = element_text(size = 40,
                                  hjust = 1,
                                  family=font1, 
                                  face = "bold",
                                  color="#4f2217"),
        plot.subtitle = element_text(color=pal1[3])) +
  coord_equal() +
  scale_fill_gradientn(colours = pal) +
  scale_x_continuous(breaks = seq(2006, 2021, 1)) 
p1 

p2 <- a %>% 
  filter(cocoa_cat == list_cocoa_cat[2]) %>% 
  ggplot(aes(review_date, country_of_bean_origin, fill=rating_mean)) +
  geom_tile(colour = "white", size = 0.2) + 
  labs(subtitle = "Chocolate that has from 76% up to 85% cacao") +
  theme_ipsum() + 
  theme_for_all + 
  theme(plot.subtitle = element_text(color=pal1[2])) +
  coord_equal() +
  scale_fill_gradientn(colours = pal) +
  scale_x_continuous(limit= c(2006, 2021), 
                     breaks = seq(2006, 2021, 1)) 
p2 

# Needed for p3
# there are surely better solutions 
tbl_null <- tibble(cocoa_cat = '86-100',
                   country_of_bean_origin = "Brazil",
                   review_date = 2022) 

p3 <- a %>% 
  filter(cocoa_cat == list_cocoa_cat[3]) %>% 
  bind_rows(tbl_null) %>% 
  ggplot(aes(review_date, country_of_bean_origin, fill=rating_mean)) +
  geom_tile(colour = "white", size = 0.2) + 
  labs(subtitle = "Chocolate that has from 86% up to 100% cacao") +
  theme_ipsum() + 
  theme_for_all + 
  theme(plot.margin = margin(0.1, 0.75, 2, 0.75, "cm"),
        plot.subtitle = element_text(color=pal1[1]),
        axis.text.x = element_text(color="#4f2217",
                                   angle = 45,
                                   size = 11),
        legend.position = "bottom",
        legend.title =element_blank()
  ) + 
  guides(colour = guide_legend(title.position = "left")) +
  coord_equal() +
  scale_fill_gradientn(colours = pal) +
  scale_x_continuous(limit= c(2006, 2021), 
                     breaks = seq(2006, 2021, 1)) 

p3

# all 3 plots together
p <- p1  / p2  / p3

ggdraw(p) +
  draw_text(text= "The average rating", 
            x=0.42, 
            y=0.055, 
            size=16, 
            fontface = "bold",
            hjust = 0,
            color="#4f2217")+
  draw_text(text = "@leynu_ | Source: Flavors of Cacao by way of Georgios and Kelsey", 
            x=0.95, 
            y=0.015, 
            color="#4f2217", 
            size=10, 
            fontface = "italic",
            hjust = 1) 

# Saving
ggsave("~/Desktop/cacao.png",
       width =8, 
       height=12)
```
