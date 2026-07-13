# 공개 포트폴리오 일관성 감사

감사 기준일: 2026-07-13

이 문서는 README의 핵심 수치, 역할 표기, 공개 산출물 범위가 서로 일치하는지 빠르게 확인하기 위한 공개 점검표입니다.

## 1. 핵심 수치 점검

| 점검 항목 | README 표기 | 공개 근거 | 상태 |
|---|---:|---|---|
| 원천 입력 행 수 | 79,670,647행 | [`portfolio_data_flow_summary.csv`](../outputs/metrics/portfolio_data_flow_summary.csv) | 일치 |
| 전체 집계 대여건수 | 88,750,388건 | [`portfolio_data_flow_summary.csv`](../outputs/metrics/portfolio_data_flow_summary.csv) | 일치 |
| 공간 분석 대상 대여건수 | 88,708,877건 | [`spatial_clustering_final_validation.csv`](../outputs/metrics/spatial_clustering_final_validation.csv) | 일치 |
| 공간 분석 대상 대여소 | 2,847개 | [`spatial_clustering_final_validation.csv`](../outputs/metrics/spatial_clustering_final_validation.csv) | 일치 |
| 최종 모델링 데이터 | 52,632행 = 17,544시간 × 3개 군집 | [`spatial_clustering_final_validation.csv`](../outputs/metrics/spatial_clustering_final_validation.csv) | 일치 |
| LightGBM Macro RMSE / MAE | 190.724 / 113.054 | [`final_test_macro_ranking.csv`](../outputs/metrics/final_test_macro_ranking.csv) | 일치 |
| SARIMA 대비 RMSE / MAE 개선 | 58.595% / 56.520% | [`final_test_improvement_summary.csv`](../outputs/metrics/final_test_improvement_summary.csv) | 일치 |

`79,670,647`은 원천 행 수이고, `88,750,388`과 `88,708,877`은 집계 대여건수이며, `52,632`는 군집·시간 조합의 모델링 행 수입니다. 서로 다른 단위를 하나의 데이터 건수로 혼용하지 않았습니다.

## 2. 역할과 기간 표기

- 원 프로젝트는 6인 팀 과제이며, 지원서 기재 기간은 2025.10.17–2025.12.07입니다.
- 팀 과제에서 맡은 범위는 이상치 정의·제거 기준 합의와 데이터 전처리입니다.
- 데이터 재수집부터 공간 군집화, 모델 비교, 시각화, 문서화까지의 공개 결과는 이후 개인 재구축 범위로 구분했습니다.
- 공개 Git 정리 기록은 2026.06.28부터이며, 팀 과제 기간과 혼용하지 않았습니다.

## 3. 공개 범위 점검

- 공개 저장소에는 README, 재현 노트북 3개, 대표 그래프, 핵심 지표 CSV, 결과 해석 문서만 유지합니다.
- 대용량 원천 데이터와 재생성 가능한 모델·전체 예측값은 저장소에서 제외하고 [`data/README.md`](../data/README.md)에 재현 경로를 기록했습니다.
- 지원서, 면접 준비 문서, 개인 연락처가 포함된 문서는 이 저장소에 공개하지 않습니다.
- 예측 성능은 전체 테스트 구간 CSV를 기준으로 확인하며, 대표 그래프 한 장만으로 성능을 판단하지 않습니다.

## 4. 해석 경계

- 결과는 개별 대여소가 아닌 3개 공간 군집 단위입니다.
- LightGBM 변수 중요도는 예측 기여도이며 인과효과가 아닙니다.
- 운영 적용 전에는 재고, 거치대 여유량, 이동 비용을 반영한 별도 KPI 검증이 필요합니다.
