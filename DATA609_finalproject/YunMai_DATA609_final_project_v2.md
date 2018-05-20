DATA609\_finalproject\_v2
================
Yun Mai
May 16, 2018

``` r
suppressMessages(suppressWarnings(library(knitr)))
suppressMessages(suppressWarnings(library(ggplot2)))
suppressMessages(suppressWarnings(library(ggthemes)))
suppressMessages(suppressWarnings(library(dplyr)))
suppressMessages(suppressWarnings(library(tidyr)))
suppressMessages(suppressWarnings(library(stringr)))
suppressMessages(suppressWarnings(library(lpSolveAPI)))
```

1. Data
-------

Get the historical price of these ETF nad USDOLLAR from Yahoo Finace and Investing.com.

``` r
close_price<-read.csv("https://raw.githubusercontent.com/YunMai-SPS/DATA609_homework/master/DATA609_finalproject/close_price.csv")
close_price<-close_price[,-1]
close_price[,1] <- as.Date(close_price[,1])
```

### 1.1 Explore the data

``` r
stock_info <- read.csv("https://raw.githubusercontent.com/YunMai-SPS/DATA609_homework/master/DATA609_finalproject/list_of_asset.txt")
kable(stock_info[,c(1,3)])
```

| Symbol   | Description                                         |
|:---------|:----------------------------------------------------|
| AGG      | iShares Core U.S. Aggregate Bond ETF                |
| DBC      | PowerShares DB Commodity Index Tracking Fund        |
| DFE      | WisdomTree Europe SmallCap Dividend Fund            |
| DIA      | SPDR Dow JonesÂ® Industrial Average ETF             |
| DXJ      | WisdomTree Japan Hedged Equity Fund                 |
| EEM      | iShares MSCI Emerging Markets ETF                   |
| EFA      | iShares MSCI EAFE ETF                               |
| EWG      | iShares MSCI Germany ETF                            |
| EWH      | iShares MSCI Hong Kong ETF                          |
| EWI      | iShares MSCI Italy Capped ETF                       |
| EWT      | iShares MSCI Taiwan ETF                             |
| EWU      | iShares MSCI United Kingdom Index Fund              |
| EWW      | iShares MSCI Mexico Capped ETF                      |
| EWY      | iShares MSCI South Korea Capped ETF                 |
| EWZ      | iShares MSCI Brazil Capped ETF                      |
| EZU      | iShares MSCI EMU ETF                                |
| FEZ      | SPDR EURO STOXX 50 ETF                              |
| FXI      | iShares China Large-Cap ETF                         |
| GDX      | VanEck Vectors Gold Miners ETF                      |
| GLD      | SPDR Gold Shares ETF                                |
| IAU      | iShares Gold Trust ETF                              |
| IBB      | iShares NASDAQ Biotechnology Index ETF              |
| ITB      | iShares U.S. Home Construction ETF                  |
| IVV      | iShares Core S&P 500 ETF                            |
| IWD      | iShares Russell 1000 Value ETF                      |
| IWM      | iShares Russell 2000 ETF                            |
| IYR      | iShares U.S. Real Estate ETF                        |
| KBE      | SPDR S&P Bank ETF                                   |
| KRE      | SPDR S&P Regional Banking ETF                       |
| LQD      | iShares iBoxx $ Investment Grade Corporate Bond ETF |
| OIL      | iPath S&P GSCI Crude Oil Total Return Index ETN     |
| SDS      | UltraShort S&P500 ETF                               |
| SH       | Short S&P500 ETF                                    |
| SLV      | iShares Silver Trust ETF                            |
| SPY      | SPDR S&P 500 ETF                                    |
| USO      | United States Oil Fund                              |
| VGK      | Vanguard FTSE Europe ETF                            |
| VNQ      | Vanguard REIT ETF                                   |
| VTI      | Vanguard Total Stock Market ETF                     |
| VWO      | Vanguard FTSE Emerging Markets ETF                  |
| XHB      | SPDR Homebuilders ETF                               |
| XLB      | Materials Select Sector SPDR Fund                   |
| XLE      | Energy Select Sector SPDR Fund                      |
| XLF      | Financial Select Sector SPDR Fund                   |
| XLI      | Industrial Select Sector SPDR Fund                  |
| XLK      | Technology Select Sector SPDR Fund                  |
| XLP      | Consumer Staples Select Sector SPDR Fund            |
| XLU      | Utilities Select Sector SPDR Fund                   |
| XLV      | Health Care Select Sector SPDR Fund                 |
| XLY      | Consumer Discretionary Select Sector SPDR Fund      |
| XME      | SPDR S&P Metals & Mining ETF                        |
| XOP      | SPDR S&P Oil & Gas Exploration & Production ETF     |
| USDOLLAR | Federal funds effective rate                        |

1.2 Select the stock with good return
-------------------------------------

``` r
#daily return
daily_return <- as.data.frame(apply(close_price[,-1] , 2 , diff )/close_price[-1259,-1])
daily_return <- cbind(Date = close_price[-1,1],daily_return)

#monthly returns
temp <-  lapply(daily_return[,-1], function(x) x+1)
temp$year <- format(daily_return[,1], "%Y")
temp$month <- format(daily_return[,1], "%m")
temp<- as.data.frame(temp)
monthly_return <- aggregate(temp[-c(54,55)] ,by=list(temp$month,temp$year), FUN = prod, na.rm=TRUE)
monthly_return <-cbind(monthly_return[,c(1,2)],monthly_return[,-c(1,2)]-1)
colnames(monthly_return)[1] <-'Month'
colnames(monthly_return)[2] <-'Year'
monthly_return$Date <- as.Date(paste0(monthly_return$Year,'-',monthly_return$Month,"-01"))
monthly_return <- monthly_return[,c(56,1:55)]

#yearly returns
annual_return <- aggregate(temp[-c(54,55)] ,list(temp$year), FUN = prod, na.rm=TRUE)
annual_return <-cbind(annual_return[,1],annual_return[,-1]-1)
colnames(annual_return)[1] <-'Year'
#$Year <- as.Date(ISOdate(plot_dt_Y$Year, 1, 1))

# time period: year
n <- tail(as.numeric(annual_return$Year), n=1) - as.numeric(as.numeric(annual_return$Year))[1]

# 5 years average annual return
ave_annual_return <- apply(annual_return[,-1]+1,2,prod)
ave_annual_return <- ave_annual_return^(1/n)-1
```

We don't want to invest on all of these 52 ETF, so pick the ETF with average annual return greater than 10%. 12 ETF have been selected.

``` r
high_return_fiveyear <- as.data.frame(ave_annual_return[ave_annual_return>0.12])
colnames(high_return_fiveyear)<-'ave_yearly_return'
candidate <- names(ave_annual_return[ave_annual_return>0.12])
kable(high_return_fiveyear)
```

