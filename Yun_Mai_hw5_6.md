DATA609\_assignment5\_6
================
Yun Mai
March 6, 2018

``` r
suppressMessages(suppressWarnings(library(knitr)))
suppressMessages(suppressWarnings(library(ggplot2)))
```

Page 228: \#1 Page 232: \#1 Page 240: \#1, \#2

Page 251: \#2 Page 264: \#6 Page 268: \#6 (i.e., only question \#6 in section 7.2) Page 278: \#6 (i.e., only question \#6 in section 7.2) Page 284: \#1 Page 295: \#3

Chapter 6 Descrete Probabilistic Modeling
-----------------------------------------

### 6.1 Probabilistic Modeling with Descrete Systems

#### Page 228: \#1

1.  Consider a model for the long-term dining behavior of the students at College USA. It is found that 25% of the students who eat at the college's Grease Dining Hall return to eat there again, whereas those who eat at Sweet Dining Hall have a 93% return rate. These are the only two dining halls available on campus, and assume that all students eat at one of these halls. Formulate a model to solve for the long-term percentage of students eating at each hall.

**Solution**

To fomulate a model for the long-term distribution of students in these two dinnign hall, we define the following variables:

*g*<sub>*n*</sub> = the percentage of student going to Grease Dining Hall at the end of period *s*<sub>*n*</sub> = the percentage of student going to Sweet Dining Hall at the end of period

The probability model:

$g\_{n+1}= 0.25g\_n + 0.7s\_n $ $a\_{n+1}= 0.93an + 0.75g\_n $

The transition matrix:

``` r
suppressMessages(suppressWarnings(library(markovchain)))
#setting up transition matrix

dinninghall <- c("Grease", "Sweet")
diningTransition <- matrix(c(0.25, 0.75, 
                            0.07, 0.93),
                            byrow = T, nrow = 2, dimnames = list(dinninghall,dinninghall))

mcDining <- new("markovchain", states = dinninghall, byrow = T, 
                transitionMatrix = diningTransition, name = "Dinning Halls")

mcDining
```

    ## Dinning Halls 
    ##  A  2 - dimensional discrete Markov Chain defined by the following states: 
    ##  Grease, Sweet 
    ##  The transition matrix  (by rows)  is defined as follows: 
    ##        Grease Sweet
    ## Grease   0.25  0.75
    ## Sweet    0.07  0.93

``` r
plot(mcDining)
```

![](Yun_Mai_hw5_6_files/figure-markdown_github/unnamed-chunk-3-1.png)

``` r
steadyStates(mcDining)
```

    ##          Grease     Sweet
    ## [1,] 0.08536585 0.9146341

In the long round, the percentage of students will eat at Grease Ding Hall is 8.54%, at Sweet Dinging Hall is 91.46%.

### 6.2 Modeling Component and System Reliability

#### Page 232: \#1

1.  Consider a stereo with CD player, FM-AM radio tuner, speakers (dual), and power amplifier (PA) components, as displayed with the reliabilities shown in Figure 6.11. Determine the system's reliability. What assumptions are required in your model?

There are three submodels. Assume that stereo with CD player, FM-AM radio tuner are independent so they could form parallel system. Two Speakers are independent and they could form parallel system. Power amplifier (PA) components will affect the whole system if it is out of order so PA and the other two submodels form a series system. If submodel of CD player and FM-AM radio tuner break, the stereo will not work. Similarly, if submodel of speakers (dual) or power amplifier are out of work, the stero will not give any sound. We assume that the reliability of the two spekers are the equal to each other.

We define the reliabilities of the four components as:

CD player rela=iability: Rc(t), FM-AM radio tuner reliability: Rr(t), SpeakerRs1 reliability:Rs(t), power amplifier reliability: Rp(t).

The combination of series and pararrel systems are:

