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

$$ 
\mathrm{L}_C(f) = \{(x_1, x_2, \cdots x_n) \vert f(x_1, x_2, \cdots x_n)=c\}
$$

## 2. Level Set Method란?
Active Contour Model이 정교한 boundary(i.e. snake)를 찾을 수는 있으나 object의 topological한 변화 merge, split에 대해서는 boundary를 잘 찾지 못합니다. 하지만 Level Set Method는 이를 해결할 수 있습니다. Leve Set Method는 curve(i.e. snake)가 아닌 surface $\phi$를 evolve 시켜 object를 segmentation 합니다. 마찬가지로 object의 내부, 외부에 대한 energy function을 minimize하며 object를 segmentation 합니다. 이 때 초기 suface $\phi$는 법선 방향으로 움직이며 시간 $t$일 때 $\phi(t)$에 의해 만들어지는 curve $C$는

$$C = \{w=(x,y) \in \mathbb{R}^2 \vert \phi(w,t) = 0 \}$$

로 표현할 수 있습니다. $\phi(w,t) = 0$ 이므로 zero level-set 이라고 합니다.

초기 폐곡선을 $\phi(w,t=0)$이라 할 때, t의 변화에 따라 곡선의 법선벡터 방향으로 curvature에 의한 속도 움직인다. 

![level-set-curve](https://user-images.githubusercontent.com/32087995/137633296-535c4745-5565-4dfc-946b-f0ecbe237932.png){: .align-center}

$$
\phi(w) = \begin{cases}
\phi(w) > 0, & \quad \omega \in \Omega_C, \\
\phi(w) = 0, & \quad \omega \in \Omega, \\
\phi(w) < 0, & \quad \omega \in \Omega/\Omega_C
\end{cases}
$$

처럼 곡선 $C$ 내부에서 $\phi$는 양의 값, 외부에서는 음의 값, 위에서는 0의 값을 가집니다.

Chain rule에 의하여 $\phi(w,t) = 0$을 $t$에 대하여 미분하면,

$$
\frac{\delta\phi(w,t)}{\delta t} + \nabla\phi(w,t)\cdot\frac{\delta w}{\delta t} = 0
$$

이고 속도함수 $F(t)$는 curve의 법선 방향으로의 속도이므로

$$
F(t) = \frac{\delta w}{\delta t}\cdot N
$$

입니다. 이 때 $N$은 법선 벡터로써 

$$
N = \frac{\nabla\phi(w,t)}{\left|\nabla\phi(w,t)\right|}
$$

이므로

$$
F(t) = \frac{\delta w}{\delta t}\cdot N = \frac{\delta w}{\delta t}\cdot\frac{\nabla\phi(w,t)}{\left|\nabla\phi(w,t)\right|}
$$

$$
F(t)\left|\nabla\phi(w,t)\right| = \frac{\delta w}{\delta t}\cdot\nabla\phi(w,t)
$$

입니다. 또한 t일 때의 surface $\phi_t(w,t)$ 는 $\frac{\delta\phi(w,t)}{\delta t}$ 로 표현 가능하므로 chain rule로 변환한 식에 속도 함수 $F$를 대입하면

$$
\phi_t(w,t) = \frac{\delta\phi(w,t)}{\delta t}
$$

$$
\frac{\delta\phi(w,t)}{\delta t} + \nabla\phi(w,t)\cdot\frac{\delta w}{\delta t} = \phi_t(w,t) + F(t)\left|\nabla\phi(w,t)\right| = 0
$$

$$
\phi_t(w,t) = -F(t)\left|\nabla\phi(w,t)\right| 
$$

따라서 curve C는 

$$
\frac{\delta C}{\delta t} = F \cdot N
$$

으로 표현 가능합니다. 
또한 $t$ 시간 후의 suface $\phi$는

$$
\phi(w, t+\nabla t) = \phi(w,t) + \nabla_tF\|\nabla\phi\|
$$
로 표현할 수 있습니다.
## 3. Energy Function
$F$를 속도함수라고 표현하였지만 energy function으로 정의하면 edge-based와 region-based 두 가지 방법으로 정의할 수 있습니다. 다음 post에서 region-based로 정의한 Active Contour without Edges 논문에 대하여 알아보겠습니다.

### Reference
1. https://www.ljll.math.upmc.fr/~frey/papers/levelsets/Phillips%20C.,%20The%20level-set%20method.pdf
2. 김선월, 조완현."새로운 속도함수를 갖는 레벨 셋 방법을 이용한 의료영상분할."응용통계연구24.2(2011):335-345.

