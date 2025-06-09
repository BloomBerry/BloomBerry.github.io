---
title: "[WebAgent] The BrowserGym Ecosystem for Web Agent Research"
---



# [WebAgent] The BrowserGym Ecosystem for Web Agent Research

- paper: https://arxiv.org/pdf/2412.05467
- github: https://github.com/ServiceNow/BrowserGym
- ICLR 2025 accpeted (인용수: 7회, '25-06-09 기준)
- downstream task: WebAgent Benchmarks (WebArena, VisualWebArena, etc)

# 1. Motivation

- LLM & VLM agent의 등장으로 다양한 Web Task benchmark가 등장했으나, 중구난방으로 code가 개발되어, 공평한 agent의 성능평가가 힘듦 (research silos)

- 이는 곧 다양한 환경 셋업과 데이터 포맷 변환, 그리고 수동으로 agent를 각각의 benchmark에 맞추는 귀찮은 과정을 수반함

  $\to$ agent benchmark를 집약한 framework를 제안해보자!

# 2. Contribution

- 공개된, 쉽게 사용 가능한, 확장성이 있는 framework인 BrowserGym ecosystem을 제안함

  ![](../images/2025-06-09/image-20250609225826629.png)

  - AgentLab 기반: web agent를 만들기 위한 tool을 제공하며, large-scale 실험에 특화됨
  - BrowserGym 기반: web task의 표준 interface를 제공함
  - MiniWoB(++) 에서 최신 AssitantBench, VisualWebArena benchmark까지 제공함

- large-scale 실험을 병렬로 쉽게 tool의 집합을 제공하는 AgentLab + BrowserGym 기반임

  - 추가로 AgentXRay를 통해 개별 task에 대한 agent의 행동을 관찰가능함
  - reusable building block 기능도 추가함

- 다양한 web agent experiment를 통해 최신 6개의 LLMs/VLMs (GPT-4, Claud 3.5, Llama 3.1, etc)의 결과를 report함

  - 해당 결과는 BorwserGym leaderboard를 통해 공개함

# 3. BrowserGym

- BrowserGym은 web agent를 위한 통합된 interface를 제공함

  ![](../images/2025-06-09/image-20250609232152271.png)

  - 좌측: chatting 창 / 우측: web browser
    - chatting: user / agent는 chatting창을 통해 상호작용함
    - web browser: user / agent모두 action (ex. click, type, tab열기 등)을 수행 가능함

- Implementation

  - Agent입장에서는 해당 interaction loop는 Partially Observable Markov Decision Process (POMDP)로 개념화될 수 있음

    - environment는 현재 관측값 & reward를 제공함
    - agent는 next action을 예측함

  - BrowserGym

    - 표준 OpenAI Gym API를 따름

    - gymnasium interface를 통해 python으로 제공 가능함

      ![](../images/2025-06-09/image-20250609232620080.png)

    - Chromium browser를 통해 구현됨

    - Playwright library를 통해 browser와 소통함

## 3.1 Extensive Observation Space

## 3.2 Expandable Action Space

## 3.3 Extensibility: create your own task

# 4. Unification of Web Agent Benchmarks

# 5. AgentLab

## 5.1 Launching experiments

## 5.2 Parallel experiments

## 5.3 AgentXRay

## 5.4 Reproducibility

## 5.5 Extensibility: create your own agent

## 5.6 Extensibility: Plug your own LLM / VLM

# 6. Experiments

## 6.1 Setup

## 6.2 Results

# 7. Related Works

## 7.1 Web Agent Benchmarks

- MiniWoB/MiniWoB++: web based task의 근간이 되는 evaluation metric을 기본적인 web interactions (form filling, button clicking)에서 제안함
- WebShop: 비교적 복잡한 e-commerce 시나리오를 대상으로 agent로 하여금 user의 관심도 & 제약에 따른 제품의 catalogs를 navigate 수행함
- Mind2Web: 더 나아가 multi-step interaction을 2,000 human tracesfmf cnlgkqgkdu 137 real-world website를 기반으로 하는 benchmark
- WebVoyager: live, online benchmark로, 15 유명한 websites를 근간으로 하는 real-world tasks로 구성됨
- WebCanvas: Mind2Web의 static environment를 개선하여 ㅐnline benchmark화한 Mind2Web-Live를 제안함
- WebArena/VisualWebArena/VideoWebArena: agent를 live website와 상화작용하는 혁신을 가져온 benchmark. Agent의 real-world variability in website를 평가하며, content의 동적인 특성을 반영함. 
  - VisualWebArena는 최신 web interface를 시각적으로 이해하는지를 평가함 (e-commerce에서 visual search, product image comparison, spatial reasoning about interace layouts 등)
- WebLINX: agent의 instruction-following 능력에 집중하며 100K human interactions (155 real-world websites)로 구성됨
- AssisantBench: 214 realistic task로 구성되며, agent가 여러 website간의 navigate하면서 synthesize 정보를 gather하는 능력을 평가
- MMInA: 여기서 더 나아가, 1,050 multimodal task로 구성되어 multiple websites간에 navigate를 하며 visual & textual 정보를 naturally compositional task를 수행하며 평가함
- GAIA: QA benchmark로, agent가 다양한 web source로부터 항해하면서 취득한 정보 기반 정답을 예측하는 능력을 평가.
- 그외에 AgentBench, AndroidBench, OSWorld, Widnows Agent Arena는 부분적으로 web task를 수행함
- ST-WebAgentBench: 최초로 회사의 정책과 safety 요구사항을 반영하는지를 중점적으로 하여 만든 benchmark

## 7.2 Web Agent Implementations

- HTML element와 직접적으로 상호작용하는 기법
- VLM을 활용하여 
  - web element를 screenshot을 이용해 grounding하는 기법
  - pixel-level로 element와 상호작용하는 기법
- Agent의 reasoning 능력 향상을 위해 prompting / tuning하는 다양한 연구 진행함
  - prompting & planning & search에 대한 연구들
    - chain-of-thought
    - tree-of-thought
    - ReAct 
    - RCI
  - training 기법들
- Web agent 개발을 위한 framework를 제안하는 연구들도 많음
