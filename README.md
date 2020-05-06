# Animal Crossing New Horizons - Tidy Tuesday

# Question: What if animal crossing villager's personalities are affected by their birth month?

# Method

```R
library(ggplot2)
library(here)
library(gganimate)
library(ggdark)
library(dplyr)

critic <- readr::read_tsv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-05-05/critic.tsv')
user_reviews <- readr::read_tsv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-05-05/user_reviews.tsv')
items <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-05-05/items.csv')
villagers <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-05-05/villagers.csv')

# do birth month affect personality?
villagers_bd <- villagers %>% 
  tidyr::separate(birthday, into = c("month", "date"), "-") %>% 
  select(month, personality)

# convert frequency of personality by month into a df
villagers_table <- data.frame(table(villagers_bd), stringsAsFactors = FALSE) # cols = month, personality, freq

bd_personality <- ggplot(villagers_table, aes(x = as.factor(month), y = Freq, col = personality, group = personality)) +
  geom_line(size = 2) +
  geom_text(size = 12, aes(x="10", y = 10, label = personality)) +
  ggtitle("ACNH personality by month") +
  scale_x_discrete(name = "month", limits = as.character(seq(1, 12))) +
  scale_color_brewer(palette = "Set1") +
  dark_theme_gray() +
  theme(legend.position = "none") + # add last because dark_mode() automatically adds legend.
  theme(text = element_text(size = 18)) + 
  # animation
  transition_states(personality)

animate(bd_personality, duration = 15, fps = 3, width = 800, height = 800, renderer = gifski_renderer())
anim_save(here("gifs/ACNH_horoscopes.gif"))
```

# Results

* Villagers born in March are mostly normal.

* Villagers are more smug if born in June or December.

* Villagers born in June are least likely to be snooty.

* Uchi personalities are highest between March and June

# Takeaways

* Initially there were too many personalities to overlay in one plot (8), so my solution was to show one personality at a time with `gganimate` to minimze distraction and keep the audience engaged.

* Use `transition_states` to go through each personality; this is not an example of layering. 

* Use `theme(legend.position = "none")` after `dark_theme_gray` because ggdark will automatically add a legend.

disclaimer: I personally do not believe in horoscopes, but I am creative enough to ask and answer this question. 
