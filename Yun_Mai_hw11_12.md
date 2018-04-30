YunMai\_DATA609\_hw\_11\_12
================
Yun Mai
April 26, 2018

Homework 6.1 (select 2 questions from below list, due next week):

Page 529: \#1, \#6 Page 536: \#7 Page 546: \#1 Page 566: \#1

Homework 6.2 (select 2 questions from below list, due this week):

Page 576: \#2 Page 585: \#2 Page 591: \#5 Page 599: \#5

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

So $r\_1 and r\_2 $ have Complex Roots & *r*<sub>1</sub> ≠ *r*<sub>2</sub>

*r*<sub>1</sub>, *r*<sub>2</sub> = ±*i*

In the case when$ r\_1 and r\_2$ have complex Roots &*r*<sub>1</sub> ≠ *r*<sub>2</sub>, if the real part of roots h &lt; 0, the steady state is stable. But now h = 0, so the steady state is unstable. In the other word, The rest point is unstable.

Page 536: \#7

1.  Show that the two trajectories leading to (m=n, a=b/ shown in Figure 12.8 are unique.

<!-- -->

1.  From system (12.6) derive the following equation:

$\\frac{dx}{dy}=\\frac{(m-nx)y}{(a-by)x}$

1.  Separate variables, integrate, and exponentiate to obtain

*y*<sup>*a*</sup>*e*<sup>−*b**y*</sup> = *K**x*<sup>*m*</sup>*e*<sup>−*n**x*</sup>

where K is a constant of integration.

1.  Let *f*(*y*)=*y*<sup>*a*</sup>/*e*<sup>*b**y*</sup> . Show that f(y) has a unique maximum of $M\_y = (a/eb)^a $ when y = a/b as shown in Figure 12.12. Similarly, show that g(x) has a unique maximum $M\_x = (x/en)^m $ when x= m/n, also shown in Figure 12.12.

**Solution:**

***Reference:*** 1. Simultaneous Systems of Differential Equations (<http://web.uvic.ca/~kumara/econ351/>) 2. Linear, Second-Order Differential Equations (<http://web.uvic.ca/~kumara/econ351/schap23.pdf>)
