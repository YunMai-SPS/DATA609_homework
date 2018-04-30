YunMai\_DATA609\_hw\_11\_12
================
Yun Mai
April 26, 2018

Options: Homework 6.1 (select 2 questions from below list, due next week):

Page 529: \#1, \#6

Page 536: \#7

Page 546: \#1

Page 566: \#1

Homework 6.2 (select 2 questions from below list, due this week):

Page 576: \#2

Page 585: \#2

Page 591: \#5

Page 599: \#5

Chapter 12 Modeling With System of Different Equations
------------------------------------------------------

### 12.1 Graphical Solutions of the Autonomous System of First-order Differential

Page 529: \#1, \#6

In Problems 1-4, verify that the given function pair is a solution to the first-order system.

1.*x* = −*e*<sup>*t*</sup> *y* = *e*<sup>*t*</sup>

$\\frac{dy}{dt}=x$ $\\frac{dx}{dt}=y$

**Prove:**

Differentiation of x gives us:

*x*′= − *e*<sup>*t*</sup> = *y*

Differentiation of y gives us:

*y*′=*e*<sup>*t*</sup> = *x*

So *x* = −*e*<sup>*t*</sup> and *y* = *e*<sup>*t*</sup> is the solution to the first-order system $\\frac{dy}{dt}=x$ $\\frac{dx}{dt}=y$

In Problems 5-8, find and classify the rest points of the given autonomous system.

1.  $\\frac{dx}{dt}=-(y-1)$ $\\frac{dy}{dt}= x-2$

**Solution:**

If simultaneously $\\frac{dx}{dt}=-(y-1) = 0$ and $\\frac{dy}{dt}= x-2 = 0$. Thus (x= 2, y=1), is the rest point of the system.

Use the Substitution Method to find the solution for the first-order syste.

*x*′=1 − *y*, *x*″= − *y*′= − *x* + 2 *x*″+*x* = 2

*y*′=*x* − 2, *y*″= − *x*′=1 − *y* *y*″+*y* = 1

*x*′=*a*<sub>11</sub>*x* + *a*<sub>12</sub>*y* + *b*<sub>1</sub>, *a*<sub>11</sub> = 0, *a*<sub>12</sub> = −1, *b*<sub>1</sub> = 1

*x*′=*a*<sub>21</sub>*x* + *a*<sub>22</sub>*y* + *b*<sub>2</sub>, *a*<sub>11</sub> = 1, *a*<sub>12</sub> = 0, *b*<sub>2</sub> = −2

Solve the homogeneous part:

*x*<sup>*h*</sup>(*t*)=*c*<sub>1</sub>*e*<sup>*r*<sub>1</sub>*t*</sup> + *c*<sub>2</sub>*e*<sup>*r*<sub>2</sub>*t*</sup>

*y*<sup>*h*</sup>(*t*)= − *r*<sub>1</sub>*c*<sub>1</sub>*e*<sup>*r*<sub>1</sub>*t*</sup> − *r*<sub>2</sub>*c*<sub>2</sub>*e*<sup>*r*<sub>2</sub>*t*</sup>

Solve the steady-state part:

$\\overline{x}=\\frac{a\_{12}b\_2 - a\_{22}b\_1}{a\_{11}a\_{22} - a\_{12}a\_{21}}$

$\\overline{x}= \\frac{(-1)\\times(-2)-0}{-(-1)\\times 1} = 2$

$\\overline{y}=\\frac{a\_{21}b\_2 - a\_{11}b\_1}{a\_{11}a\_{22} - a\_{12}a\_{21}}$

$\\overline{y}=\\frac{1\\times 1-0}{0-(-1)\\times 1} = 1$

If *x*″+*a*<sub>1</sub>*x* − 2 = 0, r\_1,r\_2 could be expressed as:

$r\_1,r\_2 = \\frac{- a\_1 \\pm \\sqrt{a\_1^2-4a\_2}}{2}$

*a*<sub>1</sub><sup>2</sup> = 0, 4*a*<sub>2</sub> = 4

So

*a*<sub>1</sub><sup>2</sup> &lt; 4*a*<sub>2</sub>

So *r*<sub>1</sub>*a**n**d**r*<sub>2</sub> have Complex Roots & *r*<sub>1</sub> ≠ *r*<sub>2</sub>

*r*<sub>1</sub>, *r*<sub>2</sub> = ±*i*

In the case when$ r\_1 and r\_2$ have complex Roots &*r*<sub>1</sub> ≠ *r*<sub>2</sub>, if the real part of roots h &lt; 0, the steady state is stable. But now h = 0, so the steady state is unstable. In the other word, The rest point is unstable.

### 12.2 Competitive Hunter Model

Page 536: \#7

