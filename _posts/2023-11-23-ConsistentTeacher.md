---
title: "[SSL][OD] Consistent-Teacher: Towards Reducing Inconsistent Pseudo-targets in Semi-supervised Object Detection"
---
# [SSL][OD\] Consistent-Teacher: Towards Reducing Inconsistent Pseudo-targets in Semi-supervised Object Detection

- paper link : https://arxiv.org/pdf/2209.01589.pdf
- github: https://github.com/Adamdad/ConsistentTeacher
- CVPR 2023 accpet ed (SenseTime Research, 인용수 : 8회, '23.11.23 기준)
- downstream task : SSL for OD

# 1. Motivation

- SSL for OD에서 Pseudo label의 Oscillation이 성능하락 (Psuedo label Loss 상승)하는 원인이 됨을 발견함

  ![](/home/jang/Documents/papers/images/2023-11-23/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202023-11-23%2021-12-44.png)

  - Polar Bear에 대해 iteration 마다 기존 방식(Mean Teacher)는 positive anchor로 할당되는 anchor가 바뀌게 되어 Loss가 증가함
  - 반면, 제안한 방식 (Consistent Teacher)는 꾸준히 iteration마다 postive anchor로 할당되는 anchor가 변하지 않아 Pseudo loss가 감소함
  - 기존의 IoU based anchor Matching을 대체할 Cost-Aware adaptive sample assignment를 제안

- Pseudo Label이 Oscillation하는 주요 원인은 Classification task와 Regression task의 Mismatch때문에 발생하는 것을 발견함

  - 즉, postive anchor의 probability score가 높다하여 반드시 IoU가 좋은 것은 아님을 발견
  - 따라서, Classificaiton feature를 가지고, regression feature를 re-ordering하는 FAM-3D를 제안함

- Fixed thresholding은 좋지 않음

  - GMM을 이용해 class별 threshold를 GMM으로 대체

# 2. Contribution

1. Adaptive Sampling Assignment
   1. 문제점: 기존 Positive/Negative anchor matching 방식의 한계 : IoU값이 높은 anchor를 GT와 matching하여 positive로 학습하였으나, IoU가 높다고 항상 Pseudo label의 confidence값이 높진 않다.
   2. 개선점: 각 pseudo gt box별로 모든 anchor에 대해 Loss값을 계산 (cls + reg + center_distance)하여 제일 낮은 top_k개를 positive anchor로 분류하여 학습함
2. 3D Feature Alignment Module
   1. 문제점 : Confidence Score가 높다고 항상 accurate box localization을 달성하진 않는다.
   2. 개선점: Classification feature를 이용해서 box localization을 re-order하여 classification aware box localization을 수행한다. 어떻게? Conv3x3+ReLU+Con1x1으로 2-step offset을 가한다. (1st step : spatial, 2nd step: channel)
3. Adaptive Threshold by Gaussian Mixture Model
   1. 문제점: Fixed threshold를 이용하여 postivie / negative를 나누게 되면, class간 & iteration간의 변화를 반영하지 못한 획일적인 분류기준을 사용함으로써 최적의 성능에 달성할 수 없다.
   2. 개선점: Gaussian distribution으로 계산한 pos/neg 사전 확률값을 EM (Expectation Maximization) algorithm을 이용하여 “student 를 위한 pseudo-target으로 setting될 확률 (사후확률)”을 max로 하도록 하는 tau값을 iteration마다 구해준다.
4. SSL for OD에서 SOTA 달성함

# 3. Consistent Teacher

- Baseline Model

- FCOS기반의 Mean Teacher를 사용
  - 단 IoU는 GIoU Loss, cls 는 Focal Loss를 사용

- overall diagram

  ![](../images/2023-11-23/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202023-11-23%2021-32-17.png)

## 4.1 Consistent Adative Sample Assignment

- 쉽게 말해 cost를 기준으로 anchor를 올림차순 해서 top-k개의 loss가 작은 k개를 anchor로 할당
- cost가 minimize되는 순으로 모든 gt box에 대한 모든 anchor에 대해 오름차순으로 정렬한 후, matching된 anchor들의 IoU총합을 clamp (ex. 8.xx → 8개) 를 positive로 할당한다. 
- 나머지는 negative로 할당한다. → if 15개가 IoU >0이고, IoU총합이 8.xx이면 8개는 positive, 나머지는 negative.

![](../images/2023-11-23/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202023-11-23%2021-41-33.png)																																																																																																																																																																																																																																																															

- $a_n$: anchor box를 의미. {1~L}은 positive, L+1~N은 background로 학습

- center point와 anchor간의 distance도 추가하여 anchor assignment할 때 stable해지도록 만듦

  ![](../images/2023-11-23/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202023-11-23%2021-43-53.png)

  - $C_{dist}$: anchor와 pseudo gt 중심간의 거리

## 4.2 BBox Consistency via 3-D Feature Alignment

- $P(i,j,l)$: (i,j)에 위치한 l번째 pyramid level layer를 의미

- Feature Alignment의 역할 : $s(P) \to P'$로 re-sampling하여 regression task를 수행하는 feature가 classification feature와 잘 align 되도록 re-ordering

  ![](../images/2023-11-23/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202023-11-23%2021-59-24.png)

- 참고한 논문의 FAM-2D를 설명한 자료

  ![](../images/2023-11-23/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202023-11-23%2022-01-01.png)

  ![](../images/2023-11-23/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202023-11-23%2022-01-26.png)
  

## 4.3 Thresholding with Gaussian Mixture Model

- 각 class별로 positive, negative gaussian model의 합으로 확률분포를 가정

  ![](../images/2023-11-23/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202023-11-23%2022-05-43.png)

- Expectation-Maximization (EM)을 통해 student의 target으로 설정될 사후 확률 분포 $P(pos|s^c, \mu_p^c, (\sigma_p^c)^2)$를 구함

- 구한 사후 확률분포의 argmax를 $\tau$로 선정

  ![](../images/2023-11-23/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202023-11-23%2022-08-34.png)

  - 1,000개만 sampling하여 Quene에 저장해서 argmax 구할때 활용



# 4. Experiment

- Inconsistency가 해결됨

  ![](../images/2023-11-23/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202023-11-23%2022-11-49.png)

  - 성능도 좋아짐

- Classification-Regression inconsistency가 해결됨

![](../images/2023-11-23/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202023-11-23%2022-09-59.png)

- COCO-Partial

  ![](../images/2023-11-23/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202023-11-23%2022-13-48.png)

- COCO-Additional

  ![](../images/2023-11-23/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202023-11-23%2022-14-14.png)

- VOC-partial

  ![](../images/2023-11-23/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202023-11-23%2022-14-30.png)

- Ablation : ASA vs. IoU based assignment

  ![](../images/2023-11-23/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202023-11-23%2022-14-57.png)

- GMM vs. Fixed Thresholding

  ![](../images/2023-11-23/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202023-11-23%2022-15-55.png)

- With and Without FAM-3D

  ![](../images/2023-11-23/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202023-11-23%2022-16-16.png)

- Different protocols

  ![](../images/2023-11-23/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202023-11-23%2022-16-38.png)