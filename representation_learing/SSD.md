# SSD

Created: March 17, 2022 12:49 PM
Last Edited Time: March 17, 2022 3:10 PM
Type: Review

<aside>
๐ก one-stage-detection ๋ผ๋ฌธ W.Liu et al 2016

</aside>

## 1. **Intro : ์ด๋ฏธ์ง ๋ด์์ ์ค๋ธ์ ํธ๋ฅด ์ด๋ป๊ฒ ์ฐพ์ ๋ด๋์ง**
- ๊ธฐ์กด์ ๋ฐฉ์์ 1) bounding box ๋ฅผ ๊ฐ์ , 2) ๊ฐ box ๋ฅผ ์ํ ํฝ์ ํน์ ํผ์ฒ๋ฅผ resample, 3) ๋์ ์ฑ๋ฅ์ ๋ณด์ด๋ ๋ถ๋ฅ๊ธฐ ์ฌ์ฉ
- SSD๋ ์ฌ๋ฌ ์ฌ์ด์ฆ์ fillter๋ฅผ ๊ฐ feature map ์ ์ฌ์ฉํ๋ฉด์ localizaion ๊ณผ classfication ์ ์ํ
- category socres (๋ถ๋ฅ ), box offsets (์ค๋ธ์ ํธ ์์น)

## 2.  **Method : Single Shot Detector**

![method](../rep_img/0.ssd_method.png)

- ๊ฐ ์ค๋ธ์ ํธ์ ๋ํด ๋ค์ํ ์ฌ์ด์ฆ์ default box๋ฅผ ํ๊ฐ
- ๊ฐ box ๋ง๋ค shape offsets , confidences ๋ฅผ ํ๊ฐ, loss ๋ ๋ ํ์ ๊ฐ์ค์น ํฉ

`feed-forard convoltional network that produces a fixed-size collection of bounding boxes and scores for presence of object class`

2-1) ์ํคํ์ฒ๋

VGG 16 (classfication head ์ ์ธ) : base network + detection network

2-2) Multi-scale feature maps for detection 

candidate of bounding box ์์ด ์ค๋ธ์ ํธ๋ฅผ ์ด๋ป๊ฒ ์ฐพ์ ๊ฒ์ธ์ง โ ๋ง์ง๋ง conv layer ์์ ๋ค์ํ scale์ map layer๋ฅผ ์ฌ์ฉํ๊ฒ ๋ค

![arc](../rep_img/0.ssd_arct.png)

> ๊ฐ layer stage ๋ง๋ค ๋ถ๋ฅ๊ธฐ๋ฅผ ์ ์ฉ โ ๋ง์ง๋ง ๋ถ๋ฅ๊ธฐ์์ ์ค๋ธ์ ํธ์ ์์น๋ฅผ ํ๋จํ๊ฒ ๋ค
conv layer๋ก latent space ๋ฅผ ํ์ด๊ฐ๋ ๊ณผ์ ์์ ๋ถ๋ฅ๊ธฐ๋ฅผ ์ํ conv layer ์ ์ฉ
> 

- ๋ณ๋๋ก ๋ถ๋ฅ๊ธฐ ๊ตฌ์ฑ

> style gan ์์ ์ฌ์ฉํ๋ feature map๊ณผ ์ฐจ์ด๊ฐ ์์
> 

> ๊ฐ layer์ ์ ์ฉ๋ ๋ถ๋ฅ layer๊ฐ ๋ค์ layer์ ์ํฅ์ ์ฃผ์ง ์๋๋ค๋ ์ 
> 

2-3) Convoulutional predictors for detection

๊ฐ layer์ conv3x3์ ์ ์ฉ

> ์๋ฌธ์  layer์ฌ์ด์ฆ๊ฐ ๋ค๋ฅธ๋ฐ ์ด๋ป๊ฒ mapping ์ ํ๋์ง
> 

**2-4) Default boxes and aspect ratios** 

box๋ฅผ default๋ก ๊ณ ์ ์ํด โ default box์ ์์น๊ฐ ๊ฐ ํฝ์  ์์์ ๊ณ ์ 

## 3. **Training**

> ground truth information needs to be assigned to specific outputs in the fixed set of detector outptus
> 

- ๋จผ์  ground truth info๋ฅผ ๋ฐํ์ผ๋ก default box๋ฅผ ์ ํ, fix ์ํจ ๋ค, classfifation ์งํ

- multi box ์ ๋ค๋ฅธ ์ ์ default box๋ฅผ ์ ํํ  ๋ maximum ๊ฐ์ผ๋ก ์ต์ ํ ์ํค์ง ์๊ณ  ์ผ์  ์ฐ๋ ์ค ํ๋ ์ด์์ธ ๊ฒ์ ์ฑํ

3-1) Matching default boxes - ground truth info

- jaccard overlap ์ฌ์ฉ(as in Multibox) , threshold (0.5)

3-2) Traing objective: 

loss func : 

$L(x,c,l,g) = 1/N (L_c(x,c) + aL_l(x,l,g))$

3-3) Choosing scales and aspect rations for default boxes : ๋ค์ํ object size๋ฅผ ์ก๊ธฐ ์ํด์ ๊ณ ์

- high feature map + global context pooling โ segmetatnion ์ฑ๋ฅ ํฅ์

- ๊ฐ layer๋ฅผ ๋ถ๋ฅ๊ธฐ์ ํต๊ณผ์ํจ output์ ์ฌ์ฉํ๊ธฐ ๋๋ฌธ์ layer์ box์ shape ๋ฅผ ๊ณ ๋ คํ์ง ์์

$$
s_k = smin + (smax - smin)/m-1 (k-1)
$$

`By combining predictions for all default boxes with different scales and aspect ratios from all locations of many feature maps, we have a diverse set of predictions, covering various input object sizes and shapes. For example, in Fig. 1, the dog is matched to a default box in the 4 ร 4 feature map, but not to any default boxes in the 8 ร 8 feature map. This is because those boxes have different scales and do not match the dog box, and therefore are considered as negatives during training.`

3-4) Hard negative minng

negative box ๋ sampling ํ์ฌ ์ฌ์ฉ 

default box๊ฐ ๋ง์์ง๋ฉด ๋ค์ ๋งํ๋ฉด detection object๊ฐ ๋ง์์ง๋ฉด ํ์ต์ํฌ negative box๋ ๋ง์์ง๋๋ฐ 

์ด ๋ class ๊ฐ inbalance ๋ฌธ์ ๊ฐ ์์, ๋ฐ๋ผ์ negative box ์ค conf loss๊ฐ ํฐ ๊ฒ๋ง ์ฌ์ฉ

> negative ํ์ต์ด ์๋ ์ด์  โ ๊ฐ class ๊ฐ์ ์ฐจ์ด๊ฐ ์ปค์ง๋๋ก ํ์ต
>