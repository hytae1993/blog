---
title:  "Active Contour Models"
excerpt: "traditional algorithm - segmentation"
toc: true
toc_sticky: true

categories:
  - math
tags:
  - segmentation

last_modified_at: 2021-10-17
---
석사 과정 동안 진행해왔던 image segmenation의 정교한 boundary를 위한 가장 기본적인 알고리즘에 대해서 알아보겠습니다. 

## 1. Active Contour Model이란?
Image segmentation에 사용되는 algorithm 중 하나로 object의 내부와 외부의 energy minimization을 통해 edge를 detection하는 모델로 snake라고도 불립니다. 이 때, snake의 energy는 snake의 shape와 image안에서의 location에 의해 계산됩니다. 따라서 Active Contour Model은 **snake에 의한 energy function을 minimizing** 하는 모델입니다. 

## 2. Energy Function of the Active Contour Model
Snake를 $C$라 하였을 때 Active Contour Model의 energy function은 두 부분으로 나뉩니다.

$$
\begin{aligned}
E(C) = E_{internal}(C) + E_{external}(C) \\
E_{internal}(C) = \int \|C^{'}(S)\|^2 + \|C^{''}(S)\|^2 \,dS \\
E_{external}(C) = \int -\|\nabla I(C(S))\|^2 \,dS 
\end{aligned}
$$

$E_{internal}(C)$의 첫번째 term은 elasticity라 불리며 두번째 term은 stifness라 불립니다. 두 term이 snake의 smoothness를 조절하게 됩니다. 하지만 $E_{internal}(C)$ 만으로는 snake에서 멀리 있는 curve를 찾아가지 못합니다. 이를 $E_{external}(C)$가 해결하게 됩니다. $E_{external}(C)$는 object의 edge는 object의 내부와 외부의 차이 즉 gradient가 클 것이라는 가정하에 만들어졌습니다. 따라서 $E_{external}(C)$는 snake가 object 내부로 가게 유도합니다. 

이 Active Contour Model은 object의 edge가 split, merge 와 같은 형태를 정확하게 분류하지 못하다는 단점이 있습니다. 