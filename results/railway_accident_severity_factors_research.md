# 철도 사고 유형별 심각도 영향 요인 분석 연구 리서치

작성일: 2026-05-17  
범위: 철도 사고 유형별 사망·부상·피해 규모 심각도에 영향을 미치는 요인, 사용 데이터와 방법론, 연구 공백 및 후속 연구 설계  
주의: 논문 제목·DOI는 Crossref/출판사 DOI 또는 공식 기관 페이지로 확인 가능한 항목 중심으로 정리했다. 국내 학술논문은 이번 조사에서 제목·저자·방법론까지 안정적으로 검증 가능한 공개 메타데이터를 충분히 확보하지 못해, 확인된 공식 자료와 데이터 출처를 중심으로 정리하고 학술논문 후보 검색 경로는 별도 표시했다.

---

## 0. 결론 요약

철도 사고 심각도 연구는 사고 유형별로 축적 정도와 종속변수 정의가 크게 다르다.

1. 탈선은 미국 FRA 자료를 이용한 연구가 강하며, 심각도는 주로 “탈선 차량 수”, “피해액”, “위험물 방출”, “사상 여부”로 측정된다. 반복적으로 확인되는 핵심 요인은 탈선 속도, 사고 원인, 선로 결함, 장비 결함, 열차 길이·편성, 위험물 차량 포함 여부다.
2. 건널목·highway-rail grade crossing 사고는 철도 분야에서 가장 체계적인 부상 심각도 모델링이 이루어진 유형이다. 열차 속도, 경보장치, 도로사용자 행동, 차량 유형, 조명·시정, 도로·선로 기하, 교통량·열차 운행량이 주요 요인으로 보고된다.
3. 무단침입·보행자·persons hit by rolling stock 사고는 치명률이 높지만, 자살과 비자살 trespass를 분리하지 않으면 분석이 왜곡된다. 성별·연령, 음주·약물, 시간대, 역/선로 접근성, 울타리·감시장치, 도시부 접근성 등이 중요하다.
4. 충돌, 여객사상, 직원사상은 공식 통계·조사보고서는 많지만, 개별 사고 단위의 부상 심각도 예측모형 논문은 건널목·탈선보다 적다. 특히 국내에서는 공개 마이크로데이터 접근성과 severity outcome 표준화가 핵심 제약이다.
5. 방법론은 전통적 이산선택모형(binary/ordered/multinomial logit·probit, random-parameters logit)과 count model, 최근의 random forest/XGBoost/SHAP, Bayesian network, association rule이 병행된다. 정책해석에는 통계모형, 예측·비선형 상호작용 탐지에는 ML, 사고 시나리오·원인구조 설명에는 Bayesian network/association rules가 유리하다.
6. 후속 연구는 “사고 유형별 공통 요인과 유형 특이 요인을 분리”하고, 동일 데이터셋에서 ordered/multinomial/random-parameters 모형과 XGBoost·random forest·SHAP을 비교하는 설계가 적합하다.

---

## 1. 국내외 핵심 논문·보고서 목록

### 1.1 국제 핵심 논문

