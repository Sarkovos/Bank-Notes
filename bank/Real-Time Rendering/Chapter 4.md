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
![[Pasted image 20240925201500.png]]
- Orthographic projections have parallel lines remaining parallel after projection, and objects maintain the same size regardless of distance to the camera.
- Orthographic projections are typically expressed by $(l, r, b, t, n, f)$, denoting the left, right, bottom, top, near and far planes.
- This matrix scales and translates the *axis-aligned bounding box* (AABB) formed by these planes into an axis-aligned cube centered around the origin.
- Minimum corner of the AABB is $(l, b, n)$ and the maximum corner is $(r, t, f)$. 
- $n > f$, because we are looking down the negative $z$-axis.
- In OpenGL, the axis-aligned cube has a minimum corner of $(-1, -1, -1)$ and a maximum corner of $(1, 1, 1)$
- In DirectX the bounds are $(-1, -1, 0)$ to $(1, 1, 1)$
- This cube is called the *canonical view volume* and the coordinates in this are called *normalized device coordinates*
- We transform into the canonical view volume to make clipping more efficient
- ![[Pasted image 20240925201725.png]]
- Geometry is clipped against the cube, and geometry not clipped is rendered by mapping to the screen.
- ![[Pasted image 20240925201842.png]]
- This is an orthographic projection matrix $P_o$. From the image above, we can think about our projection being a translation and then a scaling of our axis-aligned bounding box to the position and scale of the canonical view volume. First translation, the scaling.
- A left-hand coordinate system is most often used after projection--i.ei, for the viewport, so $z$-axis goes into the viewport.
- Because the far value is less than the near value for how we defined the AABB, the orthographic transform will include a mirroring transform.
	- If the AABB is the same size as the canonical view volume, for example, then the AABB's coordinates are $(-1, -1, 1)$ for $(l, b, n)$ and $(1, 1, -1)$ for $(r ,t ,f)$
	- ![[Pasted image 20240925202549.png]]
	- This is our mirroring matrix. It converts from right handed viewing coordinate system to left handed normalized device coordinates.
	- DirectX maps the $z$-depths to the range $[0, 1]$ instead of OpenGL's $[-1, 1]$. This can be accomplished with a scaling and translation matrix applied after the orthographic matrix.
	- ![[Pasted image 20240925202849.png]]

### Perspective Projection
=![[Pasted image 20240925210341.png]]
- Our perspective projection matrix will project onto a plane $z = -d, d > 0$. This derivation will be simplified for the sake of understanding.
- Assume the camera (viewport) is located at the origin, and that we want to project a point $P$ onto the plane $z = -d, d > 0$, yielding a new point $Q = (q_x, q_y, -d)$.
- From the similar triangles shown in the above figure, the following derivation for the $x$-component of $Q$ is obtained:
	- $\frac{q_x}{p_x} = \frac{-d}{p_z} \longleftrightarrow q_x = -d\frac{p_x}{p_z}$
- 