---
title: "ScribeAgent: Towards Specialized Web Agents Using Production-Scale Workflow Data"
---



# [WebAgent] ScribeAgent: Towards Specialized Web Agents Using Production-Scale Workflow Data

- paper: https://arxiv.org/pdf/2411.15004
- github: https://github.com/colonylabs/ScribeAgent
- archived (인용수: 9회, 25-05-25 기준)
- downstream task: Web-based Task Automation(Mind2Web, WebArena, Scribe)

# 1. Motivation

- 복잡한 Web기반 task를 LLM Agent로 자동화하는 연구가 GPT-4와 같은 proprietary model을 기반 발전하고 있음

- 이러한 general-purpose LLM은 HTML 혹은 accessibility tree기반의 specialized web contexts를 이해하는데는 한계가 있음

  $\to$ Opensource 기반 finetuning을 통해 HTML을 잘 이해하는 LLM agent를 만들어보자!

# 2. Contribution

- 여러개의 Opensource LLM을 가지고 real-world workflow dataset으로 finetuning

  ![](../images/2025-05-25/image-20250525231427936.png)

  - Scribe 웹에서 real-world dataflow를 user의 data로 수집함
    - 250 domains + 10,000 subdomains
      - raw HTML-DOM (website)
      - documentation of actions (+ 자연어 기반 설명)
      - target HTML element
  - LoRA기반 next action prediction task로 해당 데이터셋을 reformatting $\to$ 6B tokens

- Single-stage LLM agent인 ScribeAgent를 제안함

  - inputs
    - previous actions
    - website의 DOM
  - output
    - next action

- 여러 Web automation benchmark에서 SOTA

# 3. ScribeAgent

## 3.1 General Setup

- $h_t=H(o_{1:t-1}, a_{1:t-1})$: t-1 step까지의 action과 observation (html)을 history로 정의
- $a_t=L(q,o_t,h_t)$: t step의 history, observation, 그리고 user instruction *q*를 기반으로 next action을 예측
- $s_{t+1}=T(s_t, a_t)$: 환경에 action이 반영되어 상태가 변환(transform)됨 $\to$ 미리캔버스로 치면 **html2xml**

## 3.2 ScribeAgent: Specializing Web Agents Through Fine-tuning

- Scribe: Web 기반 작업의 step-by-step 가이드를 생성하는 웹사이트. 해당 사이트를 통해서 독점적인 user의 record와 상호작용을 잘 라벨링된 instruction으로 변환하여 데이터를 수집함
  - ex. Custermoer Relationship Management (CRM) tool  (HubSpot, Salesforce), productivity tools (Notion, Calendley), social platforms (ex. Facebook, LinkedIn), shopping sites (ex. Amazon, Sopify), etc

### 3.2.1 Collecting Production-scale Data

- 각 데이터는 "high-level user task"가 잘 정의되어 있음
  - ex. "Invite someone to manage Facebook ad accounts", "add a user in a Salesforce"
  - label
    - web page's URL
    - raw HTML-DOM
    - 수행된 action에 대한 자연어 기반 설명
    - action의 종류
      - *mouse_click_action*: 특정 element를 클릭
      - *keyboard_sequence_action*: 특정 element에 sequence of characters를 타이핑
      - *keyboard_combination_action*: 여러 key 조합을 동시에 누름 (ex. ctrl+c)
    - 수행된 action의 target HTML element (CSS selector로 자동생성된다고 함)
  - Non-english, Non css selected element는 제거됨
  - 해당 데이터셋은 2개월간 취득하여 250 domain + 10,000 subdomain + 평균 11 steps로, 약 6B token만큼 취득됨
  - User privacy이슈로 공개되지 않을 예정

### 3.2.2 Preprocessing

### 3.2.3 Finetuning with LoRA

### 3.2.4 Exploring the Design Space

###  

# 4. Experiments