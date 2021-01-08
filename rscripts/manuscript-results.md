---
title: "Different populations agree on which moral arguments underlie which opinions"
output:
  html_document:
    keep_md: yes
---





```r
# US

us_arg <- read_csv("../data/us-argument-data-clean.csv")

us_arg <- us_arg %>% 
  mutate(
    age_gr = cut(age, 
                 breaks = c(0, median(age, na.rm = TRUE), 100), 
                 labels = c("Younger", "Older")),
    verb_ability = ifelse(wordsum > median(wordsum, na.rm = TRUE),
                          "Higher",
                          "Lower"),
    edu = fct_collapse(edu, 
                       Lower = "<bach", 
                       Higher = c("bach", "grad")),
   position = paste0(issue, "_", type)
  )
        
# UK

uk_arg <- read_csv("../data/uk-argument-data-clean.csv")

uk_arg <- uk_arg %>% 
  mutate(
    age_gr = cut(age, 
                 breaks = c(0, median(age, na.rm = TRUE), 100), 
                 labels = c("Younger", "Older")),
    polviews = cut(polviews_10, 
                   breaks = c(0, 3, 6, 10),
                   include.lowest = TRUE,
                   labels = c("Left", "Moderate", "Right")),
       position = paste0(issue, "_", type)
  ) 

# Israel

il_arg <- read_csv("../data/il-argument-data-clean.csv")
il_arg <- il_arg %>% 
  mutate(
    age_gr = cut(age, 
                 breaks = c(0, median(age, na.rm = TRUE), 100), 
                 labels = c("Younger", "Older")),
    polviews = cut(polviews_10, 
                   breaks = c(0, 3, 6, 10),
                   include.lowest = TRUE,
                   labels = c("Left", "Moderate", "Right")),
    edu = ifelse(edu > 4, "Higher", "Lower"),
    position = paste0(issue, "_", type)
  ) 
      
# Brazil

br_arg <- read_csv("../data/br-argument-data-clean.csv")
br_arg <- br_arg %>% 
  mutate(
    age_gr = cut(age, 
                 breaks = c(0, median(age, na.rm = TRUE), 100), 
                 labels = c("Younger", "Older")),
    polviews = cut(polviews_10, 
                   breaks = c(0, 3, 6, 10),
                   include.lowest = TRUE,
                   labels = c("Left", "Moderate", "Right")),
    edu = ifelse(edu > 4, "Higher", "Lower"),
    position = paste0(issue, "_", type)
  )

# Combined dataset
common_items <- read_csv("../data/gss-and-bsa-common-items.csv")

comb_arg <- bind_rows(
  us_arg %>% 
    filter(issue %in% common_items$gss_code) %>% 
    mutate(sample = "US"), 
  uk_arg %>% 
    filter(issue %in% common_items$bsa_code) %>% 
    # use GSS variable code for issue to unify between US and UK samples
    rename(bsa_code = issue) %>% 
    left_join(common_items %>% 
                select(issue = gss_code, bsa_code, reverse)) %>% 
    # one BSA item is reversed
    mutate(
      type = case_when(
        reverse == 1 & type == "pro" ~ "against", 
        reverse == 1 & type == "against" ~ "pro",
        TRUE ~ type
      ), 
      position = paste0(issue, "_", type),
      sample = "UK") %>% 
    select(-bsa_code, -reverse), 
  il_arg %>% 
    mutate(sample = "IL"), 
  br_arg %>% 
    mutate(sample = "BR")
)
```


```r
# estimate argument applicability within separate groups defined by group_var

link_by_group <- function(data, group_var) {

group_var <- enquo(group_var)

arg_link_by_group <- data %>% 
    drop_na(!!group_var) %>% 
    mutate(group = fct_relabel(!!group_var,
                               ~as.character(as.numeric(as.factor(.))))) %>%
    group_by(mf, position, group) %>% 
    summarise(link = mean(value)) %>% 
    spread(group, link)

arg_link_by_group %>% 
  drop_na() %>% 
  mutate(group = as_label(group_var))
}

# estimate CCC between argument applicability measures in the groups defined by group_var

ccc_by_group <- function(data, group_var) {
  group_var <- enquo(group_var)
  
  arg_link_by_group <- data %>% 
    drop_na(!!group_var) %>% 
    mutate(group = fct_relabel(!!group_var, ~as.character(as.numeric(as.factor(.))))) %>% 
    group_by(mf, position, group) %>% 
    summarise(link = mean(value), .groups = "drop") %>% 
    spread(group, link) 
  
  arg_link_by_group %>% 
    drop_na() %>% 
    summarise(CCC(`2`, `1`)$rho.c,
              .groups = "drop") %>% 
    mutate(group = as_label(group_var))
}
```


