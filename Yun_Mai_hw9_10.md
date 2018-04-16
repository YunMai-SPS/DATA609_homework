YunMai\_hw9\_10
================
Yun Mai
April 10, 2018

``` r
suppressMessages(suppressWarnings(library(knitr)))
suppressMessages(suppressWarnings(library(ggplot2)))
suppressMessages(suppressWarnings(library(kableExtra)))
suppressMessages(suppressWarnings(library(formattable)))
suppressMessages(suppressWarnings(library(dplyr)))
```

Page 385: \#1 a, \#1 c Page 404: \#2 a Page 413: \#3 Page 420: \#1 Page 428: \#3 Page 440: \#2 Page 454: \#3

Page 469: \#3 Page 478: \#6 Page 481: \#1 Page 522: \#21 and \#22

Page 404: \#2 a

1.  For problems a-g build a linear programming model for each player's decisions and solve it both geometrically and algebraically. Assume the row player is maximizing his payoffs which are shown in the matrices below.

``` r
a <- data.frame('name'=rep('Rose',2),'strategy'=c('R1','R2'),'C1'=c(10,5),'C2'=c(10,0))
kable(a, "html", align = "c") %>%
  kable_styling(full_width = F) %>%
  column_spec(1, bold = T) %>%
  collapse_rows(columns = 1) %>% 
  add_header_above(c(" "," ", "Colin" = 2))
```

<table class="table" style="width: auto !important; margin-left: auto; margin-right: auto;">
<thead>
<tr>
<th style="border-bottom:hidden" colspan="1">
</th>
<th style="border-bottom:hidden" colspan="1">
</th>
<th style="text-align:center; border-bottom:hidden; padding-bottom:0; padding-left:3px;padding-right:3px;" colspan="2">
Colin

</th>
</tr>
<tr>
<th style="text-align:center;">
name
</th>
<th style="text-align:center;">
strategy
</th>
<th style="text-align:center;">
C1
</th>
<th style="text-align:center;">
C2
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:center;font-weight: bold;vertical-align: middle !important;" rowspan="2">
Rose
</td>
<td style="text-align:center;">
R1
</td>
<td style="text-align:center;">
10
</td>
<td style="text-align:center;">
10
</td>
</tr>
<tr>
<td style="text-align:center;">
R2
</td>
<td style="text-align:center;">
5
</td>
<td style="text-align:center;">
0
</td>
</tr>
</tbody>
</table>
Let

A: average payoffs x: Portion of the time to play C1 strategy 1- x: Portion of the time to play C2 strategy

If Colin plays a pure C1 strategy, the expected value is

EV(C1) = 10x+5(1-x)

Similarly, if Colin plays C2 strategy, the expected value is

EV(C2) = 10x

The objective expression: maximize A

The optimization problem for Rose:
