![[Pasted image 20240925211451.png]]
- *Projection Matrices* are $4x4$ matrices designed to transform a 3D point in camera space into its projected counterpart on the canvas
- When you multiply a 3D point by a projection matrix, you determine its 2D coordinates on the canvas within NDC (*Normalized Device Coordinates*) space.
- NDC space typically falls within the range $[-1, 1]$ (Direct3D, OpenGl) or $[0, 1]$ (Renderman)
- To correctly multiply a 3D point by a $4x4$ matrix, we treat points as 4D points with *homogenous coordinates*. Basically, a regular 3D vector with the fourth component being 1.


- blah blah blah more stuff on scratch a pixel section "What are projection matrices and where/why are they used"



- ![[Pasted image 20240925213403.png]]
- When a point $P$ is transformed by a perspective matrix, it yields point $P'$
	- The $x'$- and $y'$- coordinates represents $P'$s location on the image plane, both situated in Normalized Device Coordinates space. The perspective projection matrix maps the coordinates of a 3D point to its "2D" screen position within NDC space (spanning $[-1, 1]$) This matrix ensures that points within the camera's view (inside the frustum) map to the range $[-1, 1]$ regardless of the canvas shape - these coordiantes are in NDC, not screen space
	- Beyond that, its necessary to adjust its $z$-coordinate. GPUs remap the $z$-coordinate of $P'$ into a range of $[0, 1]$ or $[-1, 1]$, depending on graphics API used. For points on the near-clipping plane, $z'$ maps to 0 (or -1), and for points on the far-clipping plane, $z'$ maps to 1.
- ![[Pasted image 20240925214042.png]]
- The remappping of $P'$s $x, y, z$ coordinates signifies the projection matrix transforms the viewing frustum's volume into a cube, known as the *canonical view volume*
- This normalizes the viewing frustum, and with a cube, future operations become much easier.



- Starting from a real world camera, we would have the size (width and height) of our film, and the focal length. With these 2 values, we can calculate either the vertical or horizontal field of view.
	- $\tan(\frac{{fov}_x}{2}) = \frac{\frac{film-width}{2}}{focal-length}$
	- $\tan(\frac{{fov}_y}{2}) = \frac{\frac{film-height}{2}}{focal-length}$
- If we plug in values into these equations, we can get our fields of view




- ![[Pasted image 20240925235835.png]]
- This is the OpenGL perspective projection matrix
- ![[Pasted image 20240926002153.png]]
- Using the technique of similar triangles:
	- $\frac{AB}{DE} = \frac{BC}{EF}$
	- We substiture AB with n (near clipping plane), DE with $P_z$ (z coord of P) and EF with $P_y$ (y coord of P)
	- We can rewrite the equation as:
		- ![[Pasted image 20240926002511.png]]
		- $P_z$ is negative because in this case, camera is oriented along the negative z-axis.
		- ![[Pasted image 20240926002647.png]]
		- We can get the value of the x-coordinate of the projected point in the same way
- The purpose of the projection matrix is to remap the values projected onto the image plane to the canonical view volume.
- Once P is projected onto the image plane, $P_s$ (projected P) is considered visible if its x and y coordinate falls with the range $[left, right]$ for x and $[bottom, top]$ for y.
	- $P_{sx}$ can be expressed as: $l \leq P_{sx} \leq r$
		- where $l$ and $r$ represent the left and right coordinates respectively.
		- Our goal is to remap $P_{sx}$ so its final value resides with the range $[-1,1]$ (dimensions of the canonical view volume along x-axis)
		- To start, we subtract $l$ from all terms
	- $0 \leq P_{sx} - l \leq r - l$
		- Normalizing this by dividing on the right we get:
	- $0 \leq \frac{P_{sx} - l}{r - l} \leq 1$
		- Multiplying all terms by 2 gives:
	- $0 \leq 2\frac{P_{sx} - l}{r - l} \leq 2$
		- Subtracting 1 from all terms gives:
	- $-1 \leq 2\frac{P_{sx} - l}{r - l} - 1 \leq 1$
		- This can be further modified to:
	- $-1 \leq 2\frac{P_{sx} - 1}{r - l} - \frac{r - 1}{r - l} \leq 1$
		- Going further:
	- $-1 \leq \frac{2P_{sx} - 2l - r + l}{r - l} \leq 1$
		- Therefore:
	-  $-1 \leq \frac{2P_{sx} - l - r}{r - l} \leq 1$ -> $-1 \leq \frac{2P_{sx}}{r - l}-\frac{r + l}{r - 1} \leq 1$
		- Replacing $P_{sx}$ with what we got before we get:
	- $-1 \leq \frac{2nP_{x}}{-P_z(r - l)}-\frac{r + l}{r - 1} \leq 1$