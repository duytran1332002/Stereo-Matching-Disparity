# Stereo Matching Disparity
## Table of Content
- [**What is Stereo Matching**](#what-is-stereo-matching)
- [**About the project**](#about-the-project )
- [**Conventions**](#conventions)
- [**Methods**](#methods)
  - [**Pixel-wise Matching**](#pixel-wise-matching)
    - [**Vectorization**](#vectorization)
  - [**Window-based Matching**](#window-based-matching)
    - [**Vectorization**](#vectorization-1)
- [**Cost Function**](#cost-function)
  - [**L1**](#l1)
  - [**L2**](#l2)
  - [**Cosine Similarity**](#cosine-similarity)
  - [**Correlation Coefficient**](#correlation-coefficient)
- [**Result**](#result)
- [**Reference**](#reference)

## What is Stereo Matching
Stereo Matching is one of the core technologies in computer vision, which recovers 3D structures of real world from 2D images. It has been widely used in areas such as autonomous driving, augmented reality and robotics navigation. Given a pair of rectified stereo images, the goal of Stereo Matching is to compute the disparity for each pixel in the reference image, where disparity is defined as the horizontal displacement between a pair of corresponding pixels in the left and right images.
<br>
Resource: 
<a href="https://arxiv.org/abs/1909.03751">Adaptive Unimodal Cost Volume Filtering for Deep Stereo Matching </a>
## About the project 
In this project, I tried to use some simple methods (Pixel-wise Matching and Window-based Matching) to find the **Disparity Map** between 2 image - left image and right image with different focal length. In addition, I compared 2 function (vectorization and nonvectorization) to find the most efficient way. More and more, I used some cost function (L1, L2, Cosine Similarity, Correlation Coefficient) to solve some specific problems: different brigtness or contrast between 2 images.  
## Conventions
$L$: the left image
<br>
$R$: the right image
<br>
$p$: is position in image $[x_p, y_p]$
<br>
$L(p)$: the (vector) value of p
<br>
$D$: disparity range
<br>
$d$: value in disparity range $[0, D]$
<br>
$W$: image width
## Methods
### Pixel-wise Matching 
Each pixel in the map is in an interval [0, D], where D is disparity range.
<br>
<br>
Constraints (for the left image): 
1) A pixel p in the left image and its corresponding pixel q in the right image are in the same 
line $(y_p = y_q )$
2) Typically, $x_p - D \leq x_q \leq x_q$
<br>

![image](https://user-images.githubusercontent.com/78312815/193759458-beabc118-f58e-4166-bee0-79b11eb86c6e.png)

<br>

we find $C_1\(p,d\) = cost\(L\(p\), R\(p-d\)\)$ for each value of disparity. Finding d so that $C_1\(p, d\)$ is minimum. 
$$d = argmin_{d \in D} \(C_1\(p, d\)\)$$ Then, d is the value for the pixel p in disparity map

#### Vectorization 
![image](https://user-images.githubusercontent.com/78312815/193766308-806bb458-a0c4-4173-9f21-1bbe02abe78d.png)

Instead of using for function, we calculate the cost by vectorize them to save time. $C_d = cost\(L\[:, d: W\] − 𝑅\[:, 0: W − d\]\)$
### Window-based Matching
Each pixel in the map is in an interval [0, D], where D is disparity range.
<br>
<br>
Constraints (for the left image): 
1) A pixel p in the left image and its corresponding pixel q in the right image are in the same 
line $(y_p = y_q )$
2) Typically, $x_p - D \leq x_q \leq x_q$

<br>

![image](https://user-images.githubusercontent.com/78312815/193768904-97e7c447-5180-430e-961c-88ca35cb17d3.png)

<br>

we find $C_1\(p,d\) = \sum_{u \in W_p} cost\(L\(u\), R\(u-d\)\)$ for each value of disparity. Finding d so that $C_1\(p, d\)$ is minimum. 
$$d = argmin_{d \in D} \(C_1\(p, d\)\)$$ Then, d is the value for the pixel p in disparity map

#### Vectorization 
##### Integral

![image](https://user-images.githubusercontent.com/78312815/193770993-b2887935-5c42-4329-9325-20a5a4885029.png)

A = F(i + side, j + side) - F(i + side, j - side - 1) - F(i - side - 1, j + side) + F(i - side - 1, j - side - 1). with side = (kernel_size - 1) / 2
Instead of using for function, we calculate the cost by vectorize them to save time. $C_d = cost\(L\[:, d: W\] − 𝑅\[:, 0: W − d\]\)$.
## Cost Function
with $x$ and $y$ are 2 vectors
### L1
$$ \|x - y| $$

<br>

$d = argmin_{d \in D} \(C_1\(p, d\)\)$

### L2
$$ \(x - y\)^2 $$

<br>

$d = argmin_{d \in D} \(C_1\(p, d\)\)$
### Cosine Similarity
$$ cos(\pmb x, \pmb y) = \frac {\pmb x \cdot \pmb y}{||\pmb x|| \cdot ||\pmb y||} = \frac{\sum_{i} x_i y_i}{\sqrt{\sum_{i} x_i^2} \sqrt{\sum_{i} y_i^2}} $$

<br>

$d = argmax_{d \in D} \(C_1\(p, d\)\)$

### Correlation Coefficient
$$ r = \frac{n\sum_{i} x_i y_i - (\sum_i x_i)(\sum_i y_i)}{\sqrt{\sum_{i = 1} x_i^2 - (\sum_i x_i)^2} \sqrt{\sum_{i = 1} y_i^2 - (\sum_i y_i)^2}} $$

<br>

$d = argmax_{d \in D} \(C_1\(p, d\)\)$

## Result
- Disparity range: 64
- Kernel_size: 10

| Methods | L1 | L2 |
| --------------------------------- | :---: | :---: |
| Pixel-wise Matching Vectorization| 9.1s | 42s |
| Pixel-wise Matching Non-vectorization | 0.3s | 0.3s |
| Window-based Matching Vectorization | 24s | 92s |
| Window-based Matching Non-vectorization | 0.2s | 0.7s |

![Ảnh1](https://user-images.githubusercontent.com/78312815/193783819-0a9c3b73-22ed-4453-831a-5d34988481ea.png)

![Ảnh2](https://user-images.githubusercontent.com/78312815/193784447-8dc9c81b-f1b7-49fa-8531-d045be4cac8e.png)

## Reference
[AI Viet Nam](https://www.facebook.com/aivietnam.edu.vn)
