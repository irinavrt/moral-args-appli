---
title: "Different populations agree on which moral arguments underlie which opinions"
output:
  html_document:
    keep_md: yes
---









# Study 1. US

## Method


```
## # A tibble: 1 x 9
##       n `mean(n_issues)` women mean_age sd_age liberal conservative verb_higher
##   <int>            <dbl> <dbl>    <dbl>  <dbl>   <dbl>        <dbl>       <dbl>
## 1   568             18.7 0.592     39.2   12.0   0.532        0.468       0.475
## # … with 1 more variable: edu_higher <dbl>
```

## Results

![Figure 1. Boxplots showing how applicability, estimated in the entire sample of Study 1, of eight different kinds of moral arguments varied across 196 moral opinions. The box represents the interquartile (IQ) range with the dark line indicating the median. The whiskers reach the min and max values in case these are at most 1.5 times the box height outside the IQ range. Circles and stars signify outliers (values between 1.5 and 3 times the IQ range) and extreme outliers (more than 3 times the IQ range), respectively.](manuscript-results_files/figure-html/us_boxpl-1.jpeg)

<table class=" lightable-classic" style='font-family: "Arial Narrow", "Source Sans Pro", sans-serif; width: auto !important; margin-left: auto; margin-right: auto;'>
<caption>Table 2. CCC values with 95% confidence interval.</caption>
 <thead>
  <tr>
   <th style="text-align:left;"> Compared subsamples </th>
   <th style="text-align:left;"> CCC with 95% CI </th>
   <th style="text-align:right;"> Expected CCC based on random split </th>
   <th style="text-align:left;"> p </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Sex </td>
   <td style="text-align:left;"> 0.94 [0.93, 0.94] </td>
   <td style="text-align:right;"> 0.940 </td>
   <td style="text-align:left;"> 0.385 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Age </td>
   <td style="text-align:left;"> 0.94 [0.93, 0.94] </td>
   <td style="text-align:right;"> 0.942 </td>
   <td style="text-align:left;"> 0.009 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Ideology </td>
   <td style="text-align:left;"> 0.93 [0.92, 0.93] </td>
   <td style="text-align:right;"> 0.941 </td>
   <td style="text-align:left;"> 0.000 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Education </td>
   <td style="text-align:left;"> 0.95 [0.94, 0.95] </td>
   <td style="text-align:right;"> 0.940 </td>
   <td style="text-align:left;"> 0.995 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Verbal ability </td>
   <td style="text-align:left;"> 0.92 [0.91, 0.92] </td>
   <td style="text-align:right;"> 0.942 </td>
   <td style="text-align:left;"> 0.000 </td>
  </tr>
</tbody>
</table>


![Figure 2. Scatter plots of 1,568 argument applicability scores (8 types of arguments by 196 moral opinions), measured in different groups in the United States: women vs. men (A),  younger vs. older (B), liberals vs. conservatives (C), higher vs. lower education (D), and higher vs. lower verbal ability (E). Regression lines in blue and reference lines for perfect agreement in black.](manuscript-results_files/figure-html/unnamed-chunk-1-1.jpeg)

Variance ratio between higher and lower verbal ability.


```
## # A tibble: 8 x 2
##   mf         ratio
##   <fct>      <dbl>
## 1 Ingroup    0.523
## 2 Fairness   0.628
## 3 Authority  0.637
## 4 Violence   0.656
## 5 Harm       0.658
## 6 Purity     0.692
## 7 Liberty    0.698
## 8 Government 0.730
```



# Study 2. UK

## Method


```
## # A tibble: 1 x 9
##       n `mean(n_issues)` women mean_age sd_age  left moderate right edu_higher
##   <int>            <dbl> <dbl>    <dbl>  <dbl> <dbl>    <dbl> <dbl>      <dbl>
## 1   903             12.3 0.580     38.3   12.8 0.253    0.409 0.338      0.521
```

## Results

![Figure 3. Boxplots (defined in Figure 2) showing how applicability, estimated in the entire sample of Study 2, of eight different kinds of moral arguments varied across 216 moral opinions.](manuscript-results_files/figure-html/uk_boxpl-1.jpeg)


<table class=" lightable-classic" style='font-family: "Arial Narrow", "Source Sans Pro", sans-serif; width: auto !important; margin-left: auto; margin-right: auto;'>
<caption>Table 3. The CCC value with 95% confidence interval.</caption>
 <thead>
  <tr>
   <th style="text-align:left;"> Compared subsamples </th>
   <th style="text-align:left;"> CCC with 95% CI </th>
   <th style="text-align:right;"> Expected CCC based on random split </th>
   <th style="text-align:left;"> p </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Sex </td>
   <td style="text-align:left;"> 0.90 [0.89, 0.91] </td>
   <td style="text-align:right;"> 0.917 </td>
   <td style="text-align:left;"> 0.000 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Age </td>
   <td style="text-align:left;"> 0.89 [0.88, 0.90] </td>
   <td style="text-align:right;"> 0.919 </td>
   <td style="text-align:left;"> 0.000 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Ideology </td>
   <td style="text-align:left;"> 0.86 [0.85, 0.87] </td>
   <td style="text-align:right;"> 0.874 </td>
   <td style="text-align:left;"> 0.011 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Education </td>
   <td style="text-align:left;"> 0.91 [0.90, 0.92] </td>
   <td style="text-align:right;"> 0.918 </td>
   <td style="text-align:left;"> 0.001 </td>
  </tr>
