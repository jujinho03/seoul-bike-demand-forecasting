# 서울시 공공자전거 GPS 군집 기반 수요 예측

![Python](https://img.shields.io/badge/Python-Data%20Analysis-3776AB?style=flat-square&logo=python&logoColor=white)
![pandas](https://img.shields.io/badge/pandas-Data%20Processing-150458?style=flat-square&logo=pandas&logoColor=white)
![LightGBM](https://img.shields.io/badge/LightGBM-Forecasting-2E8B57?style=flat-square)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?style=flat-square&logo=jupyter&logoColor=white)

**바로가기:** [분석 노트북](notebooks/) · [분석 보고서](docs/findings.md) · [프로젝트 설명](docs/portfolio_description.md) · [최종 산출물](#9-최종-산출물)

> **문제** — 대여소별 차이를 유지하면서 다음 1시간의 지역별 공공자전거 수요를 예측했습니다.<br>
> **한 일** — 원천 79,670,647행을 검수해 공간 분석 대상 대여 88,708,877건을 구성하고, GPS로 3개 공간 군집을 만든 뒤 시간 순서를 지켜 5개 모델을 비교했습니다.<br>
> **핵심 결과** — LightGBM Macro RMSE 190.724, SARIMA 대비 58.595% 개선을 확인했습니다.<br>
> **차별점** — 대여소 ID·GPS 품질, 시차변수 누출, 단순 기준모델을 함께 검증했습니다.

## 이 프로젝트로 보여주는 역량

- 대용량 공공데이터의 품질 검수와 대여소 ID·GPS 표준화
- 공간 군집화와 미래 정보 누출을 막은 시계열 검증 설계
- 단순 기준모델부터 머신러닝까지의 비교와 운영 관점 해석

## 프로젝트 한눈에 보기

| 3분 요약 | 내용 |
|---|---|
| 문제 | 대여소별 지역 차이를 보존하면서 군집별 다음 1시간 공공자전거 대여건수를 예측했습니다. |
| 데이터 | 2023~2024년 원천 79,670,647행을 집계해 전체 대여 88,750,388건을 확인하고, GPS 검증을 통과한 2,847개 대여소의 대여 88,708,877건을 분석했습니다. |
| 설계 | 대여소 ID 표준화와 GPS 검증 → K=3 공간 군집 → 군집별 17,544시간 시계열 → 시간 순서 기반 학습·검증·테스트 → 5개 모델 비교 순으로 진행했습니다. |
| 결과 | LightGBM의 테스트 Macro RMSE/MAE는 190.724/113.054로, SARIMA보다 각각 58.595%/56.520% 낮았습니다. |
| 가장 어려웠던 점 | 행 수, 대여건수, 대여소 수, 시간 수가 서로 다른 측정 단위이므로 `88,708,877건 → 52,632행`을 단순 축약으로 오해하지 않도록 데이터 흐름을 분리해 검증했습니다. |
| 활용 범위 | 결과를 개별 대여소의 확정 수요가 아니라 3개 공간 군집의 재배치 우선순위를 정하는 보조 신호로 한정했습니다. |

## 나의 역할과 재구축 범위

| 구분 | 내용 |
|---|---|
| 프로젝트 출발점 | 6인 팀 과제 |
| 지원서 기재 프로젝트 기간 | 2025.10.17–2025.12.07 |
| 지원서에 정리한 본인 수행 | 6인 팀에서 팀원들과 이상치 정의·제거 기준을 합의하고 데이터 전처리를 담당했습니다. |
| 개인 재구축 범위 | 2023~2024년 데이터를 다시 수집·처리하고, ID 표준화, 대여건수 보존·GPS 검증, K=3 군집화, 5개 모델 비교, 시각화와 문서화를 포함해 전체 분석을 재구축했습니다. |
| 공개 Git 정리 기록 | 2026.06.28–현재 |

팀 과제에서 맡은 역할과 이후 개인 재구축 범위를 분리해 표기했습니다. 아래 결과와 공개 산출물은 개인 재구축 기준입니다.

## 1. 핵심 결과

| 항목 | 재현 결과 |
|---|---:|
| 분석 기간 | 2023-01-01 ~ 2024-12-31 |
| 원천 입력 행 수 | 79,670,647행 |
| 전체 집계 대여건수 | 88,750,388건 |
| 공간 분석 대상 대여건수 | 88,708,877건 |
| 공간 분석 대상 / 제외 대여소 | 2,847개 / 18개 |
| 최종 모델링 데이터 | 52,632행, 17,544시간 |
| GPS 기반 공간 군집 | 3개 |
| 예측 단위 | 군집별 다음 1시간 대여건수 |
| 비교 모델 | 5개 |
| 최종 우수 모델 | LightGBM |
| LightGBM Macro RMSE / MAE | 190.724 / 113.054 |
| SARIMA 대비 RMSE / MAE 개선 | 58.595% / 56.520% |
| Hourly Naive 대비 RMSE 개선 | 73.714% |

LightGBM은 직전 시간과 일·주 단위 반복 패턴을 함께 학습해 모든 비교 모델보다 낮은 오차를 보였습니다. 다만 현재 결과는 개별 대여소가 아닌 3개 공간 군집의 총수요 예측입니다.

![최종 테스트 예측 비교](outputs/figures/final_test_model_comparison_last_week.png)

## 핵심 주장과 검증 경로

| 핵심 주장 | 공개 근거 | 확인할 값 |
|---|---|---|
| 원천 행부터 모델링 행까지의 데이터 흐름 | [`portfolio_data_flow_summary.csv`](outputs/metrics/portfolio_data_flow_summary.csv) | 79,670,647행 → 대여 88,750,388건 → 공간 분석 대상 대여 88,708,877건 → 52,632행 |
| GPS 검증과 군집 시계열 정합성 | [`spatial_clustering_final_validation.csv`](outputs/metrics/spatial_clustering_final_validation.csv) | 2,847개 대여소, K=3, 군집별 17,544시간, 52,632행 |
| 시간 순서 기반 데이터 분할 | [`time_split_summary.csv`](outputs/metrics/time_split_summary.csv) | 학습 39,384행, 검증 6,624행, 테스트 6,624행 |
| 5개 모델의 최종 순위 | [`final_test_macro_ranking.csv`](outputs/metrics/final_test_macro_ranking.csv) | LightGBM RMSE/MAE 190.724/113.054 |
| SARIMA·나이브 대비 개선율 | [`final_test_improvement_summary.csv`](outputs/metrics/final_test_improvement_summary.csv) | SARIMA 대비 RMSE 58.595%, MAE 56.520%; Hourly Naive 대비 RMSE 73.714% |
| 변수 중요도 해석 | [`lightgbm_feature_importance.csv`](outputs/metrics/lightgbm_feature_importance.csv) | `lag_1`, `lag_168`, `lag_24`, `hour` 순 |
| 공통 저수요 의심 구간 | [`priority_2023_09_07_context.csv`](outputs/metrics/priority_2023_09_07_context.csv) | 2023-09-07 08:00~10:00 별도 기록 |

## 2. 분석 질문

1. 대여소 ID와 GPS 좌표는 공간 분석에 사용할 수 있도록 정합적인가?
2. 개별 대여소의 지역 차이를 유지하면서 과도한 세분화를 줄일 수 있는가?
3. 직전 시점·전일·전주 반복을 사용한 단순 기준선은 어느 정도의 성능을 보이는가?
4. 미래 정보를 사용하지 않는 1시간 앞 예측에서 SARIMA와 LightGBM은 어떻게 다른가?
5. 군집별 수요 규모 차이를 고려해 최종 모델을 어떻게 평가해야 하는가?
6. 예측 결과를 재배치 등 운영 의사결정으로 연결하려면 어떤 검증이 더 필요한가?

## 3. 데이터 품질과 누출 점검

원천 데이터는 서울시 공공자전거 시간대별 이용정보와 대여소 현황·이력입니다. 월별 이용 CSV는 용량 때문에 Git에서 제외하고, 재현에 필요한 배치 경로를 [`data/README.md`](data/README.md)에 기록했습니다.

### 원천 데이터에서 모델링 데이터까지

```text
원천 입력 79,670,647행
→ 대여소·시간 단위 집계 26,145,337행, 전체 대여 88,750,388건
→ GPS 검증: 공간 분석 대상 2,847개 대여소, 제외 18개 대여소
→ 공간 분석 대상 대여 88,708,877건, 제외 대여 41,511건
→ 공간 분석 대상 대여 비중 99.953227%
→ K=3 공간 군집
→ 군집별 17,544시간 × 3개 군집 = 최종 52,632행
```

따라서 `79,670,647`은 원천 **행 수**, `88,750,388`과 `88,708,877`은 집계된 **대여건수**, `52,632`는 군집·시간 조합의 **모델링 행 수**입니다. 단계별 수치와 단위는 [`portfolio_data_flow_summary.csv`](outputs/metrics/portfolio_data_flow_summary.csv)에 공개했습니다.

- 문자열·숫자형 대여소 ID를 하나의 형식으로 표준화
- 이용정보와 대여소 마스터 간 결합 여부 검증
- 결측·0·서울시 범위 밖 GPS 좌표를 공간 군집 대상에서 제외
- 분리 전후 행 수와 대여건수의 합계 보존 여부 기록
- 시점이 `t`인 예측에 `t` 이후 정보가 들어가지 않도록 모든 시차변수를 과거 관측치로만 생성
- 무작위 분할 대신 시간 순서 기반 학습·검증·테스트 분할 사용

`2023-09-07 08:00~10:00`에는 세 군집의 수요가 동시에 크게 하락했습니다. 외부 원인이 확인되지 않아 임의 삭제·보간하지 않고 품질 의심 구간으로 별도 기록한 뒤 원자료를 유지했습니다.

## 4. 공간 군집화와 시계열 설계

```text
원천 이용정보·대여소 정보
→ 대여소 ID 표준화·GPS 품질 점검
→ 공간 분석 대상·제외 대여소 분리
→ GPS 표준화·K-Means 후보 평가
→ K=3 공간 군집 선정
→ 군집 × 시간 대여건수 집계
→ 시계열 진단·시차변수 생성
→ 시간 순서 기반 학습·검증·테스트 분할
→ 5개 모델 학습·Macro RMSE·MAE 비교
```

K-Means의 군집 수는 Inertia, Silhouette score, 군집별 대여소 수, 공간적 분리와 해석 가능성을 함께 검토해 `K=3`으로 선정했습니다.

![GPS 기반 대여소 공간 군집](outputs/figures/spatial_station_clusters_k3.png)

| 구간 | 기간 |
|---|---|
| 학습 | 2023-01-01 00:00 ~ 2024-06-30 23:00 |
| 검증 | 2024-07-01 00:00 ~ 2024-09-30 23:00 |
| 테스트 | 2024-10-01 00:00 ~ 2024-12-31 23:00 |

모델 구조와 하이퍼파라미터는 학습·검증 구간에서 확정하고 테스트 결과를 본 뒤 변경하지 않았습니다. `lag_168`을 엄격하게 적용하면서 각 군집의 초기 168시간은 학습 행에서 제외되었습니다.

## 5. 모델 비교

| 모델 | 역할과 설계 |
|---|---|
| Hourly Naive | 직전 1시간 실제 수요를 예측값으로 사용 |
| Daily Naive | 전일 같은 시간의 실제 수요를 사용 |
| Weekly Naive | 전주 같은 요일·시간의 실제 수요를 사용 |
| SARIMA | 군집별 `(1, 0, 1) × (1, 1, 0, 24)` 모형 |
| LightGBM | 세 군집을 결합하고 `cluster_id`와 시차변수를 사용한 단일 모형 |

LightGBM 입력은 `cluster_id`, 시간·요일·월·주말 정보와 `lag_1`, `lag_2`, `lag_3`, `lag_24`, `lag_48`, `lag_168` 등 11개입니다. 최적 반복 횟수는 검증 구간의 early stopping으로 선정한 921회입니다.

## 6. 최종 테스트 성능

| 순위 | 모델 | Macro RMSE | Macro MAE |
|---:|---|---:|---:|
| 1 | LightGBM | 190.724 | 113.054 |
| 2 | SARIMA | 460.629 | 260.015 |
| 3 | Hourly Naive | 725.571 | 456.731 |
| 4 | Weekly Naive | 776.525 | 427.048 |
| 5 | Daily Naive | 865.435 | 464.011 |

| 군집 | LightGBM RMSE | LightGBM MAE |
|---|---:|---:|
| `cluster_0` | 300.230 | 174.359 |
| `cluster_1` | 131.507 | 82.274 |
| `cluster_2` | 140.436 | 82.529 |

`cluster_0`은 총수요와 변동성이 더 커 절대 오차도 높았습니다. 따라서 RMSE의 군집 간 차이를 모델 품질 차이로만 해석하지 않았습니다.

Gain 기반 중요도는 `lag_1` 65.741%, `lag_168` 18.672%, `lag_24` 7.187%, `hour` 3.943% 순이었습니다. 이 수치는 예측 기여도이며 수요를 직접 유발하는 인과효과를 의미하지 않습니다.

## 7. 운영 활용과 해석 원칙

현재 모델은 서울 전체를 하나의 수요로 합치지 않고 지역별 차이를 3개 공간 군집으로 보존합니다. 군집별 1시간 예측치는 자전거 재배치 대상 지역의 우선순위를 정하거나 피크 시간대의 운영 인력을 배치하는 보조 신호로 활용할 수 있습니다.

다만 현재 평가는 예측 오차에 한정됩니다. 실제 재배치에 적용하려면 거치대 여유량, 자전거 재고, 이동 비용, 결품·과잉 비용을 함께 반영한 운영 KPI로 별도 검증해야 합니다.

## 8. 프로젝트 구조

```text
.
├─ README.md
├─ requirements.txt
├─ config.yaml
├─ data/
│  ├─ README.md
│  ├─ raw/                         # 대용량 원본은 Git에서 제외
│  └─ processed/
│     ├─ README.md
│     ├─ station_cluster_map.csv
│     └─ cluster_hourly_demand.csv
├─ notebooks/
│  ├─ 01_data_quality_preprocessing.ipynb
│  ├─ 02_spatial_clustering_analysis.ipynb
│  └─ 03_modeling_evaluation.ipynb
├─ docs/
│  ├─ findings.md
│  ├─ portfolio_description.md
│  └─ consistency_audit.md
└─ outputs/
   ├─ README.md
   ├─ figures/
   ├─ metrics/
   │  └─ portfolio_data_flow_summary.csv
   ├─ models/                          # 재생성 가능한 대용량 모델은 Git에서 제외
   └─ predictions/
```

## 9. 최종 산출물

| 구분 | 파일 | 설명 |
|---|---|---|
| 분석 노트북 | [`01_data_quality_preprocessing.ipynb`](notebooks/01_data_quality_preprocessing.ipynb) · [`02_spatial_clustering_analysis.ipynb`](notebooks/02_spatial_clustering_analysis.ipynb) · [`03_modeling_evaluation.ipynb`](notebooks/03_modeling_evaluation.ipynb) | 품질·전처리부터 공간 군집과 모델 평가까지의 실행 경로 |
| 데이터 품질·데이터 흐름 | [`portfolio_data_flow_summary.csv`](outputs/metrics/portfolio_data_flow_summary.csv) · [`spatial_clustering_final_validation.csv`](outputs/metrics/spatial_clustering_final_validation.csv) · [`priority_2023_09_07_context.csv`](outputs/metrics/priority_2023_09_07_context.csv) | 단계별 수치·단위, GPS·시계열 정합성, 공통 저수요 의심 구간 |
| 모델 성능·시간 분할 | [`time_split_summary.csv`](outputs/metrics/time_split_summary.csv) · [`final_test_macro_ranking.csv`](outputs/metrics/final_test_macro_ranking.csv) · [`final_test_improvement_summary.csv`](outputs/metrics/final_test_improvement_summary.csv) | 시간 순서 분할과 동일 테스트 구간의 모델 순위·개선율 |
| 대표 시각화 | [`GPS 군집 지도`](outputs/figures/spatial_station_clusters_k3.png) · [`최종 테스트 비교`](outputs/figures/final_test_model_comparison_last_week.png) | 공간 군집 결과와 최종 예측 비교 |
| 해석·수치 감사 | [`findings.md`](docs/findings.md) · [`consistency_audit.md`](docs/consistency_audit.md) · [`outputs/README.md`](outputs/README.md) | 결과 해석·한계, README·CSV 간 교차검증과 산출물 색인 |

## 10. 재현 방법

### 10.1 Python 환경 만들기

저장소 최상위 폴더에서 PowerShell을 열고 다음 명령을 순서대로 실행합니다.

```powershell
python -m venv .venv
.\.venv\Scripts\python.exe -m pip install --upgrade pip
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
```

가상환경을 활성화하지 않고 전용 Python 실행파일을 직접 사용하므로 PowerShell 실행 정책의 영향을 덜 받습니다.

### 10.2 데이터 배치

[`data/README.md`](data/README.md)의 파일명과 폴더 구조에 맞춰 서울시 공공데이터 원본을 `data/raw/` 아래에 배치합니다. 원본은 용량과 재배포 관리를 위해 저장소에 포함하지 않습니다.

### 10.3 노트북 실행

```powershell
.\.venv\Scripts\python.exe -m jupyter lab
```

JupyterLab에서 `01_data_quality_preprocessing.ipynb` → `02_spatial_clustering_analysis.ipynb` → `03_modeling_evaluation.ipynb` 순으로 실행합니다. 앞 단계의 출력을 다음 단계에서 입력으로 사용하므로 순서를 유지해야 합니다.

재현 기준 환경은 Python 3.13.5, `random_state=42`, `requirements.txt`의 패키지 버전과 프로젝트 상대경로입니다.

## 11. 한계와 개선 방향

- 공간 단위가 개별 대여소가 아닌 3개 군집으로 단순화되어 있습니다.
- 테스트 기간은 2024년 4분기로 한정되어 있습니다.
- 공휴일·행사·미래 기상예보와 같은 외부 변수를 사용하지 않았습니다.
- 군집별 수요 규모가 달라 절대 오차를 직접 비교할 때 주의가 필요합니다.
- `2023-09-07 08:00~10:00`의 공통 저수요 현상에 대한 외부 원인은 확인하지 못했습니다.
- Gain 기반 변수 중요도는 인과효과를 의미하지 않습니다.

자세한 분석 결과와 해석 주의사항은 [`docs/findings.md`](docs/findings.md), 산출물 별 설명은 [`outputs/README.md`](outputs/README.md)에서 확인할 수 있습니다.

## 12. 결론

대용량 원천 데이터를 검수하고 GPS 공간 군집과 시간 순서 기반 검증을 연결했습니다. LightGBM은 동일한 테스트 기간에서 단순 기준모델과 SARIMA보다 낮은 오차를 보였지만, 결과를 3개 군집의 재배치 우선순위 보조 신호로 한정했습니다. 실제 운영 적용에는 자전거 재고·거치대 여유량·이동 비용을 포함한 추가 검증이 필요합니다.
