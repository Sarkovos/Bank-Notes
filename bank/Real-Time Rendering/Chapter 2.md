## Definitions:
- [[#^bb0a15|frustum]]

Real-time computer graphics pipeline:
Application -> Geometry Processing -> Rasterization -> Pixel Processing

Speed is expressed by *frames per second* (FPS)
Also can be represented using *Hertz* (Hz), which is notation for 1/seconds

## Application Stage
- Application stage is driven by the application
- Run on general-purpose CPUs
- Developer has full control
- A computer shader can also do some application stage work
- At the end, geometry to be rendered is fed to geometry processing stage
- One limitation is that this step is not divided into substages
- To increase performance, this stage is executed in parallel on several processor cores. This is called a *superscalar* construction.
## Geometry Processing
- Deals with transforms, projections, and other types of geometry handling
- Computes what is to be drawn, how it should be drawn, and where it should be drawn
- This stage is performed on the GPU
- Handles most per-triangle and per-vertex operations.
#### Geometry Processing Substages:
##### Vertex Shading
- 2 Main tasks:
	- Compute position for a vertex
	- Evaluate whatever the programmer may like to have as vertex output data, such as a normal and texture coordinates
- We start by describing how the vertex position is computed, a set of coordinates that is always required.
- Originally a model resides in *model space*, which means it has not been transformed at all.
- Each model can be associated with a *model transform* to be positioned and oriented. This allows several copies (*instances*) of the same model to have different transforms, without requiring replication of the basic geometry.
- Vertices and the normals are what are transformed by the model transform. The coordinates of an object are called *model coordinates*.
- After model transform has been applied, the model is said to be located in *world coordinates* or *world space*.
- To facilitate projecting and clipping, the camera and all models are transformed with the *view transform*. 
- The purpose of the view transform is to place the camera at the origin and aim it, to make it look in the direction of the $-z$ axis, with the $y$ axis pointing upwards and the $x$ axis pointing to the right.
- This space is called the *camera space*, or commonly *view space* or *eye space* ![[Pasted image 20240909220902.png]]
- Second type of output from vertex shader: Shader information
- Operation of determining the effect of a light on a material is known as *shading*, which involves computing a *shading equation* at various points on the object. Some computations are done during geometry processing, and others may be performed during per-pixel processing.
- Variety of material data can be stored at each vertex such as location, normal, color, and any other numerical information.
- Vertex shading results are then sent to the rasterization and pixel processing stages to be interpolated and used to compute the shading of a surface.
##### Projection
- As part of vertex shading, rendering systems perform *projection* and then clipping, which transforms the view volume into a unit cube with its extreme points at $(-1, -1, -1)$ and $(1, 1, 1)$.
- The unit cube is called the *canonical view volume*
- Projection is done first, and on the GPU is done by the vertex shader
- Two Commonly used projection methods ^347e12
	- *Orthographic* or *parallel* projection
		- View volume is normally a rectangular box, and the orthographic projection transforms this view volume into the unit cube.
		- Main characteristic of orthographic projection is that parallel lines remain parallel after the transform.
	- *Perspective* projection
		- Farther the object lies from the camera, the smaller is appears after projection. 
		- The view volume is a *frustum*, a truncated pyramid with rectangular base
		- frustum is transformed into the unit cube as well
	![[Pasted image 20240909223334.png]] ^bb0a15

- Other types of projection:
	- *Oblique* Projections
	- *axonometric* projections
- After projections, models are said to be in *clip coordinates*. Vertex shader must always output coordinates of this type in order for the next functional stage, clipping, to work correctly.
- They are called projections because after display the $z$-coordinate is not stored in the image generated but is stored in a $z$-buffer. In this way, models are projected from three to two dimensions.
##### Optional Vertex Processing (done in order)
###### Tesselation
- Tesselation steps
	- Hull shader
	- tessellator
	- domain shader
- Geometry is generated in patches depending on how close the patches are to the camera. This generates lot of geometry close to the camera, and minimal geometry away from the camera.
###### Geometry Shader
- Like tessellation, can take in primitives of various sorts and produce new vertices.
- Very popular for particle generation
###### Stream output
- Lets us use the GPU as a geometry engine.
- Instead of sending processed vertices down the rest of the pipeline, we can optionally output these to an array for further processing.
- This data can be used by the CPU or the GPU in a later pass.
##### Clipping
- Only primitives wholly or partially inside the view volume need to be passed on to the rasterization stage and beyond.
- A primitive the lies fully inside the view volume will be passed on to the next stage as is. Primitives entirely outside the view volume are not passed on since they are not rendered. Only primitives partially inside the view volume require clipping.
- The use of a projection matrix means that the transformed primitives are clipped against the unit cube. 
- Clipping step uses the 4-value homogeneous coordinates produced by projection to perform clipping. Values do not normally interpolate linearly across a triangle in perspective space. Fourth coordinate is needed so that data are properly interpolated and clipped when a perspective projection is used.
- *Perspective division* is performed which places the resulting triangles' positions into three-dimensional *normalized device coordinates*.
- Last step in the geometry stage is to convert from this space to window coordinates.
![[Pasted image 20240910001514.png]]

##### Screen Mapping
- Only clipped primitives inside the view volume are passed on to the screen mapping stage, and coordinates are still three-dimensional when entering this stage.
- $x$ and $y$ coordinates of each primitive are transformed to form *screen coordinates*, and with the $z$ coordinates are called *window coordinates*
- Assume that the scene should be rendered into a window with the minimum corner at $(x_1, y_1)$, and the maximum corner at $(x_2, y_2)$, where $x_1 < x_2$ and $y_1 < y_2$
- Then the screen mapping is a translation followed by a scaling operation.
- The new $x$-and $y$- coordinates are said to be screen coordinates.
- The $z$-coordinate ($[-1, +1]$ for OpenGL and $[0,1]$ for DirectX) is also mapped to $[z_1, z_2]$, with $z_1 = 0$ and $z_2 = 1$ as the default values. These can be changed depending on API.
- Window coordinates, along with remapped $z$-value are passed on to the rasterizer stage.
![[Pasted image 20240910004512.png]]
## Rasterization
- Takes as input 3 vertices, forming a triangle
- Finds all pixels considered inside that triangle, and forwards them to next step
- Performed on GPU

- Given a horizontal array of pixels and using Cartesian coordinates, the left edge of the leftmost pixel is 0.0 in floating point coordinates. The center of this pixel is at 0.5. A range of pixels $[0,9]$ cover a span from $[0.0,10.0)$. 
- The conversions are:
	- $d = floor(c)$
	- $c = d + 0.5$
- where $d$ is the discrete (integer) index of the pixel and $c$ is the continuous (floating point) value within the pixel.
- While all APIs have pixel location values that increase going from left to right, the location of zero for the top and bottom edges is inconsistent. OpenGL favors the Cartesian system throughout, treating lower left corner as the lowest-values element. DirectX sometimes defines the upper left corner as this element, depending on context.

- Goal of rasterization is to find all pixels, or *picture elements*, that are inside the primitive being rendered.
- Rasterization is split up into two functional substages:
	- Triangle setup (also called primitive assembly)
		- Differentials, edge equations, and other data for the triangle are computed.
	- Triangle traversal
		- Where each pixel that has its center (or a sample) covered by the triangle is checked and a *fragment* generated for the part of the pixel that overlaps the triangle
		- Finding which samples or pixels are inside a triangle is often called *triangle traversal*
		- Each triangle fragment's properties are generated using data interpolated among the three triangle vertices
		- All pixels or samples that are inside a primitive are then sent to the pixel processing stage. 
- Rasterization is the conversion from two-dimensional vertices in screen space, each with a $z$-value (depth value) and various shading info associated with each vertex, into pixels on the screen.
- Whether the triangle is considered to overlap the pixel depends on how you have set up the GPUs pipeline. You may use point sampling to determine "insideness". Simplest case uses a single point sample in the center of each pixel, and so if that center point is inside the triangle, then the corresponding pixel is considered inside the triangle as well.
- Use may also use more than one sample per pixel using supersampling.
	
## Pixel Processing
- Executes a program per pixel to determine its color
- May perform depth testing
- Performed on GPU

- All pixels that are considered inside a triangle or other primitive have been found as a consequence of the combination of all the previous stages. 
- Pixel processing stage is divided into two steps:
	- Pixel shading
		- Any per-pixel shading computations are performed here, using interpolated shading data as input.
		- End result is one or more colors to be passed on to the next stage
		- This stage is executed by programmable GPU cores. Programmer supplies a program for the pixel shader (or *fragment shader*) which can contain any desire computations. 
		- Lots of work can be done here, including *texturing*
	- Merging
		- Information for each pixel is stored in the *color buffer*, a rectangular array of colors (RGB values)
		- It is the responsibility of the merging stage to combine the fragment color produced by the pixel shading stage with the color currently stored in the buffer
		- This stage is also called ROP, or "raster operations pipeline", or "render output unit", depending on who's asked.
		- Unlike shading stage, the GPU subunit that performs this stage is typically not fully programmable
		- This stage is also responsible for resolving visibility. When the whole scene has been rendered, the color buffer should contain the colors of the primitives in the scene that are visible from the POV of the camera. For most graphics hardware, this is done with the $z$-buffer (also called depth buffer)
		- A $z$-buffer is the same size and shape as the color buffer, and for each pixel it stores the $z$-value to the currently closest primitive. 
		- This means that when a primitive is being rendered to a certain pixel, the $z$-value on that primitive at that pixel is being computed and compared to the contents of the $z$-buffer at the same pixel. If the new $z$-value is smaller than the $z$-value in the $z$-buffer, then the primitive that is being rendered is closer to the camera than the primitive that was previously closest to the camera at that pixel
		- Therefore, the $z$-value and the color of that pixel are updated with the $z$-value and color from the primitive that is being drawn.
		- If the computed $z$-value is greater than the $z$-value in the $z$-buffer, then the color buffer and the $z$-buffer are left untouched.
		- This algorithm has $O(n)$ convergence, where $n$ is the number of primitives being rendered
		- This algorithm also allows most primitives to be rendered in any order
		- However, the $z$-buffer stores only a single depth at each point on the screen, so it cannot be used for partially transparent primitives
		- Transparency is one of the major weaknesses of the basic $z$-buffer
	
		- The *stencil buffer* is an offscreen buffer used to record the locations of the rendered primitive. It typically contains 8 bits per pixel
		- Primitives can be rendered into the stencil buffer using various functions, and the buffer's contents can then be used to control rendering into the color buffer and $z$-buffer
		- The *framebuffer* generally consists of all the buffers on a system

## The End
- When the primitives have reached and passed the rasterizer stage, those that are visible from the POV of the camera are displayed on screen.
- The sceen displays contents of the color buffer.
- To avoid allowed the human viewer to see the primitives as they are being rasterized and sent to the screen, *double buffering* is used. This means that the rendering of a scene takes place off screen in a *back buffer*.
- Once the scene has been rendered in the back buffer, the contents of the back buffer are swapped with the contents of the *front buffer* that was previously displayed on the screen.
- The swapping often occurs during *vertical retrace*
- 




