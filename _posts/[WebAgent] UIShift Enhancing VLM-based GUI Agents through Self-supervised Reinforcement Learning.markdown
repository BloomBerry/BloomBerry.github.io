---
title: "UIShift: Enhancing VLM-based GUI Agents through Self-supervised Reinforcement Learning"
---



# [WebAgent] UIShift: Enhancing VLM-based GUI Agents through Self-supervised Reinforcement Learning

- paper: https://arxiv.org/pdf/2505.12493
- github: X
- archived (인용수: 0회, 25-05-24 기준)
- downstream task: GUI Grounding (ScreenSpot: Mobile, ScreenSpot-V2: Desktop, ScreenSpot-Pro: Web), GUI Task Automation  (AndroidControl-Low (단계별 지침), AndroidControl-High (테스크 레벨 지침))

# 1. Motivation

- Vision Language Models (VLMs)의 발전은 mobile GUI agent의 패러다임을 handcrafted heuristics $\to$ design vision-grounded policies로 바꿨음

- VLM을 학습시키기 위해서는 GUI Trajectories 라벨 데이터 annotation이 필요로 함 $\to$ 15,283 task를 위해 1년간 라벨링 수행. $\to$ 고비용 data 수집은 VLM paradigm을 확장하기 어려웠음

  $\to$ 쉽게 취득가능한 Unlabeled GUI trajectgories를 이용한 self-supervised training framework를 접목해보자!

- Robotics & Biomechanics에서 as-is state, to-be state (k-step)을 보고, control command를 agent가 예측하게 해보자! (*k-step UI Transition*)

# 2. Contribution

- Self-supervised (GRPO) UI Transition data를 기반으로 학습한 UIShft가 annotation-dependent baselines보다 GUI grounding & GUT Task Automation tasks에서 우월한 성능을 보임으로써, UI Transition이 효율적인 learning signal을 VLMs (Qwen-2.5VL 3B/7B)에 제공함을 입증함

- Reasoning이 GUI 관련 task에서 효과가 없음을 입증함. (Perception-R1 / UI-R1)

- *k*에 따른 성능을 비교 분석함. $\to$ GUI task에서 SOTA

  - *k*가 클수록 장기 계획 성능이 좋아짐 (AndroidControl-High) 

    - Task-level Action 향상 or long-horizon planning 능력 향상 But Grounding 능력 감쇠

      ex. "Share the news article on Gmail"

      : '공유 버튼 클릭' $\to$ 'Gmail 앱 선택' $\to$ '받는 사람 주소 입력' $\to$ '보내기 버튼 클릭'

  - *k*가 작으루록 단기 계획 성능이 좋아짐 (AndroidControl-Low)

    - Low-level action 예측 향상 

      ex. click, scroll, input_text, open_app, navigate_back

# 3. Related Works

## 3.1 Mobile GUI Agents

- 대량의 데이터셋 기반 SFT로 VLMs을 학습하는게 Mobile GUI Agent의 최근 진행되었음

  - Instruction과  GUI action을 instruction-following task로 mapping하도록 훈련됨 $\to$ 고품질의 human annotation이 필수적임

    - Uground [8] 논문에서는 1.3M screenshot로 visual grounding 모델 훈련

    - UI-TARS [19] 논문에서는 50B token으로 학습함

    - OS-Atlas [31] 논문에서는 13M UI element로 grounding pretraining을 수행함

    - MobileVLM [30] 논문에서는 action 예측 task를 pretraining에서 수행함 (1-step only) + downstream 일반화를 위해 annotation-heavy finetuning이 필수적

      $\to$ 본 논문에서는 *k-step inverse dynamics task* 를 정의하여 해결함 (0-step screenshot, k-step screenshot, k개의 sequencial action)

## 3.2 Rule-based Reinforcement Learning

- GRPO가 SFT의 훌륭한 대안임이 최근 입증됨 (DeepSeek)

  - Verifiable Reward [11] 논문에서는 verifiable answer를 강조함으로써 reward signal의 신뢰성을 높임

  - DeepSeek-R1 [9] 논문에서는 간단한 format & accuracy reward가 instruction-tuned 모델보다 우수한 성능을 보임을 입증

    $\to$ GUI task는 **Discretized action space** + **structured parameter**로 구성되어, correctness를 검증하는데 수월함

  - GRPO to UI 적용된 최근 논문들도 있음

    - UI-R1 [18] 에서는 136단계 insturction samples에 대해 1 stage RL로 학습

    - GUI-R1 [32]은 1 stage SFT+RL pipeline만으로 3K task-instruction sample을 5개의 platform에서 취득함

    - InfiGUI-R1 [16]는 2 stage SFT+RL pipeline으로 GUI + non GUI domain에서 32K sample을 취득함

      $\to$ 모두 annotation이 필요함 (SFT). 

      $\to$ 본 논문은 1stage RL pipeline으로 2K UI Transition samples을 취득함

# 4. UIShift

- Overall Diagram

  ![](../images/2025-05-24/image-20250524211024268.png)

## 4.1 Preliminary: GRPO

## 4.2 Reward Design in UIShift

## 4.3 K-step UI Transition Task & Training

# 5. Experiments