# Study 1. US

## Method


```r
us_sample <- us_arg %>% 
  select(id, age, sex, polviews, edu, verb_ability, issue) %>% 
  distinct() %>% 
  group_by(id, age, sex, polviews, edu, verb_ability) %>% 
  summarise(n_issues = n()) %>% 
  ungroup()

us_sample %>% 
  summarise(n = n(),
            mean(n_issues),
            women = mean(sex == "female", na.rm = TRUE),
            mean_age = mean(age, na.rm = TRUE),
            sd_age = sd(age, na.rm = TRUE),
            liberal = mean(polviews == "liberal", na.rm = TRUE),
            conservative = mean(polviews == "conservative", na.rm = TRUE), 
            verb_higher = mean(verb_ability == "Higher"), 
            edu_higher = mean(edu == "Higher"))
```

```
## # A tibble: 1 x 9
##       n `mean(n_issues)` women mean_age sd_age liberal conservative verb_higher
##   <int>            <dbl> <dbl>    <dbl>  <dbl>   <dbl>        <dbl>       <dbl>
## 1   464             19.0 0.562     39.3   12.1   0.522        0.478       0.472
## # … with 1 more variable: edu_higher <dbl>
```

## Results


```r
us_arg_connection  <- us_arg %>% 
  group_by(mf, position) %>% 
  summarise(link = mean(value))

us_arg_connection <- us_arg_connection %>% 
  mutate(iqr = IQR(link), 
         lower = quantile(link, .25), 
         higher = quantile(link, .75),
         outl_type = case_when(
          link < lower - 3*iqr ~ 8, 
          link > higher + 3*iqr ~ 8,
          link < lower - 1.5*iqr ~ 1, 
          link > higher + 1.5*iqr ~ 1
          ))

us_arg_connection %>% 
  ggplot(aes(mf, link)) +
  geom_boxplot(outlier.shape = NA) +
  geom_point(aes(shape = outl_type), size = 1.5, stroke = .5) +
  scale_shape_identity() +
  theme(legend.position = "none") +
  labs(y = "Arguments applicablity", x = "Moral foundation")
```

![Figure 1. Boxplots showing how applicability, estimated in the entire sample of Study 1, of eight different kinds of moral arguments varied across 196 moral opinions. The box represents the interquartile (IQ) range with the dark line indicating the median. The whiskers reach the min and max values in case these are at most 1.5 times the box height outside the IQ range. Circles and stars signify outliers (values between 1.5 and 3 times the IQ range) and extreme outliers (more than 3 times the IQ range), respectively.](manuscript-results_files/figure-html/us_boxpl-1.jpeg)

```r
#ggsave("fig1_us-arg-applicability.jpeg", width = 180, height = 80, units = "mm", dpi = 300)
```


```r
# Simulate 100 random splits in two even groups and calculate the mean CCC
set.seed(8404)
us_rand_gr_mult <- tibble(id = rep(unique(us_arg$id), 100),
                       draw = rep(1:100, 
                                    each = length(unique(us_arg$id)))) %>%
  mutate(rand_gr = rbinom(n = n(), size = 1, prob = .5),
         rand_gr = factor(rand_gr))


us_ccc_rand_gr <- left_join(us_arg, us_rand_gr_mult) %>%
  group_by(draw, mf, position, rand_gr) %>%
  summarise(link = mean(value)) %>%
  spread(rand_gr, link) %>% 
  group_by(draw) %>%
  summarise(DescTools::CCC(`0`, `1`)$rho.c,
            r = cor(`0`, `1`, use = "pair")) %>% 
  summarise(rand_gr = mean(est))
```



```r
us_mf_agreement <- bind_rows(
  ccc_by_group(us_arg, polviews),
  ccc_by_group(us_arg, sex),
  ccc_by_group(us_arg, age_gr),
  ccc_by_group(us_arg, verb_ability),
  ccc_by_group(us_arg, edu)
)

us_mf_agreement <- us_mf_agreement %>% 
  mutate(label = sprintf("%.2f [%.2f, %.2f]", est, lwr.ci, upr.ci), 
         group = factor(group,
                        levels = c("sex", 
                                   "age_gr",
                                   "polviews", 
                                   "edu",
                                   "verb_ability"), 
                        labels = c("Sex", 
                                   "Age", 
                                   "Ideology", 
                                   "Education",
                                   "Verbal ability"))) %>% 
  arrange(group)

us_mf_agreement %>%  
  select(group, label) %>% 
  kbl(col.names = c(" ", "CCC with 95% CI"),
      caption = "Table 2. CCC values with 95% confidence interval.") %>% 
  kable_classic(full_width = FALSE) 
```

