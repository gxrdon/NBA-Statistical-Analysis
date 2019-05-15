# NBA-Statistical-Analysis


We chose to conduct an analysis on NBA statistics from the 2014/15 season. In this tutorial, we will tidy and parse our data so that we can further analyze it. After that, we will test our hypotheses. Finally, we will use machine learning to predict the MVP for this season and future successes in the NBA. 

To begin, we first need to get the data that we want to analyze. The dataframe that we will be pulling from can be found at https://www.kaggle.com/drgilermo/nba-players-stats-20142015/. The first step is to specify any libraries you may need and then get the url of the website that you want to work with. You then inspect the page and find the id of the table that you want to pull data from using html_node(). Lastly, you take the results of that and turn it into a table using html_table(). 

The "%>%" is an operation that allows the user to send a dataset into the first parameter of the next function. For example, imagine if you had a function add() that takes a dataframe and an integer. You can either do add(dataframe, integer) or you can do dataframe %>% add(integer) which will have the same effect. In the long run, using dplyr pipelines (%>%) will save a lot of space and confusion. 

```{r setup, include=FALSE}
library(tidyverse)
library(rvest)
library(plyr)
library(dplyr)
library(tidyr)
knitr::opts_chunk$set(echo = TRUE)

url <- read_html("https://www.kaggle.com/drgilermo/nba-players-stats-20142015")

tables <- url %>%
  html_node("table.sc-kaCJFH.cyQdkY") %>%
  html_table()
```