| 번호 | 사고 유형 | 논문/보고서 | 데이터 | 방법론 | 핵심 내용 | 출처 |
|---:|---|---|---|---|---|---|
| 1 | 화물열차 탈선 | Liu, X., Saat, M. R., & Barkan, C. P. L. (2013). “Analysis of U.S. freight-train derailment severity using zero-truncated negative binomial regression and quantile regression.” Accident Analysis & Prevention. | U.S. FRA freight-train derailment data | zero-truncated negative binomial, quantile regression | 탈선 심각도를 탈선 차량 수 등 count outcome으로 분석. 속도, 원인, 열차·선로 특성의 영향 평가. | https://doi.org/10.1016/j.aap.2013.04.039 |
| 2 | 탈선 원인·사고율 | Liu, X., Saat, M. R., & Barkan, C. P. L. (2012). “Analysis of Causes of Major Train Derailment and Their Effect on Accident Rates.” Transportation Research Record. | U.S. FRA derailment accident data | 원인별 사고율·빈도 분석 | broken rail/weld, track geometry, bearing/wheel defect 등 원인별 위험 차이를 분석. | https://doi.org/10.3141/2289-20 |
| 3 | 탈선 심각도 | Martey, E. N., & Attoh-Okine, N. (2019). “Analysis of train derailment severity using vine copula quantile regression modeling.” Transportation Research Part C. | 철도 탈선 자료(논문 원문 확인 필요) | vine copula quantile regression | 탈선 심각도의 분위수별 영향 요인과 변수 의존성을 모델링. | https://doi.org/10.1016/j.trc.2019.06.015 |
| 4 | 건널목 부상심각도 | Haleem, K., & Gan, A. (2015). “Contributing factors of crash injury severity at public highway-railroad grade crossings in the U.S.” Journal of Safety Research. | U.S. FRA Highway-Rail Grade Crossing Accident/Incident data | injury severity model, logit/probit 계열 | 공공 건널목 사고의 부상 심각도 영향 요인을 분석. 열차속도, 경보장치, 차량·운전자·환경 요인 등이 중요. | https://doi.org/10.1016/j.jsr.2015.03.005 |
| 5 | 건널목 안전 추세 | Savage, I. (2005). “Why Has Safety Improved at Rail-Highway Grade Crossings?” Risk Analysis. | U.S. grade crossing safety/accident data | econometric trend decomposition | 건널목 안전 개선의 원인을 노출 변화, 경보장치, 정책·공학적 개선 등으로 설명. | https://doi.org/10.1111/j.1539-6924.2005.00642.x |
| 6 | 건널목 사고예측 | Austin, R. D., & Carson, J. L. (2002). “An alternative accident prediction model for highway-rail interfaces.” Accident Analysis & Prevention. | highway-rail crossing inventory/accident data | accident prediction model | 열차·도로 교통량, 경보장치, 건널목 특성 등 사고예측 변수 제시. | https://doi.org/10.1016/S0001-4575(00)00100-7 |
| 7 | 건널목 ML | Lee, D., Warner, J., & Morgan, C. (2019). “Discovering Crash Severity Factors of Grade Crossing With a Machine Learning Approach.” ASME Joint Rail Conference. | grade crossing crash data | machine learning | 건널목 사고 심각도 요인을 ML로 탐색. 모델·변수 세부사항은 원문 확인 필요. | https://doi.org/10.1115/jrc2019-1231 |
| 8 | 건널목 통계+ML | Soltaninejad, M., Salum, J., Kinero, A., & Alluri, P. (2025). “Modeling highway-rail grade crossing (HRGC) crash severity using statistical and machine learning methods.” International Journal of Injury Control and Safety Promotion. | HRGC crash data | statistical and machine learning methods | 건널목 사고 심각도에 대해 통계모형과 ML을 비교한 최신 연구. 세부 변수·성능은 원문 확인 필요. | https://doi.org/10.1080/17457300.2025.2541666 |
| 9 | 건널목 random parameters | Ren, Q., & Xu, M. (2024). “Injury severity analysis of highway-rail grade crossing crashes in non-divided two-way traffic scenarios: A random parameters logit model.” Multimodal Transportation. | highway-rail grade crossing crash data | random-parameters logit | 비분리 양방향 교통 상황의 건널목 사고 부상 심각도 이질성 분석. | https://doi.org/10.1016/j.multra.2023.100109 |
| 10 | 보행자·무단침입 | Silla, A., & Luoma, J. (2012). “Main characteristics of train–pedestrian fatalities on Finnish railroads.” Accident Analysis & Prevention. | Finnish railroad train-pedestrian fatality data | descriptive/statistical analysis | 보행자 사망사고의 성별·연령·장소·시간·행동 특성을 정리. | https://doi.org/10.1016/j.aap.2011.11.008 |
| 11 | 보행자 안전 리뷰 | Lobb, B. (2006). “Trespassing on the tracks: A review of railway pedestrian safety research.” Journal of Safety Research. | international literature | review | 무단침입·보행자 사고의 행동·환경·예방대책을 종합. | https://doi.org/10.1016/j.jsr.2006.04.005 |
| 12 | 유럽 치명 철도사고 | Evans, A. W. (2011). “Fatal train accidents on Europe’s railways: 1980–2009.” Accident Analysis & Prevention. | European railway fatal accident records/statistics | long-term trend/statistical analysis | 충돌·탈선·건널목 등 치명사고의 장기 추세와 국가별 차이를 분석. | https://doi.org/10.1016/j.aap.2010.09.009 |
| 13 | 철도사고 원인분석 리뷰 | “Railway accident causation analysis: Current approaches, challenges and potential solutions.” Accident Analysis & Prevention (2023). | accident causation literature | review | 인적요인, 조직요인, 기술·인프라 요인, 데이터 품질 문제를 종합. Crossref 확인 결과 2023 논문. | https://doi.org/10.1016/j.aap.2023.107049 |
| 14 | 연관규칙 | Mirabadi, A., & Sharifian, S. (2010). “Application of association rules in Iranian Railways (RAI) accident data analysis.” Safety Science. | Iranian Railways accident database | association rule mining | 사고 원인·장소·조건·결과의 빈번한 조합을 IF-THEN 규칙으로 탐색. | https://doi.org/10.1016/j.ssci.2010.06.006 |
| 15 | Bayesian network | Shi, L., Liu, Y., Zhang, Y., & Liang, J. (2024). “Data-Driven Bayesian Network Analysis of Railway Accident Risk.” IEEE Access. | railway accident data | data-driven Bayesian network | 철도사고 위험의 변수 간 조건부 의존성과 시나리오별 위험확률을 분석. | https://doi.org/10.1109/access.2024.3376590 |

### 1.2 국제 공식 데이터·보고서

