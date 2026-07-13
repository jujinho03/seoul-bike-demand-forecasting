# 분석 결과 안내

이 디렉터리는 서울시 공공자전거 GPS 군집 기반 1시간 수요예측 프로젝트에서 생성한 대표 시각화와 핵심 평가 결과를 관리합니다.

공개 GitHub 저장소에는 분석 과정과 최종 결론을 설명하는 데 필요한 대표 그래프 10개와 핵심 지표 15개만 포함합니다. 전체 예측값, 학습된 모델, 상세 감사표와 중간 산출물은 노트북을 실행하면 다시 생성할 수 있으므로 공개 대상에서 제외합니다.

## 1. 공개 디렉터리 구성

```text
outputs/
├── README.md
├── figures/
│   ├── final_test_model_comparison_last_week.png
│   ├── integrated_hourly_demand_by_cluster.png
│   ├── integrated_monthly_demand_by_cluster.png
│   ├── integrated_raw_demand_acf.png
│   ├── integrated_weekday_weekend_demand_by_cluster.png
│   ├── lightgbm_validation_last_week.png
│   ├── sarima_validation_last_week.png
│   ├── spatial_cluster_inertia_by_k.png
│   ├── spatial_cluster_silhouette_by_k.png
│   └── spatial_station_clusters_k3.png
└── metrics/
    ├── final_model_config.csv
    ├── final_sarima_fit_summary.csv
    ├── final_test_improvement_summary.csv
    ├── final_test_macro_ranking.csv
    ├── final_test_metrics.csv
    ├── lightgbm_feature_importance.csv
    ├── lightgbm_validation_metrics.csv
    ├── naive_baseline_metrics.csv
    ├── portfolio_data_flow_summary.csv
    ├── priority_2023_09_07_context.csv
    ├── sarima_candidate_macro_summary.csv
    ├── spatial_cluster_candidate_metrics.csv
    ├── spatial_clustering_final_summary.csv
    ├── spatial_clustering_final_validation.csv
    └── time_split_summary.csv
```

노트북을 실행하면 로컬 환경에는 다음 디렉터리와 추가 중간 파일도 생성됩니다.

```text
outputs/
├── models/
├── predictions/
├── reports/
├── figures/       # 중간 그래프 포함
└── metrics/       # 상세 감사표와 중간 지표 포함
```

이 파일들은 재현 가능한 실행 결과이므로 `.gitignore`를 통해 공개 GitHub 저장소에서는 제외합니다.

---

## 2. 대표 시각화

### 2.1 공간 군집화

| 파일                                    | 설명                                         |
| ------------------------------------- | ------------------------------------------ |
| `spatial_cluster_inertia_by_k.png`    | 군집 수 K에 따른 군집 내 거리 합의 변화를 비교합니다.           |
| `spatial_cluster_silhouette_by_k.png` | 군집 수 K에 따른 실루엣 점수를 비교합니다.                  |
| `spatial_station_clusters_k3.png`     | 최종 선택한 K=3 군집의 서울시 공공자전거 대여소 공간 분포를 보여줍니다. |

최종 군집 수는 하나의 지표만으로 결정하지 않고 inertia, silhouette, 군집 규모, 공간적 분리와 해석 가능성을 함께 고려하여 결정했습니다.

### 2.2 군집별 수요 패턴

| 파일                                                 | 설명                            |
| -------------------------------------------------- | ----------------------------- |
| `integrated_hourly_demand_by_cluster.png`          | 군집별 평균 시간대 수요 패턴을 보여줍니다.      |
| `integrated_monthly_demand_by_cluster.png`         | 군집별 월별 평균 수요 변화를 보여줍니다.       |
| `integrated_weekday_weekend_demand_by_cluster.png` | 평일과 주말의 군집별 시간대 수요 차이를 보여줍니다. |

이 그래프들은 군집마다 수요 규모와 시간적 이용 패턴이 서로 다르다는 점을 확인하는 데 사용했습니다.

### 2.3 시계열 진단

| 파일                              | 설명                         |
| ------------------------------- | -------------------------- |
| `integrated_raw_demand_acf.png` | 군집별 시간 수요의 자기상관 구조를 보여줍니다. |

수요 시계열에서 직전 시간과의 단기 연속성, 전일 같은 시간의 반복성, 전주 같은 요일·시간의 반복성을 확인했습니다. 이 결과를 바탕으로 SARIMA의 24시간 계절 주기와 LightGBM의 시차 변수를 구성했습니다.

