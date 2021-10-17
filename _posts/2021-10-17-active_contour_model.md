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
$$ E(C) = E_{internal}(C) + E_{external}(C) $$  
$$ E_{internal}(C) = \int \|C^{'}(S)\|^2 + \|C^{''}(S)\|^2 \,dS $$
$$ E_{external}(C) = \int -\|\nabla I(C(S))\|^2 \,dS $$