| 기관 | 자료 | 활용도 | 링크 |
|---|---|---|---|
| U.S. Federal Railroad Administration (FRA) | Office of Safety Analysis, Accident/Incident, Highway-Rail, Trespasser, Employee injury data | 사고유형, 원인코드, 속도, 사상자, 건널목 특성, 직원·무단침입 자료의 대표적 공개 데이터 | https://safetydata.fra.dot.gov/OfficeofSafety/ |
| European Union Agency for Railways (ERA) | Report on Railway Safety and Interoperability in the EU, Common Safety Indicators | EU 국가별 충돌·탈선·건널목·persons hit by rolling stock·사망/중상 통계 | https://www.era.europa.eu/library/corporate-publications |
| RSSB, UK | Annual Health and Safety Report | 영국 철도 여객·직원·공중사상·건널목·무단침입·플랫폼 사고 위험 프로파일 | https://www.rssb.co.uk/safety-and-health/risk-and-safety-intelligence/annual-health-and-safety-report |
| Transportation Safety Board of Canada | Statistical Summary: Railway Transportation Occurrences | 캐나다 탈선·충돌·건널목·무단침입·위험물 사고 통계 | https://www.tsb.gc.ca/eng/stats/rail/index.html |
| U.S. Bureau of Transportation Statistics | Rail profile/statistics | 노출변수, 철도 운송량·운행 관련 보완 지표 | https://www.bts.gov/ |

### 1.3 국내 공식 자료·데이터 출처

| 기관 | 자료 | 활용도 | 링크 |
|---|---|---|---|
| 한국교통안전공단/철도안전정보포털 | 철도안전정보종합관리시스템 | 국내 철도사고·운행장애·사상자·유형별 현황의 핵심 출처 | https://www.railsafety.or.kr |
| 국토교통부 | 국토교통 통계누리 | 철도사고 현황, 사망자·부상자, 운행장애 등 행정통계 검색 | https://stat.molit.go.kr |
| KOSIS 국가통계포털 | 철도사고현황 검색 | 공식 통계표 기반 장기 추세·유형별 사고 건수 확인 | https://kosis.kr/search/search.do?query=%EC%B2%A0%EB%8F%84%EC%82%AC%EA%B3%A0%ED%98%84%ED%99%A9 |
| e-나라지표 | 철도사고 현황 검색 | 정책지표 형태의 연도별 사고·사상자 추세 확인 | https://www.index.go.kr/search/search.jsp?query=%EC%B2%A0%EB%8F%84%EC%82%AC%EA%B3%A0 |
| 국토교통부 | 제4차 철도안전 종합계획(2023~2027) | 국내 철도안전 정책 목표, 위험요인, 사고감축 전략 | https://www.molit.go.kr |
| 국토교통부 | 철도안전 시행계획(연도별) | 당해연도 철도안전 투자·관리·점검 계획 | https://www.molit.go.kr |
| 항공·철도사고조사위원회 | 철도사고 조사보고서 | 개별 중대사고의 원인, 인적·기술·조직 요인 코딩에 유용 | https://www.araib.molit.go.kr |
| 한국철도공사 KORAIL | 철도통계연보 | 여객·화물·열차km·노선·안전통계 등 노출변수 확보 | https://info.korail.com |
| 국가철도공단 | 철도시설·노선 통계 | 노선연장, 복선화, 전철화, 시설 특성 등 맥락변수 | https://www.kr.or.kr |
| 법령정보센터 | 철도사고 등의 보고에 관한 지침 등 | 국내 사고유형·보고기준 정의 확인 | https://www.law.go.kr/lsSc.do?query=%EC%B2%A0%EB%8F%84%EC%82%AC%EA%B3%A0%20%EB%B3%B4%EA%B3%A0%20%EC%A7%80%EC%B9%A8 |
| 한국철도기술연구원 KRRI | 철도안전·위험도 평가 연구보고서 | 위험도 평가, 사고예방 기술, 안전관리체계 관련 보고서 검색 | https://www.krri.re.kr |
| 한국교통연구원 KOTI | 철도안전 정책보고서 | 철도안전관리제도, 정책평가, 교통안전 연구 | https://www.koti.re.kr |
| ScienceON/KISTI | 국내 학술논문·보고서 검색 | 국내 철도사고 심각도·위험도·건널목·인적오류 논문 탐색 | https://scienceon.kisti.re.kr |
| RISS | 국내 학위논문·학술논문 검색 | 국내 학술문헌 후보 탐색 | https://www.riss.kr/search/Search.do?query=%EC%B2%A0%EB%8F%84%EC%82%AC%EA%B3%A0%20%EC%8B%AC%EA%B0%81%EB%8F%84 |
| KCI | 국내 등재지 논문 검색 | KCI 등재 연구 검증 | https://www.kci.go.kr/kciportal/po/search/poArtiSearList.kci?searchBean.searchText=%EC%B2%A0%EB%8F%84%EC%82%AC%EA%B3%A0%20%EC%8B%AC%EA%B0%81%EB%8F%84 |

