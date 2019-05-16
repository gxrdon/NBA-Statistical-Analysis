# Statistical Analysis of the NBA 2014-15 Season


We chose to conduct an analysis on NBA statistics from the 2014/15 season. In this tutorial, we will tidy and parse our data so that we can further analyze it. After that, we will test our hypotheses. Finally, we will use machine learning to predict the MVP for this season and future successes in the NBA. 

To begin, we first need to get the data that we want to analyze. The dataframe that we will be pulling from can be found at https://www.kaggle.com/drgilermo/nba-players-stats-20142015/. The first step is to specify any libraries you may need and then get the path of the file that you want to use. For this case, the table is located under our "NBAStatisticalAnalysis" folder on my local C drive. Since the data we want to use is coming from an Excel Spreadsheet, we use the "readxl" library to read the excel file and create a table out of it. The "<-" syntax simply stores the resulting data as a variable so we now have NBAStats which is the table we will be working with for the remainder of this tutorial.

```{r setup}
library(readxl)
library(tidyverse)
path <- "C:/Users/Andrew/Documents/NBAStatisticalAnalysis/players_stats.xlsx"
NBAStats <- read_excel(path)
```

Now that we have the table that we need to analyze, it's time to tidy it. This means that we are going to remove anything from the table that might skew our results. For example, if a player's position is not listed, this could be trouble since we are going to analyze things such as rebounds based on position, shooting percentage based on position, etc. 

We can clean out table by simply using the mutate function with conditional statements inside of it to create a new table. A conditional statement refers to a querry that checks if the current entity fulfills a certain trait. For example, if the current player's position isn't listed, set them to "NA". Otherwise, keep it the same. In the table we're working with, it seems like there are multiple different attributes that are missing for some players so we will utilize the functionality discussed above to touch these areas up in the next step. 

Another way to clean up data is to change column names. Currently, our table has some column names with "%" and numbers in them. This will cause problems later on so it's important to change it now. The second chunk of code below is selecting the targeted column and setting it's name to the name that we selected. Now, we won't have issues with any of our column names down the line. 

In this example, we are looking for players whose position, age, team, etc. are unspecified and changing them to NA. To display the results of this call, we use the select function which takes a dataset and then the columns you want to display. We then use a pipeline (explained later) to send the result of that into a slice. A slice does the same thing that select does but for entities instead.

```{r}
NBAStats <- NBAStats %>%
  mutate(Birth_Place = ifelse(Birth_Place == ' ', NA, Birth_Place)) %>%
  mutate(Age = ifelse(Age == ' ', NA, Age)) %>%
  mutate(Team = ifelse(Team == ' ', NA, Team)) %>%
  mutate(Weight = ifelse(Weight == ' ', NA, Weight)) %>%
  mutate(Height = ifelse(Height == ' ', NA, Height)) 

colnames(NBAStats)[colnames(NBAStats)=="FT%"] <- "FTP"
colnames(NBAStats)[colnames(NBAStats)=="3PM"] <- "TPM"
colnames(NBAStats)[colnames(NBAStats)=="FG%"] <- "FGP"
colnames(NBAStats)[colnames(NBAStats)=="3P%"] <- "TPP"
colnames(NBAStats)[colnames(NBAStats)=="3PA"] <- "TPA"

NBAStats %>% 
  select(1, 25, 31, 32) %>% 
  slice(25:35)
```

The "%>%" above is an operation that allows the user to send a dataset into the first parameter of the next function. For example, imagine if you had a function add() that takes a dataframe and an integer. You can either do add(dataframe, integer) or you can do dataframe %>% add(integer) which will have the same effect. In the long run, using dplyr pipelines (%>%) will save a lot of space and confusion. 

Lastly, let's remove any players that have less than 50 minutes played per season due to injuries or something else. They may skew our data later on. We can simply use the filter() function to specify the entities that have more than 50 minutes in this season. 

```{r}
NBAStats <- NBAStats %>% filter(MIN > 50)

NBAStats
```

Now that the data is cleaned, we can begin to use this cleaned data to make graphs that allows us to see statistics such as central tendency, correlations between variables, skew in the data, and much more! 

In the following graph, we use ggplot() to create a scatter plot of all player's scoring stats based on the number of minutes they played this season. We expect there to be a correlation here. 

```{r}
NBAStats %>% 
  ggplot(aes(x=MIN, y=PTS)) +
  geom_point()
```
This gives us an idea of the correlation between minutes played and points per season, but we can do better. Let's now add a regression line to make the trend more clear.

```{r}
NBAStats %>% 
  ggplot(aes(x=MIN, y=PTS)) + 
  geom_point() + 
  geom_smooth(method=lm)
```

Lastly, we can color these points based on the team that their on. Since the Golden State Warriors won this season, let's color them based on whether or not they're on Golden State (GSW). We first create a new column and initialize all the entities value for that to false. After that, we use a simple loop to populate the new column with true if they're on the Warriors and false otherwise. We also have the players whose teams were unknown and they get their own color. 

As you can see from this graph, Golden State has some of the best scorers per minutes played in the league. On the contrary, they also have some of the worst scorers per minutes played (perhaps they're defensive players).

```{r}
(NBAStats$isGSW = FALSE)

for(i in 1:490){
  NBAStats[i, "isGSW"] <- ifelse(NBAStats[i, "Team"] == 'GSW', TRUE, FALSE)
}

NBAStats %>% 
  ggplot(aes(x=MIN, y=PTS, color=isGSW)) + 
  geom_point() 
```

Lastly, we can also create plots based on categorical variables on the x and numerical values on the y such as the following. In this graph, we create a boxplot to show the correlation between position and rebounds per season. As expected, centers tend to grab the most rebounds while guards tend to not get as many.

```{r}
NBAStats %>% 
  ggplot(aes(x=Pos, y=REB)) + 
  geom_boxplot()
```

Now we're going to move on to the next part of the data science pipeline: hypothesis testing. In this section, we'll make a prediction such as guards tend to score more points and then we will set up a linear model and test whether or not this is true. 

In the first part, we create a linear model of the relationship between points scored and minutes played in a season. The result gives us two values for the formula B0 + B1X where X represents the number of minutes played, -72.85 is the starting interecept and then .4733 is the amount of points that a players scores on average per minute. 

We can already tell that this isn't a very strong correlation by doing some simple math. Say a player only had 10 minutes of gametime during a season. When plugging that in, we get -72.85 + (.4733*10) = -68.5, basically saying that the average player that plays 10 minutes a season scores -68 points a season which doesn't make any sense. 

```{r}
correlation <- lm(PTS~MIN, data=NBAStats) 

correlation
```

Now we will begin to look at regression statistics and see how they match up. By calling head() on our linear model, it gives us all of the details, most importantly the fitted values, points and minutes. We can see that for low points and minutes, the fitted value is underestimated. On the contrary, for low points and high minutes, the fitted value is extremely overestimated. This alone shows the lack of a correlation, but let's go one step further. 

```{r}
corr_regress <- correlation %>% augment()

corr_regress %>% head()
```

The last thing we'll do here is plot the residuals against the fitted values. If there is a strong correlation, we expect that the line of regression is a horizontal line at 0. In this case, it starts above 0, curves and sits around 0 for a little bit and then takes a large upturn away from 0. We can now say for sure that there isn't a great correlation between points and minutes played. 

```{r}
corr_regress %>%
  ggplot(aes(x=.fitted, y=.resid)) +
    geom_point() +
    geom_smooth() +
    labs(title="Residuals over time",
         x = "fitted",
         y = "residual")
```