|     |  ave\_yearly\_return|
|-----|--------------------:|
| DFE |            0.1346335|
| DIA |            0.1284102|
| IVV |            0.1281546|
| KBE |            0.1413057|
| KRE |            0.1637118|
| SPY |            0.1278157|
| VTI |            0.1266215|
| XLF |            0.2060132|
| XLI |            0.1365640|
| XLK |            0.1927513|
| XLV |            0.1279062|
| XLY |            0.1445767|

``` r
candidate
```

    ##  [1] "DFE" "DIA" "IVV" "KBE" "KRE" "SPY" "VTI" "XLF" "XLI" "XLK" "XLV"
    ## [12] "XLY"

``` r
# Generated palette with rich rainbow and dark (12, s = 0.6, v = 0.75)
mixcolor <- c("#1B9E77", "#FF3300", "#5e1ea6", "#FEEA02", "#7570B3", "#A6761D", "#D95F02", "#E6AB02", "#E7298A",  "#000040", "#000093", "#0d4701", "#0076FF", "#0ff4ff",  "#49FB25",   "#eeff24","#ff24ef","#666666")

ggplot(close_price[,names(close_price) %in% c('Date',candidate)], aes(x = Date)) +
  geom_line(aes(y = close_price[,2],color= candidate[1]))+
  geom_line(aes(y = close_price[,3],color= candidate[2]))+
  geom_line(aes(y = close_price[,4],color= candidate[3]))+
  geom_line(aes(y = close_price[,5],color= candidate[4]))+
  geom_line(aes(y = close_price[,6],color= candidate[5]))+
  geom_line(aes(y = close_price[,7],color= candidate[6]))+
  geom_line(aes(y = close_price[,8],color= candidate[7]))+
  geom_line(aes(y = close_price[,9],color= candidate[8]))+
  geom_line(aes(y = close_price[,10],color= candidate[10]))+
  geom_line(aes(y = close_price[,11],color= candidate[11]))+
  geom_line(aes(y = close_price[,12],color= candidate[12]))+
  theme_few()+
  scale_color_manual(values = mixcolor)+
  ylab('Adjusted Close Price')+
  labs(title="Adjusted Close Price")
```

![](YunMai_DATA609_final_project_v2_files/figure-markdown_github/unnamed-chunk-6-1.png)

``` r
#The subset of the data as candidate
sub_return_1 <- monthly_return[,c('Date','Month','Year',candidate)]
plot_dt <- sub_return_1[,c(1,4:15)] %>% gather(symbol, return,2:13)   
ggplot(plot_dt, aes(x = Date, y = return,color= symbol)) +
  geom_line() +
  theme_few()+
  labs(title="Adjusted Monthly Returns")+
  facet_wrap( ~ symbol)+
  theme(axis.text.x = element_text(angle = 90, hjust = 1))
```

![](YunMai_DATA609_final_project_v2_files/figure-markdown_github/unnamed-chunk-6-2.png)

``` r
ggplot(plot_dt, aes(x = Date, y = return)) +
  geom_line() +
  theme_few()+
  labs(title="Adjusted Monthly Returns")
```

![](YunMai_DATA609_final_project_v2_files/figure-markdown_github/unnamed-chunk-6-3.png)

3.Mean Variance model
---------------------

Suppose data are observed for N securities, over T time periods. Let

*y*<sub>*j**t*</sub> = Return on one dollar invested in security j in time period t.

$\\bar{y\_j}$=Average Return on security j

*w*<sub>*j*</sub>= Portfolio allocation to security j.

*y*<sub>*p**t*</sub> = Return on portfolio in time period t

*E*<sub>*p*</sub> =Average Return on portfolio

*M*<sub>*p*</sub> = Minimum return on portfolio

The objective function:
$$min \\displaystyle\\sum\_{j=k}^{N}\\displaystyle\\sum\_{j=1}^{N}w\_jw\_ks\_{jk}$$

subject to:
$$\\displaystyle\\sum\_{j=1}^{N}w\_j\\bar{y\_j} \\geq G $$

with:
$$s\_{jk} = \\frac{1}{T-N}\\displaystyle\\sum\_{t=1}^{T}(y\_{jt} - \\bar{y\_j})(y\_{kt} -\\bar{y\_k})$$

``` r
library(rmarkdown)

#pandoc_version()
```

``` r
#The subset of the data as candidate
sub_return <- monthly_return[,c('Date','Month','Year',candidate)]

# convert to matrix and vector for later use
cov_matrix <- cov(sub_return[sub_return$Date > as.Date('2017-04-01'),][,-c(1:3)])

pstart <- rep(1/12,11)

# arithmetic mean of monthly return
mu <- apply(sub_return[sub_return$Date > as.Date('2017-04-01'),][,-c(1:3)],2,mean)

# standard deviation
std <- apply(sub_return[sub_return$Date > as.Date('2017-04-01'),][,-c(1:3)],2,sd)

#sharp ratio
sharpe <- mu /std

############################################
#Minimize the variance
############################################
SSRE  <- function(parx) {
    par<- c(parx,1-sum(parx))
    if(all(par > 0 & par < 1)) { # parameters meet requirements
       sqrt(t(par)%*% cov_matrix%*% par) # this is a  linear algebra version of your objective without  the division by xi
    } else 1e7  # penalty for parameters not satisfying constraints
}

SSRE(pstart)
```

    ##           [,1]
    ## [1,] 0.0250578

``` r
#par<- c(pstart,1-sum(pstart))
#vr <- sqrt(t(par)%*% cov_matrix%*% par)

opt_1 <- optim(pstart,SSRE)
opt_1
```

    ## $par
    ##  [1] 3.268960e-01 1.152313e-01 3.576396e-02 5.501270e-03 4.607506e-02
    ##  [6] 2.575681e-01 1.607674e-01 2.009167e-04 1.073398e-02 8.305006e-05
    ## [11] 2.724039e-02
    ## 
    ## $value
    ## [1] 0.02311251
    ## 
    ## $counts
    ## function gradient 
    ##      502       NA 
    ## 
    ## $convergence
    ## [1] 1
    ## 
    ## $message
    ## NULL

``` r
para <- c(opt_1$par, 1-sum(opt_1$par))  # final contributions

weight<- as.data.frame(t(round(para,4)))
colnames(weight)<- candidate
display <- cbind(t(weight),'Ave.Return'=mu,'Stdev'=std,'Sharp'=sharpe)
colnames(display)[1]<-'Weight'
data.frame(display)
```

    ##     Weight  Ave.Return      Stdev     Sharp
    ## DFE 0.3269 0.012497980 0.02718230 0.4597837
    ## DIA 0.1152 0.015418400 0.02825113 0.5457623
    ## IVV 0.0358 0.012256929 0.02424360 0.5055738
    ## KBE 0.0055 0.013832204 0.03971643 0.3482741
    ## KRE 0.0461 0.014741812 0.04199962 0.3509987
    ## SPY 0.2576 0.012281038 0.02419252 0.5076379
    ## VTI 0.1608 0.012198345 0.02283198 0.5342656
    ## XLF 0.0002 0.016003434 0.03459719 0.4625646
    ## XLI 0.0107 0.011018654 0.02883845 0.3820820
    ## XLK 0.0001 0.021170469 0.03515628 0.6021817
    ## XLV 0.0272 0.008889583 0.02868948 0.3098551
    ## XLY 0.0139 0.013301258 0.03341136 0.3981059