</tbody>
</table>


![Figure 4. Scatter plots of 1,728 argument applicability scores (8 types of arguments by 216 moral opinions), measured in different groups in the United Kingdom: women vs. men (A),  younger vs. older (B), right-wing vs. left-wing (C), and higher vs. lower education (D). Regression lines in blue and reference lines for perfect agreement in black.](manuscript-results_files/figure-html/unnamed-chunk-3-1.jpeg)


# Study 3. Combined US, UK, IL, and BR analysis 

## Method

### IL sample


```
## # A tibble: 1 x 8
##       n `mean(n_issues)` women mean_age sd_age  left moderate right
##   <int>            <dbl> <dbl>    <dbl>  <dbl> <dbl>    <dbl> <dbl>
## 1   223             12.2 0.525     28.8   9.31 0.242    0.448 0.309
```

### BR sample


```
## # A tibble: 1 x 8
##       n `mean(n_issues)` women mean_age sd_age  left moderate right
##   <int>            <dbl> <dbl>    <dbl>  <dbl> <dbl>    <dbl> <dbl>
## 1   294                9 0.452     44.2   13.9 0.480    0.279 0.241
```

## Results


<table class=" lightable-classic" style='font-family: "Arial Narrow", "Source Sans Pro", sans-serif; width: auto !important; margin-left: auto; margin-right: auto;'>
<caption>Table 4. The CCC value with 95% confidence interval.</caption>
 <thead>
  <tr>
   <th style="text-align:left;"> Compared subsamples </th>
   <th style="text-align:left;"> CCC with 95% CI </th>
   <th style="text-align:right;"> Expected CCC based on random split </th>
   <th style="text-align:left;"> p </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> US UK </td>
   <td style="text-align:left;"> 0.92 [0.90, 0.93] </td>
   <td style="text-align:right;"> 0.965 </td>
   <td style="text-align:left;"> 0.000 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> US IL </td>
   <td style="text-align:left;"> 0.90 [0.88, 0.92] </td>
   <td style="text-align:right;"> 0.962 </td>
   <td style="text-align:left;"> 0.000 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> US BR </td>
   <td style="text-align:left;"> 0.85 [0.82, 0.87] </td>
   <td style="text-align:right;"> 0.958 </td>
   <td style="text-align:left;"> 0.000 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> UK IL </td>
   <td style="text-align:left;"> 0.86 [0.83, 0.88] </td>
   <td style="text-align:right;"> 0.955 </td>
   <td style="text-align:left;"> 0.000 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> UK BR </td>
   <td style="text-align:left;"> 0.87 [0.84, 0.89] </td>
   <td style="text-align:right;"> 0.951 </td>
   <td style="text-align:left;"> 0.000 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> IL BR </td>
   <td style="text-align:left;"> 0.85 [0.82, 0.87] </td>
   <td style="text-align:right;"> 0.948 </td>
   <td style="text-align:left;"> 0.000 </td>
  </tr>
</tbody>
</table>



![Figure 5. Scatter plots of 432 argument applicability scores for (8 types of arguments by 54 moral opinions). Each row compares data from two of our four countries US, UK, Israel (IL) and Brazil (BR). Regression lines in blue and reference lines for perfect agreement in black.](manuscript-results_files/figure-html/com_scatter-1.jpeg)

## Agreement on argument applicability

<table class=" lightable-classic" style='font-family: "Arial Narrow", "Source Sans Pro", sans-serif; width: auto !important; margin-left: auto; margin-right: auto;'>
<caption>Table 5. CCC values, with 95% confidence intervals, measuring in four countries the extent to which people with different opinions on the underlying moral issues agreed on argument applicability.</caption>
 <thead>
  <tr>
   <th style="text-align:left;"> Country </th>
   <th style="text-align:right;"> Number of opinions </th>
   <th style="text-align:left;"> CCC with 95% CI </th>
   <th style="text-align:left;"> Expected CCC under perfect agreement </th>
   <th style="text-align:right;"> p </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> US </td>
   <td style="text-align:right;"> 194 </td>
   <td style="text-align:left;"> 0.82 [0.80, 0.83] </td>
   <td style="text-align:left;"> 0.855 </td>
   <td style="text-align:right;"> 0.008 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> UK </td>
   <td style="text-align:right;"> 216 </td>
   <td style="text-align:left;"> 0.73 [0.71, 0.75] </td>
   <td style="text-align:left;"> 0.781 </td>
   <td style="text-align:right;"> 0.048 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> IL </td>
   <td style="text-align:right;"> 54 </td>
   <td style="text-align:left;"> 0.57 [0.50, 0.63] </td>
   <td style="text-align:left;"> 0.695 </td>
   <td style="text-align:right;"> 0.011 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> BR </td>
   <td style="text-align:right;"> 52 </td>
   <td style="text-align:left;"> 0.53 [0.46, 0.60] </td>
   <td style="text-align:left;"> 0.722 </td>
   <td style="text-align:right;"> 0.015 </td>
  </tr>
</tbody>
</table>


