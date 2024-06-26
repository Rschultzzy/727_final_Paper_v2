title: "727_paper"
author: "Robert Schultz"
date: "10/25/2020"
output: word_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

```{r cars}
library(tidyverse)
library(xml2)
library(rvest)
library(gtrendsR)
library(magrittr)
library(scales)
library(ggpubr)
library(dplyr)
library(magrittr)
```
*To see interest in bumper sticker for each candidate*
```{r}
Bumper_Sticker <- gtrends(c("Trump Bumper Sticker", "Biden Bumper Sticker"), geo = "US", time = "2020-07-01 2020-11-02", low_search_volume = T)
plot(Bumper_Sticker)
```

```{r}
Yard_Sign <- gtrends(c("Trump Yard Sign", "Biden Yard Sign"), geo = "US", time = "2020-07-01 2020-11-02", low_search_volume = T)
plot(Yard_Sign)
```

```{r}
Flags <- gtrends(c("Trump Flag", "Biden Flag"), geo = "US", time = "2020-07-01 2020-11-02", low_search_volume = T)
plot(Flags)
```

```{r}
head(Yard_Sign$interest_over_time)
```

*Look at Bumper Sticker by State (Using _by_region)*
```{r}
byRegion <- as_tibble(Yard_Sign$interest_by_region) %>%
  group_by(keyword)
view(byRegion)
```
*Map of Biden Bumper Sticker*
```{r}
res <- gtrends("Biden Bumper Sticker", 
               geo = "US",
               time = "2020-07-01 2020-11-02")
 
state <- map_data("state")
 
res$interest_by_region %>%
  mutate(region = tolower(location)) %>%
  filter(region %in% state$region) %>%
  select(region, hits) -> my_df
 
ggplot() +
  geom_map(data = state,
           map = state,
           aes(x = long, y = lat, map_id = region),
           fill="#ffffff", color="#ffffff", size=0.15) +
  geom_map(data = my_df,
           map = state,
           aes(fill = hits, map_id = region),
           color="#ffffff", size=0.15) +
  scale_fill_continuous(low = 'grey', high = 'blue') +
  theme(panel.background = element_blank(),
        axis.ticks = element_blank(),
        axis.text = element_blank(),
        axis.title = element_blank())
```
```{r}
res <- gtrends("Biden Yard Sign",
               geo = "US",
               time = "2020-07-01 2020-11-02")
 
state <- map_data("state")
 
res$interest_by_region %>%
  mutate(region = tolower(location)) %>%
  filter(region %in% state$region) %>%
  select(region, hits) -> my_df
 
ggplot() +
  geom_map(data = state,
           map = state,
           aes(x = long, y = lat, map_id = region),
           fill="#ffffff", color="#ffffff", size=0.15) +
  geom_map(data = my_df,
           map = state,
           aes(fill = hits, map_id = region),
           color="#ffffff", size=0.15) +
  scale_fill_continuous(low = 'grey', high = 'blue') +
  theme(panel.background = element_blank(),
        axis.ticks = element_blank(),
        axis.text = element_blank(),
        axis.title = element_blank())
```
*Trump Bumper Sticker Map*
```{r}
res <- gtrends("Trump Bumper Sticker",
               geo = "US",
               time = "2020-07-01 2020-11-02")
 
state <- map_data("state")
 
res$interest_by_region %>%
  mutate(region = tolower(location)) %>%
  filter(region %in% state$region) %>%
  select(region, hits) -> my_df
 
ggplot() +
  geom_map(data = state,
           map = state,
           aes(x = long, y = lat, map_id = region),
           fill="#ffffff", color="#ffffff", size=0.15) +
  geom_map(data = my_df,
           map = state,
           aes(fill = hits, map_id = region),
           color="#ffffff", size=0.15) +
  scale_fill_continuous(low = 'grey', high = 'red') +
  theme(panel.background = element_blank(),
        axis.ticks = element_blank(),
        axis.text = element_blank(),
        axis.title = element_blank())
```
*Trump Yard Sign Map*
```{r}
res <- gtrends("Trump Yard Sign",
               geo = "US",
               time = "2020-07-01 2020-11-02")
 
state <- map_data("state")
 
res$interest_by_region %>%
  mutate(region = tolower(location)) %>%
  filter(region %in% state$region) %>%
  select(region, hits) -> my_df
 
ggplot() +
  geom_map(data = state,
           map = state,
           aes(x = long, y = lat, map_id = region),
           fill="#ffffff", color="#ffffff", size=0.15) +
  geom_map(data = my_df,
           map = state,
           aes(fill = hits, map_id = region),
           color="#ffffff", size=0.15) +
  labs(title = "Trump Yard Sign By State") +
  scale_fill_continuous(low = 'grey', high = 'red') +
  theme(panel.background = element_blank(),
        axis.ticks = element_blank(),
        axis.text = element_blank(),
        axis.title = element_blank())
```
```{r}
res <- gtrends("Biden Yard Sign",
               geo = "US",
               time = "2020-06-01 2020-11-02")
 
state <- map_data("state")
 
res$interest_by_region %>%
  mutate(region = tolower(location)) %>%
  filter(region %in% state$region) %>%
  select(region, hits) -> my_df
 
ggplot() +
  geom_map(data = state,
           map = state,
           aes(x = long, y = lat, map_id = region),
           fill="#ffffff", color="#ffffff", size=0.15) +
  geom_map(data = my_df,
           map = state,
           aes(fill = hits, map_id = region),
           color="#ffffff", size=0.15) +
  labs(title = "Trump Yard Sign By State") +
  scale_fill_continuous(low = 'grey', high = 'blue') +
  theme(panel.background = element_blank(),
        axis.ticks = element_blank(),
        axis.text = element_blank(),
        axis.title = element_blank())
```