``` r
cat('\n','The monthly return of the portfolio at the minimum global variance is:',max_value <-sprintf("%1.3f%%", para%*%mu*100),'\n')
```

    ## 
    ##  The monthly return of the portfolio at the minimum global variance is: 1.273%

``` r
cat('\n','The minimum global variance is:',min_vr <-opt_1$value,'\n')
```

    ## 
    ##  The minimum global variance is: 0.02311251

``` r
#or
#opt_1 <-nlminb(pstart, SSRE) 

############################################
#Maximize the return
############################################
MaxReturn  <- function(parx) {
    par<- c(parx,1-sum(parx))
    if(all(par > 0 & par < 1)) { # parameters meet requirements
       -t(par)%*%mu  # this is a  linear algebra version of your objective without  the division by xi

    } else 1e7  # penalty for parameters not satisfying constraints
}

-MaxReturn (pstart)
```

    ##            [,1]
    ## [1,] 0.01363418

``` r
opt_2 <-nlminb(pstart, MaxReturn) 
opt_2
```

    ## $par
    ##  [1] 6.748382e-02 1.252656e-01 6.261799e-02 9.393781e-02 1.118949e-01
    ##  [6] 6.305802e-02 6.154753e-02 1.368475e-01 3.811412e-02 2.392327e-01
    ## [11] 2.846900e-14
    ## 
    ## $objective
    ## [1] -0.01569107
    ## 
    ## $convergence
    ## [1] 1
    ## 
    ## $iterations
    ## [1] 80
    ## 
    ## $evaluations
    ## function gradient 
    ##      199     1247 
    ## 
    ## $message
    ## [1] "false convergence (8)"

``` r
para_2 <- c(opt_2$par, 1-sum(opt_2$par))  # final contributions

weight<- as.data.frame(t(round(para_2,4)))
colnames(weight)<- candidate
data.frame('Weight'=t(weight))
```

    ##     Weight
    ## DFE 0.0675
    ## DIA 0.1253
    ## IVV 0.0626
    ## KBE 0.0939
    ## KRE 0.1119
    ## SPY 0.0631
    ## VTI 0.0615
    ## XLF 0.1368
    ## XLI 0.0381
    ## XLK 0.2392
    ## XLV 0.0000
    ## XLY 0.0000

``` r
cat('\n','The maximized monthly return of the portfolio is:',max_value <-sprintf("%1.3f%%", -opt_2$objective*100),'\n')
```

    ## 
    ##  The maximized monthly return of the portfolio is: 1.569%

``` r
cat('\n','The global variance is:',vr_mxreturn <- sqrt(t(para_2)%*%cov_matrix%*%para_2),'\n')
```

    ## 
    ##  The global variance is: 0.02545288

4.Minimax Model
---------------

Suppose data are observed for N securities, over T time periods. Let

*y*<sub>*j**t*</sub> = Return on one dollar invested in security j in time period t.

$\\bar{y\_j}$=Average Return on security j

*w*<sub>*j*</sub>= Portfolio allocation to security j.

*y*<sub>*p**t*</sub> = Return on portfolio in time period t

*E*<sub>*p*</sub> =Average Return on portfolio

*M*<sub>*p*</sub> = Minimum return on portfolio

The objective function:

*m**a**x* *M*<sub>*p*</sub>

subject to:

$$\\displaystyle\\sum\_{j=1}^{N}w\_jy\_{jt} \\geq M\_p $$
 t = 1,...,T

$$\\displaystyle\\sum\_{j=1}^{N}w\_j\\bar{y\_j} \\geq G $$

``` r
#the worst monthly return was calculated by subtract average return by the standard deviation
worst_return<-mu-std

#The subset of the data as candidate
sub_return <- monthly_return[,c('Date','Month','Year',candidate)]

# convert to matrix and vector for later use
cov_matrix <- cov(sub_return[sub_return$Date > as.Date('2017-04-01'),][,-c(1:3)])

pstart <- rep(1/12,11)

# arithmetic mean of monthly return
mu <- apply(sub_return[sub_return$Date > as.Date('2017-04-01'),][,-c(1:3)],2,mean)

# standard deviation
std <- apply(sub_return[sub_return$Date > as.Date('2017-04-01'),][,-c(1:3)],2,sd)

#sharp ratio
sharpe <- mu /std

worst_return <- mu-std

############################################
#Minimize the variance
############################################
portfolio_return  <- function(parx) {
    par<- c(parx,1-sum(parx))
    if(all(par > 0 & par < 1)) { # parameters meet requirements
       -par%*%worst_return # this is a  linear algebra version of your objective without  the division by xi
    } else 1e7  # penalty for parameters not satisfying constraints
}

portfolio_return(pstart)
```

    ##            [,1]
    ## [1,] 0.01712502

``` r
opt_3 <- optim(pstart, portfolio_return) 
opt_3
```

    ## $par
    ##  [1] 0.0994537294 0.1452682208 0.1462794499 0.0023891333 0.0008070838
    ##  [6] 0.0022393046 0.2653749609 0.0803326942 0.1117421283 0.0297404672
    ## [11] 0.0906883547
    ## 
    ## $value
    ## [1] 0.01422341
    ## 
    ## $counts
    ## function gradient 
    ##      502       NA 
    ## 
    ## $convergence
    ## [1] 1
    ## 
    ## $message
    ## NULL

``` r
para_3 <- c(opt_3$par, 1-sum(opt_3$par))  # final contributions

weight<- as.data.frame(t(round(para_3,4)))
colnames(weight)<- candidate
display <- cbind(t(weight),'Worst.Return'=worst_return,'Ave.Return'=mu,'Stdev'=std,'Sharp'=sharpe)
colnames(display)[1]<-'Weight'
data.frame(display)
```

    ##     Weight Worst.Return  Ave.Return      Stdev     Sharp
    ## DFE 0.0995  -0.01468432 0.012497980 0.02718230 0.4597837
    ## DIA 0.1453  -0.01283273 0.015418400 0.02825113 0.5457623
    ## IVV 0.1463  -0.01198667 0.012256929 0.02424360 0.5055738
    ## KBE 0.0024  -0.02588422 0.013832204 0.03971643 0.3482741
    ## KRE 0.0008  -0.02725780 0.014741812 0.04199962 0.3509987
    ## SPY 0.0022  -0.01191148 0.012281038 0.02419252 0.5076379
    ## VTI 0.2654  -0.01063364 0.012198345 0.02283198 0.5342656
    ## XLF 0.0803  -0.01859376 0.016003434 0.03459719 0.4625646
    ## XLI 0.1117  -0.01781980 0.011018654 0.02883845 0.3820820
    ## XLK 0.0297  -0.01398581 0.021170469 0.03515628 0.6021817
    ## XLV 0.0907  -0.01979990 0.008889583 0.02868948 0.3098551
    ## XLY 0.0257  -0.02011010 0.013301258 0.03341136 0.3981059

