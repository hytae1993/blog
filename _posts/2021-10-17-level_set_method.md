---
title:  "Level Set Method"
excerpt: "traditional algorithm - segmentation"
toc: true
toc_sticky: true

use_math: true

categories:
  - math
tags:
  - segmentation

last_modified_at: 2021-10-17
---
참고: [Curvature](https://suhak.tistory.com/283)

## 1. Level Set 이란?
Level Set은 값이 실수인 함수 즉 real-valued function $f$이 실수값 $c$를 가지게 하는 정의역의 집합입니다. 
\[ L_C(f) = \{(x_1, x_2, \cdots x_n) \vert f(x_1, x_2, \cdots x_n)=c\} \]

## 2. Level Set Method란?
Active Contour Model이 정교한 boundary(i.e. snake)를 찾을 수는 있으나 object의 topological한 변화 merge, split에 대해서는 boundary를 잘 찾지 못합니다. 하지만 Level Set Method는 이를 해결할 수 있습니다. Leve Set Method는 curve(i.e. snake)가 아닌 surface $\phi$를 evolve 시켜 object를 segmentation 합니다. 마찬가지로 object의 내부, 외부에 대한 energy function을 minimize하며 object를 segmentation 합니다. 이 때 초기 suface $\phi$는 법선 방향으로 움직이며 시간 $t$일 때 $phi(t)$에 의해 만들어지는 curve $C$는
\[C = \{w=(x,y) \in \mathbb{R}^2 \vert \phi(w,t) = 0 \}\]
로 표현할 수 있습니다. $\phi(w,t) = 0$ 이므로 zero level-set 이라고 합니다.

초기 폐곡선을 $\phi(w,t=0)$이라 할 때, t의 변화에 따라 곡선의 법선벡터 방향으로 curvature에 의한 속도 움직인다. 

![level-set-curve](https://user-images.githubusercontent.com/32087995/137633296-535c4745-5565-4dfc-946b-f0ecbe237932.png){: .align-center}

\[\begin{equation*}
\phi(w) = \begin{cases}
\phi(w) > 0, & \quad \omega \in \Omega_C, \\
\phi(w) = 0, & \quad \omega \in \Omega, \\
\phi(w) < 0, & \quad \omega \in \Omega/\Omega_C
\end{cases}
\end{equation*}\]

처럼 곡선 $C$ 내부에서 $\phi$는 양의 값, 외부에서는 음의 값, 위에서는 0의 값을 가진다.

## 3. Energy Function