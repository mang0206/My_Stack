# Way to Beat Covid-19 ( Version Diet )

- With Corona 시대에 예방과 회복을 위한 식단을 추천드립니다.

## 1. 프로젝트 소개

  - Dataset
    + 식품영양소 데이터셋
      - 식품의약안전처, 2021-11-24, https://www.foodsafetykorea.go.kr/fcdb/
    + 영양소 분포에 따른 코로나 확진률 및 회복률 데이터
      - PRB, 2021-08, https://interactives.prb.org/2021-wpds/download-files/ (World Population Data)
      - JOHNS HOPKINS, 2021-08-30, https://github.com/CSSEGISandData/COVID-19/tree/master/csse_covid_19_data/csse_covid_19_daily_reports (Global Confirmed)
      - FAO, 2019-12-31, https://www.fao.org/faostat/en/#data/QI (Food Group Production)
    + 이스라엘 보건청의 증상에 따른 코로나 검사 데이터
      - 이스라엘 보건청, https://data.gov.il/dataset/covid-19/resource/d337959a-020a-4ed3-84f7-fca182292308 (Symptom-Confirmed Data)   
  - 기술 스택 (python, javascript, MySQL 등)
	- Python ( Flask, Pandas, SQLAlchemy )
	- Tableau
	- ML ( LightGBM, CatBoost )   
  - 웹서비스에 대한 자세한 개요
	- 코로나 감염 예방 및 회복을 위한 식단 추천
	- 코로나 시대에 달라진 활동패턴에 맞춘 식단 추천

## 2. 프로젝트 목표

**데이터 분석 결과로 도출되는 인사이트와 웹서비스의 해결과제에 대한 논의 (50자 이상)**
  - 프로젝트 아이디어 동기
    - 위드코로나 시대를 이겨내기 위한 방안 제시   
  - 문제를 해결하기 위한 특정 질문 명시
    - 의학적 전문성이 아닌 데이터 기반의 해석을 통해 코로나 대항 식단을 제공 할 수 있을까?   
  - 데이터를 통해 탐색하려는 문제를 구체적으로 작성
    -  식단과 영양소에 따른 데이터로 코로나 대항 좋은 영양소 제안 ( + 공급자에 필요한 데이터 제공 )
    -  추천된 영양소 위주의 식단 제공
    -  배달음식 주문 증가로 인한 불균형 식단 관리

## 3. 프로젝트 기능 설명

**웹서비스의 유용성, 편의성 및 시각화의 실용성에 대한 설명**
  - 주요 기능 (주된 활용성) 및 서브 기능
	- 식단선정에 의미있는 시각 데이터 제공
	- 탄단지 + 좋은/나쁜 영양소 = 추천 ( 올리브유 = naver/recipe/?=올리브유 )
	- 증상에 따른 코로나 확진 확률 -> 전문의학으로 항상 진료받을수없다.   
	
  - 프로젝트만의 차별점, 기대 효과
	- 진단키트를 사지않고 확진률을 알 수 있다.
	- 그에 따른 맞춤 식단을 제공 할 수 있다.
	- 코로나에 따른 생활 패턴 변화에 적응하기 어려운 사람들에게 좋은 식단을 제공할 수 있다.

## 4. 프로젝트 팀원 역할 분담
| 이름 | 담당 업무 |
| ------ | ------ |
| 김준철 | 데이터 분석, 모델링 |
| 김현경 | 프론트엔드 개발 |
| 박미주 | 프론트엔드 개발, 상호 가능한 시각화 |
| 맹민재 | 데이터 분석, 벡엔드 개발 |
| 손창엽 | 데이터 분석, 벡엔드 개발 |
| 정현우 | 데이터 분석 |

## 5. 프로젝트 repository 주소
https://github.com/mang0206/diet_with_covid_mirror
