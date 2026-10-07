From google: this algorithm builds on the concept of finding a congruence of squares $(x^2\equiv y^2\pmod{n})$ which reveals factors of n via $gcd(x-y,n)$

- for those who didn't have to suffer through discreet math, congruence means that for numbers $A,B\in\mathbb{Z},A\pmod n=B\pmod n$

- instead of working in only regular integers (look up quadratic seive, it does this), GNFS moves its calculations into algebraic number fields to find smaller "smooth" numbers (numbers with small prime factors)