``` r
cat('\n','The average minimum losses is:',min_losses <-sprintf("%1.3f%%", opt_3$value*100,'\n'))
```

    ## 
    ##  The average minimum losses is: 1.422%

``` r
cat('\n','The variance is:',SSRE(para_3[-12]))
```

    ## 
    ##  The variance is: 0.02428136

``` r
alocation <- rbind('worst_return'=worst_return,'weight'=weight)
colnames(alocation) <- candidate
kable(alocation)
```

|               |         DFE|         DIA|         IVV|         KBE|         KRE|         SPY|         VTI|         XLF|         XLI|         XLK|         XLV|         XLY|
|---------------|-----------:|-----------:|-----------:|-----------:|-----------:|-----------:|-----------:|-----------:|-----------:|-----------:|-----------:|-----------:|
| worst\_return |  -0.0146843|  -0.0128327|  -0.0119867|  -0.0258842|  -0.0272578|  -0.0119115|  -0.0106336|  -0.0185938|  -0.0178198|  -0.0139858|  -0.0197999|  -0.0201101|
| weight        |   0.0995000|   0.1453000|   0.1463000|   0.0024000|   0.0008000|   0.0022000|   0.2654000|   0.0803000|   0.1117000|   0.0297000|   0.0907000|   0.0257000|

``` r
#dt_plot<- data.frame(t(rbind(seq(1,12,1),alocation)))
#ggplot(dt_plot,aes(x=X1))+
  #geom_line(y=dt_plot$worst_return,colour ='red')+
  #geom_line(y=dt_plot$weight,colour ='green')+
  #theme_few() 
```

``` r
MV = para * mu 
MiniMax = para_3 * mu 
para_4 <- rep(1/12,12)
Even = para_4* mu 

compare <- cbind(candidate,MV,MiniMax,Even)

m <- as.matrix(sub_return[sub_return$Date > as.Date('2017-04-01'),][,-c(1:3)])
matrix_1 <- matrix(rep(para,13),nrow=12) * t(m)
tb_1 <- as.data.frame(cbind('Date'=sub_return[sub_return$Date > as.Date('2017-04-01'),][,'Date'],t(matrix_1)))
tb_1$Date <- as.Date(tb_1$Date, origin = "1966-01-01") 

matrix_2 <- matrix(rep(para_3,13),nrow=12) * t(m)
tb_2 <- as.data.frame(cbind('Date'=sub_return[sub_return$Date > as.Date('2017-04-01'),][,'Date'],t(matrix_2)))
tb_2$Date <- as.Date(tb_2$Date, origin = "1966-01-01") 

matrix_3 <- matrix(rep(para_4,13),nrow=12) * t(m)
tb_3 <- as.data.frame(cbind('Date'=sub_return[sub_return$Date > as.Date('2017-04-01'),][,'Date'],t(matrix_3)))
tb_3$Date <- as.Date(tb_3$Date, origin = "1966-01-01") 

compare<-as.data.frame(cbind(tb_1[,c('Date','VTI','XLK')],tb_2[,c('VTI','XLK')],tb_3[,c('VTI','XLK')]))
colnames(compare) <-c('Date','MV.VTI','MV.XLK','Min.VTI','Min.XLK','Even.VTI','Even.XLK')

plot_compare <- compare[,c(1,2,4,6)] %>% gather(method,ave_return,2:4)
par(mfrow=c(1,2))
ggplot(plot_compare,aes(x = Date,y = ave_return,colour=method))+
  geom_line()+
  ylab('Return on Poetfolio')+
  labs(title="Compare the optimized return of VTI")+
  theme_few()+
  scale_color_manual(values = mixcolor)
```

![](YunMai_DATA609_final_project_v2_files/figure-markdown_github/unnamed-chunk-10-1.png)

``` r
ggplot(compare,aes(x=Date))+  
  geom_line(aes(y=MV.XLK,color = 'MV.XLK'))+
  geom_line(aes(y=Min.XLK,color = 'Min.XLK'))+
  geom_line(aes(y=Even.XLK,color = 'Even.XLK'))+
  theme_few()+
  scale_color_manual(values = mixcolor)+
  ylab('Return on Poetfolio')+
  labs(title="Compare the optimized return on XLK")
```

![](YunMai_DATA609_final_project_v2_files/figure-markdown_github/unnamed-chunk-10-2.png)

5. Game theory model: Maximin Model
-----------------------------------

``` r
worst_return <- mu-std
average_return <- mu
best_return <- mu+std
game <- data.frame(cbind('worst' = worst_return,'average' = average_return,'best'= best_return))
game
```

    ##           worst     average       best
    ## DFE -0.01468432 0.012497980 0.03968028
    ## DIA -0.01283273 0.015418400 0.04366953
    ## IVV -0.01198667 0.012256929 0.03650053
    ## KBE -0.02588422 0.013832204 0.05354863
    ## KRE -0.02725780 0.014741812 0.05674143
    ## SPY -0.01191148 0.012281038 0.03647356
    ## VTI -0.01063364 0.012198345 0.03503033
    ## XLF -0.01859376 0.016003434 0.05060062
    ## XLI -0.01781980 0.011018654 0.03985710
    ## XLK -0.01398581 0.021170469 0.05632675
    ## XLV -0.01979990 0.008889583 0.03757907
    ## XLY -0.02011010 0.013301258 0.04671262

``` r
w <- c(paste0('y',seq(1,12)))
  
paste(paste(round(game$worst,4),w),collapse = "")
```

    ## [1] "-0.0147 y1-0.0128 y2-0.012 y3-0.0259 y4-0.0273 y5-0.0119 y6-0.0106 y7-0.0186 y8-0.0178 y9-0.014 y10-0.0198 y11-0.0201 y12"

``` r
paste(paste(round(game$average,4),w,'+'),collapse = "")
```

    ## [1] "0.0125 y1 +0.0154 y2 +0.0123 y3 +0.0138 y4 +0.0147 y5 +0.0123 y6 +0.0122 y7 +0.016 y8 +0.011 y9 +0.0212 y10 +0.0089 y11 +0.0133 y12 +"

