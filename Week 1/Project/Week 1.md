Tried to make bounding circles around enclosed objects. Using below code was able to put bounding circles around contours, but found 2 issues.
1. Most contours aren't fully closed loops or objects
2. Some objects / edges don't have contours because while the color changes, the brightness stays the same
```python
#pylint:disable=no-member

import cv2 as cv

import numpy as np

img = cv.imread('Resources/Photos/cats.jpg')

cv.imshow('Cats', img)

  

blank = np.zeros(img.shape, dtype='uint8')
gray = cv.cvtColor(img, cv.COLOR_BGR2GRAY)
blur = cv.GaussianBlur(gray, (5,5), cv.BORDER_DEFAULT)
canny = cv.Canny(blur, 125, 175)
contours, hierarchies = cv.findContours(canny, cv.RETR_LIST, cv.CHAIN_APPROX_SIMPLE)

print(f'{len(contours)} contour(s) found!')

for contour in contours:
  (centerX,centerY), radius = cv.minEnclosingCircle(contour)
  center = (int(centerX), int(centerY))
  cv.circle(blank, center, int(radius), (0,255,0),2 )


cv.drawContours(blank, contours, -1, (0,0,255), 1)
cv.imshow('Contours Drawn', blank)

cv.waitKey(0)
```
![[Pasted image 20260622110306.png]]
![[Pasted image 20260622105007.png]]
## Attempt at solving the first issue:
I converted the image to HSV, and used that as the canny. HSV stands for hue (color), saturation, value.
![[Pasted image 20260622111211.png|388]]

```python
hsv = cv.cvtColor(img, cv.COLOR_BGR2HSV)
blur = cv.GaussianBlur(hsv, (5,5), cv.BORDER_DEFAULT)
canny = cv.Canny(blur, 125, 175)
```
![[Pasted image 20260622110624.png]]I accidentally found out that I can plug the whole hsv color, which is 3 values into `cv.Canny()`, rather than the 1D grayscale provided in the tutorial. Googling this showed that it does all 3, and picks the gradient that is the highest. This means it can react to changes in color as well as saturation, when detecting edges.

Tomorrow I want to see if I can find all contours which form a closed loop, as thought might be closer to objects, my goal is to detect the basket as one object, although I'm not sure what I want to do with the tree. Also there isn't really depth information so if I wanted to isolate the cats or the basket, I'll see if there is a way to ignore the background somehow. Finally I want to add my own pictures, I tried that today but they were way to big and I need to resize them.

Currently the end goal is a object detector, that can identify edges around a object and see it as a whole thing.


# Day 2

Started by adding gray scale back, to compare to HSV
![[Pasted image 20260627204356.png]]
There is some noise in the black bits I notices, and setting the hue when the v (think intensity) value is below 30 to 0 fixed it. Additionally by adding in the saturation value s, we can detect changes in essentially the amount of color. I had to threshold this as well or noise appeared.

Messing with threshold a bit more got me here

![[Pasted image 20260627205414.png]]

(Using the tutorial contours.py for reference)
![[Pasted image 20260627215719.png]]

At this point I went to AI and asked for help sence I didn't really have a direction to go in to clean it up.

This turned into alot of wierd places including some funny abstract art, (HSV detecting the color changes)
![[Pasted image 20260627210114.png]]

HSV seems to break down when being used for complex and fuzzy images
![[Pasted image 20260627213559.png]]![[Pasted image 20260627215244.png]]

I found that removing the background using remove.bg makes this a lot easier lol.
My method did tend to add extra information, and displaying using RETR_EXTERNAL helps

Code at this point, (has ai generated bits)
```python
import cv2 as cv
import numpy as np

img = cv.imread('Resources/Photos/dicee.png')

clahe = cv.createCLAHE(clipLimit=2.0, tileGridSize=(8, 8))

# --- HSV: all three channels ---
hsv = cv.cvtColor(img, cv.COLOR_BGR2HSV)
(h, s, v) = cv.split(hsv)

h[v < 30] = 0  # kill hue noise in dark regions
s[v < 5] = 0

h[v > 225] = 0  # kill hue noise in bright/white areas
s[v > 225] = 0

blur_h = cv.GaussianBlur(h, (5, 5), cv.BORDER_DEFAULT)
canny_h = cv.Canny(blur_h, 175, 250)

blur_s = cv.GaussianBlur(s, (5, 5), cv.BORDER_DEFAULT)
canny_s = cv.Canny(blur_s, 175, 250)

blur_v = cv.GaussianBlur(v, (5, 5), cv.BORDER_DEFAULT)
canny_v = cv.Canny(blur_v, 175, 250)

# Grayscale with CLAHE
gray = cv.cvtColor(img, cv.COLOR_BGR2GRAY)
gray_eq = clahe.apply(gray)
blur_gray = cv.GaussianBlur(gray_eq, (5, 5), cv.BORDER_DEFAULT)
canny_gray = cv.Canny(blur_gray, 125, 175)

# Combine all four edge maps
combined_edges = cv.bitwise_or(canny_h, cv.bitwise_or(canny_s, cv.bitwise_or(canny_v, canny_gray)))
contours, _ = cv.findContours(combined_edges, cv.RETR_EXTERNAL, cv.CHAIN_APPROX_SIMPLE)
print(f'Combined: {len(contours)} contour(s) found!')

blank = np.zeros(img.shape, dtype='uint8')
cv.drawContours(blank, contours, -1, (0, 0, 255), 1)

target_width = 800
scale = target_width / img.shape[1]
display = cv.resize(blank, (0, 0), fx=scale, fy=scale)
cv.imshow('Combined Contours', display)

originalDisplay = cv.resize(img, (0, 0), fx=scale, fy=scale)
cv.imshow('Original', originalDisplay)

cv.waitKey(0)
```

tutorial:
![[Pasted image 20260627224209.png]]
mine:
![[Pasted image 20260627224224.png]]

# Removing Background
From what I found, there isn't a good image processing way to remove backgrounds with opencv. I installed a library called rembg: https://github.com/danielgatis/rembg, which uses a neural network called **isNET**, which is trained on images where parts are 

![[Pasted image 20260627222711.png]]

While it is cool, it dosn't really help in terms of individual object detection when people are close together.

it also took like 10 seconds which is way to long to do anything in real time 

# Camera Stream

At this point I figured out how to make it go to my laptop camera in real time
![[Pasted image 20260627230201.png]]

