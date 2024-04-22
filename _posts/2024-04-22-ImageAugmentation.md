---
title: "[AUG] A Comprehensive Survey of Image Augmentation Techniques for Deep Learning"
---
# [AUG] A Comprehensive Survey of Image Augmentation Techniques for Deep Learning

- paper: https://www.sciencedirect.com/science/article/pii/S0031320323000481
- github: x
- Pattern Recognition 2023 accepted (인용수: 198회, '24-04-22 기준)
- downstream task: X

# 1. Motivation

- Deep Learning에 적용하는 Image augmentation에 대한 새로운 분류체계를 제안해보자

# 2. Contribution

- Deep Learning에 challenges와 image augmentation의 필요성을 제안함
- 폭넓은 survey를 바탕으로 image augmentation의 새로운 분류를 제안함
- 미래 방향에 대해 토의함
  - image augmentation에 대한 이해
  - image augmentation을 활용할 새로운 전략
  - image augmentation vs. feature augmentation

# 3. Survey

- 새로운 분류체계 : model-free, model-based, optimizing policy-based

  ![](../images/2024-04-22/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202024-04-22%2023-42-44.png)

- Computer vision task의 challenges

  ![](../images/2024-04-22/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202024-04-22%2023-44-40.png)

  - Image variation의 예시

    ![](../images/2024-04-22/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202024-04-22%2023-45-27.png)

- vicinity distribution

  - image augmentation을 통해 real image, label point와 근접한 image label point를 도출한다는 얘기

    ![](../images/2024-04-22/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202024-04-22%2023-47-35.png)

    - $P_e$: empirical distribution
    - $delta$: direc-delta function

    ![](../images/2024-04-22/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202024-04-22%2023-48-08.png)

    - $P_v$: vicinity probability distribution

- Model-free methods

  - single-image augmentation : single image만 가지고 augmentation 수행

    - Geometric & Color & Intensity transformation

      ![](../images/2024-04-22/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202024-04-22%2023-51-04.png)

      - Intensity transformation의 예시들

      - 그 중에 Hide-and-seek 예시

        ![](../images/2024-04-22/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202024-04-22%2023-51-33.png)

      - 그 중에 random erasing 예시

        ![](../images/2024-04-22/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202024-04-22%2023-52-10.png)

  - multi-images augmentation : multi images를 통해 augmentation 수행

    ![](../images/2024-04-22/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202024-04-22%2023-51-52.png)

    - 그 중에 gridMask 예시

      ![](../images/2024-04-22/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202024-04-22%2023-52-53.png)

      - 3가지 hyper parameter로 구성: *d, r*, $\delta_x, \delta_y$

    - Cutmix, Cutout, Mixup, Pariring samples 비교

      ![](../images/2024-04-22/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202024-04-22%2023-54-24.png)

    - Cut & Paste & Learn 예시

      ![(../images/2024-04-22/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202024-04-22%2023-55-53.png)

- Model-based 방법들

  - unconditional, conditional, 그리고 image-conditional image generation이 있음

  ![](../images/2024-04-22/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202024-04-22%2023-54-55.png)

  - Label-conditional

    - GAN의 Variants

      ![](../images/2024-04-22/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202024-04-22%2023-56-41.png)

    - DAGAN의 flow

      ![](../images/2024-04-22/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202024-04-22%2023-57-11.png)

  - Image-conditional 

    - label-preserving: label은 보존하는 generation

      - 한계점: image의 bias가 묻어날 수 있음 (침팬지 + lemon)

      ![](../images/2024-04-22/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202024-04-23%2000-00-04.png)

    - label-changing : label을 변경하는 generation

      - GAN-MBD 예시

        ![](../images/2024-04-22/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202024-04-22%2023-57-36.png)

      - StyleMix, MixUp, CutMix, StyleCutMix 예시

        ![](../images/2024-04-22/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202024-04-23%2000-01-58.png)

      

  - Optimizing policy-based

    - Reinforcement Learning: 최적의 hyperparameter 를 찾기

      ![](../images/2024-04-22/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202024-04-23%2000-03-53.png)

    - Generative Adversarial Learning : Train loss는 최대화, test loss는 최소화 되는 hyperparameter를 추출

      - SPA 예시

        ![](../images/2024-04-22/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202024-04-23%2000-04-06.png)