```{r}
df <- president_polls_3 %>%
    filter(answer %in% c("Trump", "Biden")) %>%
    mutate(date = as.Date(start_date, format = "%m/%d/%y")) %>%
    group_by(state) %>%
    mutate(mean.biden.share = mean(pct[answer == "Biden"])) %>%
    filter(state != "")
```

```{r}
# order by mean biden share
df$state <- with(df, reorder(state, mean.biden.share))
```

```{r}
g <- ggplot(data = df, aes(x = date, y = I(pct/100), colour = factor(answer))) +
    geom_point() +
    geom_smooth(aes(group = answer)) + 
    xlim(as.Date(c('1/7/2020', '30/10/2020'), format = "%d/%m/%Y")) +
    facet_wrap(~state, ncol = 5) +
    theme_bw() +
    scale_colour_manual(values = c("Blue", "Red")) +
    xlab("Polling start date") +
    ylab("Pct Support") +
    theme(legend.position = "top") + 
    scale_y_continuous(labels = scales::percent, breaks = seq(0,1, 0.25))
print(g)
```

```{r}
g2 <- ggplot(data = df %>% filter(mean.biden.share < 55 & mean.biden.share > 45),
            aes(x = date, y = I(pct/100), colour = factor(answer))) +
    geom_point() +
    ylim(0.45, 0.55) + 
    geom_smooth(aes(group = answer)) + 
    xlim(as.Date(c('1/7/2020', '30/10/2020'), format = "%d/%m/%Y")) +
    facet_wrap(~state, ncol = 5) +
    theme_bw() +
    scale_colour_manual(values = c("Blue", "Red")) +
    xlab("Polling start date") +
    ylab("Pct Support") +
    theme(legend.position = "top") + 
    scale_y_continuous(labels = scales::percent, breaks = seq(0,1, 0.25))
print(g2)
```
```{r}
df2 <- president_polls_2 %>%
    filter(answer %in% c("Trump", "Biden")) %>%
    mutate(date = as.Date(start_date, format = "%m/%d/%y")) %>%
    group_by(state) %>%
    mutate(mean.biden.share = mean(pct[answer == "Biden"])) %>%
    filter(state != "")
```

```{r}
# order by mean biden share
df2$state <- with(df2, reorder(state, mean.biden.share))
```

```{r}
g3 <- ggplot(data = df2, aes(x = date, y = I(pct/100), colour = factor(answer))) +
    geom_smooth(aes(group = answer)) + 
    xlim(as.Date(c('1/5/2020', '30/10/2020'), format = "%d/%m/%Y")) +
    theme_bw() +
    scale_colour_manual(values = c("Blue", "Red")) +
    xlab("Polling start date") +
    ylab("Percent Support") +
    theme(legend.position = "top") + 
    scale_y_continuous(labels = scales::percent, breaks = seq(0,1, 0.25))
print(g3)
```
```{r}
g4 <- ggplot(data = df2, aes(x = date, y = I(pct/100), colour = factor(answer))) +
    geom_smooth(aes(group = answer)) + 
    xlim(as.Date(c('1/7/2020', '30/10/2020'), format = "%d/%m/%Y")) +
    theme_bw() +
    scale_colour_manual(values = c("Blue", "Red")) +
    xlab("Polling start date") +
    ylab("Percent Support") +
    theme(legend.position = "top") + 
    scale_y_continuous(labels = scales::percent, breaks = seq(0,.5, 0.25))
print(g4)
```
```{r}
c1 <- c(0.61, .190, .223, .140, .262, .331, .379, .327, .340, .352, .366, .416, .386, .360)
c2 <- c(.127, .222, .315, .264, .329, .427, .380, .298, .265, .271, .238, .267, .301,.245)

plot(c1, type="o", col = "green", xlab = "Days", ylab = " Negative Correlation",
     main = "Correlation up to 09/01/20 - YARD SIGNS" )
```
```{r}
plot(c2, type="o", col = "orange", xlab = "Days", ylab = "Negative Correlation",
     main = "Correlation up to 09/01/20 - BUMPER STICKERS")
```
```{r}
par(mfrow=c(2,1))
plot(c1, type="o", col = "red", xlab = "Days", ylab = "Correlation",
     main = "Correlation up to 09/01/20")
plot(c2, type="o", col = "blue", xlab = "Days", ylab = "Correlation")
```


```{r}
c3 <- c(0.397, .451, .462, .469, .406, .370, .360, .386, .396, .365, .312, .310, .305, .319)
c4 <- c(.247, .292, .285, .294, .306, .291, .263, .296, .339, .326, .344, .359, .348, .265)


plot(c3, type="o", col = "green", xlab = "Days", ylab = "Negative Correlation",
     main = "Correlation After 09/01/20 - YARD SIGNS")
plot(c4, type="o", col = "orange", xlab = "Days", ylab = "Negative Correlation", 
     main = "Correlation After 09/01/20 - BUMPER STICKERS")
```
```{r}
plot(c4, type="o", col = "green", xlab = "Days", ylab = "Correlation",
     main = "Correlation After 09/01/20")
```