### 2.4 모델 검증과 테스트

| 파일                                          | 설명                                                 |
| ------------------------------------------- | -------------------------------------------------- |
| `sarima_validation_last_week.png`           | 검증 기간 마지막 1주의 실제 수요와 SARIMA 예측값을 비교합니다.            |
| `lightgbm_validation_last_week.png`         | 검증 기간 마지막 1주의 실제 수요와 LightGBM 예측값을 비교합니다.          |
| `final_test_model_comparison_last_week.png` | 테스트 기간 마지막 1주에서 실제 수요와 SARIMA·LightGBM 예측값을 비교합니다. |

대표 그래프는 일부 기간의 예측 형태를 직관적으로 확인하기 위한 자료입니다. 최종 모델 평가는 전체 테스트 기간에 대해 계산한 군집별 RMSE·MAE와 세 군집의 Macro Average를 기준으로 수행했습니다.

---

## 3. 핵심 평가 결과

### 3.1 공간 군집화 결과

| 파일                                        | 설명                                        |
| ----------------------------------------- | ----------------------------------------- |
| `spatial_cluster_candidate_metrics.csv`   | K 후보별 inertia, silhouette와 군집 규모 관련 평가 결과 |
| `spatial_clustering_final_summary.csv`    | 최종 K=3 군집의 대여소 수와 수요 규모 요약                |
| `spatial_clustering_final_validation.csv` | 최종 군집화 결과의 행 수, 기간, 군집 매핑과 수요 보존 여부 검증 결과 |

### 3.2 데이터 품질 점검

| 파일                                | 설명                                             |
| --------------------------------- | ---------------------------------------------- |
| `portfolio_data_flow_summary.csv` | 원천 행, 집계 대여건수, 공간 분석 대상, 최종 모델링 행의 단계별 단위와 수치를 요약한 공개 근거 |
| `priority_2023_09_07_context.csv` | 2023년 9월 7일 오전의 비정상적인 공통 저수요 구간과 전후 맥락을 확인한 결과 |

`2023-09-07 08:00~10:00`에는 세 군집의 수요가 조건부 정상 수준의 약 1% 이하로 동시에 하락한 뒤 11시부터 회복했습니다.

해당 현상은 우선 확인이 필요한 데이터 품질 의심 구간으로 기록했습니다. 확인되지 않은 원인을 단정하거나 값을 임의로 보간·삭제하지 않고 원자료를 유지한 상태로 분석에 포함했습니다.

### 3.3 시계열 분할과 기준모델

| 파일                           | 설명                                         |
| ---------------------------- | ------------------------------------------ |
| `time_split_summary.csv`     | 학습·검증·테스트 기간과 관측치 수 요약                     |
| `naive_baseline_metrics.csv` | Daily Naive와 Weekly Naive의 군집별·Macro 검증 성능 |

데이터는 시간 순서를 유지하여 다음과 같이 분할했습니다.

| 구간  | 기간                      |
| --- | ----------------------- |
| 학습  | 2023-01-01 ~ 2024-06-30 |
| 검증  | 2024-07-01 ~ 2024-09-30 |
| 테스트 | 2024-10-01 ~ 2024-12-31 |

시계열의 미래 정보를 과거 학습 구간에 포함할 수 있는 무작위 분할은 적용하지 않았습니다.

### 3.4 SARIMA와 LightGBM 검증

| 파일                                   | 설명                               |
| ------------------------------------ | -------------------------------- |
| `sarima_candidate_macro_summary.csv` | 두 SARIMA 후보 설정의 수렴 상태와 검증 결과 요약  |
| `lightgbm_validation_metrics.csv`    | LightGBM의 군집별·Macro 검증 RMSE와 MAE |
| `lightgbm_feature_importance.csv`    | 최종 LightGBM 입력 변수의 gain 기반 중요도   |

최종 SARIMA 설정은 다음과 같습니다.

```text
order = (1, 0, 1)
seasonal_order = (1, 1, 0, 24)
```

LightGBM은 세 군집의 데이터를 결합한 하나의 모델로 학습했으며 다음 11개 변수를 사용했습니다.

```text
cluster_id
hour
weekday
month
is_weekend
lag_1
lag_2
lag_3
lag_24
lag_48
lag_168
```

검증 단계에서 선택된 LightGBM의 최적 반복 횟수는 `921`회입니다.

