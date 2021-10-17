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

## 1. Active Contour Models이란?
Image segmentation에 사용되는 algorithm 중 하나로 object의 내부와 외부의 energy minimization을 통해 edge를 detection하는 모델로 snake라고도 불립니다. 이 때, snake의 energy는 snake의 shape와 image안에서의 location에 의해 계산됩니다. 
