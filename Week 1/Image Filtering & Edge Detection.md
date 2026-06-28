
![[Pasted image 20260621191506.png]]
- Take derivative, find the spikes, issue...


![[Pasted image 20260621191412.png]]
- Filter / Kernel smooths it out, making the derivatives easier to spot for when edges happen


Can do this in 2d well Dell Operator, figure out how much pixel value changing in one bit of image to another
![[Pasted image 20260621191629.png]]
![[Pasted image 20260621191806.png]]


Sobel Filter, Gradient of image in directions, vs the magnitude total![[Pasted image 20260621191852.png]]
![[Pasted image 20260621191956.png]]

# Canny Edge Detector
- Smooth w/ Gaussian Filter
- Compute Derivative
- Magnitude of derivatives
- Non-Maximum Suppression
	- Something something lines aren't supposed to have width
	- Making sure lines don't have width
	- Along direction of gradient, what is brightest
- Thresholding (Hysteresis)
	- Hysteresis: if between 2 thresholds, it might be an edge not sure


Also sobel:
- dosn't care about non max supression
- 