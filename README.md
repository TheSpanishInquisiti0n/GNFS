From google: this algorithm builds on the concept of finding a congruence of squares $(x^2\equiv y^2\pmod{n})$ which reveals factors of n via $gcd(x-y,n)$

- for those who didn't have to suffer through discreet math, congruence means that for numbers $A,B\in\mathbb{Z},A\pmod n=B\pmod n$

- instead of working in only regular integers (look up quadratic seive, it does this), GNFS moves its calculations into algebraic number fields to find smaller "smooth" numbers (numbers with small prime factors)

### Steps (still hazy like patrick swayzee)

#### 1: Polynomial Selection

Choose 2 irreducable polynomials $f(x),g(x)$ that share a common integer root $m$ modulo $n$

these polynomials link regular modular arithmetic with algebraic number fields

#### 2: Seiving

Search for pairs of coprime integers $(a,b)$ across a large grid (using line or lattice seiving)

- Coprime numbers are 2 numbers who have a greatest common divisor is 1. these numbers do not need to be prime individually, 8 and 15 are coprime because the factors of 8 are 1,2,4,8 and the factors of 15 are 1,3,5,15

- heres probably a good place to figure out what the fuck lattice seiving is

#### 3: Filtering

Remove redundant or duplicate relations and clean the sparse matrix of prime factors (there will be linear algebra in here, sorry)

### 4: Speak of the Devil, Linear Algebra

Build a massive, sparse matrix from the collected relations and solve it over $mathbb{F}_2$ using an algorithm like Block Lanczos to find a subset of pairs whose product forms a perfect square in both rational and algebraic roots

- prolly a good spot to look up Block lanczos

### 5: Square Root Extraction

Extract the square roots in both domains and compute the greatest common divisor (gcd) with $n$ to reveal the non-trivial factors of $n$

### Important Note

from everybody online, gnfs has a super high setup cost, and while this algoritm is excellent for big ass numbers ($n\ge2^64$), running any other algoritm for a number smaller than that is going to be faster (even the most basic $O(n^2)$ solution)


