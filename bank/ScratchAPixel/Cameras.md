- In 3D, we use *pinhole cameras*
- In the real world, it consists of a lightproof box with a very small hole in the front known as the *aperture*, and some light-sensitive film placed in the box on the side facing the pinhole.
- ![[Pasted image 20240925223916.png]]
- Small apertures require longer exposure times but produce sharper images, large apertures produce fuzzy images.
- ![[Pasted image 20240925224327.png]]
- The plane where our image is projected is our *image plane*
- The distance from the film plane to the aperture is referred to as the *focal length*
- The *field of view* is the angle at the apex of the triangle defined by the aperture and the film edges
- ![[Pasted image 20240925225208.png]]
- ![[Pasted image 20240925225227.png]]
- In 3D graphics, the exent of the scene visible through the camera can be determined by a triangle connecting the aperture to either the top and bottom edges of the film, *vertical FOV*, or the left and right edges, *horizontal FOV* (Different APIs choose different ones of course they do why wouldn't they)
- A relationship exists between focal length and FOV
	- AB is the distance from eye to canvas
	- BC is half the canvas size
	- $\theta$ is half the FOV
	- ABC forms a right triangle, so applying Pythagorean trig identities, we can calculate BC
	- ![[Pasted image 20240925225705.png]]
- This relationship helps zoom in and out by only changing the FOV
- The extent of the scene also depends on the film size. The smaller the surface, the narrower the FOV.
- The FOV is determined by 2 critical parameters: focal length and film size. Altering either changes angle of view.
	- When film size is fixed, altering focal length changes FOV. Longer focal length results in a narrower angle of view.
	- When the focal length is fixed, changing film sizes alters FOV. Larger film leads to a wider FOV.
	- To modify film size while maintaining same FOV, you must adjust focal length accordingly.
- With 2 parameters, the third can be inferred.



## Virtual Pinhole camera
- Virtual cameras have near and far clipping planes. Objects closer than the near clipping plane or farther than the far clipping plane are invisible to the camera.
- ![[Pasted image 20240925231517.png]]
- ![[Pasted image 20240925231642.png]]
- The *canvas* represents the 2D surface within the image plane where the scene's image is projected
- ![[Pasted image 20240925231924.png]]
- ![[Pasted image 20240925231940.png]]
- 