---
title: "[SFDA][OD] MemXFormer: Towards Online Domain Adaptive Object Detection"
---
# [SFDA][OD] MemXFormer: Towards Online Domain Adaptive Object Detection



- paper : https://openaccess.thecvf.com/content/WACV2023/papers/VS_Towards_Online_Domain_Adaptive_Object_Detection_WACV_2023_paper.pdf
- git :https://github.com/Vibashan/online-da
- WACV2023 accpeted (인용수: 21회, '23.12.29 기준)
- downstream task: Source-Free Domain Adaptation (SFDA) for OD

# Abstract

- Contribution
  - Transformer기반의 MemXFormer module을 통해 online으로 target domain을 대표하는 prototypes를 업데이트하여 실시간으로 adaptation이 가능하도록 구현함
  - Online/Offline으로 SFDA에서 OD를 푼 최초의 논문

# 1. Introduction

- OD Task 수행시 다양한 Target domain에서 offline으로 adaptation하는 것은 infeasible하므로, online/offline adaptation 모두 해봐야 한다고 문제제기한 최초의 논문

  - Online-SFDA(Source-Free Domain Adaptation) setting

- 두 가지 기술을 Online-SFDA에 적절히 변형하여 활용

  - Self-Training : Mean teacher를 EMA로 업데이트하여 pseudo target label로 학습

    - 단점 : Target domain 분포를 fully 추출할 수 없음 → Robust target feature 학습이 안됨
    - 해결 : Memory module을 두어 target domain 을 approximately 추출

  - Contrastive Learning

    ![](../images/2023-12-29/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202023-12-29%2021-44-38.png)

    - $z_i, z_j$: i번째 feature, j번째 feature (같은 이미지, 다른 augmentation view)
    - 단점 :
      - Classification task 풀때 주로 사용. Image-level로 Augmentation 수행하여 복제함. → Computational cost가 많이 듦.(?)
      - Large batch size를 요구함
    - 해결 : MemXFormer에 Teacher feature의 cross-attention된 값을 MemXFormer를 통해 Positive, Negative pair 생성 → temporal ensemble feature 를 기반으로 Contrastive Learning 수행. (RoI Features)

# 2. Related Work

### Online Adaptation

- SFDA (Source Free Domain Adaptation)

  - Deploy 환경에서는 Source data를 활용해서 adaptation 할 수 없으므로, Source image, label은 사용하지 않음
  - Target unlabel image만 사용

- Online-SFDA

  ![](../images/2023-12-29/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202023-12-29%2021-46-08.png)

  - Large batch size는 infeasible하므로, one-by-one 연속적으로 들어오는 setting으로 adaptation 수행

### Contrastive Learning

- Positive pair는 당기고, Negative pair는 밀도록 학습 → Strong feature representation 학습이 가능
- 일반적으론 자기 자신(image) 을 다른 augmentation 준 image를 positive, batch 내 다른 image를 negative로 학습 → Large batch size 요구
- Small batch size에도 Memory를 활용하면 positive, negative (hard negative)를 제공할 수 있음

# 3. Approach

- Overall Diagram

![](../images/2023-12-29/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202023-12-29%2021-46-54.png)

### Memory-based Contrastive Learning

- MemXFormer

  - cross-attention transformer-based memory module

  - target의 distribution shift 정보를 저장 및 online-adaptation 환경에서 Contrastive Learning에 사용될 pair를 제공 $M=\{m^i \in \mathbb{R}^{1 \times C}\}^{N_l}$

    - $m^i$: memory module에 저장된 temporal ensemble RoI feature (prototype 역할)

  - 주요기능

    - read : Student RoI feature를 query로 활용해서 weighted sum된 similar / non-similar memory를 불러옴.
    - write : Teacher RoI feature를 저장함.

  - Write

    - Student보다 reliable한 값을 출력하는 Teacher의 RoI feature를 Memory에 저장 $F_t=\{f_t^i \in \mathbb{R}^{1 \times C}\}_{i=1}^{N_f}$
      - Key, Value : Teacher RoI feature $k_t^i=W_kf_t^i \text{,   }v_t^i=W_vf_t^i$
      - Query : memory 저장된 vector
    - Similarity matrix $s_t^{i,j}=\frac{exp(m^j(k_t^i)^T)}{\sum_{l \in M}exp(m^l(k_t^i)^T}$
    - update equation $m_j=F(m_j+\sum_{i \in V}s_t^{(i,j)}v_t^i)$
      - F=L2 norm

  - Read

    $q_s^i=W_qf_s^i \text{,  }s_s(i,j)=\frac{exp(q_s^i(m^j)^T)}{\sum_{l \in M}exp(q_s^i(m^l)^T)}$

    - Query : Student RoI Feature $q_s^i$
    - Key, Value : Memory $m^l$
    - positive pairs : $N_l \text{개 중에} N_f$를 선택 $p_s^i=\sum_{j \in M}s_s^{(i,j)}m^j\text{, } P_s=\{p_s^i\}_{i=1}^{N_f}$
    - negative pairs : Similairity가 하위 10%인 것들로 정의 $N_s=\{m_i^n\}_{i=1}^{N_s}$

  - Memory contrasive loss (MemCLR)

  $$ L_{MemCLR}(x_n)=-log{\frac{1}{|F_s|}\sum_{i \in F_s}\frac{exp(f_s^ip_s^i)}{exp(f_s^ip_s^i)+\sum_{n \in N_s}exp(f_s^im^n)}} $$

- Total Loss

$$ L_{total}(x_n)=L_{pl}^{st}(x_n)+L_{MemCLR}(x_n) $$

# 4. Experiments

### Dataset

- Scenario
  - clear-weather to foggy-weather
  - real to artistic
  - synthetic to real
  - cross-camera adaptation
- On/Offline
  - Offline : SFDA setting.
    - Train data : Target-train set
    - Test data : Target-test set
  - Online : Online-SFDA setting
    - Train data : Target-test set
    - Test data : Target-test set

### Implementation Details

- Model : Faster-RCNN w/ ResNet50 backbone
- Resize : shorter side to be 600
- batch size : 1
- EMA : $\alpha=0.99$
- Threshold : 0.9
- Optimizer : SGD w/ learning rate 1e-3 & momentum update 0.9
- epoch : 10
- Evaluation Metric : mAP@0.5

### Cityscapes to Foggy Cityscapes

![](../images/2023-12-29/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202023-12-29%2021-48-24.png)

### Synthetic to Real world Adaptation & Cross Camera Adaptation

- Synthetic to Real: Sim10K → Citisycapes
- Cross Camera: Cityscapes → Kitti

![](../images/2023-12-29/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202023-12-29%2021-48-41.png)

### Real to artistic adaptation

- Pascal  VOC (Real world) → WaterColor (Cartoon)

![](../images/2023-12-29/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202023-12-29%2021-49-01.png)

### Ablation Studies

![](../images/2023-12-29/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202023-12-29%2021-49-18.png)

- SupCon : Supervised Contrastive Loss

- T-SNE

  ![](../images/2023-12-29/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202023-12-29%2021-49-36.png)

  - RoI Features of 500 test images

- Test sample order에 따른 영항 분석

  ![](../images/2023-12-29/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202023-12-29%2021-49-51.png)

  - 분산이 적으므로 순서가 상관 없다고 함(?) → correlated image가 애초에 아니여서 그런듯..
