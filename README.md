## Proposed GNFS in python (we're pivoting from c++)

Kate and Sam Shoemaker

will probably need a library similar to BigInteger in java like gmpy2

## All the research/info
From google: this algorithm builds on the concept of finding a congruence of squares $(x^2\equiv y^2\pmod{n})$ which reveals factors of n via $\gcd(x-y,n)$, as long as $x\not\equiv\pm y\pmod{n}$, otherwise the gcd is just $1$ or $n$, which tells you nothing

- for those who didn't have to suffer through discrete math, congruence means that for numbers $A,B\in\mathbb{Z},A\pmod n=B\pmod n$

- instead of working in only regular integers (look up quadratic sieve, it does this), GNFS moves its calculations into algebraic number fields to find smaller "smooth" numbers (numbers with small prime factors)

### Steps (still hazy like Patrick Swayze)

### 1: Polynomial Selection

Choose 2 irreducible polynomials $f(x),g(x)$ that share a common integer root $m$ modulo $n$

- in practice $g$ is just linear: $g(x)=x-m$, so $m$ is trivially its root

- the simplest way to get $f$ is the base-m method:

    - pick a degree $d$ (3 is fine for small $n$, we will probably want to use 5 or 6)

    - pick $m$ around $n^{1/d}$

    - write $n$ in base $m$: $n=c_dm^d+c_{d-1}m^{d-1}+\dots+c_1m+c_0$

    - use those digits as coefficients: $f(x)=c_dx^d+c_{d-1}x^{d-1}+\dots+c_1x+c_0$, so $f(m)=n\equiv0\pmod{n}$

- $f$ has to be irreducible. if it factors as $f=h\cdot k$ then $n=h(m)\cdot k(m)$ and you've (usually) factored $n$

these polynomials link regular modular arithmetic with algebraic number fields

### 2: Sieving

Search for pairs of coprime integers $(a,b)$ across a large grid (using line or lattice sieving)

- Coprime numbers are 2 numbers who have a greatest common divisor is 1. these numbers do not need to be prime individually, 8 and 15 are coprime because the factors of 8 are 1,2,4,8 and the factors of 15 are 1,3,5,15

- Hey look, we accidentally found the wrong algorithm here, Gauss, nv, and k-tuple solve the shortest vector problem, in the context of GNFS, we want to use Pollard's special-q lattice sieve

#### Pollard special-q Lattice Sieving Steps

- Fix a medium sized prime $q$

- restrict to the $(a,b)$ pairs whose algebraic norm is divisible by q. those pairs form a 2d sublattice

- reduce that sublattice basis with Lagrange (Gauss) basis reduction

- build the sieve array over the coordinates of the special-q sublattice instead of over raw $(a,b)$ values

- we are specifically looking for a pair $(a,b)$ where both norms $a-b\cdot m$ and $b^d-f(\frac{a}{b})$ are smooth over their factor bases

- a factor base is a fixed list of small primes that we will allow. a number counts as smooth if it factors completely over that list. GNFS uses 3 factor bases

    - Rational factor base: all primes $p\le B$ for some b bound that we will choose

    - Algebraic factor base: pairs $(p,r)$ with $p\le B$ and $f(r)\equiv 0 \mod{p}$ a prime can appear several times with different roots r

    - quadratic factor base: a few pairs $(q,s)$ in the same form but with q larger than B. these function in the linear algebra we are going to be doing to make sure that the algebraic product is really a square

### 3: Filtering

Remove redundant or duplicate relations and clean the sparse matrix of prime factors

### 4: Speak of the Devil, Linear Algebra

Build a massive, sparse matrix from the collected relations and solve it over $\mathbb{F}_2$ using an algorithm like Block Lanczos to find a subset of pairs whose product forms a perfect square in both rational and algebraic roots

- $\mathbb{F}_2$ denotes the finite field with 2 elements, this is also called a galois field of 2 elements, these elements are 0 and 1

- Arithmetic in GF(2) (same as $\mathbb{F}_2$) is performed modulo 2. addition in GF(2) is identical to the logical XOR, and multiplication is identical to the logical AND

- Block Lanczos is an iterative algorithm used to find nullspaces of very large sparse matrices by working with blocks of multiple vectors simultaneously instead of a single vector

- Block Lanczos is very similar to the Lanczos algorithm but not identical. the block lanczos finds primarily nullspaces

- What is a nullspace?: the nullspace (also called kernel) of a matrix A is the set of all input vectors x that result in the zero vector when multiplied by A. you see this primarily denoted as Ax=0

#### Block Lanczos steps

1: Initialization

- given a symmetric/Hermitian matrix A and an initial block of vectors $V_1$ and perform a QR or similar orthogonalization (so that $V^T_1V_1=I_p$). set the initial recurrence coefficient $\beta_1$ (often derived from the QR factorization of the starting block)

2: Iteration Loop (k=1,2,...)

2.1: Matrix-Block Multiplication & 3 term recurrence

- Compute the action of the matrix on the current block $W=AV_k-V_{k-1}\beta^T_k$ where $\beta^T_k$ acts as the block coupling term from step 1

2.2: Orthogonalization (projection)

- Compute the block projection coefficient $\alpha_k=V^T_kW$

- Orthogonalize W against the current block: $W=W-V_k\alpha_k$

2.3: Block QR Decomposition for the next block:

- Factor the residual block W using QR decomposition (or singular value decomposition to handle rank-deficiencies/breakdowns)

- $W=V_{k+1}\beta_{k+1}$ where $V_{k+1}$ has orthonormal columns and $\beta_{k+1}$ is an upper triangular block

2.4 Advance index:

- increment $k\leftarrow k+1$ and repeat until convergence or the max number of iterations is reached

3: Rayleigh-Ritz Reduction/Spectrum Extraction:

- Assemble the generated block tridiagonal matrix $T_k$ constructed from the block coefficients ($\alpha_i,\beta_i$)

- Solve the smaller eigenvalue or nullspace problem on the block tridiagonal matrix $T_k$ to approximate the desired eigenvalues and ritz vectors of the original large matrix A

### 5: Square Root Extraction

- I'm being told that these are not in fact integer square roots, but algebraic square roots which are much more difficult to solve (bummer). the algebraic square root is a square root in the number field

Extract the square roots in both domains and compute the greatest common divisor (gcd) with $n$ to reveal the non-trivial factors of $n$

### Important Note

from everybody online and from how long this has ended up being, gnfs has a super high setup cost, and while this algorithm is excellent for big ass numbers ($n\ge2^{330}$), running a quadratic sieve before that point would be faster and then below $2^{64}$ is where trial division would be more efficient