---

## 2. 사고 유형별 심각도 영향 요인 정리

### 2.1 열차 충돌

주요 출처: Evans (2011), ERA 보고서, FRA Office of Safety Analysis, RSSB Annual Health and Safety Report, ARAIB 조사보고서.

영향 요인:
- 속도: 충돌속도가 높을수록 사망·중상·차량 손상 가능성이 커진다.
- 충돌 상대: 열차-열차, 열차-장애물, 작업장/입환 중 충돌 여부에 따라 결과가 다르다.
- 열차 종류와 탑승 노출: 여객열차·통근열차는 승객 노출이 커 인명피해 규모가 커질 수 있다.
- 신호·열차제어: ATP, ETCS, PTC, 신호시스템, 관제·통신 오류는 충돌 예방과 심각도 완화의 핵심 요소다.
- 사고 위치: 본선, 역 구내, 차량기지·입환장, 터널·교량 여부.
- 탈선 동반 여부: 충돌 후 탈선·전복·화재가 동반되면 심각도가 크게 증가한다.
- 구조적 안전성: 차량 crashworthiness, 객차 구조, 좌석·입석 상황, 비상탈출 환경.

연구상 특징:
- 충돌 단독의 개별 부상 심각도 모델 논문은 건널목·탈선보다 적고, 공식 통계와 조사보고서 기반의 원인분석·사례분석이 많다.

### 2.2 열차 탈선

주요 출처: Liu et al. (2013), Liu et al. (2012), Martey & Attoh-Okine (2019), FRA data.

영향 요인:
- 탈선 속도: 거의 모든 탈선 심각도 연구에서 중요한 요인이다. 속도가 높을수록 탈선 차량 수, 피해액, 위험물 방출 가능성이 증가한다.
- 사고 원인:
  - broken rail/weld
  - track geometry defect
  - track buckling
  - bearing failure
  - wheel/axle defect
  - braking/train handling/human factor
- 열차 길이·편성·중량: 긴 화물열차, 큰 tonnage, 위험물 차량 포함 여부는 피해 규모와 관련된다.
- 위험물 포함: 화재·폭발·환경피해와 2차 피해 심각도를 크게 높인다.
- 탈선 위치: 선두부·중간부·후미부 탈선 여부와 차량 배열은 탈선 차량 수와 피해 확산에 영향을 준다.
- 선로 조건: track class, mainline/yard, 곡선, 구배, 분기기, 유지보수 상태.
- 시간·기상: 폭우·고온·한파 등은 선로·장비 결함과 결합될 수 있다.

심각도 정의:
- 탈선 차량 수
- 피해액
- 위험물 방출 여부
- 사상자 발생 여부
- 운행중단 시간

### 2.3 건널목 사고 / highway-rail grade crossing

주요 출처: Haleem & Gan (2015), Savage (2005), Austin & Carson (2002), Lee et al. (2019), Ren & Xu (2024), FRA Highway-Rail data.

영향 요인:
- 열차 속도: 차량 탑승자·보행자 부상 심각도의 대표적 증가 요인.
- 도로사용자 행동: 정지위반, 차단기 우회, 경보 무시, 판단오류.
- 경보장치: passive sign만 있는 건널목과 flashing lights/gates 등 active warning의 차이.
- 도로차량 유형: 승용차, 화물차, 버스, 이륜차 등 차량 중량·탑승자 보호 수준.
- 교통량·열차운행량: AADT, 열차 빈도, 선로 수가 노출과 사고기회를 결정.
- 건널목 기하: 교차각, 시거, 차로 수, 복선 여부, 도로 종단경사.
- 조명·시정·기상: 야간, 안개, 비·눈, 젖은 노면.
- 지역 특성: rural/urban, 응급대응 접근성, 접근속도.

심각도 정의:
- no injury / minor / serious / fatal
- fatal or severe vs non-severe
- property damage only vs injury/fatality

### 2.4 무단침입·보행자·persons hit by rolling stock

주요 출처: Silla & Luoma (2012), Lobb (2006), ERA, RSSB, FRA trespasser data.

영향 요인:
- 행동 목적 분리: suicide, suspected suicide, non-suicide trespass, shortcut crossing을 구분해야 한다.
- 성별·연령: 남성, 청소년·청년 또는 특정 고위험 연령대의 비중이 높게 보고되는 경우가 많다.
- 음주·약물: 보행자·무단침입 사고의 반복적 위험요인.
- 장소: 역 주변, 도시부 선로, 울타리 취약 구간, 비공식 보행로, 플랫폼 끝단.
- 시간대: 야간, 주말, 음주활동 시간대.
- 접근통제: fencing, CCTV, 침입감지, 경고표지, 조명.
- 열차속도·운전자 회피 가능성: 충돌 회피 가능 시간이 짧을수록 치명도가 높다.

분석 주의:
- 자살과 사고성 무단침입을 혼합하면 정책요인이 왜곡된다.
- 사망사고 중심 데이터는 비치명 사고의 누락과 표본편향을 유발할 수 있다.