``` r
paste(paste(round(game$best,4),w,'+'),collapse = "")
```

    ## [1] "0.0397 y1 +0.0437 y2 +0.0365 y3 +0.0535 y4 +0.0567 y5 +0.0365 y6 +0.035 y7 +0.0506 y8 +0.0399 y9 +0.0563 y10 +0.0376 y11 +0.0467 y12 +"

``` r
paste(w,collapse = ",")
```

    ## [1] "y1,y2,y3,y4,y5,y6,y7,y8,y9,y10,y11,y12"

Suppose our investor has $1 to allocate among the 12 assets with the unknown amounts *y*<sub>1</sub>, *y*<sub>2</sub>, *y*<sub>3</sub>, *y*<sub>4</sub>, *y*<sub>5</sub>, *y*<sub>6</sub>, *y*<sub>7</sub>, *y*<sub>8</sub>, *y*<sub>9</sub>, *y*<sub>10</sub>, *y*<sub>11</sub>, *y*<sub>12</sub> respectively. That is,

*y*<sub>1</sub> + *y*<sub>2</sub> + *y*<sub>3</sub> + *y*<sub>4</sub> + *y*<sub>5</sub> + *y*<sub>6</sub> + *y*<sub>7</sub> + *y*<sub>8</sub> + *y*<sub>9</sub> + *y*<sub>10</sub> + *y*<sub>11</sub> + *y*<sub>12</sub> = 1

Then we can view y as weight.

If the smallest return is R , the returns should be:

−0.0295*y*<sub>1</sub> − 0.0387*y*<sub>2</sub> − 0.0334*y*<sub>3</sub> − 0.0255*y*<sub>4</sub> − 0.0143*y*<sub>5</sub> − 0.0332*y*<sub>6</sub> − 0.0304*y*<sub>7</sub> − 0.0399*y*<sub>8</sub> − 0.043*y*<sub>9</sub> − 0.0281*y*<sub>10</sub> − 0.0406*y*<sub>11</sub> − 0.0363*y*<sub>12</sub> ≥ *R* {if worst return}

0.0044*y*<sub>1</sub> + 0.0027*y*<sub>2</sub> + 0.0057*y*<sub>3</sub> + 0.0111*y*<sub>4</sub> + 0.0172*y*<sub>5</sub> + 0.0059*y*<sub>6</sub> + 0.0061*y*<sub>7</sub> + 0.004*y*<sub>8</sub> − 0.001*y*<sub>9</sub> + 0.0194*y*<sub>10</sub> + 0.0023*y*<sub>11</sub> + 0.0138*y*<sub>12</sub> ≥ *R* {if average return}

0.0384*y*<sub>1</sub> + 0.0441*y*<sub>2</sub> + 0.0447*y*<sub>3</sub> + 0.0476*y*<sub>4</sub> + 0.0486*y*<sub>5</sub> + 0.0449*y*<sub>6</sub> + 0.0427*y*<sub>7</sub> + 0.0478*y*<sub>8</sub> + 0.0409*y*<sub>9</sub> + 0.0669*y*<sub>10</sub> + 0.0451*y*<sub>11</sub> + 0.064*y*<sub>12</sub> ≥ *R* {if best return}

*y*<sub>1</sub>, *y*<sub>2</sub>, *y*<sub>3</sub>, *y*<sub>4</sub>, *y*<sub>5</sub>, *y*<sub>6</sub>, *y*<sub>7</sub>, *y*<sub>8</sub>, *y*<sub>9</sub>, *y*<sub>10</sub>, *y*<sub>11</sub>, *y*<sub>12</sub> ≥ 0

``` r
suppressMessages(suppressWarnings(library(linprog)))  
# form c vector 
cvec <- matrix(c(1,rep(0,11)))

# form b vector 
bvec <- matrix(c(t(game)[,12],rep(1,11),1))

# form matrix A
A <- cbind(rep(1,3),rbind((game[12,1]-game[-12,1]),(game[12,2]-game[-12,2]),(game[12,3]-game[-12,3])))
y_constrain <- c(0,rep(1,11))
y_constrain_2 <- cbind(rep(0,11),diag(1,11,11))
A <- rbind(A,y_constrain,y_constrain_2)

res <- solveLP(cvec,bvec,A,maximum=TRUE)
res$solution
```

    ##          1          2          3          4          5          6 
    ## 0.02117047 0.00000000 0.00000000 0.00000000 0.00000000 0.00000000 
    ##          7          8          9         10         11         12 
    ## 0.00000000 0.00000000 0.00000000 0.00000000 1.00000000 0.00000000

``` r
data.frame(cbind(candidate,'Weight'=c(res$solution[-1],0)))
```

    ##    candidate Weight
    ## 2        DFE      0
    ## 3        DIA      0
    ## 4        IVV      0
    ## 5        KBE      0
    ## 6        KRE      0
    ## 7        SPY      0
    ## 8        VTI      0
    ## 9        XLF      0
    ## 10       XLI      0
    ## 11       XLK      1
    ## 12       XLV      0
    ##          XLY      0

Solving this problem by LP solution algorithm, the optimal solution is *y*<sub>1</sub> = 0, *y*<sub>2</sub> = 0, *y*<sub>3</sub> = 0, *y*<sub>4</sub> = 0, *y*<sub>5</sub> = 0, *y*<sub>6</sub> = 0, *y*<sub>7</sub> = 0, *y*<sub>8</sub> = 0, *y*<sub>9</sub> = 0, *y*<sub>10</sub> = 0, *y*<sub>11</sub> = 100, 000, *y*<sub>12</sub> = 0, and R = 0.1941. That is, the investor must put all the money in the XLV account with the accumulated return rate at 2.117% .

6. Linear Programming
---------------------

### beta value

YTD instead of the average yearly return based on the past 5 years adjusted close pricewill be used in this method.

``` r
library(XML)

stock_summary <-read.csv('https://raw.githubusercontent.com/YunMai-SPS/DATA609_homework/master/DATA609_finalproject/stock_summary.csv')
stock_summary <- stock_summary[,-1]
colnames(stock_summary) <- c("symbol","Previous.Close","Open", "Bid", "Ask", "Day's.Range", "52 Week Range", "Volume", "Avg. Volume", "Net Assets", "NAV", "PE Ratio (TTM)", "Yield", "YTD Return",  "Beta (3y)","Expense Ratio (net)", "Inception Date")

stock_summary[,"YTD Return"] <- str_replace_all(stock_summary[,"YTD Return"],"%","")
stock_summary[,"YTD Return"] <- as.numeric(stock_summary[,"YTD Return"])

#compare 5-years and 5-month average return
plot_dt <- as.data.frame(cbind(stock_summary[,"YTD Return"]/100,ave_annual_return[-53]))
plot_dt <- cbind(stock_summary[,"symbol"],plot_dt)
plot_dt$id <- seq(1,52)
colnames(plot_dt) <- c("symbol","YTD_Return","5_year_return","id")

ggplot(plot_dt, aes(x = id)) +
  geom_line( aes(y = plot_dt$'YTD_Return',color = 'YTD_Return')) +
  geom_line( aes(y = plot_dt$'5_year_return', color = '5_year_return' )) +
  theme_few()+
  labs(title="Returns")+
  ylab('Return')
```

