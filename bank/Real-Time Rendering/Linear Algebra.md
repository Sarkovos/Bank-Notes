- The $n$-dimensional real Euclidean space is denoted $\mathbb{R}^n$. A vector $V$ in this space is an $n$-tuple, that is, an ordered list of real numbers:
- $V \in \mathbb{R}^n \Longleftrightarrow V = \left( \begin{array}{c} v_0 \\ v_1 \\ \vdots \\ v_n-1 \end{array} \right)$ with $v_i \in \mathbb{R}^n$, $i = 0$, $\ldots$ ,$n -1$

### Addition:

$\mathbf{u} + \mathbf{v} = \left( \begin{array}{c} u_0 \\ u_1 \\ \vdots \\ u_{n-1} \end{array} \right) + \left( \begin{array}{c} v_0 \\ v_1 \\ \vdots \\ v_{n-1} \end{array} \right) = \left( \begin{array}{c} u_0 + v_0 \\ u_1 + v_1 \\ \vdots \\ u_{n-1} + v_{n-1} \end{array} \right) \in \mathbb{R}^n$

### Multiplication by a scalar:

$a\mathbf{u} = \left( \begin{array}{c} au_0 \\ au_1 \\ \vdots \\ au_{n - 1} \end{array} \right) \in \mathbb{R}^n$

- $\mathbb{R}^n$  means that addition and multiplication by a scalar yield vectors of the same space

- A series of rules holds for Euclidean space:
	- Associativity: $(\mathbf{u} + \mathbf{v}) + \mathbf{w} = \mathbf{u} + (\mathbf{v} + \mathbf{w})$
	- Commutativity: $\mathbf{u} + \mathbf{v} = \mathbf{v} + \mathbf{u}$

- Zero vector, which is a vector of n zeroes such that:  $\mathbf{0} + \mathbf{v} = \mathbf{v}$

### Dot Product
- $\mathbf{u} \cdot \mathbf{v} = ||\mathbf{u}||$ $||\mathbf{v}||\cos\phi$
- $\phi$: Smallest angle between $\mathbf{u}$ and $\mathbf{v}$
- If the dot product of U and V is 0, U and V are orthogonal (perpendicular)

- *Basis*: $\hat{i}$ and $\hat{j}$ are the basis vectors of the xy coordinate system. $\hat{i}$ being $[1,0]$ and $\hat{j}$ being $[0,1]$ The full definition is a set of linearly independent vectors that span the full space.
- *Linear Combination*: With vectors $\mathbf{v}$ and scalars $a$, then the *linear combination* of those vectors with those scalars as coefficients is:
	- $a_1\mathbf{v}_1 + a_2\mathbf{v}_2 + a_3\mathbf{v}_3 + \ldots + a_n\mathbf{v}_n$
- *span*: The set of all possible vectors that you can reach with a linear combination is the *span* of those two vectors
- *Linearly dependent*: One of the vectors can be expressed as a linear combination of the others. Put another way, it does not contribute to the span
- *Linearly independent*: A vector that contributes to the span
- *Linear transformation*: Visually, all lines stay straight, and the origin does not move


### Matrix Multiplication as composition
- Each 2x2 matrix can be seen as a linear transformation in 2D space
- If you apply multiple transforms, you will get a composition matrix which stores all of the information of all the transforms, as one action
- Example:
	- $\left[ \begin{array}{cc} 1 && 1 \\ 0 && 1 \end{array} \right]$$\left[ \begin{array}{cc} 0 && -1 \\ 1 && 0 \end{array} \right] = \left[ \begin{array}{cc} 1 && -1 \\ 1 && 0 \end{array} \right]$
	- First matrix is a shear, second matrix is a rotation, and the last matrix is the composition. We apply the transforms from right to left. In this case it would be rotation, and then a shear



