### 2.5 여객사상·승객 사고

주요 출처: RSSB Annual Health and Safety Report, ERA, FRA casualty data, 국내 철도안전정보포털·KORAIL 통계.

영향 요인:
- platform-train interface: 승하차 중 발빠짐, 문 끼임, 플랫폼 gap, 스크린도어 유무·고장.
- 승객 밀집: 혼잡한 플랫폼·열차, 출퇴근 시간대.
- 취약 이용자: 고령자, 장애인, 영유아 동반, 이동보조기기 이용자.
- 미끄러짐·넘어짐: 젖은 바닥, 계단·에스컬레이터, 조명, 안내체계.
- 열차 내 사고: 급제동, 입석, 수하물, 문 작동.
- 비상대피 환경: 터널·교량, 화재·연기, 안내방송, 대피로.

연구상 특징:
- 많은 정보가 철도운영기관 내부 안전DB에 존재하지만, 공개 개별자료 기반 severity modeling 논문은 제한적이다.

### 2.6 직원사상·작업자 사고

주요 출처: FRA employee injury data, RSSB workforce safety reporting, ERA, 국내 철도안전정보포털·조사보고서.

영향 요인:
- 작업 유형: 선로 유지보수, 입환, 연결·분리, 차량검수, 전기·신호 작업.
- 이동 장비 근접성: 운행선 인접 작업, 차량기지 이동, 후진·입환.
- 작업보호 절차: 선로점유, 차단, lookout, lockout/tagout, 관제 승인.
- 의사소통: 관제-현장-기관사 간 통신 오류.
- 피로·근무시간: 야간, 교대근무, 장시간 작업.
- 하청·외주 구조: 작업자 숙련도, 안전교육, 감독체계.
- 환경: 조명, 악천후, 협소 공간, 보행면 상태.

연구상 특징:
- 산업안전 관점 자료는 있으나, 철도 사고유형별 개별 부상 심각도 모델로 공개된 연구는 제한적이다.

---

## 3. 데이터·방법론 비교

### 3.1 데이터 출처 비교

| 데이터 | 국가/기관 | 장점 | 한계 | 적합한 연구 |
|---|---|---|---|---|
| FRA Accident/Incident | 미국 FRA | 공개성 높음, 원인코드·속도·피해액·사상자 연계 가능 | 국가 맥락이 미국에 한정 | 탈선, 충돌, 장비·선로 결함, 사상 severity |
| FRA Highway-Rail | 미국 FRA | 건널목 사고·건널목 inventory·경보장치·교통량 변수 풍부 | 도로측 자료와 결측·품질 문제 가능 | 건널목 부상 심각도, ML/SHAP |
| FRA Trespasser/Employee | 미국 FRA | 무단침입·직원 사상 분석 가능 | 자살 구분·노출변수 제한 가능 | trespasser/employee severity |
| ERA Common Safety Indicators | EU | 국가 간 비교 가능 | 대체로 집계자료, 개별 사고모형에는 제한 | 국가별 추세·benchmarking |
| RSSB/SMIS/AHSR | 영국 | 위험모델·여객/직원/공중사상 세분화 강점 | 원자료 접근 제한 | 사고유형별 risk profile |
| TSB Canada | 캐나다 | 공식 발생통계와 조사보고서 | 집계·사례 중심 | 탈선·충돌·건널목 trend |
| 한국 철도안전정보포털 | 한국 | 국내 사고유형·사상자·운행장애 핵심 출처 | 공개 변수 세부수준 확인 필요 | 국내 유형별 severity 기초분석 |
| ARAIB 조사보고서 | 한국 | 원인 메커니즘·사례 세부사항 풍부 | 표본 수 적고 중대사고 중심 | 원인코딩, 질적·혼합방법 연구 |
| KORAIL/국가철도공단 통계 | 한국 | train-km, passenger-km, 노선·시설 노출변수 | 사고 마이크로데이터와 결합 필요 | 노출 보정, 노선/운영기관 단위 분석 |

### 3.2 방법론 비교

