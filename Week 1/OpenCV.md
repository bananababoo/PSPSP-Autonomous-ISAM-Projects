![[Pasted image 20260621184828.png]]

![[Pasted image 20260621184948.png]]

waitKey(): keep images open untill key pressed

capture.release()/cv.destroyAllWindows() resource cleanup


![[Pasted image 20260621190031.png]]

Adaptive: Define threshold for each region???

Book with shadow on it
- If one threshold then could screw you up
- Adaptive would be good in this case 

Thresholding:
- Way to determine if pixel is active enough or not

Contour:
![[Pasted image 20260621190456.png]]
Edge detection:
- Edge = Light to dark.
- Canny Edge Detection

Blur: reduces noise

If there is salt and pepper noise, random pixels are random weird. Blurring smooths that out so edge detection doesn't mess up.