*R**s*(*t*)=*R**s*1(*t*)*R**s*2(*t*)*R**s*3(*t*)=*R**p*(*t*)×(*R**c*(*t*)+*R**r*(*t*)−*R**c*(*t*)*R**r*(*t*)) × (*R**s*(*t*)+*R**s*(*t*)−*R**s*(*t*)*R**s*(*t*)) = 0.95 \* (0.98 + 0.97 − .98 \* 0.97)\*(0.99 + 0.99 − 0.99 \* 0.99)=0.95

So the the system's reliability is 0.95.

Chapter 7. Optimazation of Descrete Models
------------------------------------------

### 7.1 An Overview of Optimization Modeling

#### Page 251: \#2

1.  Nutritional Requirements. A rancher has determined that the minimum weekly nutritional requirements for an average-sized horse include 40 lb of protein, 20 lb of carbohydrates, and 45 lb of roughage. These are obtained from the following sources in varying amounts at the prices indicated:

| Food                                |  Protein (lb)|  Carbohydrates (lb)|  Roughage (lb)|  Cost($)|
|:------------------------------------|-------------:|-------------------:|--------------:|--------:|
| Hay (per bale)                      |           0.5|                 2.0|            5.0|      1.8|
| Oats (per sack)                     |           1.0|                 4.0|            2.0|      3.5|
| Feeding blocks (per block)          |           2.0|                 0.5|            1.0|      0.4|
| High-protein concentrate (per sack) |           6.0|                 1.0|            2.5|      1.0|
| Requirements per horse (per week)   |          40.0|                20.0|           45.0|       NA|

Formulate a mathematical model to determine how to meet the minimum nutritional requirements at minimum cost.

**Solution**

Let c1,c2,c3,c4 denote hey, oats,feeding blocks, high-protein concentrate. The objective function is:

Minimum the cost: 1.8 c1 + 3.5c2 + 0.4c3 + c4

subject to:

0.5c1+c2+2c3+6c4 &gt;= 40 2c1+4c2+0.5c3+c4 &gt;= 20 5c1+2c2+c3+2c4 &gt;= 45 c1,c2,c3,c4 &gt; 0

or

-0.5c1-c2-2c3-6c4 &lt;= -40 -2c1-4c2-0.5c3-c4 &lt;= -20 -5c1-2c2-c3-2c4 &lt;= -45 c1,c2,c3,c4 &gt; 0

``` r
suppressMessages(suppressWarnings(library(lpSolveAPI)))
#make.lp(nrow = 0, ncol = 0, verbose = "neutral")

lpmodel <-make.lp(7,4)
column <- 0
#build the model column per column
for(fd in 1:4){
  column <- column+1
  #this takes the arguments 'column','values' & 'indices' (as in where these values should be placed in the column)
  set.column(lpmodel,column,c(-tb_2[fd,2],-tb_2[fd,3],-tb_2[fd,4]), indices=c(1,2,3))
}

for(i in 4:7){
  set.mat(lpmodel, i,i-3,1)

}

lpmodel
```

    ## Model name: 
    ##             C1    C2    C3    C4         
    ## Minimize     0     0     0     0         
    ## R1        -0.5    -1    -2    -6  free  0
    ## R2          -2    -4  -0.5    -1  free  0
    ## R3          -5    -2    -1  -2.5  free  0
    ## R4           1     0     0     0  free  0
    ## R5           0     1     0     0  free  0
    ## R6           0     0     1     0  free  0
    ## R7           0     0     0     1  free  0
    ## Kind       Std   Std   Std   Std         
    ## Type      Real  Real  Real  Real         
    ## Upper      Inf   Inf   Inf   Inf         
    ## Lower        0     0     0     0

