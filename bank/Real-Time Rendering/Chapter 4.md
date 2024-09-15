## Transforms

- A *linear transform* is one that preserves vector addition and scalar multiplication, following:
	- $f(x) + f(y) = f(x+y)$
	- $kf(x) = f(kx)$
- Example: $f(x) = 5x$
	- To be a linear transform, must meet the 2 conditions above.
	- First condition holds since any 2 vectors multiplied by 5 and then added will be the same as adding the vectors and then multiplying.
	- Scalar multiplication condition also holds.

- Scaling and rotation are linear transforms, and all linear transforms for 3-element vectors can be represented using a 3 X 3 matrix.
-  Translation, however, is not a linear transform, but it is very helpful.
- Combining linear transforms and translations can be done using an *affine transform*, typically stored as a $4 X 4$ matrix. An affine translation is one that performs a linear transform and then a translation.
- To represent four-element vectors, we use *homogeneous notation*
- A direction vector is represented as $V = (v_x, v_y, v_z, 0)^T$, and a point as $V = (v_x, v_y, v_z, 1)^T$ 
- All translation, rotation, scaling, reflection, and searing matrices are affine.
- The main characteristic of an affine matrix is that it preserves the parallelism of lines, but not necessarily lengths and angles. An affine transform may also be any sequence of concatenations of individual affine transforms.
- 