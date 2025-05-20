---
title: "CHARTEDIT: How Far Are MLLMs From Automating Chart Analysis? Evaluating MLLMs’ Capability via Chart Editing"
---



# [Chart] CHARTEDIT: How Far Are MLLMs From Automating Chart Analysis? Evaluating MLLMs’ Capability via Chart Editing

- paper: https://arxiv.org/pdf/2505.11935
- github: https://github.com/xxlllz/ChartEdit
- ACL 2025 accepted (인용수: 0회, 2025-05-20 기준)
- downstream task: Chart-to-code generation (Chart editing) task

# 1. Motivation

- Rendering 가능한 Chart를 생성하는 일은 labor-intensive하므로, 자동화에 대한 Needs가 있다.

- 기존에 Chart rendering, chart editing task를 MLLM의 능력을 평가하는 benchmark가 부재하다

  $\to$ 고품질의 Chart editing 능력을 평가하는 benchmark를 만들어보자!

# 2. Contribution

- 1,405개의 다양한 editing instruction & 233개의 real-world charts로 구성된 CHARTEDIT benchmark를 제안함
  - source: Arxiv에서 crawling
  - diversity: 다양성 확보를 위해 19개 chart type & 6개 editing instruction을 사전에 정의함
- 10개의 mainstream MLLM을 두가지 타입의 실험으로 평가하여 결과를 정리함 (code & chart level)
  - MLLM의 능력을 평가하기 위한 **evaluation metric**을 제안 & 평가를 수행함
    - execution rate: MLLM이 생성한 code가 실행 가능한가?
    - code-level accuracy: 편집의 정확성을 유지하는가?
    - chart-level consistency: 시각화된 결과가 정답과 일관되는가?
  - 3가지 type의 MLLM에 대해 report (Proprietary, general-domain open-source, chart-domain models) $\to$ GPT-4o가 짱!
  - SOTA 모델이 59.96점을 맞고 있음을 밝히며, 해당 task처럼 정밀한 코드 수정이 어려운 task임을 보임

# 3. Related Works

## 3.1 Chart-Domain MLLMs

- ChartLlama: LLaVA기반 Chart dataset으로 finetuned.
- mPLUG-Owl / mPLUG-Owl2: 고해상도 chart image에서 좋은 성능
- ChartVLM: LLM의 개입이 필요한지 여부를 판단하는 discriminator를 도입
- TinyChart: token merging + PoT-based reasoning 전략을 통해 inference 효율성 & 이해력 향상
- ChartMoE: MoE 구조를 채택

## 3.2 MLLMs for Code

- Deepseek-coder : code generation & error fixing 능력의 진전을 보임. (text-only)
- sign2Code / Web2Code: webpage용 HTML code 생성
- RoboCodeX: robot behavior synthesis하는 code 생성
- ChartMimic/ChartVLM: Chart-to-code generation task를 수행했으나, 1가지 타입의 modification만 수행하여 다양성 부족

# 4. ChartEdit

- Overall pipeline

  ![](../images/2025-05-20/image-20250520232846608.png)

## 4.1 Chart Editing Task Definition

![](../images/2025-05-20/image-20250520233957288.png)

- *M*: MLLM 모델
- *X*: Chart 이미지 (from Archiv papers)
- *C*: 해당 Chart 이미지와 연관된 code
- *I*: 편집을 어떻게 수행해야 하는지 정의한 instruction
- *O*: 모델이 생성한 code. 해당 코드를 돌리면 chart가 랜더링 되어야 한다.

## 4.2 Data Construction

### 4.2.1 Chart Collection & Filtering

### 4.2.2 Code Annotation

### 4.2.3 Instruction Generation

## 4.3 Dataset Statistics & Analysis

# 5. Experiments