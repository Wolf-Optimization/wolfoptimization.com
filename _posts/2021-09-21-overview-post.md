---
layout: post
use_math: true
---

# Introduction
The Wolf Optimization solver is a high-performance implementation of a simplified homogeneous self-dual primal-dual interior-point method that is especially efficient at solving large, sparse conic optimization problems. A brief overview of the capabilities and interfaces is included below.

# Conic constraints
The Wolf conic optimization solver currently accepts the following conic constraints:
- Linear or non-negative orthant, $x \ge 0$
- Second-order cone of size $n$ (also known as the conic quadratic cone and Lorentz cone), $x_1 \ge \sqrt{x_2^2 + ... + x_n^2}$
- Rotated second-order cone of size $n$ (also known as the rotated conic quadratic cone), $2x_1x_2 \ge x_3^2 + ... + x_n^2$
[//] # "- Semidefinite cone of size $n \times n$, $v^TXv \ge 0 \, \forall \, v \in \mathbb{R}^n$ (i.e. the symmetric matrix $X$ is positive semidefinite or, equivalently, does not have negative eigenvalues)"
- Power cone, $x_1^\alpha x_2^{1-\alpha} \ge \|x_3\|, \, x_1,x_2 \ge 0$
[//] # "- Exponential cone, $x_1 \ge x_2 e^{x_3/x_2}$"
- Free cone, $x \in \mathbb{R}$. While free variables have conventionally been transformed into the difference between two linear variables, it can be advantageous to try and eliminate the free variables from the problem and thus reducing the size of the problem being solved. Those that are not eliminated can then be dealt with explicitly or transformed into linear variables.

# Interfaces
The Wolf solver is compiled from C into a library. The library has been tested using C, C#, and MATLAB. Refer to the User Manual for guidance.
