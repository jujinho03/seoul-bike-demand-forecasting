# 원본 데이터 준비

월별 공공자전거 이용 CSV는 개별 파일의 용량이 수백 MB에 이르므로 GitHub 저장소에 포함하지 않습니다.

1. 서울시 공공데이터에서 2023년 1월~2024년 12월 `서울특별시 공공자전거 이용정보(시간대별)`를 내려받습니다.
2. `서울시 공공자전거 대여소 위치정보`와 2023·2024년 대여소 이력 파일을 준비합니다.
3. 압축을 해제하고 원본 구조를 수정하지 않은 채 다음 경로에 배치합니다.

```text
data/raw/
├─ bike_usage/       # 월별 시간대별 이용 CSV
├─ station/          # 최신 대여소 마스터
└─ station_history/  # 2023·2024년 대여소 이력
```

현재 최종 모델은 기온·강수량·풍속 등 기상 변수를 사용하지 않습니다. 미래 실측 날씨를 입력에 포함하는 정보 누출을 피하고 모든 비교 모델을 동일한 조건에서 평가하기 위한 선택입니다.

원본 배치 후 다음 노트북을 실행하면 ID·좌표·스키마를 점검하고 처리 데이터를 생성합니다.

```text
notebooks/01_data_quality_preprocessing.ipynb
→ notebooks/02_spatial_clustering_analysis.ipynb
```

최종 공개 데이터는 `data/processed/station_cluster_map.csv`와 `data/processed/cluster_hourly_demand.csv`입니다. 컬럼과 생성 기준은 [`processed/README.md`](processed/README.md)에서 확인할 수 있습니다.
