# 서울특별시 내 시니어들의 특성에 따른 문화생활 참여 예측 및 소비분석


## 개요

### 1. 소개

- 목적 : 머신러닝을 통해 서울특별시 내 시니어들의 특성에 따른 문화생활 참여 예측, 예측 결과를 통한 소비분석
- 팀원 : 김종원, 김지우, 김연진, 김예리
- 사용 언어 : ![Python](https://img.shields.io/badge/-Python-white?style=flat&logo=python&logoColor=#3776AB) ![R](https://img.shields.io/badge/-R-blue?style=flat&logo=R&logoColor=#276DC3)
- 소요 기간 : 3주
- 내용이 너무 길어서 각 분석 마지막에 결론을 추가했습니다. 참고 바랍니다.

### 2. 주제 선정 배경

한국은 타 선진국과 비교했을 때 가장 빠르게 고령화가 진행 중이며, 2025년에는 초고령사회에 진입할 것으로 예상된다고 한다.<br>
따라서 고령 사회로 진입하는 과정에서 소비 패턴의 변화가 예상된다.

시니어들의 구매력의 증가율 또한 높은 것으로 보인다.<br>
2012년에서 2022년 사이 50대의 소비 지출액 증감률은 어느 연령대보다 높다.

![ppt08p](https://github.com/kimj0ngw0n/multicam_project_gimgane/assets/127815502/25af298a-c59d-44cd-80b5-45471fcf3e4f)

### 3. 데이터

분석에 활용한 데이터는 2개로 2018, 2020년에 수집한 설문 데이터이다.

1. 국민문화예술활동조사(`.csv`, 이하 `data1`)

    - 각 개인의 '성별', '연령' 등 기본 정보와 '문화예술행사 직접 관람 횟수' 등과 같은 해당 개인이 응답한 답변이 하나의 행으로 이루어진 `csv` 파일이다.

<div align="center">data1의 예시</div>

![ppt13p](https://github.com/kimj0ngw0n/multicam_project_gimgane/assets/127815502/5f36d2b8-0b85-46b9-9122-ea4c0f561ed9)

2. 서울서베이 도시정책지표조사 정보(`.csv`, 이하 `data2`)

    - 각 개인의 '성별', '연령' 등 기본 정보와 '문화 활동 연간 총 비용' 등과 같은 해당 개인이 응답한 답변이 하나의 행으로 이루어진 `csv` 파일이다.

    - 비용과 관련된 데이터이기 때문에 소비분석에서 사용했다.

<div align="center">data2의 예시</div>

![ppt14p](https://github.com/kimj0ngw0n/multicam_project_gimgane/assets/127815502/2c258d1b-bb39-46e5-918a-25c3dd201ce1)


## 목차

1. 데이터 전처리

1. EDA

1. 연령/거주지 별 문화예술행사 관람 횟수 차이 분석

1. 군집화

1. 머신러닝
    - 여러 feature에 따른 문화예술행사 관람 빈도 그룹의 분류

1. 소비분석

1. 결론

1. 분석의 한계점

1. 후기

1. 기타(데이터 출처)


## 1. 데이터 전처리

1. 열 추출, 순서 설정, 이름 변경

    - 팀원이 4명이므로 원활한 소통을 위해 각 변수들의 이름을 변경했다.

2. 범주형 변수 코드값 통일

    - 2018년과 2020년 설문지가 달랐기 때문에 응답 범주 값을 통일 시켰다.

    - ex) 2018년에는 독서 자료를 구입 대여의 경우, 문학과 비문학으로 나누어 설문했지만, 2020년에는 나누지 않았다.

3. 결측치 제거 및 대치

    - 문화활동에는 무료 프로그램도 존재한다. 무료 프로그램에만 참여한 사람의 경우 소비 비용이 0원으로 나타난다.

    - 소비 비용이 `NaN` 인 경우, 문화활동에 아예 참여하지 않는 사람이기 때문에 제외했다.


## 2. EDA

사용 데이터 : `data1`

아래는 문화예술행사 선택 기준 순위를 나타낸 그래프이다.

문화예술행사를 선택하는 기준 1순위는 '컨텐츠 및 퀄리티' 이고, 2순위는 '비용' 이었다.

<div align="center">문화예술행사 선택 기준 순위</div>

![ppt20p](https://github.com/kimj0ngw0n/multicam_project_gimgane/assets/127815502/e00ecf1a-9a0b-4be5-b30c-7445a3a8b735)

반대로 문화예술행사 관람에 걸림돌의 순위는 아래와 같다.

시간이 가장 큰 걸림돌이었고, 비용과 관심도가 그 다음이었다.

<div align="center">문화예술행사 관람 걸림돌 순위</div>

![ppt21p](https://github.com/kimj0ngw0n/multicam_project_gimgane/assets/127815502/099757e6-c101-4c1d-8ed2-7ec3112af6d7)


## 3. 연령/거주지 별 문화예술행사 관람 횟수 차이 분석

사용 데이터 : `data1`

연령 또는 거주지 별로 문화예술행사 관람 횟수에 차이가 있는지 알아보기 위해 각각 ANOVA Test를 실시했다.

> ANOVA Test는 정규성, 등분산성, 독립성을 만족해야 하지만, 검정 결과 모두 만족하지 않았다.<br>
하지만 데이터의 개수가 충분히 많기 때문에 만족한다고 가정 후 분석을 진행했다.

### 1. 연령 별 문화예술행사 관람 횟수 차이 분석

- H0 : 연령에 따른 문화예술행사 관람 횟수 평균의 차이가 없다.
- H1 : 연령에 따른 문화예술행사 관람 횟수 평균의 차이가 있다.

<div align="center">연령 별 문화예술행사 관람 횟수 평균</div>

![ppt25p](https://github.com/kimj0ngw0n/multicam_project_gimgane/assets/127815502/ae6f9e7c-faf6-4039-8cbb-ab718cd29a7c)

위 그래프는 연령 별 문화예술행사 관람 횟수의 평균이다.

시각적으로는 연령 별 차이가 없어 보인다. 통계적으로도 유의미한 차이가 없는지 확인을 위해 ANOVA Test를 진행했다.

<div align="center">ANOVA Test 및 사후검정 결과</div>

![ppt28p](https://github.com/kimj0ngw0n/multicam_project_gimgane/assets/127815502/ce1b339a-a106-4e74-8347-52e04d9afbbc)

위의 캡처본은 순서대로 ANOVA Test와 사후검정 결과이다.

- 결과

    ANOVA Test 결과, p-value ≒ 0.0025 로 유의수준 0.05에서 유의하다.

    즉, 연령에 따라 문화예술행사 관람 횟수 평균의 차이가 있다.

    사후 검정 결과, 5-60대의 사람들은 1~20대 및 3-40대인 사람들과 문화예술행사 관람 횟수에 유의미한 차이가 존재한다.

### 2. 거주지 별 문화예술행사 관람 횟수 차이 분석

- H0 : 수도권과 비수도권은 문화예술행사 관람 횟수 평균의 차이가 없다.
- H1 : 수도권과 비수도권은 문화예술행사 관람 횟수 평균의 차이가 있다.

<div align="center">수도권과 비수도권의 문화예술행사 관람 횟수 평균</div>

![ppt31p](https://github.com/kimj0ngw0n/multicam_project_gimgane/assets/127815502/c469616c-71b3-4d18-bc14-d5af1d72918e)

위 그래프는 수도권과 비수도권의 문화예술행사 관람 횟수의 평균이다.

시각적으로 뚜렷하게 차이가 보이지만, 통계적으로도 거주지에 따라 관람 횟수에 유의미한 차이가 있는지 확인하기 위해 ANOVA Test를 진행했다.

<div align="center">ANOVA Test 결과</div>

![ppt33p](https://github.com/kimj0ngw0n/multicam_project_gimgane/assets/127815502/259eaf45-eee7-44fc-89de-5362c8f474a9)

위의 캡처본은 ANOVA Test 결과이다.

- 결과

    ANOVA Test 결과, p-value ≒ 0.0000 으로 유의수준 0.05에서 유의하다.
    
    즉, 수도권과 비수도권은 문화예술행사 관람 횟수 평균의 차이가 있다.

### ANOVA Test 결과

> 연령/거주지 별로 문화예술행사 관람 횟수의 평균에 확실한 차이가 있으므로 시니어를 타겟으로 선정한 것과 그렇지 않은 분석에는 차이가 존재한다.<br>
따라서 우리는 서울특별시의 시니어를 특정하여 분석을 진행했다.


## 4. 군집화

사용 데이터 : `data1`

K-Modes, K-Means, K-Prototypes 세 가지 알고리즘 중 가장 결과가 좋았던 K-Prototypes를 채택했다.

<div align="center">엘보우 커브</div>

![ppt41p](https://github.com/kimj0ngw0n/multicam_project_gimgane/assets/127815502/4bd74fa0-5dba-4729-9f53-9911976a9eb2)

위 그래프는 엘보우 커브를 시각화한 결과이다. K가 2~4일 때 군집화가 효과적일 것이라고 생각했다.

<div align="center">K가 2, 3, 4 일 때 실루엣 분석</div>

![ppt42p](https://github.com/kimj0ngw0n/multicam_project_gimgane/assets/127815502/1fca0a62-5ea3-4c0a-af8c-782f3ed769a3)

위 그래프는 K가 각각 2, 3, 4 일 때의 실루엣 분석을 시각화한 결과이다.

### 군집화 결과

> 위 그래프들을 참고하여 군집 개수는 3으로 채택했다.<br>
그 결과, 아래와 같이 문화예술행사 관람 횟수가 적은 그룹, 보통인 그룹, 많은 그룹으로 군집화가 잘 되었다.

> 관람 횟수가 적은 그룹은 '영화'를 가장 많이 보고, 많은 그룹은 '연극 및 뮤지컬'을 많이 본다.

> '영화'에 비해 상대적으로 비싼 '연극 및 뮤지컬'의 경우 접근성 부분에서도 차이가 있기 때문에 생기는 현상으로 보인다.<br>
우리는 문화예술행사 관람 횟수가 많은 그룹은 적은 그룹에 비해 문화예술에 지출하는 비용이 더 높을 것이라고 생각했다.

<div align="center">군집의 개수가 3개일 때 군집화 결과</div>

![ppt43p](https://github.com/kimj0ngw0n/multicam_project_gimgane/assets/127815502/6de58b15-beda-4a72-b9e4-f7c2720ba9db)

<div align="center">관람횟수가 적은/많은 군집이 가장 많이 소비한 문화예술분야</div>

![ppt44p](https://github.com/kimj0ngw0n/multicam_project_gimgane/assets/127815502/19566d0c-b704-4537-8471-4726e0300d05)


## 5. 머신러닝

사용 데이터 : `data1`

`target` : 범주화된 문화예술행사 관람 빈도

`feature` : 성별, 학력, 문화예술행사 선택 기준 등

`target`은 기존에 연속형 변수였던 `문화예술행사 관람 횟수`를 아래와 같이 범주화하여 사용했다.

> 하위 관람 그룹(target = 1) : 최솟값 ~ 제 1 사분위수 (7회 이하)<br>
평균 관람 그룹(target = 2) : 제 1 사분위수 ~ 평균 (25회 이하)<br>
상위 관람 그룹(target = 3) : 평균 ~ 최댓값

여러 머신러닝 기법을 적용해본 결과 가장 좋은 분류 성능(정확도 0.5466)을 보인 `Random Forest` 모델을 최종 모델로 결정했다.

<div align="center">Feature Importance</div>

![ppt64p](https://github.com/kimj0ngw0n/multicam_project_gimgane/assets/127815502/f295c69f-a245-4222-bfdf-b0097148c74c)

### 머신러닝 결과

> Feature Importance 순위<br>
> 1. 가구소득<br>
> 2. 문화예술행사(직접관람) 선택 기준<br>
> 3. 문화예술행사(직접관람) 관람 걸림돌


## 6. 소비분석

사용 데이터 : `data2`

### 1. 소비 비용 비교

<div align="center">연간 문화예술행사 소비 비용에 따른 소비 비용 평균 비교</div>

![ppt67p](https://github.com/kimj0ngw0n/multicam_project_gimgane/assets/127815502/5a35fd91-631d-4681-8a9e-0aae6b7d76a8)

<div align="center">연간 문화예술행사 소비 비용에 따른 소비 비용 총합 비교</div>

![ppt68p](https://github.com/kimj0ngw0n/multicam_project_gimgane/assets/127815502/d3cb2b7e-742b-4aaa-94fd-14ee413639e7)

위의 두 그래프들을 통해 하위 그룹과 상위 그룹 사이 소비 금액의 편차가 심한 것을 알 수 있다.

<div align="center">문화생활 참여도 하위 관람 그룹의 문화생활 관람 걸림돌 순위</div>

![ppt75p](https://github.com/kimj0ngw0n/multicam_project_gimgane/assets/127815502/7e8ebfaa-8d5b-4f7d-8ace-e69849c3c7de)

> 위 그래프를 통해 문화생활 참여도가 낮은 그룹의 경우, '비용', '시간', '관심도' 순으로 관람에 걸림돌이 된다고 응답했다.


### 2. 분야별 소비 비용 비교

<div align="center">저소득층/고소득층의 연간 분야별 문화활동 소비 비용 평균 값</div>

![ppt71p](https://github.com/kimj0ngw0n/multicam_project_gimgane/assets/127815502/c4aaf096-ee23-48cc-9635-28e4a4da7d0e)

위 사진은 각각 저소득층/고소득층의 연간 분야별 문화활동 소비 비용 평균 값을 나타낸 그래프이다.

저소득층과 고소득층의 연간 문화활동 소비 비용 평균은 각각 42,125원과 245,843원으로 **저소득층의 연간 문화활동 소비 비용은 전체적으로 고소득층의 비용보다 훨씬 낮은 것을 확인할 수 있다.**

<div align="center">문화 활동 분야별 소비 금액 평균의 비율의 차</div>

![ppt73p](https://github.com/kimj0ngw0n/multicam_project_gimgane/assets/127815502/77eac14f-b6bf-4dbc-924c-79d52669a198)

위 그래프는 저소득층/고소득층의 연간 문화 활동 분야별 소비 금액 평균의 비율의 차이를 나타낸 그래프다.

### 소비 분석 결과

> 위 그래프에서 맨 위의 museum_price 와 art_price 는 박물관 관람 또는 음악 및 무용 발표회에 소비한 금액의 비율 차이다.<br>
양의 값을 가지므로 고소득층이 위 분야에 소비하는 비율이 저소득층 보다 높다.

> 위 그래프에서 맨 아래의 movie_price 는 영화 관람에 소비한 금액의 비율 차이다.<br>
음의 값을 가지므로 저소득층이 영화 관람에 소비하는 비율이 고소득층에 비해 높다.


## 7. 결론

### 내용 정리

- 가구소득에 따라 문화예술 행사 소비 비용은 명확하게 차이가 존재한다.

- 문화예술 행사 관람에 걸림돌이 되는 상위 요인은 '비용', '시간', '관심도', '관련 정보 부족' 이다.

- 고소득층에 비해 저소득층은 박물관 관람 또는 음악 및 무용 발표회 참여도가 낮다.

### 시사점 및 기대효과

시니어 저소득층의 문화 소외 현상을 해소하고 참여도를 고취시킬 방안을 제시하려한다.

관람에 걸림돌이 되는 상위 요인을 고려한 복지 및 사업 제도가 필요하다.

- 비용 : 기존 문화누리카드 제도 타겟층 확대
- 관심도, 관련 정보 부족 : 저소득층 시니어 대상 문화예술 행사 참여 사업 및 프로그램 개발

특히, 참여도가 낮은 분야에 대한 복지 제도 마련이 비교적 우선되어야 할 것이다.

이와 같은 국가적 차원에서 선제적으로 지원해주는 복지 정책을 통해, 저소득층의 시니어 또한 액티브 시니어와 비교하여 상대적으로 소외되는 현상을 완화시킬 수 있을 것이다.


## 8. 분석의 한계점

1. ANOVA Test의 기본 가정을 만족하지 않았다.

2. 2018, 2020년 데이터이므로 최신 데이터가 아니다.

3. 2020년 데이터는 코로나의 영향 때문에 왜곡되었을 가능성이 존재한다.

4. 머신러닝 성능이 좋지 않다.


## 9. 후기

4명이 같이 진행한 프로젝트는 처음이었다. 인원이 비교적 많아서 그런지 의견도 많고 시간이 생각보다 오래 걸렸다.<br>
하지만 그만큼 각자의 장점이 빛을 발하고, 많은 토의를 통해 단단한 분석을 할 수 있어 좋았다.

이렇게 여러 방법과 다각도의 분석을 한 번에 할 수 있어 다양하고 계속해서 새로운 결과를 얻을 수 있었다.<br>
특히, 본문에 작성하지 않았지만 시도했던 회귀 분석과 로지스틱 회귀 등등의 분석과 검증을 통해 얻는 것이 많았다.

3주라는 긴 시간 동안 다각도에서 분석을 하다 보니 사용한 데이터를 모두 외워버릴 정도로 데이터를 이해하게 되었다.<br>
하지만 시각을 조금만 바꾸거나 놓친 부분을 찾는 경우 새로운 데이터를 보는 느낌이었다.<br>
분석 방향과 데이터의 중요성에 대해 다시 한 번 깨닫게 되었다.


## 10. 기타(데이터 출처)

직접 활용 데이터 출처

- [국민문화예술활동 조사 - 문화체육관광부](https://mdis.kostat.go.kr/ofrData/selectOfrDataDetail.do?survId=11301&itmDiv=2&nPage=3&itemId=2008&itemNm=#)

- [서울시 문화활동 연간 평균비용 통계 - 열린 데이터 광장(사용자 설문 데이터)](https://data.seoul.go.kr/dataList/10304/S/2/datasetView.do)

- [서울시 문화활동 연간 평균비용 통계 [원천데이터] - 열린 데이터 광장(사용자 설문 데이터)](https://data.seoul.go.kr/dataList/OA-15564/F/1/datasetView.do)

간접 활용 데이터 출처

- [2022 고령자 통계 - 통계청](https://kostat.go.kr/board.es?mid=a10301010000&bid=10820&tag=&act=view&list_no=420896&ref_bid)

- [가구주 연령별 가구당 월평균 가계수지 - 통계청](https://kosis.kr/statHtml/statHtml.do?orgId=101&tblId=DT_1L9V007&vw_cd=MT_ZTITLE&list_id=G_A_10_004_001&scrId=&seqNo=&lang_mode=ko&obj_var_id=&itm_id=&conn_path=MT_ZTITLE&path=%252FstatisticsList%252FstatisticsListIndex.do)

- [여가문화 유형별 관심도](https://www.bigdata-culture.kr/bigdata/user/data_market/detail.do?id=6a5451f0-eb98-11ec-a6e8-cdf27550dc0d)