<table class=" lightable-classic" style='font-family: "Arial Narrow", "Source Sans Pro", sans-serif; width: auto !important; margin-left: auto; margin-right: auto;'>
<caption>Table 2. CCC values with 95% confidence interval.</caption>
 <thead>
  <tr>
   <th style="text-align:left;">   </th>
   <th style="text-align:left;"> CCC with 95% CI </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Sex </td>
   <td style="text-align:left;"> 0.93 [0.92, 0.94] </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Age </td>
   <td style="text-align:left;"> 0.93 [0.92, 0.93] </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Ideology </td>
   <td style="text-align:left;"> 0.92 [0.91, 0.93] </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Education </td>
   <td style="text-align:left;"> 0.93 [0.93, 0.94] </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Verbal ability </td>
   <td style="text-align:left;"> 0.91 [0.90, 0.91] </td>
  </tr>
</tbody>
</table>



```r
us_mf_link_by_gr <- bind_rows(
  link_by_group(us_arg, polviews),
  link_by_group(us_arg, sex),
  link_by_group(us_arg, age_gr),
  link_by_group(us_arg, verb_ability),
  link_by_group(us_arg, edu)
)

scatter_plot <- function(data) {

  data %>% 
    ggplot(aes(`1`, `2`)) +
    geom_point(alpha = .2) +
    geom_abline() +
    geom_smooth(method = "lm", se = FALSE) +
    scale_x_continuous(limits = c(0, 1), 
                       breaks = c(0, .25, .50, .75, 1))+ 
    scale_y_continuous(limits = c(0, 1), 
                       breaks = c(0, .25, .5, .75, 1)) +
    theme(axis.text = element_text(size = 9),
          plot.margin = margin(t = 1, r = 5, b = 1, l = 5))
}

pl1 <-  us_mf_link_by_gr %>% 
  filter(group == "sex") %>% 
  scatter_plot() +
  labs(x = "Women", y = "Men") +
  theme(strip.background = element_blank(), strip.text = element_blank())

pl2 <-  us_mf_link_by_gr %>% 
  filter(group == "age_gr") %>% 
  scatter_plot() +
  labs(x = "Younger", y = "Older")+
  theme(strip.background = element_blank(), strip.text = element_blank())

pl3 <- us_mf_link_by_gr %>% 
  filter(group == "polviews") %>% 
  scatter_plot() +
  labs(x = "Liberals", y = "Conservatives")

pl4 <- us_mf_link_by_gr %>% 
  filter(group == "edu") %>% 
  scatter_plot() +
  labs(x = "Lower education", y = "Higher education")+
  theme(strip.background = element_blank(), strip.text = element_blank())

pl5 <-  us_mf_link_by_gr %>% 
  filter(group == "verb_ability") %>% 
  scatter_plot() +
  labs(x = "Higher verbal ability", y = "Lower\nverbal ability")+
  theme(strip.background = element_blank(), strip.text = element_blank())


cowplot::plot_grid(pl1, pl2, pl3, pl4, pl5, 
                   labels = "AUTO", 
                   label_size = 11)
```

![Figure 2. Scatter plots of 1,568 argument applicability scores (8 types of arguments by 196 moral opinions), measured in different groups in the United States: women vs. men (A),  younger vs. older (B), liberals vs. conservatives (C), higher vs. lower education (D), and higher vs. lower verbal ability (E). Regression lines in blue and reference lines for perfect agreement in black.](manuscript-results_files/figure-html/unnamed-chunk-1-1.jpeg)

```r
# ggsave("fig2_us-scatter-by-groups.jpeg", width = 180, height = 100, 
#        units = "mm", dpi = 300)
```


# Study 2. UK

## Method


```r
uk_sample <- uk_arg %>% 
  select(polviews, id, issue, age, sex, edu) %>% 
  distinct() %>% 
  group_by(polviews, id, age, sex, edu) %>% 
  summarise(n_issues = n(), .groups = "drop")

uk_sample %>% 
  summarise(n = n(),
            mean(n_issues),
            women = mean(sex == "Female", na.rm = TRUE),
            mean_age = mean(age, na.rm = TRUE),
            sd_age = sd(age, na.rm = TRUE),
            left = mean(polviews == "Left", na.rm = TRUE),
            moderate = mean(polviews == "Moderate", na.rm = TRUE),
            right = mean(polviews == "Right", na.rm = TRUE),
            edu_higher = mean(edu == "higher", na.rm = TRUE))
```