![](YunMai_DATA609_final_project_v2_files/figure-markdown_github/unnamed-chunk-13-1.png)

``` r
  theme(axis.text.x = element_text(angle = 90, hjust = 1))
```

    ## List of 1
    ##  $ axis.text.x:List of 11
    ##   ..$ family       : NULL
    ##   ..$ face         : NULL
    ##   ..$ colour       : NULL
    ##   ..$ size         : NULL
    ##   ..$ hjust        : num 1
    ##   ..$ vjust        : NULL
    ##   ..$ angle        : num 90
    ##   ..$ lineheight   : NULL
    ##   ..$ margin       : NULL
    ##   ..$ debug        : NULL
    ##   ..$ inherit.blank: logi FALSE
    ##   ..- attr(*, "class")= chr [1:2] "element_text" "element"
    ##  - attr(*, "class")= chr [1:2] "theme" "gg"
    ##  - attr(*, "complete")= logi FALSE
    ##  - attr(*, "validate")= logi TRUE

Choose the ETFs with positive annual return.

``` r
postive_return <- stock_summary[stock_summary[,'YTD Return'] >0,][,1]

stock_summary[which(stock_summary$symbol %in% postive_return),c(1,14)]
```

    ##    symbol YTD Return
    ## 2     DBC       5.72
    ## 3     DFE       0.47
    ## 7     EFA       0.61
    ## 9     EWH       0.63
    ## 10    EWI      10.28
    ## 11    EWT       0.25
    ## 12    EWU       1.20
    ## 13    EWW       4.69
    ## 14    EWY       0.89
    ## 15    EWZ       4.57
    ## 16    EZU       2.19
    ## 17    FEZ       2.38
    ## 18    FXI       2.32
    ## 20    GLD       0.76
    ## 21    IAU       0.88
    ## 26    IWM       0.80
    ## 28    KBE       1.63
    ## 29    KRE       5.07
    ## 31    OIL      11.77
    ## 33     SH       0.19
    ## 36    USO      14.90
    ## 37    VGK       0.90
    ## 43    XLE       2.87
    ## 46    XLK       2.68
    ## 50    XLY       5.39
    ## 52    XOP       6.12

Assuming there is only two outcomes for each investment, success and failure.

Typically, market risks for individual stocks range from 0.5 to 2.5 or 3.0.

Assuming 2% is a point due to the minimal, yet apparent, system risk.

Assuming the general risk for investing stock range from 10% to 50%.

If there is a linear relation between beta value and risk, we can estimate risk based on the beta value.

A negative beta indicates an inverse relation to the market.Some stock like gold and gold stocks should have negative betas because they tended to do better when the stock market declines. The smaller the negative beta value, the more volatility. As such, negative beta should be converted to positive when calculate the risk as risk can not be negative.

``` r
stock_summary[,'YTD Return']<- str_replace_all(stock_summary[,'YTD Return'],"%","")
stock_summary[,c(14,15)] <- lapply(stock_summary[,c(14,15)],as.numeric)
stock_summary[,'YTD Return']<- stock_summary[,'YTD Return']/100

x <- c(0,0.5,3)
y <- c(0.02,0.1,0.8)

plot(x,y,main="linear relationship", xlab="beta-value", ylab="Risk")
abline(lm(y ~ x))
```

![](YunMai_DATA609_final_project_v2_files/figure-markdown_github/unnamed-chunk-15-1.png)

``` r
fit.lm <-lm(y ~ x)
intercept <- coef(fit.lm)[1]
slope <- coef(fit.lm)[2]

stock_summary$risk <- intercept + slope*abs(stock_summary[,'Beta (3y)'])

return_df <- stock_summary[stock_summary$`YTD Return`>0,c(1,14,15,18)]
  
return_df$exp.return <- (1-return_df$risk)*return_df[,'YTD Return']/100-return_df$risk

return_df[return_df$exp.return>0,]
```

    ## [1] symbol     YTD Return Beta (3y)  risk       exp.return
    ## <0 rows> (or 0-length row.names)

``` r
E_return <- (1-return_df$risk)*return_df[,'YTD Return']-return_df$risk
```

But the expected return of the stocks are all less than 0, suggesting investment will fail in every stock list here.

If the link function between beta value and risk is a quadratic function, the expected return of two of the stocks will be positive.

``` r
fit.quadratic <- lm(y~x+I(x^2))
c <-fit.quadratic$coefficients[1]
a <- fit.quadratic$coefficients[3]
b <- fit.quadratic$coefficients[2]

# or  
# solve(cbind(1, x, x^2), y)

se <- seq(0, 30, 0.1)
predictedcounts <- predict(fit.quadratic,list(x=se, x2=se^2))
plot(x, y, pch=16, main="quadratic relationship",,xlab="beta-value", ylab="Risk", cex.lab = 1.3, col = "blue")
lines(se, predictedcounts, col = "darkgreen", lwd = 1.5)
```

![](YunMai_DATA609_final_project_v2_files/figure-markdown_github/unnamed-chunk-16-1.png)

``` r
stock_summary$risk.qdr <- a*(slope*stock_summary[,'Beta (3y)'])^2+b*slope*abs(stock_summary[,'Beta (3y)'])+c

return_df <- stock_summary[,c(1,14,15,19)]
  
return_df$exp.return <- (1-stock_summary$risk.qdr)*stock_summary[,'YTD Return']-stock_summary$risk.qdr

return_df[return_df$exp.return>0,]
```

    ##    symbol YTD Return Beta (3y)   risk.qdr exp.return
    ## 10    EWI     0.1028      1.16 0.06709306 0.02880978
    ## 36    USO     0.1490      1.90 0.10112802 0.03280391

We will select these two stocks, EWI and USO, with expected return as 2.88% and 3.28% respectively.

To diversify the portfolio, we can invest bonds. U.S. savings bonds. U.S. savings bonds are endorsed by the federal government, they are considerted risk free and considered one of the safest types of investments. Suppose we purchase Series I Bonds, the current interest rate is 2.52%. The expected return for Series EE Bonds is: 100%\*2.52% = 2.52%

``` r
investment_summray <- return_df[return_df$exp.return>0,]
investment_summray[,1] <- as.character(investment_summray[,1])
colnames(investment_summray)<- c("Investment", "Rate of Return","Beta", "Risk","Expected Return")
investment_summray <- rbind(investment_summray, c('Series I Bonds',0.0252,NA,0,0.0252))
investment_summray[,c(2,3,4,5)] <- lapply(investment_summray[,c(2,3,4,5)],as.numeric)
investment_summray
```

    ##        Investment Rate of Return Beta       Risk Expected Return
    ## 10            EWI         0.1028 1.16 0.06709306      0.02880978
    ## 36            USO         0.1490 1.90 0.10112802      0.03280391
    ## 3  Series I Bonds         0.0252   NA 0.00000000      0.02520000