1.  Show that the two trajectories leading to (m=n, a=b/ shown in Figure 12.8 are unique.

<!-- -->

1.  From system (12.6) derive the following equation:

$\\frac{dy}{dx}=\\frac{(m-nx)y}{(a-by)x}$

**Prove:**

system (12.6)

$\\frac{dx}{dt}=(a-by)x$

$\\frac{dy}{dt}=(m-nx)y$

According to the chain rule, we have

$\\frac{dy}{dx}\\frac{dx}{dt}=\\frac{dy}{dt}$

$\\frac{dx}{dy}(a-by)x=(m-nx)y$

$\\frac{dx}{dy}=\\frac{(m-nx)y}{(a-by)x}$

1.  Separate variables, integrate, and exponentiate to obtain

*y*<sup>*a*</sup>*e*<sup>−*b**y*</sup> = *K**x*<sup>*m*</sup>*e*<sup>−*n**x*</sup>

where K is a constant of integration.

**Solution:**

Separate the variables and write

$\\frac{(a-by)}{y}dy=\\frac{(m-nx)}{x}dx$

$aln|y|-by + C\_1 = mln|x|-nx + C\_2 $

*a* × *l**n*|*y*|−*b**y* = *m* × *l**n*|*x*|−*n**x* + *C*<sub>2</sub> − *C*<sub>1</sub>

Applying the natural logarithm to each side:

*e*<sup>*a* × *l**n*|*y*|−*b**y*</sup> = *e*<sup>*m* × *l**n*|*x*|−*n**x*</sup> × *e*<sup>*C*<sub>2</sub> − *C*<sub>1</sub></sup>

*e*<sup>*l**n*|*y*|<sup>*a*</sup> − *b**y*</sup> = *e*<sup>*l**n*|*x*|<sup>*m*</sup> − *n**x*</sup> × *e*<sup>*C*<sub>2</sub> − *C*<sub>1</sub></sup>

Let

*K* = *e*<sup>*C*<sub>2</sub> − *C*<sub>1</sub></sup>

Then

*y*<sup>*a*</sup>*e*<sup>−*b**y*</sup> = *K**x*<sup>*m*</sup>*e*<sup>−*n**x*</sup>

1.  Let *f*(*y*)=*y*<sup>*a*</sup>/*e*<sup>*b**y*</sup> . Show that f(y) has a unique maximum of *M*<sub>*y*</sub> = (*a*/*e**b*)<sup>*a*</sup> when y = a/b as shown in Figure 12.12. Similarly, show that g(x) has a unique maximum *M*<sub>*x*</sub> = (*x*/*e**n*)<sup>*m*</sup> when x= m/n, also shown in Figure 12.12.

**Prove:**

When

*f*′(*y*)=*a**y*<sup>*a* − 1</sup>*e*<sup>−*b**y*</sup> + *y*<sup>*a*</sup>*e*<sup>−*b**y*</sup>(−*b*)=0

y reach M\_y.

From the above equation, we have

*y* = *a*/*b*

$M\_y=y^a/e^{by}=(a/b)^a/e^{b\\frac{a}{b}}$

*M*<sub>*y*</sub> = (*a*/*e**b*)<sup>*a*</sup>

Similarly, when

*g*′(*x*)=*m**y*<sup>*m* − 1</sup>*e*<sup>−*n**y*</sup> + *y*<sup>*m*</sup>*e*<sup>−*n**y*</sup>(−*n*)=0

x reach M\_x.

From the above equation, we have

*x* = *m*/*n*

$M\_y=y^a/e^{by}=(a/b)^a/e^{b\\frac{a}{b}}$

*M*<sub>*x*</sub> = (*x*/*e**n*)<sup>*m*</sup>

Chapter 13 Optimization of Continuous Models
--------------------------------------------

### 13.1 An Inventory Problem: Minimizing the Cost of Delivery and Storage

Page 576: \#2

1.  Consider a company that allows back ordering. That is, the company notifies customers that a temporary stock-out exists and that their order will be filled shortly. What conditions might argue for such a policy? What effect does such a policy have on storage costs? Should costs be assigned to stock-outs? Why? How would you make such an assignment? What assumptions are implied by the model in Figure 13.7? Suppose a "loss of goodwill cost" of w dollars per unit per day is assigned to each stock-out. Compute the optimal order quantity Q\* and interpret your model.

**Solution:**

When the demand higher than offer might argue for such a policy because back ordering will decrease the revenue. Such a policy can reduce storage costs. But costs should also be assigned to stock-outs because the sales may decrease when the commodity is out of stock. The decrease of sales because of stock-outs will be assigned to cost.

The following notation are used for constructing the model:

s = storage costs per item per day

d = handling and delivery cost in dollars per sale

l = loss of goodwill cost

r = demand rate of the item per day

Q = quantity of the orders

T = time in days

Assuming Q = q, is sold at time T = 0, and the item is sold out after T = t days. l = w dollars per unit per day. The same cycle is then repeated, as illustrated in Figure 13.7.

Average daily inventory is q/2

cost per cycle: $d+s\\frac{q}{2} t+w$

which, upon division by t , yields the average daily cost:

$c = \\frac{d+w}{t}+s\\frac{q}{2}$

For a single cyclic period, the amount delivered equals the amount demanded. So q = rt. Subtitution yield

$c = \\frac{d}{t}+s\\frac{rt}{2}$

to find the crytical point

*c*′= = −(*d* + *w*)/*t*<sup>2</sup> + *s**r*/2 = 0

$T\* = \\sqrt{\\frac{2(d+w)}{sr}}$

This critical point provides a relative minimum for the cost function because the second derivative

*c*″= − 2(*d* + *w*)/*t*<sup>3</sup>

is always ngative. So we have maximum q=Q\* at this point.

Let's find the order quantity Q\* that minimizes the average daily cost. Differentiating c with respect to t and setting c0 D 0 yields

### 13.2 Methods to Optimize Functions of Several Variables

Page 585: \#2

***Reference:*** 1. Simultaneous Systems of Differential Equations (<http://web.uvic.ca/~kumara/econ351/>)

1.  Linear, Second-Order Differential Equations (<http://web.uvic.ca/~kumara/econ351/schap23.pdf>)
