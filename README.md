# 🎮 Steam 게임 추천 시스템
### 하이브리드 접근법: LightGCN (그래프 신경망) + LLM (거대 언어 모델)

[![Python](https://img.shields.io/badge/Python-3.8+-3776AB?style=flat&logo=python&logoColor=white)](https://www.python.org/)
[![PyTorch](https://img.shields.io/badge/PyTorch-EE4C2C?style=flat&logo=pytorch&logoColor=white)](https://pytorch.org/)
[![OpenAI](https://img.shields.io/badge/OpenAI-GPT--4-412991?style=flat&logo=openai&logoColor=white)](https://openai.com/)

---

## 🚀 프로젝트 개요
본 저장소는 개인화된 Steam 게임 추천을 제공하기 위한 하이브리드 추천 시스템을 포함하고 있습니다. **LightGCN**의 구조적 학습 능력과 **LLM (OpenAI GPT)**의 시맨틱 추론 능력을 결합하여, 기존 협업 필터링의 한계점인 **롱테일 문제**와 **데이터 희소성**을 해결하는 모델을 제안합니다.

### 주요 특징
- **📂 동적 번들링 (Dynamic Bundling)**: TF-IDF와 제약 조건 기반 K-Means 클러스터링을 활용해 유사한 게임들을 '번들' 단위로 그룹화하여 추천 복잡도를 낮췄습니다.
- **🧠 그래프 신경망 (GNN)**: LightGCN 모델을 통해 유저의 선호도를 번들 수준에서 예측합니다.
- **✨ LLM 재정렬 (Re-ranking)**: OpenAI 모델을 활용하여, 추천된 번들 내의 개별 게임들을 유저의 과거 플레이 이력과 게임 메타데이터를 기반으로 정교하게 재정렬합니다.

---

## 📁 저장소 구조

| 노트북 파일 | 주요 목표 | 핵심 방법론 |
| :--- | :--- | :--- |
| [**1_Bundle_LLM**](1_SteamRec_BundlewithLLM.ipynb) | 유사도 기반 번들링 | TF-IDF + 코사인 유사도 |
| [**2_Constrained_Clustering**](2_SteamRec_BundlewithLLM_ConstrainedClustering.ipynb) | 균형 잡힌 번들링 | 제약 K-Means 클러스터링 |
| [**3_Baseline_Separate**](3_SteamRec_SeparateItems.ipynb) | 아이템 레벨 베이스라인 | 순수 LightGCN (번들링 미적용) |

---

## 🛠️ 연구 방법론

### 1. 데이터 전처리
- **콘텐츠 속성 활용**: 게임의 `Genre(장르)`와 `Tags(태그)` 텍스트를 결합하여 TF-IDF 임베딩을 생성했습니다.
- **암시적 피드백 (Implicit Feedback)**: 플레이 시간 데이터를 로그 스케일 변환 및 Min-Max 정규화를 거쳐 0~1 사이의 평점으로 변환하였습니다.

### 2. 번들 구성
- **번들링의 필요성**: 개별 아이템 추천 방식은 인기 게임에만 치중될 우려가 있습니다. 번들 단위 접근은 LLM이 정교하게 재정렬할 수 있는 풍부하고 다양한 후보군을 제공합니다.
- **제약 K-Means**: 각 번들이 너무 크거나 작아지지 않도록 크기를 일정하게 유지하여 학습의 안정성을 확보했습니다.

### 3. 그래프 학습 (LightGCN)
- **아키텍처**: 유저와 번들을 노드로 하는 이분 그래프(Bipartite Graph) 상에서 메시지 패싱(Message Passing)을 통해 임베딩을 학습합니다.
- **최적화**: MSE Loss 대신 랭킹 성능이 우수한 **BPR (Bayesian Personalized Ranking) Loss**와 **Negative Sampling**을 적용하였습니다.

### 4. LLM 재정렬
- **프롬프트 엔지니어링**: 유저의 상위 5개 플레이 게임과 LightGCN이 추천한 번들 내 후보 게임들을 입력값으로 제공합니다.
- **맥락 인식**: GPT 모델이 장르, 테마, 가격 등을 종합적으로 분석하여 최종적인 리스트를 도출합니다.

---

## 📈 실험 결과 요약
- **BPR Loss** 적용 시 일반 MSE Loss 대비 추천 정밀도(Precision)가 크게 향상됨을 확인하였습니다.
- **Negative Sampling**은 모델의 변별력을 높이는 데 핵심적인 역할을 수행하였습니다.
- **번들링 + LLM** 결합 방식은 아이템 기반의 베이스라인 대비 연산 효율성과 추천의 다양성 측면에서 우수한 수치를 기록하였습니다.

---

## ⚙️ 주요 요구 사양
- `torch`, `pandas`, `numpy`, `scikit-learn`
- `k-means-constrained`, `openai`

> [!IMPORTANT]
> LLM 재정렬 기능을 실행하려면 해당 셀의 플레이스홀더에 본인의 OpenAI API 키를 입력해야 합니다.

---
© 2026 RecSys Project - Back JongHwan
