# 최종 가공 데이터 안내

이 디렉터리는 서울시 공공자전거 수요예측 프로젝트에서 생성한 최종 가공 데이터를 관리합니다.

GitHub 저장소에는 분석 결과를 확인하고 노트북을 이해하는 데 필요한 다음 두 개의 CSV 파일만 공개합니다.

```text
data/processed/
├── README.md
├── station_cluster_map.csv
└── cluster_hourly_demand.csv
```

## 1. `station_cluster_map.csv`

서울시 공공자전거 대여소의 GPS 좌표를 이용하여 K-Means 군집화를 수행한 결과입니다.

각 행은 하나의 대여소를 나타냅니다.

### 주요 열

| 열 이름                             | 설명                             |
| -------------------------------- | ------------------------------ |
| `station_id`                     | 대여소 식별자                        |
| `station_name`                   | 대여소명                           |
| `district`                       | 대여소가 위치한 자치구                   |
| `address`                        | 대여소 주소                         |
| `latitude`                       | 위도                             |
| `longitude`                      | 경도                             |
| `x_meter`                        | 거리 계산을 위해 변환한 평면 X 좌표          |
| `y_meter`                        | 거리 계산을 위해 변환한 평면 Y 좌표          |
| `cluster_id`                     | K-Means로 배정된 군집 번호             |
| `cluster_label`                  | 군집 표시 이름                       |
| `distance_to_centroid_m`         | 해당 대여소와 군집 중심 사이의 거리           |
| `rental_count`                   | 분석 기간의 대여량 합계                  |
| `meaningful_coordinate_movement` | 분석 기간 중 의미 있는 좌표 이동이 확인되었는지 여부 |
| `max_distance_m`                 | 기간별 좌표 간 최대 이동 거리              |
| `active_month_count`             | 분석 기간 중 이용정보가 확인된 개월 수         |
| `first_active_month`             | 최초 이용 확인 월                     |
| `last_active_month`              | 마지막 이용 확인 월                    |

### 활용 목적

이 파일은 다음 작업에 사용됩니다.

1. 대여소의 공간적 분포 확인
2. GPS 기반 군집 결과 시각화
3. 각 대여소와 군집의 연결 관계 확인
4. 군집별 시간당 대여 수요 집계

군집 번호는 위치 기반 K-Means 결과를 구분하기 위한 식별자입니다. 군집 번호 자체에 서열이나 우열의 의미는 없습니다.

---

## 2. `cluster_hourly_demand.csv`

대여소별 이용정보를 GPS 군집과 시간 단위로 집계한 최종 수요 데이터입니다.

각 행은 다음 분석 단위를 나타냅니다.

```text
하나의 군집 × 하나의 시간대
```

### 주요 열

| 열 이름            | 설명                   |
| --------------- | -------------------- |
| `datetime`      | 시간 단위 기준 시점          |
| `cluster_id`    | K-Means 군집 번호        |
| `cluster_label` | 군집 표시 이름             |
| `rental_count`  | 해당 군집에서 발생한 시간당 대여건수 |
| `year_month`    | 월별 점검과 요약을 위한 연월 값   |

### 데이터 규모

* 분석 기간: 2023-01-01 ~ 2024-12-31
* 시간 단위: 1시간
* 군집 수: 3개
* 전체 시간대: 17,544개
* 전체 행 수: 52,632행
* 총 대여량: 88,708,877건
* 예측 타깃: `rental_count`

전체 행 수는 다음 관계로 구성됩니다.

```text
17,544개 시간대 × 3개 군집 = 52,632행
```

---

## 3. 모델링 변수 생성

`cluster_hourly_demand.csv`에는 원천 집계 결과만 저장합니다.

LightGBM 모델에서 사용하는 다음 변수들은 모델링 노트북에서 별도로 생성합니다.

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

시차 변수는 예측 대상 시점보다 이전에 관측된 값만 사용하여 생성합니다.

---

## 4. 데이터 생성 노트북

두 최종 파일은 다음 노트북을 순서대로 실행하여 생성합니다.

```text
notebooks/01_data_quality_preprocessing.ipynb
→ notebooks/02_spatial_clustering_analysis.ipynb
```

모델 학습과 평가는 다음 노트북에서 수행합니다.

```text
notebooks/03_modeling_evaluation.ipynb
```

---

## 5. 주의사항

* 분석 단위는 개별 대여소가 아니라 GPS 기반 3개 군집입니다.
* `rental_count`는 대여 기록의 행 개수가 아니라 원천 데이터의 이용건수를 합산한 값입니다.
* 군집 번호는 공간적 그룹을 구분하는 값이며 순서나 등급을 의미하지 않습니다.
* 이 데이터에는 기상 변수가 포함되어 있지 않습니다.
* 원천 데이터 변경이나 대여소 정보 업데이트에 따라 군집 결과가 달라질 수 있습니다.
