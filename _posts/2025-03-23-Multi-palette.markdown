---
title: "Color Recommendation for Vector Graphic Documents based on Multi-Palette Representation"

---



# [Design] Color Recommendation for Vector Graphic Documents based on Multi-Palette Representation

- paper: https://arxiv.org/pdf/2209.10820
- github: https://github.com/CyberAgentAILab/multipalette
- WACV 2023 accpeted (인용수: 4회, '25-03-23 기준)
- downstream task: color palette recommendation

# 1. Motivation

- Pre-defnined template에서 사용할 요소를 추가할 경우, color harmony가 깨지는 문제가 있음

- 디자이너/일반인 모두 해당 상황에서 color 선택에 어려움이 있다.

- 기존 연구들은 이를 single palette로 표현하고자 헀으나, **vector graphics document**는 다양한 요소들(images, shapes, texts)로 구성되어 있고, 요소별로 color palette로 구성된 **multi palette** 상황이라 훨씬 복잡함ㅌ

  $\to$ 주어진 **multi palette** color-context 상황에서 새로 추가된 요소의 색상을 추천해주는 모델을 만들어보자!

# 2. Contribution

- Vector Graphic Design에서 **Multi palette**를 표현할 새로운 masked color model을 제안함

  ![](../images/2025-03-23/image-20250323094926804.png)

- Recommended color로 해당 graphic document를 **interactive**하게 recolor하는 **system**을 제안함

  ![](../images/2025-03-23/image-20250323095008692.png)

- 다양한 정량적 & 정성적 실험을 통해 제안한 방법의 효과를 입증함

# 3. Multi Palette Recommendation

## 3.1 Dataset

- Crello
  - templates: social media posts, blog headers, banner ads, printed posters
  - categories: coloredBackground, svgElement, textElement, imageElement, maskElement

- Multi palette 

  - TextElement, coloredBackground 요소들은 1가지 색상만 존재 $\to$ 요소들을 3가지 묶음으로 나누어 multi palette(3개)를 구성

    ![](../images/2025-03-23/image-20250323124815958.png)

    - Image element group $\to$ K-mean cluster를 사용하여 color를 추출함
      - imageElement, maskElement
    - Scalable Vector Graphic (SVG) group $\to$ color 정보를 주어진 meta  data에서 추출함
      - coloredBackground, svgElement
    - Text element $\to$ color 정보를 주어진 meta  data에서 추출함
      - textElement

  - Max color palette number: 5개로 제한

  - train / val / test = 18,768 / 2,315 / 2,278

## 3.2 Representation Learning with masked color model

- Color embedding model

  - Word embedding model과 유사하게 BERT기반으로 사전학습 수행

    ![](../images/2025-03-23/image-20250323125554417.png)

    - input: text corpus

      - Token embeddings: 색상에 대한 정보

        - color = word로 표현

          - 3 Steps

            - RGB to CIELAB conversion

            - $ b \times b \times b$로 quantize (*b < 255*) (b=16)
            - word로 concat

            ex. (255, 255, 255) $\to$ 15_8_8

        - Crello dataset에는 796개의 color vocabulary가 존재

        - palette = sentence로 표현

      - Segment embeddings: Palette의 갯수에 대한 정보. Image / SVG / text Palette별 고유 카테고리 (1 / 2/ 3)

        - Multi palette representation으로 학습을 가능하게 함

      - Positional embedding: Palette내 color의 순서

    - output: feature vectors corresponds to words

  ## 3.3 Color recommendation system

  - concept

    ![](../images/2025-03-23/image-20250323095008692.png)

    1. Template에 해당하는 json을 선택
    2. Image elements에 대한 편집 (replacement)
    3. Multi palette 추출  & 유저가 변경할 색상을 선택
       - SVG palette: 간단한 interpolation으로 추천된 color로 변경
       - text / image palette: 유저가 선택한 색상에 대해 추천된 color를 제안함
         - palette기반 k-mean recoloring 방식을 도입
    4. 추천된 color를 top-N개 추출
    5. 추천된 color 선택 및 recolored 결과를 보여주기
    6. 선호하는 결과를 선택하기

​	

# 4. Experiments

- Evaluation metrics

  - top-N accuracy
  - CIEDE2000: color간 distance로 사용

- 정량적 결과

  - top-N accuracy

    ![](../images/2025-03-23/image-20250323230316368.png)

    ![](../images/2025-03-23/image-20250323230302919.png)

  - CIEDE2000

    ![](../images/2025-03-23/image-20250323230351954.png)

    ![](../images/2025-03-23/image-20250323230411474.png)

- 정성적 결과

  - 80개의 template에서 1개의 color를 3개의 multi palette중 randomly 선택 후, 추천된 결과로 대체함

  - Test sample의 Hue 분포

    ![](../images/2025-03-23/image-20250323230712414.png)

  - Neutral color (흰/검으로 대체)이면서 매우 작은 text는 제외 (inperceptable elements) 

    ![](../images/2025-03-23/image-20250323230729865.png)

  - 84명의 참가자에게 평가 의뢰

    - Non-designers (68명)

      ![](../images/2025-03-23/image-20250323230847277.png)

      ![](../images/2025-03-23/image-20250323230855747.png)

    - Designers (16명)

      ![](../images/2025-03-23/image-20250323230908883.png)

      ![](../images/2025-03-23/image-20250323230918692.png)

- Ablation study

  - segment / position 유무에 따른 성능 비교

  ![](../images/2025-03-23/image-20250323230207639.png)

- Interviews

  - 6가지 항목별 긍정/모름/부정으로 인터뷰

    ![](../images/2025-03-23/image-20250323231047868.png)

- 한계점

  - 예측해야할 color수가 늘어날수록 성능이 하락함

    ![](../images/2025-03-23/image-20250323231116971.png)

  