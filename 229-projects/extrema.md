# Questions to be handed in on extrema:



To get started, we load the `Gadfly` backend to the `Plots`package,  so that we can make plots, and load the `Roots` package for `D` and `fzero`:

```
using Plots
gadfly()
using Roots			# for D and fzero
```

### Quick background

Read about this material here: [Maximization and minimization with julia](http://mth229.github.io/extrema.html).

For the impatient, *extrema* is nothing more than a fancy word for describing either a maximum *or* a minimum. In
calculus, we have two concepts of these: *relative* extrema and
*absolute* extrema. Let's focus for a second on *absolute* extrema which are
stated as:

> A value $y=f(x)$ is an absolute maximum over an interval
> $[a,b]$ if $y \geq f(x)$ for all $x$ in $[a,b]$. (An absolute minimum has $y \leq f(x)$ instead.)

Of special note is that an absolute extrema involves *both* a function and an interval.


There are two theorems which help identify extrema here. The first,
due to Bolzano, says that any continuous function on a *closed*
interval will have an absolute maximum and minimum on that
interval. The second, due to Fermat, tells us where to look: these
absolute maximums and minimums can only occur at end points or
critical points.

Bolzano and Fermat are historic figures. For us, we can plot a
function to visually see extrema. The value of Bolzano is the
knowledge that yes, plotting isn't a waste of time, as we are
*guaranteed* to see what we look for. The value of Fermat is that if
we want to get *precise* numeric answers, we have a means: identify
the end points and the critical points then compare  the function at *just* these values.

The notes walk us through the task of finding among all rectangles
with perimeter 20 the one with maximum area. This is done quickly via:

```
A(b, h) = h * b		        # base times height is area
## From P = 2b + 2h -> h = (P -2b)/2
h(b) = (20 - 2b)/2		# Relates perimeter, height, base
A(b) = A(b, h(b))		# substitution step: express Area in terms of b alone
x = fzero(D(A), [0, 10])	# critical point of A(b) on its domain
A(0), A(x), A(10)
```

Here `x` is a critical point. Following Fermat, we  checked the
value of the function at `x` along with the endpoints, $0$ and $10$.
However, a simple graph also illustrates that any maximum occurs in
between these endpoints (with the minimum occurring at both):

```
plot([A, D(A)], 0, 10)		# Notice zero of D(A) corresponds to maximum of A
```

Notice what is done. The original problem had two variables (a base
and a height) and a fixed relationship between them (the perimeter is
20). From this one variable can be deduced in terms of another leaving
us a continuous function (`A`) with extrema of interest (in this case
the maximum).


The typical steps are to write out the key relationships: $A=hb$ and
$P=2b+2h$, then use the constraint on the perimeter to substitute into
the equation we are tying to find the extrema of to make it depend on
a single variable. Then we can leverage various facts from
calculus. The substitution can be algebraically tedious. With `julia`
we leverage *multiple dispatch* to avoid the algebra: the substitution
step of math is done using **composition** of functions. That is we
define a funciton `A(b)` in terms of `A(b,h)` and let the computer
sort out what is meant based on the number of arguments we use when it is
called.


To solve for the $x$-value corresponding to the extrema, we used
`fzero` with bracketing, as it is guaranteed to converge and it is clear
that the interval $[0,10]$ is a bracket for the derivative
function. We could also have identified a good initial guess for the
maximum from the graph, say 5, and just called `fzero` with this initial guess, as
`fzero(D(A), 5)`.


For other problems, we might use `fzeros` to identify all the critical points. This would return a vector of values, so some additional care is needed. Here we repeat the last two steps to show how the critical points can be checked:

```
cps = fzeros(D(A), [0,10])
check_these = [0, cps..., 10]   # one way to add the endpoints to the values to check.
map(A, check_these)
```


### Questions

For the following questions (which were cribbed from various internet
sources), find the most precise answer available, a graphical solution
is not enough, the answers should  use one of the zero-finding methods.

* <em>Ye olde post office</em>

A box with a square base is taller than it is wide. In order to send
the box through the U.S. mail, the height of the box and the perimeter
of the base can sum to no more than 108 inches. Show how to compute the maximum
volume for such a box.


```
```

```
longq("Maximum volume for box with 108 = h + 4w", rows=6)
```

* <em>Inscription</em>

A trapezoid is inscribed in a semicircle of radius $r=2$ so that one
side is along the diameter. Find the maximum possible area for the
trapezoid.

Draw a picture of a semicircle and a trapezoid. The trapezoid
intersects the circle at $(r,0)$, $(-r,0)$, $(r \cos(t), r\sin(t))$
and $(-r\cos(t), r\sin(t))$ where $t$ is some angle in $[0,
\pi/2]$. The area of trapezoid is the height times the average of the
two bases.


```
```

```
longq("Commands to find maximum area of inscribed trapezoid",
"\\verb#r=2;A(t) = (2r+2r*cos(t))/2 * r*sin(t);fzero(D(A), 1) has 1.04; A=5.19#",
rows=6)
```

* <em>Cheap paper cups</em>

A cone-shaped paper drinking cup is to hold 100 cubic centimeters of
water (about 4 ozs). Find the height and radius of the cup that will
require the least amount of paper. 
The volume of such a cup is given by the volume of a cone formula: $V
= (1/3)\pi r^2 h$; and the area of the paper is given by for formula for surface area of a cone: $A=\pi r
\sqrt{r^2 + h^2}$. Show any work.

```
```

```
longq("work to minimize paper used in conical drink cup", 
"\\verb#h(r)=100/((1/3)*pi*r^2); A(r,h)=pi*r*sqrt(r^2+h^2); A(r)=A(r,h(r))#, r is around 4.0721",
rows=6)
```



* <em>How big is that can?</em>

A cylindrical can, **open on top**, is to hold 355 cubic centimeters of
liquid. Find the height and radius that minimizes the amount of
material needed to manufacture the can. (These are metric units, so
the answer will be in centimeters with 2.54cm=1in.)  Illustrate how this is done:

```
```

```
longq("Minimize material in can", "\\verb#SA(r,h)=2pi*r*h+pi*r^2;h(r)=355/(pi*r^2)#", rows=6)
```


Do these proportions match those that are typical for a 12 oz can?

```
```

```
choices = ["Yes, the height is about 2 times the diameter",
	"No, the can has a square profile",
	"No, the diameter is twice the height"]
ans = 3
radioq(choices, ans, "Do proportions match?")
```
	



* <em>Getting closer</em>


Let $f(x) = \tan(x)$. Find the point on the graph of $f(x)$ that is closest to the point $(\pi/4, 0)$. Show any work.

```
```

```
longq("Minimize distance to graph", 
"\\verb#f(x) = tan(x); d2(x)=(x-pi/4)^2+(f(x)-0)^2; fzero(D(d2), 0, pi/3)# is 0.35...", rows=6)
```


* <em>Best size for a phone</em>

A cell phone manufacturer wishes to make a rectangular phone with
total surface area of 12,000 $mm^2$ and maximal screen area. The
screen is surrounded by bezels with sizes of 8$mm$ on the long sides
and 32$mm$ on the short sides. (So the width of the screen would be
the width of the phone minus 2 time 8mm, and the height of the screen
similarly adjusted.)
  
What are the dimensions (width and
height) that allow the maximum screen area? 

```
```

```
longq("maximize printable area with 1/4-inch margins",
"\\verb+w(h)=12000/h; A(w,h)=(w-2*8)*(h-2*32); A(h)=A(w(h),h); h = fzero(D(A),100)->219..; w(h)=54... +",
rows=6)
```

  The dimensions for the most phones have a height that is about twice the
  width. Is that the case for your answer?

```
choices = [
"No the height to width ratio is closer to 1 to 1",
"Yes, the height to width ratio is basically 2 to 1",
"No, the height to width ratio is closer to 3 to 1",
"No the height to width ratio is closer to 4 to 1",
]
ans = 4
radioq(choices, ans)
```

* <em>Will you be in the water?</em>

The Statue of Liberty stands 92 meters high, including the pedestal
which is 46 meters high. How far from the base is it when
the viewing angle, theta, is as large as possible?
[figure](http://astro.temple.edu/~dhill001/maxmin/viewanglepic.gif)

```
```

```
longq("Commands to maximize viewing angle","\\verb#Theta(x)=atan(92/x)-atan(46/x);fzero(D(Theta), 50)# is 65", rows=6)
```