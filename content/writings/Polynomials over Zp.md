+++
title = "Polynomials over Zp"
date = 2024-01-12
tags = ["math"]
draft = false
+++

## Abstract {#abstract}

There are known processes to define some blackbox polynomials, such
as Newtons' or Lagrange's Interpolation methods, but these are require
a large set of points, and are only meant to approximate the
polynomial. This article proposes an algorithm to do a similar task with
only one point, through a test oracle, within the degree of the
poynomial iterations.


## Introduction {#introduction}

A polynomial is an object that resides inside an abelian group (whose
direct operation is addition), more precisely a commutative
ring. In general, univariate polynomial expressions can be written as:

\begin{equation}
\label{eq:1}
P(x) = a\_n x^n + a\_{n-1} x^{n-1} + \cdots + a\_0, \quad a\_n \in \mathbb{Z}, \quad n \in \mathbb{N}
\end{equation}

or:

\begin{equation}
P(x) = \sum\_{k=0}^{n} a\_k x^k, \quad a\_k \in \mathbb{Z}, \quad n \in \mathbb{N}
\end{equation}

The method presented, works when one has the possibility to guess
parameters such as:

-   The degree;
-   The maximum coefficient of the polynomial.

Note that if one may ask the blackbox for a large prime number, \\(\log\_{q}
P(q) + 1 \approx \deg P\\) is a good guess for the degree of the polynomial.


## Method {#method}

This algorithm is designed to find all the coefficients of a given
polynomial over a ring \\(Z\_p[x]\\), where the coefficients are positive integers,
(it is also possible to execute a variation when the coeffiecients can
be negative integers). The algorithm needs as input exacly one point,
ie. \\((x,P(x))\\). The output is a list of the coefficients of the given
polynomial, with the size of the estimation of the degree.

Sets of the algorithm:

1.  Let \\(deg\\) be the guessed degree of the polynomial, \\((p,q)\\) the pair
    \\((x,P(x))\\), and L an empty list;
2.  Generate a prime bigger than \\(max\\);
3.  From \\(i=0\\) to \\(deg\\);
    1.  \\(tmp = q \mod p\\);
    2.  Append to \\(L\\) the value \\(tmp\\);
    3.  \\(tmp = tmp // p\\);
    4.  \\(i = i + 1\\);
4.  Return \\(P(x) = L[0] + L[1]x + \cdots + L[deg]x^{deg}\\);
5.  Halt.


### Complexity {#complexity}

For a polynomial where the coefficients are positive or null integers,
it takes \\(\mathcal{O}(deg)\\) where \\(deg \approx \deg P(x)\\) iterations. However,
if there might be negative coefficients, then the algorithm needs to
assume that the value computed in that <span class="underline">iteration</span> might be wrong,
ie. save both the value of \\(temp\\), compute again \\(tmp = p-tmp\\) and
save \\(tmp\\) in another list that has all the coefficients until now
computed. This nondeterministic approach would make this algorithm
belong to **NEXPTIME** and **NEXPSPACE**. Still, note that in practice one
might assume somethings about blackbox polynomials, and this
complexity be reduced to a smaller class (both in time and space). If
there is the possibility to have two points from the polynomial, its
possible to verify if the coefficient is indeed positive or negative,
removing the need for saving all the possibilities and checking at the
end which branch returned, thus having the same complexity as the
method proposed above, downgrading the time and space complexity.


### Example {#example}

Given a polynomial \\(P(x)\\), for example \\(P(x) = 7 x^3 + 4 x^2 + 2 x +
8\\), ask for a random prime point, for example \\(x = 11\\), we have:

\begin{equation}
$P(11) = 7 (11)^3 + 4 (11)^{2} + 2 (11) + 8 = 9381$
\end{equation}

We want to extract the coefficients with this point, \\(P(11) \equiv 7 (11)^3 + 4 (11)^{2} + 2 (11) + 8 \pmod{11}\\). Since \\(P(11)=9831\\) is a the sum of multiples of \\(11\\), then:
\\(P(11) \equiv 8 \pmod{11}\\). We found \\(a\_{0}=8\\), let's find \\(a\_{1}\\):

\begin{align\*}
P(11) - a\_{0} & = 9831 - 8 = 9823 \\\\
P(11) - 8 & = 7 (11)^3 + 4 (11)^{2} + 2 (11) + 8 - 8 \\\\
P(11) - 8 & = 11( 7 (11)^2 + 4 (11) + 2) \\\\
\end{align\*}

Note that \\(P(11) - 8 \equiv 0 \pmod{11}\\)

\begin{align\*}
\frac{P(11) - 8}{11} &\equiv \frac{11(7(11)^2 + 4(11) + 2)}{11} \pmod{11} \\\\
                     &\equiv 7(11)^2 + 4(11) + 2 \pmod{11} \\\\
                     &\equiv 2 \pmod{11} \\\\
\end{align\*}

Found \\(a\_{1}=2\\), let's find \\(a\_{2}\\):

\begin{align\*}
\frac{P(11) - 8}{11} - 2 &\equiv 7(11)^2 + 4(11) + 2 - 2 \pmod{11} \\\\
\frac{P(11) - 8}{11} - 2 &\equiv 7(11)^2 + 4(11) \pmod{11} \\\\
                         &\equiv \frac{7(11)^2 + 4(11)}{11} \pmod{11} \\\\
                         &\equiv \frac{11(7(11) + 4)}{11} \pmod{11} \\\\
                         &\equiv 7(11) + 4 \pmod{11} \\\\
                         &\equiv 4 \pmod{11} \\\\
\end{align\*}

Found \\(a\_2=4\\), we just need one more iteration:

\begin{align\*}
\frac{\frac{P(11) - 8}{11} - 2}{11} - 4 &\equiv 7(11) + 4 - 4 \pmod{11} \\\\
\frac{\frac{P(11) - 8}{11} - 2}{11} - 4 &\equiv 7(11) \pmod{11} \\\\
\frac{\frac{\frac{P(11) - 8}{11} - 2}{11} - 4}{11} &\equiv \frac{7(11)}{11} \pmod{11} \\\\
                                                   &\equiv 7 \pmod{11} \\\\
\end{align\*}

At last, found \\(a\_{3}=7\\). We defined the whole polynomyal with only
one point. Nevertheless, why did we ask for the \\(P(11)\\)? Since we knew
in advance that \\(a\_{3},a\_{2}, a\_{1} \text{ and } a\_{0} \in
\mathbb{Z}[8]\\), a _good guess_ for \\(x\\), would be the first following
prime number after the maximum element of the set of coefficients (in
this case it is \\(8\\)), to get the best optimization regarding space and
time.


## Conclusion {#conclusion}

The method presented offers a potential approach for attacking
polynomials, assuming that certain parameters, such as the degree and
maximum coefficient, can be guessed accurately. However, the accuracy of the initial
guesses remain crucial for the success of the attack.
