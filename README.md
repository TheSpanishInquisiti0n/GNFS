From google: this algorithm builds on the concept of finding a congruence of squares $(x^2\equiv y^2\pmod{n})$ which reveals factors of n via $gcd(x-y,n)$

- for those who didn't have to suffer through discreet math, congruence means that for numbers $A,B\in\mathbb{Z},A\pmod n=B\pmod n$

- instead of working in only regular integers (look up quadratic seive, it does this), GNFS moves its calculations into algebraic number fields to find smaller "smooth" numbers (numbers with small prime factors)

### Steps (still hazy like patrick swayzee)

### 1: Polynomial Selection

Choose 2 irreducable polynomials $f(x),g(x)$ that share a common integer root $m$ modulo $n$

these polynomials link regular modular arithmetic with algebraic number fields

### 2: Seiving

Search for pairs of coprime integers $(a,b)$ across a large grid (using line or lattice seiving)

- Coprime numbers are 2 numbers who have a greatest common divisor is 1. these numbers do not need to be prime individually, 8 and 15 are coprime because the factors of 8 are 1,2,4,8 and the factors of 15 are 1,3,5,15

- Lattice seiving is an algorithmic technique used to solve the shortest vector problem in high-dimensional lattices there are many different variations of this, like Gauss Seive, NV Seive, or k-tuple seiving. we will probably have to decide which to use when we get here

#### Lattice Seiving Steps

1: Initialization and sampling

- Start with a large list of random, relatively long lattice vectors

- define a target norm or a bounding ball or radius R which the vectors reside in

2: The Iterative Seiving Loop

- Pair or k-tuple comparison: examine pairs (k=2) or higher-order tuples (k-tuple) of vectors in the list to see if their vector sum or difference yeilds a shorter vector. Specifically, vectors that form a small angle (less than $\pi$/3) or large angle can be combined to reduce length

- Cluster/center mapping: to avoid checking every single pair (which is slow) algorithms might use a "covering" set or center points to map vectors to local neighborhoods (thats a math term, i hate it too, dont worry)

- Reduction and replacement: 

    - if a newly combined vector is shorter (scaled down by a generic factor of $\gamma<1$), it is kept for the next generation list

    - Longer or redundant vectors are discarded to keep memory and list sizes bounded

3: Output

- After repeating the seiving steps enough times until no further significant reductions happen, the algoritm takes pairwise differences or extracts the remaining vecotrs to output the shortest non-zero lattice vector (approximating the first successive minimum $\lambda_1$)

### 3: Filtering

Remove redundant or duplicate relations and clean the sparse matrix of prime factors (there will be linear algebra in here, sorry)

### 4: Speak of the Devil, Linear Algebra

Build a massive, sparse matrix from the collected relations and solve it over $mathbb{F}_2$ using an algorithm like Block Lanczos to find a subset of pairs whose product forms a perfect square in both rational and algebraic roots

- $mathbb{F}_2$ denotes the finite field with 2 elements, this is also called a galois field of 2 elements, these elements are 0 and 1

- Arithmetic in GF(2) (same as $mathbb{F}_2$) is performed modulo 2. addition in GF(2) is identical to the logical XOR, and multiplication is identical to the logical AND

- Block Lanczos is an iterative algorithm used to find nullspaces of very large sparse matrices by working with blocks of multiple vectors simultaneously instead of a single vector

- Block Lanczos is very similar to the Lanczos algorithm but not identical. the block lanczos finds primarily nullspaces

- What is a nullspace?: the nullspace (also called kernal) of a matrix A is the set of all input vectors x that result in the zero vector when multiplied by A. you see this primarily denoted as Ax=0

#### Block Lanczos steps

1: Intitialization

- given a symmetric/Hermitian matrix A and an initial block of vectors $V_1$ and perform a QR or similar orthagonalization (so that $V^T_1V_1=I_p$). set the initial recurrence coefficient $\beta_1$ (often derived from the QR factorization of the starting block)

2: Iteration Loop (k=1,2,...)

2.1: Matrix-Block Multiplication & 3 term recurrence

- Compute the action of the matrix on the current block $W=AV_k-V_{k-1}\beta^T_k$ where $\beta^T_k$ acts as the block coupling term from step 1

2.2: Othagonalization (projection)

- Compute the block projection coefficient $\alpha_k=V^T_kW$

- Orthagonalize W against the current block: $W=W-V_k\alpha_k$

2.3: Block QR Decomposition for the next block:

- Factor the residual block W using QR decomposition (or singular value decomposition to handle rank-deficiencies/breakdowns)

- $W=V_{k+1}\beta_{k+1}$ where $V_{k+1}$ has orthonormal columns and $\beta_{k+1}$ is an upper triangular block

2.4 Advance index:

- increment $k\leftarrow k+1$ and repeat until convergence or the max number of iterations is reached

3: Rayleigh-Ritz Reduction/Spectrum Extraction:

- Assemble the generated block tridiagonal matrix $T_k$ constructed from the block coefficients ($\alpha_i,\beta_i$)

- Solve the smaller eigenvalue or nullspace problem on the block tridiagonal matrix $T_k$ to approximate the desired eigenvalues and ritz vectors of the original large matrix A

### 5: Square Root Extraction

Extract the square roots in both domains and compute the greatest common divisor (gcd) with $n$ to reveal the non-trivial factors of $n$

### Important Note

from everybody online and from how long this has ended up being, gnfs has a super high setup cost, and while this algoritm is excellent for big ass numbers ($n\ge2^{64}$), running any other algoritm for a number smaller than that is going to be faster (even the most basic $O(n^2)$ solution)


