---
title:  "Distilling Global and Local Logits with Densely Connected Relations"
excerpt: "Knowledge distillation - classification"
toc: true
toc_sticky: true

use_math: true

categories:
  - paper
tags:
  - classification
  - segmentation
  - object detection
  - knowledge distillation

last_modified_at: 2021-10-18
---
이번에 말씀드릴 논문은 ICCV 2021에 발표될 **"Distilling Global and Local Logits with Densely Connected Relations"** 입니다. 이 논문은 기존 knowledge distillation 연구들이 global logits들만 이용하기 때문에 발생하는 spatial information의 손실을 local logits의 이용을 통해 해결한 논문입니다.

## 1. Knowledge distillation이란?
Knowledge distillation은 "미리 잘 학습된 Teacher Network의 Student Network에게 전달"하는 연구입니다. 

대량의 parameter를 통해 잘 학습된 network는 성능이 뛰어나지만 메모리 등의 문제로 인해 현실 세계에 적용하기 매우 어렵습니다. 이에 Knowledge distillation는 Teacher Network(대량의 parameter로 잘 학습된 network)를 통해 Student Network(적은 양의 parameter를 가진 network)를 학습시키는 것을 목표로 합니다. 
![image](https://user-images.githubusercontent.com/32087995/137644654-a69326e1-95dd-4d5b-8ea6-07e7eba6942b.png){: .align-center}

이미지 출처: https://intellabs.github.io/distiller/knowledge_distillation.html

자세한 설명은 [Knowledge distillation](https://light-tree.tistory.com/196)을 참고해주시기 바랍니다.

## 2. Distilling Global and Local Logits with Densely Connected Relations
### 1) Contribution
- 기존 Knowledge distillation과는 달리 global logits 뿐만 아니라 local logits도 사용하였으며 global logits과 local logits 간의 intra-relationship과 inter-relationship을 이용하였습니다. Intra-relationship은 한장의 input image에서의 logits간의 관계를 의미하며 inter-relationship은 mini-batch 내에서 모든 image들 간의 관계를 의미합니다. 아래 그림이 intra, inter-relationship을 잘 보여주는 논문의 그림입니다.
- 파악 중
- Image Classification 에서의 generalizability를 확보했을 뿐만 아니라 semantic segmentation, object detection 영역에도 이용할 수 있다는 것을 증명하였습니다.

![image](https://user-images.githubusercontent.com/32087995/137646603-6c802372-acd3-4d10-9fa6-2cbe052967db.png){: .align-center}

### 2) Method
논문에서 이용한 기호들에 대해서 먼저 살펴보겠습니다.

- $f = F(\cdot ;W^F)$ : convolutional layer of a network, $f$는 output feature
- $z = C(\cdot ;W^C)$ : classifier of a network, $z$는 logit
- $W$ : trainable weight in a layer
- GAP($\cdot$) : global average pooling
- $X = \{x_1, x_2, \cdots x_n\}$ : input samples in a mini-batch
- $Y = \{y_1, y_2, \cdots y_n\}$ : target label
- teacher는 각각의 기호에 $_f$, student는 $_s$가 붙어있습니다.

논문에서는 global average pooling으로 인하여 사라지는 spatial information을 보완하기 위하여 global feature를 나누어 local feature들을 만든 후 이들을 이용하여 local logit들을 만듭니다. 이때, local features와 global features는 같은 classifier를 사용합니다. global feature는 $f^0$이며 local feature는 $f^l (l =1,2, \cdots , L)$ 입니다. 이 때 $L$은 local features의 갯수이며 global feature를 widht, height가 $d$인 크기로 나누면 $L=d^2$가 됩니다.

이 내용을 수식으로 표현하면

$$
z^0 = C(GAP(f^0);W^C), z^l = C(GAP(f^l); W^C)
$$

입니다. 

#### Relationship between local and local, global and global
global logits과 local logits에서 logit distillation을 이용하기 위하여 knowledge distillation의 [첫 논문](https://arxiv.org/abs/1503.02531)에서 soft label을 만들기 위하여 이용한 **Temperature Softmax**를 변환하여 이용하였습니다. 

원래의 distillation loss부터 살펴보겠습니다. 

Temperature Softmax는 기존의 softmax에 $\tau$라는 hyperparmeter를 추가하여 큰 것은 매우 크게, 작은 것은 아주 작게 만드는 softmax 함수의 성질을 완화 시킨 것입니다. 

Temperautre Softmax를 이용한 distillation loss $L_{hinton}$은 Kullback-Leibler divergence에 의하여 계산됩니다.

$K$가 class의 갯수일 때,

$$
L_{hinton}(Z_t, Z_s) = \frac{1}{n} \sum_{i=1}^{n}\tau^2KL(\psi(\frac{z_{t,i}}{\tau}), \psi(\frac{z_{s,i}}{\tau}))
$$

$$
\psi(z)_j = \frac{e^{z_k}}{\Sigma_{k=1}^{K}e^{z_k}}, for j = 1,2,\cdots, K
$$


그런데 global logit과 local logit은 다른 statistical characteristics을 가지고 있기 때문에 $L_{hinton}$을 사용할 수 없습니다. 이에 논문에서는 normalized logits을 이용하여 **adaptive distillation loss** $L_{ND}$를 제안하였습니다.

feature의 mean, std를 이용하여 normalize하였으며 이 normalize한 값으로 $L_{hinton}$의 $\frac{z}{\tau}$를 대체하였습니다.

$$
\begin{aligned}
L_{ND}(Z_t, Z_s) \\
= \frac{1}{n}\sum_{i=1}^{n}KL(\psi(\frac{z_{t,i} - \mu(z_{t,i})}{\sigma(z_{t,i})}), \psi(\frac{z_{t,i} - \mu(z_{s,i})}{\sigma(z_{s,i})}) \\
= \frac{1}{n}\sum_{i=1}^{n}KL(\psi(\frac{z_{t,i}}{\sigma(z_{t,i})} - \frac{\mu(z_{t,i})}{\sigma(z_{t,i})}), \psi(\frac{z_{s,i}}{\sigma(z_{s,i})} - \frac{\mu(z_{s,i})}{\sigma(z_{s,i})}) \\
= \frac{1}{n}\sum_{i=1}^{n}KL(\psi(\frac{z_{t,i}}{\sigma(z_{t,i})}), \psi(\frac{z_{t,i}}{\sigma(z_{s,i})})
\end{aligned}
$$

$$
L_{global} = L_{ND}(Z_t^0, Z_s^0), L_{local} = \sum_{l=1}^L L_{ND}(Z_t^l, Z_s^l)
$$

다른 statistical characteristic을 가진 global logtis, local logits을 이용하기 위하여 normalize한 뒤 KL-Divergence를 통해 energy function을 최소화 하니, distillation performance를 올릴 수 있었으며 $L_{local}$을 통해 teacher로 부터 spatial class information을 배울 수 있었다고 합니다.

지금까지는 teacher와 student의 global logits간, local logits간의 relationship에 대하여 알아보았다면 이제부터는 global logits과 local logits간의 relationship에 대하여 알아보도록 하겠습니다.

#### Densly Connected Relationship
어떤 정보가 전달되어야 할 knowledge에 포함되어야 할지 알아내기 위하여 각각의 image들과 mini-batch image 들의 global, local logits 들 간의 $L_2$ distance matrix을 구성하였습니다. 따라서 생성된 matrix의 크기는 $m=n(L+1)$ 일 때, $m \times m$ 입니다. inter-relationship들만 이용하는 다른 work들과는 다르게 intra-relationship도 이용하였기에 더 general한 method라고 합니다.

Intra-relationship의 중요한 점은 intra-relationship이 co-activating behaviors를 찾아낼 수 있다는 점이라고 합니다. 

Co-activating behaviors를 아직 잘 이해하지 못하였지만 논문에서 든 예시는 **'a local logit dominates the global statisitc if the global and local logits are storgly related'** 라고 합니다. 이는 CNN이 final prediction에 영향을 미치는 specific local features에 biased 하는 것과 동시에 일어나는 현상이라고 합니다.

계속해서 논문에 대해 얘기하면 relationship을 파악하기 위하여 $L_2$ distance를 사용하였으며 이는 

$$
\tilde D(Z)_{i1, i2} = \|z_{i1}, z_{i2}\|^2, (z_{i1}, z_{i2}) \in Z
$$
$$
Z = Z^0 \cup Z^1 \cdots\cup Z^l \cdots \cup Z^L
$$

$Z$가 $Z^0$과 $Z^l$의 union set이므로 inter-relationship 뿐만 아니라 intra-relationship 또한 계산하게 됩니다.

Distance matrix를 위하여 계산한 $L_2$ distance를 normalize 해줍니다. 이 때, normalize는 row-wise로 계산됩니다.

$D(Z)_{[r,c]}$가 $D(Z)$의 $(r,c)^{th}$ element를 의미할 때,

$$
D(Z)_{[r,c]} = \frac{\tilde D(Z)_{[r,c]}}{\|\tilde D(Z)_{[r,c]}\|_2}
$$

이를 이용하여 teacher에서 student로 relational knwledge를 전달하는 distillation loss $L_{relation}$을 만들었습니다.

$$
L_{relation} = \frac{1}{m^2}\sum_{r=1}^m\sum_{c=1}^m\|D_t(Z_t)_{r,c} - D_s(Z_s)_{r,c}\|^2
$$

또한 image의 true label과 student network output간의 cross entropy인 $L_{task}$을 위에서 구한 $L_{global}$과 혼합하여 기존의 $L_{distill}$을 변형하였습니다.

$$
L_{distill} = (1-\alpha)L_{task} + \alpha L_{global}
$$

최종 loss인 $L_{GLD}$는 

$$
L_{GLD} = L_{distill} + L_{local} + \beta L_{relation}
$$

입니다.

#### Object Detection and Semantic Segmentation
distilled student로 ImageNet에 대한 ResNet 18을 사용하였습니다. 

Object detection을 위해서는 Single Shot Detector를 이용하였으며 semantic segmentaion을 위해서는 DeepLab V3+를 이용하였습니다.

![image](https://user-images.githubusercontent.com/32087995/137647766-edfe6a1d-271e-4c63-a177-d43626a69a4a.png){: .align-center}
![image](https://user-images.githubusercontent.com/32087995/137647772-c0f0de06-6de1-4211-bac8-56c3c60a0579.png){: .align-center}

두 표로 보았을 때, object detection과 semantic segmentation에도 충분히 적용가능하다고 합니다.