```
## # A tibble: 1 x 9
##       n `mean(n_issues)` women mean_age sd_age  left moderate right edu_higher
##   <int>            <dbl> <dbl>    <dbl>  <dbl> <dbl>    <dbl> <dbl>      <dbl>
## 1   904             12.3 0.580     38.3   12.8 0.253    0.409 0.338      0.521
```

## Results


```r
uk_arg_connection  <- uk_arg %>% 
  group_by(mf, position) %>% 
  summarise(link = mean(value))

uk_arg_connection <- uk_arg_connection %>% 
  mutate(iqr = IQR(link), 
         lower = quantile(link, .25), 
         higher = quantile(link, .75),
         outl_type = case_when(
          link < lower - 3*iqr ~ 8, 
          link > higher + 3*iqr ~ 8,
          link < lower - 1.5*iqr ~ 1, 
          link > higher + 1.5*iqr ~ 1
          ))

uk_arg_connection %>% 
  ggplot(aes(mf, link)) +
  geom_boxplot(outlier.shape = NA) +
  geom_point(aes(shape = outl_type), size = 1.5, stroke = .5) +
  scale_shape_identity() +
  theme(legend.position = "none") +
  labs(y = "Arguments applicablity", x = "Moral foundation")
```

![Figure 3. Boxplots (defined in Figure 2) showing how applicability, estimated in the entire sample of Study 2, of eight different kinds of moral arguments varied across 216 moral opinions.](manuscript-results_files/figure-html/uk_boxpl-1.jpeg)

```r
# ggsave("fig3_uk-arg-applicability.jpeg", width = 180, height = 80, units = "mm", dpi = 300)
```


```r
# Simulate 100 random splits in two even groups and calculate the mean CCC
set.seed(8405)
uk_rand_gr_mult <- tibble(id = rep(unique(uk_arg$id), 100),
                       draw = rep(1:100, 
                                    each = length(unique(uk_arg$id)))) %>%
  mutate(rand_gr = rbinom(n = n(), size = 1, prob = .5),
         rand_gr = factor(rand_gr))


uk_ccc_rand_gr <- left_join(uk_arg, uk_rand_gr_mult) %>%
  group_by(draw, mf, position, rand_gr) %>%
  summarise(link = mean(value)) %>%
  spread(rand_gr, link) %>% 
  group_by(draw) %>%
  summarise(DescTools::CCC(`0`, `1`)$rho.c,
            r = cor(`0`, `1`, use = "pair")) %>% 
  summarise(rand_gr = mean(est))
```



```r
uk_mf_agreement <- bind_rows(
  ccc_by_group(filter(uk_arg, 
                      polviews %in% c("Left", "Right")) %>% 
                 droplevels(), 
               polviews),
  ccc_by_group(uk_arg, sex),
  ccc_by_group(uk_arg, age_gr),
  ccc_by_group(uk_arg, edu)
)

uk_mf_agreement <- uk_mf_agreement %>% 
  mutate(label = sprintf("%.2f [%.2f, %.2f]", est, lwr.ci, upr.ci), 
         group = factor(group,
                        levels = c("sex", 
                                   "age_gr",
                                   "polviews", 
                                   "edu"), 
                        labels = c("Sex", 
                                   "Age", 
                                   "Ideology", 
                                   "Education"))) %>% 
  arrange(group)

uk_mf_agreement %>%  
  select(group, label) %>% 
  kbl(col.names = c(" ", "CCC with 95% CI")) %>% 
  kable_classic(full_width = FALSE) 
```

<table class=" lightable-classic" style='font-family: "Arial Narrow", "Source Sans Pro", sans-serif; width: auto !important; margin-left: auto; margin-right: auto;'>
 <thead>
  <tr>
   <th style="text-align:left;">   </th>
   <th style="text-align:left;"> CCC with 95% CI </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Sex </td>
   <td style="text-align:left;"> 0.90 [0.89, 0.91] </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Age </td>
   <td style="text-align:left;"> 0.89 [0.88, 0.90] </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Ideology </td>
   <td style="text-align:left;"> 0.86 [0.85, 0.87] </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Education </td>
   <td style="text-align:left;"> 0.91 [0.90, 0.92] </td>
  </tr>
</tbody>
</table>