| 방법론 | 종속변수 예시 | 적합 사고유형 | 장점 | 한계/주의 |
|---|---|---|---|---|
| Binary logistic regression | fatal/severe vs other | 건널목, 보행자, 직원·여객 사고 | 해석 쉽고 odds ratio 제시 가능 | 심각도 단계 손실 |
| Ordered logit/probit | no injury/minor/serious/fatal | 부상 심각도 전반 | 순서형 outcome 반영 | parallel-lines assumption 검토 필요 |
| Generalized ordered logit/probit | ordered severity | 건널목·여객·직원 | ordered model 제약 완화 | 해석 복잡 |
| Multinomial logit | no injury/injury/fatal 등 | 건널목, 사고유형 혼합자료 | 범주 유연성 | IIA 가정, 순서정보 손실 가능 |
| Random-parameters/mixed logit | severity class | 건널목 등 이질성 큰 사고 | 미관측 이질성 반영 | 계산 복잡, 표본 수 필요 |
| Latent class model | severity 또는 사고유형별 class | heterogeneous accidents | 잠재집단 분리 | class 해석 주관성 |
| Poisson/negative binomial | 사고건수, 사상자 수, 탈선차량 수 | 탈선, 노선별 사고건수 | count outcome에 적합 | 개별 부상 severity와 다름 |
| Zero-truncated/zero-inflated model | 양수 탈선차량 수, 많은 0 casualty | 탈선·사상자수 | 자료 생성구조 반영 | 모델 선택 민감 |
| Quantile regression | 피해액·탈선차량 수 분위수 | 탈선 | tail severity 분석 가능 | 범주형 injury outcome에는 별도 처리 필요 |
| Bayesian network | accident risk/severity probability | 원인구조, 충돌·탈선·건널목 | 시나리오·조건부 확률 설명 강점 | 구조학습·사전지식 품질 의존 |
| Association rule mining | 사고요인 조합과 결과 | 사고 원인·패턴 탐색 | IF-THEN 규칙으로 해석 용이 | 예측성능·통계적 인과해석 제한 |
| Random forest | severity class | 건널목, 탈선, 여객·직원 | 비선형·상호작용 포착, 결측·고차원 처리 | 계수·유의확률 없음, 정책효과 해석 약함 |
| XGBoost/LightGBM | severity class | 건널목, 혼합 사고유형 | 예측성능 우수, 불균형 처리 가능 | 튜닝·과적합 관리 필요 |
| SHAP | ML 모델 설명 | ML 전반 | 전역·국소 설명, threshold/interaction 해석 | 상관변수·인과해석 오해 주의 |
| GIS hot spot/spatial model | 사고밀도, fatality hot spot | 건널목, 무단침입 | 위치기반 대책 수립 | 노출·공간자기상관 처리 필요 |

---

## 4. 사고 유형별 요인-방법 매트릭스

| 사고 유형 | 주요 심각도 outcome | 반복 확인 요인 | 권장 baseline 모형 | 권장 확장 모형 |
|---|---|---|---|---|
| 충돌 | 사망/중상, 사상자 수, 피해액 | 속도, 충돌상대, 여객노출, 신호·제어, 위치, 탈선/화재 동반 | binary/ordered logit, count model | Bayesian network, random forest/SHAP, 사례기반 원인코딩 |
| 탈선 | 탈선차량 수, 피해액, 위험물 방출, 사상 여부 | 속도, 원인코드, 선로결함, 장비결함, 열차길이·중량, 위험물, mainline/yard | negative binomial, zero-truncated NB, quantile regression | XGBoost/RF+SHAP, vine copula quantile, Bayesian network |
| 건널목 | no/minor/serious/fatal, fatal/severe | 열차속도, 경보장치, 도로사용자 행동, 차량유형, 조명·기상, 교통량·열차량, 선로 수 | ordered/multinomial logit/probit | random-parameters logit, RF/XGBoost+SHAP |
| 무단침입·보행자 | fatal vs nonfatal, 사망사고 특성 | 자살 여부, 성별·연령, 음주, 시간대, 역/선로 접근성, 울타리, 도시부 | binary logit, case-control | GIS hotspot, survival/rare-event model, BN |
| 여객사상 | minor/serious/fatal, 사고 장소별 부상 | 플랫폼 gap, 혼잡, PSD, 고령자, 미끄러짐, 승하차, 급제동 | ordered logit, multinomial logit | RF/SHAP, hierarchical model |
| 직원사상 | lost-time/severe/fatal, 사고유형 | 작업유형, 선로점유, 관제·통신, 야간, 피로, 외주, 장비근접성 | binary/ordered logit | random effects/hierarchical model, BN, text mining |

---

## 5. 연구 공백

1. 사고유형별 severity 정의가 통일되어 있지 않다.
   - 탈선: 탈선 차량 수·피해액·위험물 방출.
   - 건널목: 차량 탑승자 injury severity.
   - 보행자: fatal/nonfatal 또는 fatality characteristics.
   - 여객·직원: injury severity, lost-time injury, platform/operation incident.

2. 국내 공개 마이크로데이터 기반 연구가 부족하다.
   - 국내 공식 통계와 조사보고서는 존재하지만, 연구자가 바로 모델링할 수 있는 사고 단위 변수 테이블 접근성이 제한적이다.
   - 국내 학술문헌은 RISS/KCI/ScienceON에서 추가 검증이 필요하다.

3. 충돌·여객·직원 사고의 개별 severity model이 부족하다.
   - 공식 보고서는 많지만 학술모형은 건널목·탈선보다 적다.

4. 자살과 무단침입 사고의 분리 문제가 크다.
   - persons hit by rolling stock 자료는 suicide, suspected suicide, accidental trespass를 구분해야 정책처방이 가능하다.

