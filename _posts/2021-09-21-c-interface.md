---
layout: post
use_math: true
---

# C quick start
The below solves the problem $\min. x_1, \textrm{subject to } x_3 = 1, x_1 \ge \sqrt{x_2^2 + x_3^2}$. This obviously has the optimal solution $x= (1, 0, 1)^T$.

First we include the headers we need.
```
#include "wolf.h"
#include <stdio.h>
```
Then we define the function to output the solver progress during the solution process:
```
static int print_progress(integer iteration, double *dnfo)
{
	/* Output progress update. */
	printf("%3i %8.1e %8.1e %8.1e %8.1e %11.4e %11.4e %7.3f %8.1e %8.1e %7.1f\n", (int)iteration, dnfo[0], dnfo[1], dnfo[2], dnfo[3], dnfo[4], dnfo[5], dnfo[6], dnfo[7], dnfo[8], dnfo[9]);
	return 0;
}
```

Inside our main function we declare the problem struct and specify the number of primitive cones for each cone type:
```
int main()
{
	/* Set up problem struct. */
	wolf_cos_t prob;
	prob.m = 1;
	prob.n = 3;
	prob.nF = 0;
	prob.nL = 0;
	prob.nQ = 1;
	prob.nR = 0;
	prob.nS = 0;
	prob.nP = 0;
	prob.nE = 0;
```

The actual problem is defined here, with $A=[0 0 1]$, $b=[1]$ and $c=[1 0 0]^T$, and the pointer structure for the second-order cone of size 3.
```	
	integer pQ[2] = { 0, 3 };
	integer pA[4] = { 0, 0, 0, 1 };
	integer iA[1] = { 0 };
	double xA[1] = { 1.0 };
	double b[1] = { 1.0 };
	double c[3] = { 1.0, 0.0, 0.0 };
```

The unknown variables being optimized, $x$, $y$ and $s$ for the primal and dual problems are declared.
```
	double x[3], y[1], s[3];
```

Pointers to all of these arrays are then needed in the problem structure being passed to the solver.
```
	prob.x = x;
	prob.y = y;
	prob.s = s;

	prob.pQ = pQ;
	prob.pA = pA;
	prob.iA = iA;
	prob.xA = xA;
	prob.b = b;
	prob.c = c;
```

Set the default parameters, modify the maximum number of iterations or other parameters as desired.
```
	/* Set default parameters. */
	wolf_setpar(&prob);

	/* Modify default parameters. */
	prob.ipar[WOLF_COS_IS_MAXITER] = 99;
```

In preparation for calling the solver, we output a header for the callback function output.
```
	/* Output progress header. */
	printf("\nWolf Conic Optimization Solver\n");
	printf(" It    pinf     dinf     ginf      u       pobj        dobj      alpha    tau     kappa     time\n");
	printf("-------------------------------------------------------------------------------------------------\n");
```

We now call the solver and check for error messages upon return, displaying the error message if necessary.
```
	/* Solve the problem. */
	wolf_cos(&prob, print_progress);

	/* Check for error and display message. */
	if (prob.error != 0)
		printf("*** %s ***\n", wolf_geterrormsg(prob.error));
```

We can then display various solution information details, as required. Here we select a relevant subset for the problem at hand.
```
	printf("\nSolution info:\n--------------\n");
	printf("Average objective:      %9.7f\n", 0.5*(prob.dnfo[0] + prob.dnfo[1]));
	printf("Iterations:             %d\n", prob.info[0]);
	printf("Primal infeasibility:  %8.1e\n", prob.dnfo[2]);
	printf("Dual infeasiblity:     %8.1e\n", prob.dnfo[3]);
	printf("Relative duality gap:  %8.1e\n", prob.dnfo[4]);

	printf("x = [ %9.7f %9.7f %9.7f ]\n", x[0], x[1], x[2]);
	printf("y = [ %9.7f ]\n", y[0]);
	printf("s = [ %9.7f %9.7f %9.7f ]\n", s[0], s[1], s[2]);

	printf("Second-order cones:    %10li\n", prob.info[5]);
	printf("Non-zeros in A:        %10li (originally %10li)\n", prob.info[7], prob.pA[prob.n]);
	printf("Non-zeros in M:        %10li\n", prob.info[8]);
	printf("Non-zeros in L:        %10li\n", prob.info[9]);
}
```

This gives the following output before and during the solver's progress.
```
Wolf Conic Optimization Solver

 It    pinf     dinf     ginf      u       pobj        dobj      alpha    tau     kappa     time
-------------------------------------------------------------------------------------------------
  0  1.0e+00  4.1e-01  2.4e+00  1.0e+00  1.4142e+00  0.0000e+00   0.000  1.0e+00  1.0e+00     0.0
  1  1.7e-01  6.9e-02  4.0e-01  1.7e-01  1.1089e+00  9.0709e-01   0.841  7.9e-01  2.4e-01     0.0
  2  1.7e-02  7.1e-03  4.1e-02  1.7e-02  1.0115e+00  9.9023e-01   0.900  7.8e-01  2.5e-02     0.0
  3  1.7e-03  7.1e-04  4.2e-03  1.7e-03  1.0012e+00  9.9901e-01   0.900  7.8e-01  2.5e-03     0.0
  4  1.7e-04  7.2e-05  4.2e-04  1.7e-04  1.0001e+00  9.9990e-01   0.900  7.8e-01  2.5e-04     0.0
  5  1.8e-05  7.3e-06  4.2e-05  1.7e-05  1.0000e+00  9.9999e-01   0.900  7.8e-01  2.5e-05     0.0
  6  1.8e-06  7.3e-07  4.3e-06  1.8e-06  1.0000e+00  1.0000e+00   0.900  7.8e-01  2.6e-06     0.0
  7  1.8e-07  7.4e-08  4.3e-07  1.8e-07  1.0000e+00  1.0000e+00   0.900  7.8e-01  2.6e-07     0.0
  8  1.8e-08  7.5e-09  4.4e-08  1.8e-08  1.0000e+00  1.0000e+00   0.900  7.8e-01  2.6e-08     0.0
  9  5.9e-09  7.5e-10  4.4e-09  2.7e-09  1.0000e+00  1.0000e+00   0.900  7.3e-01  4.2e-09     0.0
```

The extra information displayed after solving is as follows.
```
Solution info:
--------------
Average objective:      1.0000000
Iterations:             9
Primal infeasibility:   8.1e-09
Dual infeasiblity:      1.0e-09
Relative duality gap:   1.3e-10
x = [ 1.0000000 0.0000000 1.0000000 ]
y = [ 1.0000000 ]
s = [ 1.0000000 0.0000000 -1.0000000 ]

Problem size:
-------------
Number of constraints:          1 (originally          1)
Number of variables:            3 (originally          3)
Second-order cones:             1
Non-zeros in A:                 1 (originally          1)
Non-zeros in M:                 1
Non-zeros in L:                 1
```
