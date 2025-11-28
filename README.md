# 미세먼지 중심의 의료 취약 지역 재정의

## 📄 프로젝트 문서 (PDF)
👉 [프로젝트 설명서 PDF](docs/data-mining-project.pdf)
데이터 마이닝 기법을 활용하여 미세먼지 농도와 유해시설, 의료기관 접근성을 종합적으로 고려한 의료 취약 지역 분석 프로젝트입니다.

## 프로젝트 개요

기존 의료 취약 지역 선정 기준에 **미세먼지 농도**와 **유해시설 분포**를 추가하여, 환경적 요인을 고려한 새로운 의료 취약 지역을 재정의합니다.

### 주요 특징

- **다차원 분석**: 미세먼지, 유해시설, 의료기관 접근성(인구당/면적당)을 종합 분석
- **머신러닝 기반**: Random Forest로 유해시설 가중치 산출, K-means로 지역 군집화
- **시각화**: Folium을 활용한 대한민국 시군구 단위 인터랙티브 지도 생성
- **예측 모델**: XGBoost로 의료 취약 확률 예측

## 분석 파이프라인

### 1. 유해시설 점수 산출

- **타겟 생성**: 미세먼지 상위 50% 지역 추출
- **가중치 학습**: Random Forest Regressor로 유해시설 항목별 중요도 계산
  - 사업장 수/배출구 수/방지시설 수/배출시설 수 (1종/2종/3종 각각)
- **점수 계산**: 12개 항목에 학습된 가중치를 적용하여 유해시설점수 산출

### 2. 클러스터링 분석

- **변수 정규화**: 4개 변수를 0-100 스케일로 표준화
  - `pm_score`: 미세먼지 농도
  - `hazard_score`: 유해시설 점수
  - `area_score`: 면적당 의료기관 수 (역수)
  - `pop_score`: 인구당 의료기관 수 (역수)
- **K-means**: 4개 군집으로 지역 분류

### 3. 군집 해석
<img width="1300" height="700" alt="스크린샷 2025-11-29 오전 12 22 37" src="https://github.com/user-attachments/assets/9d113932-9bdf-4580-bc04-79b9edcb89e6" />

| 군집 | 특징 | 대표 지역 |
|------|------|-----------|
| **군집 A** | 높은 미세먼지 + 높은 유해시설 | 울산, 평택, 여수, 구미, 안산 등 |
| **군집 B** | **높은 미세먼지 + 낮은 의료접근성** | 수도권, 광역시 외곽 지역 (총 63개) |
| **군집 C** | 높은 인구밀도 + 양호한 의료접근성 | 서울, 부천 |
| **군집 D** | 낮은 미세먼지 + 양호한 의료접근성 | 농촌 및 산간 지역 |

**군집 B**를 **미세먼지 중심의 의료 취약 지역**으로 정의합니다.

### 4. 기존 기준과 비교
<img width="1300" height="700" alt="스크린샷 2025-11-29 오전 12 23 23" src="https://github.com/user-attachments/assets/974ad8fb-2859-401b-899d-5fa5df799e98" />

기존 의료취약점수(0.515점 미달 기준) 대비, 새롭게 발견된 의료 취약 지역:

- 경기: 동두천, 안성, 포천
- 충북: 충주, 제천, 증평, 음성
- 충남: 서산, 당진
- 강원: 춘천, 강릉
- 전북: 군산, 익산

<img width="1300" height="700" alt="스크린샷 2025-11-29 오전 12 23 53" src="https://github.com/user-attachments/assets/786f1496-894d-4d75-8f13-669dc61a0295" />

### 5. 예측 모델

- **알고리즘**: XGBoost Classifier
- **클래스 불균형 처리**: SMOTE 오버샘플링
- **출력**: 0-100% 의료 취약 확률

## 데이터셋

### LatLng

- **설명**: 행정구역시군구_경계
- **다운로드**: https://drive.google.com/drive/folders/1N-fPoLz3qM2ozBbYCdtk6FhaCt4RX1iM?usp=drive_link
- **출처**: https://www.data.go.kr/data/15125045/fileData.do?recommendDataYn=Y#

### hazard_facility

- **설명**: 시군구 단위 대기 배출원
- **출처**: https://www.data.go.kr/data/15068820/fileData.do

### hospital_per_area

- **설명**: 면적 당 병원 수

### hospital_per_pop

- **설명**: 인구 당 병원 수

### tmp_score_result

- **설명**: 기존 선행연구 바탕의 의료취약점수

### WhoMergedPm

- **설명**: 월별 미세먼지 농도 데이터

## 실행 방법

### 필요 라이브러리

```bash
pip install pandas numpy scikit-learn xgboost imbalanced-learn folium geopandas matplotlib
```

### Jupyter Notebook 실행

```bash
jupyter notebook main.ipynb
```

## 주요 결과물

- `cluster_map.html`: 4개 군집별 시군구 지도
- `medical_vulnerability_map.html`: 기존 의료취약점수 시각화
- `new_region.html`: 새롭게 발견된 의료 취약 지역 하이라이트

## 프로젝트 구조

```
Data-mining-Project/
├── main.ipynb                  # 전체 분석 파이프라인
├── README.md
├── WhoMergedPm.csv            # 미세먼지 데이터
├── hazard_facility.csv        # 유해시설 데이터
├── hospital_per_area.csv      # 면적당 병원 수
├── hospital_per_pop.csv       # 인구당 병원 수
├── tmp_score_result.csv       # 기존 의료취약점수
└── LatLng/                    # 행정구역 경계 shp 파일
```

## 기술 스택

- **데이터 분석**: Pandas, NumPy
- **머신러닝**: scikit-learn, XGBoost
- **클래스 불균형**: SMOTE (imbalanced-learn)
- **시각화**: Folium, Matplotlib
- **공간 데이터**: GeoPandas

## 주요 인사이트

1. **환경-건강 통합 접근**: 기존 의료접근성 지표에 미세먼지와 유해시설을 추가하여 환경 건강 위험까지 고려
2. **수도권 외곽 지역 주목**: 대도시 인근이지만 높은 미세먼지로 인한 잠재적 의료 수요 증가 지역 발견
3. **데이터 기반 정책**: 머신러닝 기반 점수화로 객관적인 의료 취약 지역 선정 기준 제시
