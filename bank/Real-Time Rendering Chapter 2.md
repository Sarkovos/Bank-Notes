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
- Each model can be associated with a *model transform* to be positioned and oriented. This allows several copies (*instances*) of the same model to have difference transforms, without requiring replication of the basic geometry.
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
## Rasterization
- Takes as input 3 vertices, forming a triangle
- Finds all pixels considered inside that triangle, and forwards them to next step
- Performed on GPU
## Pixel Processing
- Executes a program per pixel to determine its color
- May perform depth testing
- Performed on GPU