```r
uk_mf_diff <- bind_rows(
  link_by_group(filter(uk_arg, polviews %in% c("Left", "Right")) %>%
                  droplevels(), 
                polviews),
  link_by_group(uk_arg, sex),
  link_by_group(uk_arg, age_gr),
  link_by_group(uk_arg, edu)
) 

scatter_plot <- function(data) {

  data %>% 
    ggplot(aes(`1`, `2`)) +
    geom_point(alpha = .2) +
    geom_abline() +
    geom_smooth(method = "lm", se = FALSE) +
    scale_x_continuous(limits = c(0, 1), 
                       breaks = c(0, .25, .50, .75, 1)) + 
    scale_y_continuous(limits = c(0, 1), breaks = c(0, .25, .5, .75, 1)) +
    theme_half_open(9) 
}


pl1 <-  uk_mf_diff %>% 
  filter(group == "sex") %>% 
  scatter_plot() +
  labs(x = "Women", y = "Men") +
  theme(strip.background = element_blank(), strip.text = element_blank())

pl2 <-  uk_mf_diff %>% 
  filter(group == "age_gr") %>% 
  scatter_plot() +
  labs(x = "Younger", y = "Older")+
  theme(strip.background = element_blank(), strip.text = element_blank())

pl3 <- uk_mf_diff %>% 
  filter(group == "polviews") %>% 
  scatter_plot() +
  labs(x = "Left-wing", y = "Right-wing")

pl4 <- uk_mf_diff %>% 
  filter(group == "edu") %>% 
  scatter_plot() +
  labs(x = "Lower education", y = "Higher education")+
  theme(axis.title.y = element_text(size = 8))


cowplot::plot_grid(pl1, pl2, pl3, pl4,
                   labels = "AUTO", 
                   label_size = 9)
```

![](manuscript-results_files/figure-html/unnamed-chunk-2-1.jpeg)<!-- -->

```r
# ggsave("fig4_uk-scatter-by-groups.jpeg", width = 85, height = 70, units = "mm", dpi = 300)
```


# Study 3. Combined US, UK, IL, and BR analysis 

## Method


```r
common_items %>% 
  mutate(bsa_text = ifelse(reverse == 1, 
                           paste0(bsa_text, "*"),
                           bsa_text)) %>% 
  select(gss_text, bsa_text) %>% 
  kbl(col.names = c("General Social Survey items",
                    "British Social Attitudes items"),
      caption = "Table 4. Matching items identified in the GSS and BSA. An asterisk (*) indicates that the issue positions were reverse coded in the UK version to match the US version.") %>% 
    kable_classic() 
```