Suppose the manager have $50,000 and wish to invest. If we invest x, y and z amount of money in EWI, USO and Series I Bonds respectively, the expected net gain will be 0.0288x+0.0328y+0.0252z.

Assuming there is only two outcomes for each investment, success and failure, there are eight different scenarios that may occur with our investments. The expected net gain and likelihood for each situation to occur is summarized in the table below (A,B and C represents EWI, USO and Series I Bonds respectively).

``` r
s1 <- (1-investment_summray[1,4])
s2 <- (1-investment_summray[2,4])
s3 <- (1-investment_summray[3,4])

outcome_summary <- as.data.frame(rbind(c("1", "S", "S", "S",round(s1*s2*s3,4)),c("2", "F", "S", "S",round((1-s1)*s2*s3,4)),c("3", "S", "F", "S",round(s1*(1-s2)*s3,4)),c("4", "S", "S", "F",round(s1*s2*(1-s3),4)),c("5", "F", "F", "S",round((1-s1)*(1-s2)*s3,4)),c("6", "F", "S", "F",round((1-s1)*s2*(1-s3),4)),c("7", "S", "F", "F",round(s1*(1-s2)*(1-s3),4)),c("8", "F", "F", "F",(1-s1)*(1-s2)*(1-s3))))

outcome_summary <- cbind(outcome_summary,c('0.1028x+0.1490y+0.0252z','-x+0.1490y+0.0252z','0.1028x-y+0.0252z','0.1028x+0.1490y-z','-x-y+0.0252z','-x+0.1490y-z','0.1028x-y-z','-x-y-z'))
colnames(outcome_summary) <- c("Case", "A", "B", "C", "Probability","Net Gain")
outcome_summary[,5]<-as.numeric(as.character(outcome_summary[,5]))
outcome_summary
```

    ##   Case A B C Probability                Net Gain
    ## 1    1 S S S      0.8386 0.1028x+0.1490y+0.0252z
    ## 2    2 F S S      0.0603      -x+0.1490y+0.0252z
    ## 3    3 S F S      0.0943       0.1028x-y+0.0252z
    ## 4    4 S S F      0.0000       0.1028x+0.1490y-z
    ## 5    5 F F S      0.0068            -x-y+0.0252z
    ## 6    6 F S F      0.0000            -x+0.1490y-z
    ## 7    7 S F F      0.0000             0.1028x-y-z
    ## 8    8 F F F      0.0000                  -x-y-z

``` r
outcome_summary[1,5]+outcome_summary[2,5]+outcome_summary[5,5]
```

If we hope the gain being greater than or equal to 90%, there are three different combinations: 1+2+3,1+2+5,1+3.

Optimize the portfolio by linear programing.

The objective function is:

Max E(G) = 0.0288x+0.0328y+0.0252z

subjected to:

x, y, z &gt;0

x + y + z &lt;= 50000

0.1028x+0.1490y+0.0252z &gt;=0

-x+0.1490y+0.0252z &gt;= 0

0.1028x-y+0.0252z &gt;= 0

or we solve this:

Max E(G) = 0.0288x+0.0328y+0.0252z

subjected to:

x, y, z &gt;0

x + y + z &lt;= 50000

0.1028x+0.1490y+0.0252z &gt;=0

-x+0.1490y+0.0252z &gt;= 0

-x-y+0.0252z &gt;= 0

or we solve this:

Max E(G) = 0.0288x+0.0328y+0.0252z

subjected to:

x, y, z &gt;0

x + y + z &lt;= 50000

0.1028x+0.1490y+0.0252z &gt;=0

0.1028x-y+0.0252z &gt;= 0

``` r
lprec <- make.lp(0, 3)
lp.control(lprec,sense='max')
```

    ## $anti.degen
    ## [1] "fixedvars" "stalling" 
    ## 
    ## $basis.crash
    ## [1] "none"
    ## 
    ## $bb.depthlimit
    ## [1] -50
    ## 
    ## $bb.floorfirst
    ## [1] "automatic"
    ## 
    ## $bb.rule
    ## [1] "pseudononint" "greedy"       "dynamic"      "rcostfixing" 
    ## 
    ## $break.at.first
    ## [1] FALSE
    ## 
    ## $break.at.value
    ## [1] 1e+30
    ## 
    ## $epsilon
    ##       epsb       epsd      epsel     epsint epsperturb   epspivot 
    ##      1e-10      1e-09      1e-12      1e-07      1e-05      2e-07 
    ## 
    ## $improve
    ## [1] "dualfeas" "thetagap"
    ## 
    ## $infinite
    ## [1] 1e+30
    ## 
    ## $maxpivot
    ## [1] 250
    ## 
    ## $mip.gap
    ## absolute relative 
    ##    1e-11    1e-11 
    ## 
    ## $negrange
    ## [1] -1e+06
    ## 
    ## $obj.in.basis
    ## [1] TRUE
    ## 
    ## $pivoting
    ## [1] "devex"    "adaptive"
    ## 
    ## $presolve
    ## [1] "none"
    ## 
    ## $scalelimit
    ## [1] 5
    ## 
    ## $scaling
    ## [1] "geometric"   "equilibrate" "integers"   
    ## 
    ## $sense
    ## [1] "maximize"
    ## 
    ## $simplextype
    ## [1] "dual"   "primal"
    ## 
    ## $timeout
    ## [1] 0
    ## 
    ## $verbose
    ## [1] "neutral"

``` r
set.objfn(lprec, c(investment_summray[1,5], investment_summray[2,5], investment_summray[3,5]))
add.constraint(lprec, c(1, 1, 1), "<=", 50000)
add.constraint(lprec, c(investment_summray[1,2],
                        investment_summray[2,2],
                        investment_summray[3,5]), ">=",0)
add.constraint(lprec, c(-1,
                        investment_summray[2,2],
                        investment_summray[3,5]), ">=",0)
add.constraint(lprec, c(investment_summray[1,2],
                        -1,
                        investment_summray[3,5]), ">=",0)

RowNames <- c("ROW1", "ROW2", "ROW3","ROW4")
ColNames <- c("COLONE", "COLTWO", "COLTHREE")
dimnames(lprec) <- list(RowNames, ColNames)

solve(lprec)
```

    ## [1] 0

``` r
get.objective(lprec)
```

    ## [1] 1275.164

``` r
get.variables(lprec)
```

    ## [1]  1390.149  1334.252 47275.599