변수 중요도는 각 변수가 예측 과정에 기여한 정도를 나타내며, 해당 변수가 수요 변화를 직접 일으켰다는 인과관계를 의미하지 않습니다.

### 3.5 최종 모델 설정과 테스트 평가

| 파일                                   | 설명                                     |
| ------------------------------------ | -------------------------------------- |
| `final_model_config.csv`             | 검증 단계에서 확정한 최종 SARIMA·LightGBM 설정      |
| `final_sarima_fit_summary.csv`       | 군집별 최종 SARIMA 수렴 상태, 학습 시간과 음수 예측값 수   |
| `final_test_metrics.csv`             | 다섯 모델의 군집별·Macro 테스트 RMSE와 MAE         |
| `final_test_macro_ranking.csv`       | 세 군집의 Macro Average를 기준으로 정렬한 최종 모델 순위 |
| `final_test_improvement_summary.csv` | LightGBM의 SARIMA 및 나이브 기준모델 대비 성능 개선율  |

---

## 4. 최종 테스트 성능

아래 수치는 세 군집의 RMSE와 MAE를 동일한 비중으로 평균한 Macro Average입니다.

| 순위 | 모델           | Macro RMSE | Macro MAE |
| -: | ------------ | ---------: | --------: |
|  1 | LightGBM     |    190.724 |   113.054 |
|  2 | SARIMA       |    460.629 |   260.015 |
|  3 | Hourly Naive |    725.571 |   456.731 |
|  4 | Weekly Naive |    776.525 |   427.048 |
|  5 | Daily Naive  |    865.435 |   464.011 |

LightGBM은 SARIMA와 비교하여 다음과 같이 오차를 줄였습니다.

* RMSE 개선율: `58.595%`
* MAE 개선율: `56.520%`

LightGBM의 군집별 테스트 RMSE는 다음과 같습니다.

| 군집          |    RMSE |
| ----------- | ------: |
| `cluster_0` | 300.230 |
| `cluster_1` | 131.507 |
| `cluster_2` | 140.436 |

`cluster_0`은 다른 군집보다 수요 규모와 변동성이 커 절대 오차인 RMSE도 상대적으로 높게 나타났습니다.

최종 테스트 기간은 다음과 같습니다.

```text
2024-10-01 00:00:00
~
2024-12-31 23:00:00
```

이 기간은 모델 구조, 입력 변수와 하이퍼파라미터 선택에 사용하지 않은 최종 평가 구간입니다.

---

## 5. 결과 해석 시 주의사항

* 예측 단위는 개별 대여소가 아니라 GPS 좌표를 이용해 구성한 공간 군집입니다.
* 예측 목표는 각 군집의 다음 1시간 공공자전거 대여건수입니다.
* LightGBM은 세 군집의 데이터를 하나로 결합한 단일 모델입니다.
* SARIMA는 `statsmodels`의 `SARIMAX` 클래스로 구현했지만 외생변수를 입력하지 않았으므로 실제 모델 구조는 SARIMA입니다.
* 미래 실측 기상값을 입력 변수로 사용하면 실제 예측 시점에 알 수 없는 정보를 이용하는 누출이 발생할 수 있습니다.
* 별도의 미래 기상예보 데이터는 최종 입력 데이터에 포함하지 않았으므로 기상 변수는 최종 모델에서 제외했습니다.
* LightGBM 변수 중요도는 예측 기여도이며 인과관계의 근거가 아닙니다.
* `2023-09-07 08:00~10:00`의 공통 저수요 현상은 데이터 품질 의심 구간으로만 기록했으며 원인을 단정하지 않았습니다.
* 대표 그래프는 일부 기간만 보여주므로 최종 성능은 전체 테스트 기간을 평가한 CSV 지표를 기준으로 확인해야 합니다.

---

## 6. 권장 확인 순서

프로젝트의 결과는 다음 순서로 확인하는 것을 권장합니다.

```text
1. K 후보별 공간 군집 평가
2. 최종 K=3 대여소 군집 지도
3. 군집별 시간·월·평일/주말 수요 패턴
4. 자기상관과 주요 반복 주기
5. 시간 순서 기반 데이터 분할과 나이브 기준모델
6. SARIMA 후보 비교
7. LightGBM 검증 성능과 변수 중요도
8. 최종 테스트 모델 순위와 개선율
```

전체 분석 과정과 코드는 프로젝트 루트의 `README.md`와 `notebooks/` 디렉터리에서 확인할 수 있습니다.