<table class=" lightable-classic" style='font-family: "Arial Narrow", "Source Sans Pro", sans-serif; margin-left: auto; margin-right: auto;'>
<caption>Table 4. Matching items identified in the GSS and BSA. An asterisk (*) indicates that the issue positions were reverse coded in the UK version to match the US version.</caption>
 <thead>
  <tr>
   <th style="text-align:left;"> General Social Survey items </th>
   <th style="text-align:left;"> British Social Attitudes items </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Do you think it should be possible for a pregnant woman to obtain a legal abortion if the woman wants it for any reason? </td>
   <td style="text-align:left;"> Do you think the law should allow an abortion in case the woman decides on her own she does not wish to have a child. </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Do you think it should be possible for a pregnant woman to obtain a legal abortion if she is not married and does not want to marry the man? </td>
   <td style="text-align:left;"> Do you think the law should allow an abortion in case the woman is not married and does not wish to marry the man. </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Do you think it should be possible for a pregnant woman to obtain a legal abortion if the family has a very low income and cannot afford any more children? </td>
   <td style="text-align:left;"> Do you think the law should allow an abortion in case  the couple cannot afford any more children. </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Do you think it should be possible for a pregnant woman to obtain a legal abortion if there is a strong chance of serious defect in the baby? </td>
   <td style="text-align:left;"> Do you think the law should allow an abortion in case there is a strong chance of a defect in the baby. </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Do you think it should be possible for a pregnant woman to obtain a legal abortion if the woman's own health is seriously endangered by the pregnancy? </td>
   <td style="text-align:left;"> Do you think the law should allow an abortion in case  the woman’s health is seriously endangered by the pregnancy. </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Do you think it should be possible for a pregnant woman to obtain a legal abortion if she became pregnant as a result of rape? </td>
   <td style="text-align:left;"> Do you think the law should allow an abortion in case  the woman became pregnant as a result of rape. </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Do you favor the death penalty for persons convicted of murder? </td>
   <td style="text-align:left;"> Are you in favour of the death penalty for murder? </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Do you think a person has the right to end his or her own life if this person has an incurable disease? </td>
   <td style="text-align:left;"> Suppose a person has a painful incurable disease. Do you think that doctors should be allowed by law to end the patient’s life, if the patient requests it? </td>
  </tr>
  <tr>
   <td style="text-align:left;"> What is your opinion about a married person having sexual relations with someone other than the marriage partner--is it wrong? </td>
   <td style="text-align:left;"> What about a married person having sexual relations with someone other than his or her partner, is it always wrong? </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Consider a man who admits that he is a homosexual. Should such a person be allowed to teach in a college or university? </td>
   <td style="text-align:left;"> Now I would like you to tell me whether, in your opinion, it is acceptable for a homosexual person to be a teacher in a college or university? </td>
  </tr>
  <tr>
   <td style="text-align:left;"> What about sexual relations between two adults of the same sex--do you think it is always wrong? </td>
   <td style="text-align:left;"> What about sexual relations between two adults of the same sex, is it wrong? </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Do you think the use of marijuana should be made legal? </td>
   <td style="text-align:left;"> Smoking cannabis (marijuana) should be legalised. </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Consider a person who advocates doing away with elections and letting the military run the country. If such a person wanted to make a speech in your community -- should he be allowed to speak? </td>
   <td style="text-align:left;"> There are some people whose views are considered extreme by the majority. Consider people who want to overthrow the government by revolution. Do you think such people should be allowed to hold public meetings to express their views? </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Consider a person who advocates doing away with elections and letting the military run the country. Suppose he wrote a book advocating doing away with elections and letting the military run the country. Somebody in your community suggests that the book be removed from the public library. Would you favor removing it? </td>
   <td style="text-align:left;"> There are some people whose views are considered extreme by the majority. Consider people who want to overthrow the government by revolution. Do you think such people should be allowed to publish books expressing their views?* </td>
  </tr>
  <tr>
   <td style="text-align:left;"> There's been a lot of discussion about the way morals and attitudes about sex are changing in this country. If a man and woman have sex relations before marriage -- do you think it is wrong? </td>
   <td style="text-align:left;"> Do you think it is wrong if a man and a woman have sexual relations before marriage? </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Women should take care of running their homes and leave running the country up to men. </td>
   <td style="text-align:left;"> We would like to ask about how you think family jobs should generally be shared between men and women. Do you think it is mainly the woman who should do the household cleaning? </td>
  </tr>
  <tr>
   <td style="text-align:left;"> In general - would you say that people should obey the law without exception rather than there are exceptional occasions on which people should follow their consciences even if it means breaking the law? </td>
   <td style="text-align:left;"> The law should always be obeyed, even if a particular law is wrong. </td>
  </tr>
  <tr>
   <td style="text-align:left;"> People who want children ought to get married. </td>
   <td style="text-align:left;"> People who want children ought to get married. </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Both the husband and the wife should contribute to the household income. </td>
   <td style="text-align:left;"> Both the man and woman should contribute to the household income. </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Do you personally think it is wrongfor a woman to have an abortion if there is a strong chance of serious defect in the baby. </td>
   <td style="text-align:left;"> Do you personally think it is wrong for a woman to have an abortion if there is a strong chance of serious defect in the baby? </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Do you personally think it is wrong for a woman to have an abortion if the family has a very low income and cannot afford any more children. </td>
   <td style="text-align:left;"> Do you personally think it is wrong for a woman to have an abortion if the family has a very low income and cannot afford any more children? </td>
  </tr>
  <tr>
   <td style="text-align:left;"> People should support their country even if the country is in the wrong. </td>
   <td style="text-align:left;"> People should support their country even if their country is in the wrong. </td>
  </tr>
  <tr>
   <td style="text-align:left;"> For certain problems - like environmental pollution - international bodies should have the right to enforce solutions. </td>
   <td style="text-align:left;"> For certain problems, like environment pollution, international bodies should have the right to enforce solutions. </td>
  </tr>
  <tr>
   <td style="text-align:left;"> America should follow its own interests - even if this leads to conflicts with other nations. </td>
   <td style="text-align:left;"> Britain should follow its own interests, even if this leads to conflicts with other nations. </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Foreigners should not be allowed to buy land in America. </td>
   <td style="text-align:left;"> Foreigners should not be allowed to buy land in Britain. </td>
  </tr>
  <tr>
   <td style="text-align:left;"> American television should give preference to American films and programs. </td>
   <td style="text-align:left;"> Britain’s television should give preference to British films and programmes. </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Ethnic minorities should be given government assistance to preserve their customs and traditions. </td>
   <td style="text-align:left;"> Ethnic minorities should be given government assistance to preserve their customs and traditions. </td>
  </tr>
</tbody>
</table>

### IL sample


