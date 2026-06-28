https://web.stanford.edu/class/cs231a/course_notes/01-camera-models.pdf

# Pinhole Camera ![[Pasted image 20260628113713.png]]
Aperture: Opening of the camera
![[Pasted image 20260628113950.png]]
f = focal length


![[Pasted image 20260628114027.png]]
Lens makes it easier to shorten angle from object to film

2D -> 3D = Projective Transformation
![[Pasted image 20260628114056.png]]


Cx and Cy are center of image, displaces it.
- Intrensic, made based on how the camera is designed
- Extrensic = rotation / translation

We need a conversion factor to go from meters to pixels. Pixel to mm ratio in x and y axis.
![[Pasted image 20260628114258.png]]


Factor out non-linearity using homogenous coordinates, figure out the x y z from the intrensic parameters
![[Pasted image 20260628114419.png]]


Sometimes you know where a object is in the world coordinate frame, and need to understand it locally.
![[Pasted image 20260628114529.png]]

![[Pasted image 20260628115108.png]]

Can set up transformation system of equations to translate stuff from camera space to world space and etc.

![[Pasted image 20260628115334.png]]

R = rotation, T= translation, K is intrensicts, pixels per milimeter

![[Pasted image 20260628115448.png]]

![[Pasted image 20260628115543.png]]


Can figure out this from 
![[Pasted image 20260628115911.png]]

Can also give depth in monocular scene