5. 노출변수가 부족하다.
   - train-km, passenger-km, employee-hours, AADT, train frequency, route-km, station passenger volume, trespass exposure 같은 분모가 없으면 사고빈도와 심각도 해석이 왜곡된다.

6. ML/XAI 적용은 도로교통보다 늦다.
   - random forest/XGBoost/SHAP은 건널목 분야에서 증가 중이나, 철도 전체 사고유형 통합 severity 연구는 아직 제한적이다.
   - SHAP 결과는 상관관계 설명이지 인과효과가 아니므로 통계모형·정책지식과 병행해야 한다.

7. 조사보고서 텍스트의 비정형 원인정보 활용이 부족하다.
   - ARAIB, FRA narrative, TSB/RSSB 보고서 텍스트를 NLP로 구조화하면 인적·조직·정비·관제 요인을 보완할 수 있다.

---

## 6. 후속 연구 설계 제안

### 6.1 연구 제목 예시

“사고 유형별 철도사고 심각도 영향요인의 통계모형과 설명가능 머신러닝 비교: 충돌·탈선·건널목·무단침입·여객·직원 사고를 중심으로”

### 6.2 연구 질문

1. 철도 사고 심각도에 공통적으로 영향을 미치는 요인은 무엇인가?
2. 충돌, 탈선, 건널목, 무단침입, 여객사상, 직원사상별로 고유한 심각도 요인은 무엇인가?
3. ordered/multinomial/random-parameters logit과 random forest/XGBoost의 예측성능과 설명 결과는 어떻게 다른가?
4. SHAP으로 확인한 비선형 threshold와 상호작용은 기존 통계모형 결과와 일치하는가?
5. 국내 자료에 적용 가능한 변수체계와 사고유형별 대책 우선순위는 무엇인가?

### 6.3 데이터 설계

1단계: 국제 공개데이터 기반 모델 개발
- U.S. FRA Accident/Incident data
- U.S. FRA Highway-Rail Grade Crossing data
- FRA Trespasser and Employee injury data
- 보완: ERA, RSSB, TSB Canada 집계자료로 외부 타당성 비교

2단계: 국내 적용
- 철도안전정보포털 사고유형·사상자 자료
- KOSIS/e-나라지표 장기 추세
- KORAIL 철도통계연보: train-km, passenger-km 등 노출변수
- 국가철도공단 시설자료: 노선·시설 맥락변수
- ARAIB 조사보고서: 원인·인적·조직요인 코딩

### 6.4 변수 설계

종속변수:
- 모형 A: fatal/severe injury vs other
- 모형 B: no injury / minor / serious / fatal
- 모형 C: 사상자 수 또는 탈선 차량 수
- 모형 D: 피해액·운행중단시간 등 consequence severity

공통 설명변수:
- accident type
- train speed
- train/service type
- location type: mainline, yard, station, tunnel, bridge, crossing
- cause code: human, track, signal, rolling stock, external, weather
- time: hour, weekday/weekend, season
- weather/light
- urban/rural
- operator/line fixed effects
- exposure: train-km, passenger-km, traffic volume, employee-hours

유형별 변수:
- 탈선: track class, consist length, tonnage, hazmat, derailment point, track defect type
- 건널목: warning device, AADT, train frequency, vehicle type, driver action, number of tracks, sight distance
- 무단침입: suicide flag, fencing, station proximity, alcohol/drug, access point, hotspot density
- 여객: platform gap, PSD, station crowding, passenger age/vulnerability, boarding/alighting
- 직원: job type, work protection, night work, contractor status, communication failure

### 6.5 분석 절차

1. 데이터 통합 및 사고유형 표준화.
2. 결측·이상치·불균형 class 진단.
3. 사고유형별 기술통계와 severity 분포 분석.
4. baseline 통계모형:
   - binary logit
   - ordered logit/probit
   - multinomial logit
   - negative binomial/zero-truncated NB for counts
5. 이질성 모형:
   - random-parameters logit
   - hierarchical/multilevel model
6. ML benchmark:
   - random forest
   - XGBoost/LightGBM
   - class weighting/SMOTE는 데이터 특성에 따라 검토
7. 설명가능성:
   - permutation importance
   - SHAP global summary
   - SHAP dependence plot
   - 사고유형별 SHAP interaction
8. 강건성 검토:
   - 연도별 train/test split
   - 사고유형별 stratified validation
   - rare-event sensitivity
   - suicide 포함/제외 민감도 분석
9. 정책 매핑:
   - 공통요인: 속도관리, 접근통제, 안전관리체계
   - 유형별 요인: 건널목 경보장치, 선로결함 유지보수, 플랫폼 안전, 작업보호 절차

### 6.6 기대 기여

- 사고유형별로 분절된 철도 심각도 연구를 하나의 비교 프레임으로 통합.
- 전통적 통계모형의 해석성과 ML의 예측력을 동시 비교.
- SHAP 기반으로 변수의 비선형 threshold와 상호작용을 제시.
- 국내 철도안전정보·조사보고서 데이터 구조화 방향 제시.
- 사고유형별 정책 우선순위 도출.

