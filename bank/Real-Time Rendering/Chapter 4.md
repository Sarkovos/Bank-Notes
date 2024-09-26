## Transforms (unfinished)

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

## Projections
- For the examples, we are assuming a *right hand coordinate system*, so screen is looking out on the line of negative z.

### Orthographic Projections
- Orthographic projections have parallel lines remaining parallel after projection, and objects maintain the same size regardless of distance to the camera.
- Orthographic projections are typically expressed by $(l, r, b, t, n, f)$, denoting the left, right, bottom, top, near and far planes.
- This matrix scales and translates the *axis-aligned bounding box* (AABB) formed by these planes into an axis-aligned cube centered around the origin.
- Minimum corner of the AABB is $(l, b, n)$ and the maximum corner is $(r, t, f)$. 
- $n > f$, because we are looking down the negative $z$-axis.
- In OpenGL, the axis-aligned cube has a minimum corner of $(-1, -1, -1)$ and a maximum corner of $(1, 1, 1)$
- In DirectX the bounds are $(-1, -1, 0)$ to $(1, 1, 1)$
- This cube is called the *canonical view volume*