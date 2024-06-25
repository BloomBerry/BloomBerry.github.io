---
title: "[MM] A Survey on Multimodal Large Language Models"
---

# [MM] A Survey on Multimodal Large Language Models

- paper: https://arxiv.org/pdf/2306.13549
- github: https://github.com/BradyFU/Awesome-Multimodal-Large-Language-Models
- Archived (인용수: 467회, '24-06-23 기준)
- Downstream task: Multi-Modal 

# Abstract

- Multimodal LLM의 architecture, training 전략, data, 그리고 evaluation에 대해 다룸
- Multimodal LLM의 응용 사례 (granularity, modalities, languages, scenarios) 소개
- Multimodal Hallucination 소개
- Extended techniques 소개 (Multimodal ICL, Multimodal CoT, LLM-aided Visual Reasoning (LAVAR))
- Future research direction 소개

# 1. Introduction

- LLM은 reasoning 수행이 가능하나 blind함

- LVM (Large Vision Model)은 볼 수 있으나, reasoning을 못함

  $\to$ MLLM 은 LLM과 LVM의 장점을 취합한 형태

- MLLM의 정의

  1. billion-scale parameter

  2. instruction tuning 기반의 new paradigm으로 학습

- Timeline

  ![](images/2024-06-24/image-20240624231726005.png)

# 2. Architecture

- 3가지 모듈로 구성 

  ![](images/2024-06-24/image-20240624231924554.png)

  - Modality Encoder: Eyes / Ears 역할

    ex. Image / Audio Encoder

  - Pretrained LLM: Brain 역할

  - Modality Interface : 다른 modality를 align하는 역할

## 2.1 Modality Encoder

- Text에 align된 CLIP과 같은 pretrained encoder를 주로 활용

  ![](images/2024-06-24/image-20240624232024232.png)

- Encoder-less 구조 (Fuyu-8B)도 있음 $\to$ MLP로 directly projection 수행

  ![](images/2024-06-24/image-20240624232213240.png)

- Parameter size, training data composition에 비해 input resolution이 더욱 중요한 parameter임 $\to$ [52] 참고!

## 2.2 Pretrained LLM

- Pretrained LLM을 사용하는게 계산 효율 및 비용 측면에서 현실적임

- 최근 LLM은 causal attention 기반으로 구성 $\to$ Next token prediction & Decoder-only 구조이기 때문으로 사료됨

  ![](images/2024-06-24/image-20240624232456162.png)

- Scalability가 보장된다고 함 (7B $\to$ 13B $\to$ 34B일수록 성능 향상)

- MoE (Mixture of Experts) 기반의 sparse architecture가 computational cost 없이 scaling-up이 가능해서 성능 향상됨

  ![](images/2024-06-24/image-20240624232804578.png)

## 2.3 Modality Interface

- VFM, LLM을 end-to-end로 학습하는 것은 학습 비용이 많이 들게 되므로, 이를 freeze하고 connector만 학습시킴 (PEFT)

- 2가지 type

  - token-level: Modality encoder의 feature를 token으로 변환후 text token과 함께 사용

    - Q-former 기반: Blip-2, etc
    - MLP 기반: LLaVa, etc

    $\to$ token adapter type보다 visual token 갯수, image resolution이 더 중요한 변수라고 함 $\to$ [52] 참고!

  - feature-level: visual cue (feature)와 text embedding을 augmenting 수행 : ex. Flamingo

  - feature-level보다 token-level 성능이 좋암 

    - cross-attention 기반 hyperparameter space가 크므로 최적화가 힘들기 때문이라고 함  $\to$ [73] 참고!

- Expert Model 사용

  - Blip-2와 같은 Expert Model (Image Captioning) 를 이용해 image를 text로 바꾸어 training-free하게 사용

    $\to$ learning보다는 flexable한 구조는 아님

# 3. Training Strategy and Data

## 3.1 Pre-training

- 목적: multi modality간의 alignment 수행 (CE loss) 및 world knowledge 취득 (MLM loss)

- 데이터: large-scale image-text paired data

  ![](images/2024-06-24/image-20240624234238241.png)

  - Coarse-grained : Web-crawled data 특성상 규모가 크고, noisy & short caption이 특징.
    - CC-3M/CC-12M
      - 부적절한 Aspect ratio / size image는 fitering
      - NLP tool로 text annotation을 얻되, Heuristic design에 의해 filtering
    - SBU Captions (Flicklr에서 추출, 1M)
      - Heuristic하게 caption이 충분히 길 경우만 취득
      - 전치사와 pre-defined word가 존재할 경우만 취득
    - LAION
      - 너무 큰/작은 image, 짧은 text length는 filtering
      - URL에 의거 duplicate된 이미지 fitering
      - CLIP embedding 추출하여 illigal content, text&image similarity score 낮은 이미지 filtering
        - LAION-COCO: LAION-5B dataset 중 영어에 대해서만 사용. Caption은 Blip으로 caption 대체
    - COYO-7B
      - 너무 큰/작은 image, 부적절한 aspect ratio filtering
      - pHash value로 기존 benchmark dataset 중복 시 제거 (ImageNet, MS-COCO, etc)
      - 명사형, 적절한 길이의 text, 영어 text만 추출
      - 중복 띄어쓰기 수정, 10번 이상 사용된 문구 제거, 등 수행
  - Fine-grained: GhatGPT-4V를 사용하여 instruction tuning
    - ShareGPT4V-PT: cost-effective하게 100K data만 ChatGPT 사용, 나머지 1.2M는 pre-trained captioner로 추출

  

- 학습

  ![](images/2024-06-24/image-20240624234003351.png)

  - VFM, LLM은 freeze하고 learable interface만 학습하는게 일반적임

  - 아래 테이블처럼 Autoregressive하게 Cross-Entropy loss를 활용

  - image, text pair가 noisy 할수록, short할수록 low image resolution /  clean 할수록, longer할수록 high image resolution 으로 학습해야 hallucination이 방지됨

## 3.2 Instruction Tuning



## 3.3 Alignment Tuning

# 4. Evaluation

## 4.1 Closed-set

## 4.2 Opened-set

# 5. Extensions

# 6. Multi-modal Hallucination

## 6.1 Preliminaries

## 6.2 Evaluation Methods

## 6.3 Mitigation Methods

# 7. Extended Techniques

## 7.1 Multimodal In-Context Learning

## 7.2 Multimodal Chain-of-Thought

## 7.3 LLM aided Visual Reasoning

# 8. Challenges & Future Directions