---

## 7. 출처 링크 모음

### 논문 DOI

- Liu et al. (2013), freight-train derailment severity: https://doi.org/10.1016/j.aap.2013.04.039
- Liu et al. (2012), major train derailment causes: https://doi.org/10.3141/2289-20
- Martey & Attoh-Okine (2019), derailment severity: https://doi.org/10.1016/j.trc.2019.06.015
- Haleem & Gan (2015), HRGC injury severity: https://doi.org/10.1016/j.jsr.2015.03.005
- Savage (2005), rail-highway crossing safety: https://doi.org/10.1111/j.1539-6924.2005.00642.x
- Austin & Carson (2002), highway-rail interfaces: https://doi.org/10.1016/S0001-4575(00)00100-7
- Lee, Warner & Morgan (2019), grade crossing ML: https://doi.org/10.1115/jrc2019-1231
- Soltaninejad et al. (2025), HRGC statistical and ML methods: https://doi.org/10.1080/17457300.2025.2541666
- Ren & Xu (2024), HRGC random parameters logit: https://doi.org/10.1016/j.multra.2023.100109
- Silla & Luoma (2012), train-pedestrian fatalities: https://doi.org/10.1016/j.aap.2011.11.008
- Lobb (2006), railway pedestrian safety review: https://doi.org/10.1016/j.jsr.2006.04.005
- Evans (2011), fatal train accidents in Europe: https://doi.org/10.1016/j.aap.2010.09.009
- Railway accident causation analysis (2023): https://doi.org/10.1016/j.aap.2023.107049
- Mirabadi & Sharifian (2010), association rules: https://doi.org/10.1016/j.ssci.2010.06.006
- Shi et al. (2024), Bayesian network railway accident risk: https://doi.org/10.1109/access.2024.3376590

### 공식 데이터·보고서

- U.S. FRA Office of Safety Analysis: https://safetydata.fra.dot.gov/OfficeofSafety/
- ERA corporate publications: https://www.era.europa.eu/library/corporate-publications
- RSSB Annual Health and Safety Report: https://www.rssb.co.uk/safety-and-health/risk-and-safety-intelligence/annual-health-and-safety-report
- TSB Canada railway statistics: https://www.tsb.gc.ca/eng/stats/rail/index.html
- 한국 철도안전정보포털: https://www.railsafety.or.kr
- 국토교통 통계누리: https://stat.molit.go.kr
- KOSIS 철도사고현황 검색: https://kosis.kr/search/search.do?query=%EC%B2%A0%EB%8F%84%EC%82%AC%EA%B3%A0%ED%98%84%ED%99%A9
- e-나라지표 철도사고 검색: https://www.index.go.kr/search/search.jsp?query=%EC%B2%A0%EB%8F%84%EC%82%AC%EA%B3%A0
- 국토교통부: https://www.molit.go.kr
- 항공·철도사고조사위원회: https://www.araib.molit.go.kr
- KORAIL 정보공개/통계: https://info.korail.com
- 국가철도공단: https://www.kr.or.kr
- 법령정보센터 철도사고 보고 지침 검색: https://www.law.go.kr/lsSc.do?query=%EC%B2%A0%EB%8F%84%EC%82%AC%EA%B3%A0%20%EB%B3%B4%EA%B3%A0%20%EC%A7%80%EC%B9%A8
- KRRI: https://www.krri.re.kr
- KOTI: https://www.koti.re.kr
- ScienceON: https://scienceon.kisti.re.kr
- RISS 철도사고 심각도 검색: https://www.riss.kr/search/Search.do?query=%EC%B2%A0%EB%8F%84%EC%82%AC%EA%B3%A0%20%EC%8B%AC%EA%B0%81%EB%8F%84
- KCI 철도사고 심각도 검색: https://www.kci.go.kr/kciportal/po/search/poArtiSearList.kci?searchBean.searchText=%EC%B2%A0%EB%8F%84%EC%82%AC%EA%B3%A0%20%EC%8B%AC%EA%B0%81%EB%8F%84

---

## 8. 검증 메모

- Crossref로 DOI가 확인된 논문만 핵심 목록에 포함했다.
- 일부 최신 ML 논문은 DOI와 Crossref 메타데이터는 확인했으나, 원문 세부 변수·성능지표는 원문 접근 후 추가 확인이 필요하다.
- 국내 학술논문은 이번 조사에서 제목·저자·방법론을 확정 인용할 만큼 검증하지 못했으므로, 공식 데이터·정책자료와 학술 DB 검색 링크로 분리했다.
- “SHAP을 사용한 철도 사고 심각도 연구”는 도로교통 분야에 비해 철도 분야에서 아직 제한적이다. 확인 가능한 핵심 방향은 HRGC 통계+ML 연구이며, SHAP은 후속 연구 설계의 권장 설명도구로 제시했다.