```r
il_sample <- il_arg %>% 
  select(id, issue, age, sex, polviews) %>% 
  distinct() %>% 
  group_by(id, age, sex, polviews) %>% 
  summarise(n_issues = n()) %>% 
  ungroup()

il_sample %>% 
  summarise(n = n(),
            mean(n_issues),
            women = mean(sex == "Female", na.rm = TRUE),
            mean_age = mean(age, na.rm = TRUE),
            sd_age = sd(age, na.rm = TRUE),
            left = mean(polviews == "Left", na.rm = TRUE),
            moderate = mean(polviews == "Moderate", na.rm = TRUE),
            right = mean(polviews == "Right", na.rm = TRUE))
```

```
## # A tibble: 1 x 8
##       n `mean(n_issues)` women mean_age sd_age  left moderate right
##   <int>            <dbl> <dbl>    <dbl>  <dbl> <dbl>    <dbl> <dbl>
## 1   223             12.2 0.527     28.8   9.31 0.242    0.448 0.309
```

### BR sample


```r
br_sample <- br_arg %>% 
  select(id, issue, age, sex, polviews) %>% 
  distinct() %>% 
  group_by(id, age, sex, polviews) %>% 
  summarise(n_issues = n()) %>% 
  ungroup()

br_sample %>% 
  summarise(n = n(),
            mean(n_issues),
            women = mean(sex == "Female", na.rm = TRUE),
            mean_age = mean(age, na.rm = TRUE),
            sd_age = sd(age, na.rm = TRUE),
            left = mean(polviews == "Left", na.rm = TRUE),
            moderate = mean(polviews == "Moderate", na.rm = TRUE),
            right = mean(polviews == "Right", na.rm = TRUE))
```

```
## # A tibble: 1 x 8
##       n `mean(n_issues)` women mean_age sd_age  left moderate right
##   <int>            <dbl> <dbl>    <dbl>  <dbl> <dbl>    <dbl> <dbl>
## 1   294                9 0.452     44.2   13.9 0.480    0.279 0.241
```

## Results



```r
improve_plot <- function(pl, label_data) {
  ccc_label <- sprintf("%.2f [%.2f, %.2f]", 
                       label_data$est, 
                       label_data$lwr.ci, 
                       label_data$upr.ci)
  
  pl +
    geom_point(alpha = .4) +
    geom_abline() +
    geom_smooth(method = "lm", se = FALSE) +
    annotate(geom = "text", label = ccc_label,
             x = 0, y = .95, hjust = 0, size = 3.3) +
    scale_x_continuous(limits = c(0, 1), 
                       breaks = c(0, .25, .50, .75, 1)) + 
    scale_y_continuous(limits = c(0, 1), 
                       breaks = c(0, .25, .5, .75, 1)) +
    theme(axis.text = element_text(size = 9),
          plot.margin = margin(t = 1, r = 5, b = 1, l = 5), 
          strip.text = element_text(size = 12))
}


arg_link_by_group <- comb_arg %>%
  group_by(mf, position, sample) %>%
  summarise(link = mean(value), .groups = "drop") %>%
  spread(sample, link)


us_uk_pl <- arg_link_by_group %>% 
  ggplot(aes(US, UK)) %>% 
  improve_plot(label_data = summarise(arg_link_by_group, 
                                      CCC(US, UK)$rho.c)) + 
  labs(x = "US sample", y = "UK sample")

us_il_pl <- arg_link_by_group %>% 
  ggplot(aes(US, IL)) %>% 
  improve_plot(label_data = summarise(arg_link_by_group, 
                                      CCC(US, IL)$rho.c)) + 
  labs(x = "US sample", y = "IL sample") 

us_br_pl <- arg_link_by_group %>% 
  ggplot(aes(US, BR)) %>% 
  improve_plot(label_data = summarise(arg_link_by_group, 
                                      CCC(US, BR)$rho.c)) + 
  labs(x = "US sample", y = "BR sample") 

uk_il_pl <- arg_link_by_group %>% 
  ggplot(aes(UK, IL)) %>% 
  improve_plot(label_data = summarise(arg_link_by_group, 
                                      CCC(UK, IL)$rho.c)) + 
  labs(x = "UK sample", y = "IL sample") 


uk_br_pl <- arg_link_by_group %>% 
  ggplot(aes(UK, BR)) %>% 
  improve_plot(label_data = summarise(arg_link_by_group, 
                                      CCC(UK, BR)$rho.c)) + 
  labs(x = "UK sample", y = "BR sample") 

il_br_pl <- arg_link_by_group %>% 
  ggplot(aes(IL, BR)) %>% 
  improve_plot(label_data = summarise(arg_link_by_group, 
                                      CCC(IL, BR)$rho.c)) + 
  labs(x = "IL sample", y = "BB sample") 

cowplot::plot_grid(us_uk_pl, NULL, NULL,
                   us_il_pl, uk_il_pl, NULL,
                   us_br_pl, uk_br_pl, il_br_pl) 
```

