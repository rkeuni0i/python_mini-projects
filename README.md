# 07. Mini Projects & Comprehensive Practice

한 노트북 안에서 여러 주제(NumPy, pandas, 시각화, 웹 스크래핑)를 함께 다루거나, 실제 공개 데이터로 분석을 진행한 종합 실습/미니 프로젝트 모음입니다.

## 학습 내용

- 여러 주차 동안 배운 NumPy/판다스/시각화/스크래핑 내용을 한 노트북에 모아 복습
- 여러 데이터셋(영화 박스오피스, 제품 판매, 성적, 기온, 분양가)을 한 세션에서 연속으로 분석
- 온실가스 배출량 데이터와 에너지 소비 통계를 병합해 산업별 탄소집약도 분석
- 검색 결과 페이지를 스크래핑해 최신 통계 수치를 가져와 시각화에 활용

## 실습 파일

| 파일 | 학습 내용 |
|---|---|
| `01_cumulative_review_numpy_pandas_scraping.ipynb` | NumPy 기초 → pandas Series/DataFrame → 웹 스크래핑까지 이어지는 대용량 누적 복습 노트북(148개 셀) |
| `02_scraping_and_multi_dataset_analysis.ipynb` | 환율 스크래핑 + 박스오피스/제품/성적/기온/분양가 데이터 분석을 한 세션에서 연습 |
| `03_mixed_topics_practice_session.ipynb` | BMI 계산(NumPy) + 배우 DataFrame + 여행사 데이터 정제/시각화 + 환율 스크래핑을 한 노트북에서 실습 |
| `04_ghg_emissions_search_scraping_and_chart.ipynb` | 온실가스 배출량 데이터 전처리 + 검색 결과 페이지 스크래핑 + 분야별 배출량 시각화 |
| `05_ghg_energy_integrated_analysis.ipynb` | 온실가스 배출량 + 국가 에너지 수급 통계를 병합해 산업별 에너지-배출량 관계와 탄소집약도 분석 |

`01`~`04`는 여러 개념을 한 세션 안에서 실습한 결과라 하나의 카테고리로 명확히 분류하기보다 "종합 실습"으로 묶었습니다. `05`는 두 개의 공개 데이터셋을 실제로 병합·분석한, 이 저장소에서 가장 완성도 있는 미니 프로젝트입니다.

## 실행 결과

- `01`, `02`, `03`, `04`는 스크래핑(실시간 웹 요청)이 포함되어 있어 **재실행하지 않고 원본 출력을 그대로 유지**했습니다.
- `05`는 외부 데이터 파일만 사용하고 스크래핑이 없어 이번 정리 작업 중 실제로 재실행하여 결과를 검증했습니다.

### 대표 결과

![온실가스-에너지 소비 관계](images/05_ghg_energy_integrated_analysis_01.png)

![산업별 온실가스 배출 추이](images/05_ghg_energy_integrated_analysis_02.png)

## Troubleshooting

### 1. `UnicodeDecodeError` (재실행 중 실제로 발생한 오류 → 수정함)

**문제**

`05_ghg_energy_integrated_analysis.ipynb`를 이번에 재실행했을 때 `pd.read_csv("./data/온실가스_분야별_추이.csv")`에서 `UnicodeDecodeError: 'utf-8' codec can't decode byte 0xb1 in position 0`가 발생했습니다.

**원인**

이 CSV 파일은 UTF-8이 아니라 `cp949`(한글 Windows 기본 인코딩)로 저장되어 있는데, `encoding` 인자를 지정하지 않아 파이썬이 기본값인 UTF-8로 읽으려다 실패했습니다.

**해결**

`pd.read_csv(..., encoding="cp949")`로 인코딩을 명시해 해결했습니다. 저장소의 다른 노트북들(`08_data_cleaning_practice_housing_price.ipynb` 등)도 같은 이유로 `encoding="cp949"`를 사용하고 있어, 이번 수정은 이 저장소의 기존 관례와도 일치합니다.

### 2. `ValueError: Must have equal len keys and value when setting with an iterable` (실제 발생한 오류)

**문제**

`01_cumulative_review_numpy_pandas_scraping.ipynb`에서 `df.loc[2] = [...]`로 행을 추가한 직후 `df['소속사'] = ['SM','쿠팡','카카오']`로 새 컬럼을 만들려다 오류가 발생했습니다.

**원인**

리스트로 새 컬럼을 만들 때는 리스트의 길이가 `DataFrame`의 행 개수와 정확히 같아야 합니다. 이 시점의 `df` 행 개수와 대입하려는 리스트의 길이가 서로 맞지 않아 발생한 오류입니다.

**해결**

`len(df)`로 실제 행 개수를 먼저 확인한 뒤 그 개수에 맞는 리스트를 대입하거나, `df['소속사'] = pd.Series([...], index=[...])`처럼 인덱스를 지정해 대입하면 행 개수가 달라도 안전하게 처리할 수 있습니다.

### 예상 오류: 실시간 데이터로 인한 재현 불가능성

`02`, `03`, `04`에서 스크래핑하는 주가·환율·검색 결과는 실행하는 시점마다 값이 달라집니다. 이 저장소에 저장된 실행 결과는 실습 당시(2025년) 시점의 값이며, 지금 다시 실행하면 다른 숫자가 나오는 것이 정상입니다. 이는 오류가 아니라 실시간 데이터를 다루는 코드의 자연스러운 특성입니다.