For the first scenario, we got the maximum net gain $1275.164 by put $1390.149 in EWI, $1334.252 USO in and $47275.599 in Series I Bonds.

``` r
lprec_2 <- make.lp(0, 3)
lp.control(lprec_2,sense='max')
```

    ## $anti.degen
    ## [1] "fixedvars" "stalling" 
    ## 
    ## $basis.crash
    ## [1] "none"
    ## 
    ## $bb.depthlimit
    ## [1] -50
    ## 
    ## $bb.floorfirst
    ## [1] "automatic"
    ## 
    ## $bb.rule
    ## [1] "pseudononint" "greedy"       "dynamic"      "rcostfixing" 
    ## 
    ## $break.at.first
    ## [1] FALSE
    ## 
    ## $break.at.value
    ## [1] 1e+30
    ## 
    ## $epsilon
    ##       epsb       epsd      epsel     epsint epsperturb   epspivot 
    ##      1e-10      1e-09      1e-12      1e-07      1e-05      2e-07 
    ## 
    ## $improve
    ## [1] "dualfeas" "thetagap"
    ## 
    ## $infinite
    ## [1] 1e+30
    ## 
    ## $maxpivot
    ## [1] 250
    ## 
    ## $mip.gap
    ## absolute relative 
    ##    1e-11    1e-11 
    ## 
    ## $negrange
    ## [1] -1e+06
    ## 
    ## $obj.in.basis
    ## [1] TRUE
    ## 
    ## $pivoting
    ## [1] "devex"    "adaptive"
    ## 
    ## $presolve
    ## [1] "none"
    ## 
    ## $scalelimit
    ## [1] 5
    ## 
    ## $scaling
    ## [1] "geometric"   "equilibrate" "integers"   
    ## 
    ## $sense
    ## [1] "maximize"
    ## 
    ## $simplextype
    ## [1] "dual"   "primal"
    ## 
    ## $timeout
    ## [1] 0
    ## 
    ## $verbose
    ## [1] "neutral"

``` r
set.objfn(lprec_2, c(investment_summray[1,5], investment_summray[2,5], investment_summray[3,5]))
add.constraint(lprec_2, c(1, 1, 1), "<=", 50000)
add.constraint(lprec_2, c(investment_summray[1,2],
                        investment_summray[2,2],
                        investment_summray[3,5]), ">=",0)
add.constraint(lprec_2, c(-1,
                        investment_summray[2,2],
                        investment_summray[3,5]), ">=",0)
add.constraint(lprec_2, c(-1,
                        -1,
                        investment_summray[3,5]), ">=",0)

RowNames <- c("ROW1", "ROW2", "ROW3","ROW4")
ColNames <- c("COLONE", "COLTWO", "COLTHREE")
dimnames(lprec_2) <- list(RowNames, ColNames)

solve(lprec_2)
```

    ## [1] 0

``` r
get.objective(lprec_2)
```

    ## [1] 1269.345

``` r
get.variables(lprec_2)
```

    ## [1]     0.000  1229.028 48770.972

For the second senario, we got the maximum net gain $1269.345 by put $0 in EWI, $1229.028 USO in and $48770.972 in Series I Bonds.

``` r
lprec_3 <- make.lp(0, 3)
lp.control(lprec_3,sense='max')
```

    ## $anti.degen
    ## [1] "fixedvars" "stalling" 
    ## 
    ## $basis.crash
    ## [1] "none"
    ## 
    ## $bb.depthlimit
    ## [1] -50
    ## 
    ## $bb.floorfirst
    ## [1] "automatic"
    ## 
    ## $bb.rule
    ## [1] "pseudononint" "greedy"       "dynamic"      "rcostfixing" 
    ## 
    ## $break.at.first
    ## [1] FALSE
    ## 
    ## $break.at.value
    ## [1] 1e+30
    ## 
    ## $epsilon
    ##       epsb       epsd      epsel     epsint epsperturb   epspivot 
    ##      1e-10      1e-09      1e-12      1e-07      1e-05      2e-07 
    ## 
    ## $improve
    ## [1] "dualfeas" "thetagap"
    ## 
    ## $infinite
    ## [1] 1e+30
    ## 
    ## $maxpivot
    ## [1] 250
    ## 
    ## $mip.gap
    ## absolute relative 
    ##    1e-11    1e-11 
    ## 
    ## $negrange
    ## [1] -1e+06
    ## 
    ## $obj.in.basis
    ## [1] TRUE
    ## 
    ## $pivoting
    ## [1] "devex"    "adaptive"
    ## 
    ## $presolve
    ## [1] "none"
    ## 
    ## $scalelimit
    ## [1] 5
    ## 
    ## $scaling
    ## [1] "geometric"   "equilibrate" "integers"   
    ## 
    ## $sense
    ## [1] "maximize"
    ## 
    ## $simplextype
    ## [1] "dual"   "primal"
    ## 
    ## $timeout
    ## [1] 0
    ## 
    ## $verbose
    ## [1] "neutral"

``` r
set.objfn(lprec_3, c(investment_summray[1,5], investment_summray[2,5], investment_summray[3,5]))
add.constraint(lprec_3, c(1, 1, 1), "<=", 50000)
add.constraint(lprec_3, c(investment_summray[1,2],
                        investment_summray[2,2],
                        investment_summray[3,5]), ">=",0)
add.constraint(lprec_3, c(investment_summray[1,2],
                        -1,
                        investment_summray[3,5]), ">=",0)

RowNames <- c("ROW1", "ROW2", "ROW3")
ColNames <- c("COLONE", "COLTWO", "COLTHREE")
dimnames(lprec_3) <- list(RowNames, ColNames)

solve(lprec_3)
```

    ## [1] 0

``` r
get.objective(lprec_3)
```

    ## [1] 1459.105

``` r
get.variables(lprec_3)
```

    ## [1] 45339.137  4660.863     0.000

For the third senario, we got the maximum net gain $1459.105 by put $45339.137 in EWI, $4660.863 USO in and $0 in Series I Bonds.

``` r
Net.Gain <- format(c(get.objective(lprec),get.objective(lprec_2),get.objective(lprec_3)))
EWI = format(c(get.variables(lprec)[1],get.variables(lprec_2)[1],get.variables(lprec_3)[1]))
USO = format(c(get.variables(lprec)[2],get.variables(lprec_2)[2],get.variables(lprec_3)[2]))
Series.I.Bonds = format(c(get.variables(lprec)[3],get.variables(lprec_2)[3],get.variables(lprec_3)[3]))
library(kableExtra)
(opti_oneyear <- as.data.frame(cbind(EWI,USO,Series.I.Bonds,Net.Gain)))
```

    ##         EWI      USO Series.I.Bonds Net.Gain
    ## 1  1390.149 1334.252       47275.60 1275.164
    ## 2     0.000 1229.028       48770.97 1269.345
    ## 3 45339.137 4660.863           0.00 1459.105