![Figure 5. Scatter plots of 432 argument applicability scores for (8 types of arguments by 54 moral opinions). Each row compares data from two of our four countries US, UK, Israel (IL) and Brazil (BR). Every plot includes the CCC value with 95% confidence interval. Regression lines in blue and reference lines for perfect agreement in black.](manuscript-results_files/figure-html/com_scatter-1.jpeg)

```r
# ggsave("fig5_scatter-by-cntry.jpeg", width = 180, height = 120, 
#        units = "mm", dpi = 300)
```

## Agreement on argument applicability


```r
comb_arg_full <- bind_rows(
  comb_arg %>% 
    filter(sample %in% c("IL", "BR")),
  us_arg %>%  mutate(sample = "US"),
  uk_arg %>% mutate(sample = "UK") 
)

# Probability to agree for each issue
  p_agree <- comb_arg_full %>%
    filter(type == "pro") %>%
    select(sample, id, issue, opinion) %>%
    distinct() %>%
    group_by(sample, issue) %>%
    mutate(p = mean(opinion)) %>% 
    select(-opinion)

estimate_random_ccc <- function(data) {
  
  # Draw random opinion for each issue and espimated p
  rand_opin_simulated <- data %>% 
    mutate(pro = rbinom(n = n(), size = 1, prob = p),
           against = 1 - pro) %>%
    gather("type", "rand_opinion", pro, against)
  
  # Join argument data and calculate CCC
  left_join(rand_opin_simulated, 
            comb_arg_full %>% 
              select(sample, id, issue, mf, value),
            by = c("sample", "id", "issue")) %>% 
    group_by(sample, mf, issue, type, rand_opinion) %>%
    summarise(link = mean(value), .groups = "drop") %>%
    spread(rand_opinion, link) %>%
    drop_na() %>%
    group_by(sample) %>%
    summarise(est = CCC(`0`, `1`)$rho.c$est, .groups = "drop") 
}

set.seed(28943)

# Make 100 ccc simulations and average 
random_ccc <- map_df(1:100, ~estimate_random_ccc(p_agree)) %>% 
  group_by(sample) %>% 
  summarise(exp_ccc = mean(est))

ccc_for_agreement <- comb_arg_full %>% 
  group_by(sample, mf, issue, position, opinion) %>% 
  summarise(link = mean(value), .groups = "drop") %>% 
  spread(opinion, link) %>% 
  drop_na() %>% 
  group_by(sample) %>% 
  summarise(n_opinin = n_distinct(position), CCC(`0`, `1`)$rho.c) %>% 
  transmute(sample, 
            n_opinin, 
            ccc = sprintf("%.2f [%.2f, %.2f]", est, lwr.ci, upr.ci)) 

left_join(ccc_for_agreement, random_ccc) %>%
  mutate(sample = fct_rev(sample),
         exp_ccc = sprintf("%.2f", exp_ccc)) %>% 
  arrange(sample) %>% 
  kbl(col.names = c(
    "Country", 
    "Number of opinions",
    "CCC with 95% CI",
    "Expected CCC under perfect agreement"
    )) %>% 
  kable_classic(full_width = FALSE) 
```

<table class=" lightable-classic" style='font-family: "Arial Narrow", "Source Sans Pro", sans-serif; width: auto !important; margin-left: auto; margin-right: auto;'>
 <thead>
  <tr>
   <th style="text-align:left;"> Country </th>
   <th style="text-align:right;"> Number of opinions </th>
   <th style="text-align:left;"> CCC with 95% CI </th>
   <th style="text-align:left;"> Expected CCC under perfect agreement </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> US </td>
   <td style="text-align:right;"> 194 </td>
   <td style="text-align:left;"> 0.80 [0.79, 0.82] </td>
   <td style="text-align:left;"> 0.83 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> UK </td>
   <td style="text-align:right;"> 216 </td>
   <td style="text-align:left;"> 0.73 [0.71, 0.75] </td>
   <td style="text-align:left;"> 0.78 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> IL </td>
   <td style="text-align:right;"> 54 </td>
   <td style="text-align:left;"> 0.57 [0.50, 0.63] </td>
   <td style="text-align:left;"> 0.69 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> BR </td>
   <td style="text-align:right;"> 52 </td>
   <td style="text-align:left;"> 0.53 [0.46, 0.60] </td>
   <td style="text-align:left;"> 0.74 </td>
  </tr>
</tbody>
</table>