``` r
#set rhs volume constraints
set.constr.value(lpmodel, rhs=unlist(tb_2[5,2:4]), constraints=seq(1,3))

#set rhs volume constraints
set.constr.value(lpmodel, rhs=rep(0,4), constraints=seq(4,7))
 
#set objective coefficients
set.objfn(lpmodel, tb_2$`Cost($)`[1:4])

lpmodel
```

    ## Model name: 
    ##             C1    C2    C3    C4          
    ## Minimize   1.8   3.5   0.4     1          
    ## R1        -0.5    -1    -2    -6  free  40
    ## R2          -2    -4  -0.5    -1  free  20
    ## R3          -5    -2    -1  -2.5  free  45
    ## R4           1     0     0     0  free   0
    ## R5           0     1     0     0  free   0
    ## R6           0     0     1     0  free   0
    ## R7           0     0     0     1  free   0
    ## Kind       Std   Std   Std   Std          
    ## Type      Real  Real  Real  Real          
    ## Upper      Inf   Inf   Inf   Inf          
    ## Lower        0     0     0     0

``` r
#set objective direction
lp.control(lpmodel,sense='min')
```

    ## $anti.degen
    ## [1] "none"
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
    ## [1] -1e+30
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
    ## [1] "minimize"
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
#solve the model, if this return 0 an optimal solution is found
solve(lpmodel)
```

    ## [1] 0

``` r
#this return the proposed solution
get.objective(lpmodel)
```

    ## [1] 0

The solution is 0, suggesting more contrains are needed to get meaningful results.

### 7.2. Linear Programing I: Geometric Solution

#### Page 264: \#6

Solve Problems 4-7 using graphical analysis.

1.  Maximize 10x+35y

subject to

8x + 6y &lt;= 48 (board-feet of lumber)

4x + y &lt;= 20 (hours of carpentry)

y &gt;= 5 (demand)

x, y &gt;= 0 (nonnegativity)

``` r
# plot constraints
# first provide dummy data set
p <- ggplot(data = data.frame(x = 0), mapping = aes(x = x))

#constraints
fun.1 <- function(x) 8-(8/6)*x
fun.2 <- function(x) 20-4*x
fun.3 <- function(x) 5
fun.4 <- function(x) 0

x<-as.data.frame(seq(0,20,0.05))
y1<-8-(8/6)*x
y2<-20-4*x

g1<-data.frame('x'=x,'y1'=y1,'y2'=y2)

#pplot multiple functions
p + stat_function(fun = fun.1, aes(colour = "fun.1")) +
  
  stat_function(fun = fun.2, aes(colour = "fun.2"), size = 1) +
  stat_function(fun = fun.3, aes(colour = "fun.3"), size = 1) +
  stat_function(fun = fun.4, aes(colour = "fun.4"), size = 1) +
  xlim(-7,7)+
  geom_vline(xintercept = 0) + 
  geom_hline(yintercept = 0) +
  geom_text(data = g1, aes(x = 0, y = 20, label = "(0,20)"))+
  geom_text(data = g1, aes(x = 0, y = 8, label = "(0,8)"))+
  geom_text(data = g1, aes(x = 2.25, y = 5, label = "(2.25,5)"))+
  geom_text(data = g1, aes(x = 3.75, y = 5, label = "(3.75,5)"))
```

![](Yun_Mai_hw5_6_files/figure-markdown_github/unnamed-chunk-8-1.png)

Find the intersection points of the constraints

``` r
fun.5<- function(x) 6-(6/8)*x
point_1 <- uniroot(function(x) fun.5(x),c(-10,1e8),extendInt = 'no')$root

fun.6<- function(x) 5-(1/4)*x
point_2 <- uniroot(function(x) fun.6(x),c(-10,1e8),extendInt = 'no')$root

point_3 <- uniroot(function(x) fun.1(x)-fun.3(x),c(0,1e8),extendInt = 'no')$root
point_4 <- uniroot(function(x) fun.2(x)-fun.3(x),c(0,1e8),extendInt = 'no')$root

(int_points <- data.frame('x'=c(0,0,point_3,point_4),'y'=c(point_1,point_2,5,5)))
```

    ##      x  y
    ## 1 0.00  8
    ## 2 0.00 20
    ## 3 2.25  5
    ## 4 3.75  5

``` r
opti_v <- max(10*int_points$x+35*int_points$y)
cat('The maximized objective function value is: ',opti_v ) 
```

    ## The maximized objective function value is:  700
