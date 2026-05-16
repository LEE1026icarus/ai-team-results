# Research Worker
이카루스님, 요청하신 리서치 보고서를 Markdown 파일로 작성해 저장했습니다.

현재 상황
- 철도 사고 유형별 심각도 영향 요인 관련 국내외 연구를 조사했습니다.
- 논문 DOI는 Crossref/출판사 DOI 기반으로 확인 가능한 항목 위주로 정리했습니다.
- 국내 학술논문은 공개 메타데이터 검증이 제한되어, 확정 인용 가능한 공식 자료·데이터 출처와 학술 DB 검색 경로를 분리해 표기했습니다.

완료된 일
- 산출물 (1) 국내외 핵심 논문/보고서 목록
- 산출물 (2) 사고 유형별 심각도 영향 요인 정리
- 산출물 (3) 데이터/방법론 비교
  - logistic / ordered / multinomial / random-parameters logit
  - count model
  - random forest / XGBoost / SHAP
  - Bayesian network / association rule 등
- 산출물 (4) 연구 공백과 후속 연구 설계 제안
- 산출물 (5) 모든 출처 링크 포함

저장 위치
/home/ubuntu/ai_team/hermes_bridge/results/railway_severity_research_20260517/railway_accident_severity_factors_research.md

검증
- 파일 생성 확인 완료
- 총 415줄 Markdown 보고서로 저장됨

주의점
- SHAP을 명시적으로 적용한 철도 사고 심각도 연구는 도로교통 분야보다 제한적이어서, 확인 가능한 HRGC 통계+ML 연구와 후속 연구 설계 방향 중심으로 정리했습니다.
- 국내 학술논문은 hallucination 방지를 위해 미검증 제목·저자 정보를 억지로 넣지 않고, 공식 자료 및 검색 링크 중심으로 정리했습니다.


# Reviewer 피드백
이카루스님, 파일 원문을 확인하고 DOI 메타데이터 일부를 Crossref로 교차검증했습니다.

현재 상황
- 보고서는 전체적으로 “철도 사고 심각도 영향요인”의 큰 지형을 잘 잡고 있습니다.
- 핵심 DOI 15건은 Crossref 기준으로 대부분 제목·연도·저널이 일치했습니다.
- 다만 “국내 학술논문 검증 불가” 판단은 일부 보완이 필요합니다. Crossref에서 직접 관련 국내 논문 1건이 확인됩니다.
- 최종 평가는 “초안/기획서용으로는 양호하지만, 학술적 문헌고찰로 쓰기에는 보완 필요”입니다.

품질 평가: 3.8 / 5

1. 강점

1) 사고 유형 분류가 적절합니다.
- 충돌
- 탈선
- 건널목
- 무단침입·보행자
- 여객사상
- 직원사상

이 구분은 철도 사고 심각도 연구에서 매우 중요합니다. 특히 건널목과 탈선 연구가 상대적으로 풍부하고, 여객·직원·충돌은 공개 개별자료 기반 모델링이 부족하다는 판단도 타당합니다.

2) DOI 검증 태도가 좋습니다.
보고서가 검증 불가능한 국내 문헌을 억지로 넣지 않은 점은 hallucination 방지 측면에서 바람직합니다. 실제로 주요 DOI 15건은 Crossref 메타데이터와 대체로 일치했습니다.

3) 방법론 스펙트럼이 넓습니다.
logit/probit, random-parameters logit, count model, quantile regression, RF/XGBoost/SHAP, Bayesian network, association rule까지 포함한 것은 후속 연구 설계 관점에서 유용합니다.

4) 연구 공백 제시가 실용적입니다.
특히 다음 지적은 좋습니다.
- severity 정의 불일치
- 국내 마이크로데이터 접근성 부족
- 자살과 무단침입의 혼합 문제
- 노출변수 부족
- 조사보고서 텍스트 활용 부족

2. 주요 문제점과 논리적 취약점

1) “국내 학술논문 검증 제한”은 과도하게 보수적입니다.
보고서에는 국내 학술논문을 확정 인용하지 않았다고 되어 있지만, Crossref에서 다음 논문이 확인됩니다.

- Song, Jiyoung; Kwak, Ho-Chan; Eom, Jinki; Lee, Inmook. 2018.
  “A Study on Factors Influencing Accident Injury Severity by Railway Type”
  Journal of the Korean Society for Railway
  DOI: 10.7782/jksr.2018.21.6.604

제목상으로는 보고서 주제와 직접적으로 맞닿아 있습니다. 이 논문은 반드시 국내 핵심 문헌 후보로 추가 검토해야 합니다.

2) “핵심 논문 목록” 중 일부는 세부 검토가 부족합니다.
예를 들어 다음 항목들은 표 안에서도 “원문 확인 필요”라고 되어 있습니다.

- Martey & Attoh-Okine 2019
- Lee, Warner & Morgan 2019
- Soltaninejad et al. 2025

이들을 “핵심 논문”으로 넣는 것은 가능하지만, 현재 수준에서는 “핵심 후보” 또는 “추가 원문 검토 필요”로 구분하는 편이 더 정확합니다.

3) Crossref DOI 확인과 내용 검증이 혼동될 위험이 있습니다.
Crossref는 DOI, 제목, 저널, 연도 확인에는 유용하지만, 다음 내용까지 보증하지는 않습니다.
- 실제 사용 변수
- 표본 기간
- 종속변수 정의
- 모델 성능
- 통계적 유의성
- SHAP 사용 여부
- 정책적 결론

따라서 보고서의 “검증” 표현은 “서지 메타데이터 검증”으로 좁히는 것이 좋습니다.

4) 사고빈도 모델과 사고심각도 모델이 일부 혼재되어 있습니다.
보고서는 “심각도 영향요인”이 중심인데, 일부 문헌과 방법론은 사고 발생 빈도 또는 사고예측 모델에 가깝습니다.

예:
- Austin & Carson 2002: accident prediction model 성격
- Savage 2005: 건널목 안전 개선 추세·경제모형 성격
- count model: 사고건수 또는 탈선차량 수에는 적합하지만, 개인 부상심각도와는 다른 문제

보완 방향:
- Frequency model
- Conditional severity model
- Consequence magnitude model

이 세 범주를 분리해야 합니다.

5) 후속 연구 설계에서 “통합 모델”의 위험이 충분히 설명되지 않았습니다.
보고서는 충돌·탈선·건널목·무단침입·여객·직원을 하나의 비교 프레임으로 통합하자고 제안합니다. 방향은 좋지만, 단일 모델에 accident type을 넣는 방식은 위험할 수 있습니다.

이유:
- 사고 유형별 위험집합이 다릅니다.
- 종속변수 정의가 다릅니다.
- 관측 단위가 다릅니다.
  - 탈선: 사고 1건, 탈선 차량 수
  - 건널목: 도로 이용자/차량 부상 심각도
  - 직원사상: 작업자 injury
  - 여객사상: 플랫폼/열차 내 개인 부상
- 설명변수도 유형별로 완전히 다릅니다.

권장 보완:
- 1단계: 사고유형별 개별 모델
- 2단계: 공통 변수만 meta-analysis 또는 hierarchical framework로 비교
- 3단계: SHAP/partial dependence 결과를 유형별로 병렬 비교

3. 누락된 핵심 문헌 후보

보고서에 추가하면 좋은 문헌입니다. Crossref 기준 확인된 항목입니다.

1) 국내 직접 관련 문헌
- Song, Jiyoung; Kwak, Ho-Chan; Eom, Jinki; Lee, Inmook. 2018.
  A Study on Factors Influencing Accident Injury Severity by Railway Type.
  Journal of the Korean Society for Railway.
  DOI: 10.7782/jksr.2018.21.6.604

2) 탈선 ML/비정형 데이터
- Lotfi, A.; Bagheri, M.; Ahmadi, A. 2023.
  Using Machine Learning Methods for Modeling Freight Train Derailment Severity.
  Transportation Research Record.
  DOI: 10.1177/03611981221119193

- Song, Bing; Zhang, Zhipeng; Qin, Yong; Liu, Xiang. 2022.
  Quantitative analysis of freight train derailment severity with structured and unstructured data.
  Reliability Engineering & System Safety.
  DOI: 10.1016/j.ress.2022.108563

이 두 편은 보고서의 “ML/XAI 및 비정형 원인정보 활용 부족” 주장과 직접 연결됩니다.

3) 건널목 심각도 추가 문헌
- Kang, Yashu; Khattak, Aemal. 2017.
  Cluster-Based Approach to Analyzing Crash Injury Severity at Highway–Rail Grade Crossings.
  Transportation Research Record.
  DOI: 10.3141/2608-07

- Fan, Wei; Gong, Linfeng; Washing, Edward Matt; Yu, Miao. 2016.
  Key factors contributing to crash severity at highway-rail grade crossings.
  Journal of Modern Transportation.
  DOI: 10.1007/s40534-016-0110-x

- Lu, Pan; Zheng, Zijian; Ren, Yihao; Zhou, Xiaoyi. 2020.
  A Gradient Boosting Crash Prediction Approach for Highway-Rail Grade Crossing Crash Analysis.
  Journal of Advanced Transportation.
  DOI: 10.1155/2020/6751728

4) 무단침입·보행자 심각도
- Zhang, Meng; Khattak, Asad J.; Liu, Jun; Clarke, David. 2018.
  A comparative study of rail-pedestrian trespassing crash injury severity between highway-rail grade crossings and non-crossings.
  Accident Analysis & Prevention.
  DOI: 10.1016/j.aap.2018.02.001

- Wang, Xin; Liu, Jun; Khattak, Asad J.; Clarke, David. 2016.
  Non-crossing rail-trespassing crashes in the past decade: A spatial approach to analyzing injury severity.
  Safety Science.
  DOI: 10.1016/j.ssci.2015.08.017

5) 공간/Bayesian 접근
- Guadamuz, Renato; Aguero-Valverde, Jonathan. 2021.
  Bayesian spatial models of injury severity at railway crossings.
  Journal of Transportation Safety & Security.
  DOI: 10.1080/19439962.2019.1667932

4. 보완해야 할 분석 관점

1) Severity의 층위를 명확히 분리해야 합니다.
현재 보고서는 다음 개념이 함께 들어가 있습니다.

- Injury severity: no injury / minor / severe / fatal
- Accident consequence: 피해액, 운행중단 시간, 위험물 방출
- Physical severity: 탈선 차량 수
- Frequency/risk: 사고 발생 건수
- Fatal accident trend: 사망사고 장기 추세

권장 분류:
- Type A: Human injury severity
- Type B: Material damage severity
- Type C: Operational disruption severity
- Type D: Environmental/hazmat consequence severity
- Type E: Accident frequency, 별도 분석

2) 노출변수의 역할을 구분해야 합니다.
보고서에서는 train-km, passenger-km, AADT, employee-hours 등을 노출변수로 제시합니다. 맞는 방향이지만, 노출변수는 주로 “사고 발생 가능성” 분석에 더 직접적입니다.

심각도 모델에서는 사고가 이미 발생한 조건에서의 결과를 설명하므로 다음 구분이 필요합니다.

- 사고발생 모델: exposure 필수
- 조건부 심각도 모델: exposure는 보조 변수 또는 selection correction 변수
- 통합 위험도 모델: 발생확률 × 조건부 심각도

3) 인과해석 주의가 더 강하게 들어가야 합니다.
SHAP, RF, XGBoost는 영향요인 탐색에는 유용하지만 정책효과 추정에는 한계가 있습니다.

추가해야 할 문장:
- SHAP importance는 causal effect가 아니다.
- 경보장치가 있는 건널목에서 심각도가 높게 나와도, 이는 위험도가 높은 지점에 active warning이 우선 설치되는 selection bias일 수 있다.
- 속도, 경보장치, 교통량, 지역특성은 정책 배정과 상관되어 내생성이 발생할 수 있다.

4) 데이터 누락·보고편향 문제를 더 다뤄야 합니다.
특히 철도 사고 자료는 다음 편향이 큽니다.
- 경미 사고 과소보고
- 사망사고 중심 조사보고서 편향
- 자살/사고성 무단침입 분류 오류
- 직원사상과 일반 산업재해 자료의 경계
- 국가별 severity coding 차이
- 금액 피해액의 물가·환율·보고기준 차이

5) 검증 설계가 조금 더 엄격해야 합니다.
보고서의 validation 제안은 좋지만, 다음이 추가되면 좋습니다.
- Temporal split: 과거 연도 학습, 이후 연도 테스트
- Spatial split: 노선/주/운영기관 단위 외부검증
- Rare event metrics: AUCPR, macro-F1, balanced accuracy
- Calibration: Brier score, calibration plot
- Cost-sensitive evaluation: fatal/severe misclassification penalty
- Interpretability stability: SHAP ranking bootstrap 안정성

5. 구체적 수정 제안

보고서에 다음 구조를 추가하는 것을 권합니다.

1) “1.1 국제 핵심 논문”을 세 그룹으로 재분류
- 직접 severity modeling 문헌
- 사고빈도/예측 문헌
- 리뷰/통계/정책 문헌

2) “1.4 국내 학술문헌 후보” 신설
우선 다음 논문 추가:
- Song et al. 2018, Journal of the Korean Society for Railway, DOI: 10.7782/jksr.2018.21.6.604

3) “3.3 종속변수 정의별 모델 적합성” 추가
예:
- 부상등급: ordered/multinomial/random-parameters logit
- 탈선 차량 수: zero-truncated negative binomial
- 피해액: log-linear, gamma, quantile regression
- 사망 여부: rare-event logit, Firth logit, Bayesian logistic
- 사고건수: Poisson/NB, zero-inflated, spatial count model

4) “6.5 분석 절차”에 selection bias와 causal caution 추가
특히 건널목 경보장치, 속도제한, 도시/농촌 변수는 정책 배정과 얽혀 있어 단순 상관해석이 위험합니다.

5) “출처 링크 모음”은 일반 링크보다 정확한 문서/데이터 링크로 보강
현재 공식자료 링크 중 일부는 기관 메인페이지 또는 검색 링크입니다. 최종 보고서에서는 다음처럼 바꾸는 것이 좋습니다.
- 정확한 데이터 다운로드 페이지
- 보고서 PDF 링크
- 통계표 ID
- 접근일
- 데이터 기간
- 변수 사전 링크

6. 추가 조사 우선순위

우선순위 1
- Song et al. 2018 국내 논문 원문 확인
- 국내 철도사고 유형별 injury severity 정의 확인
- 철도안전정보포털에서 사고 단위 자료 접근 가능성 확인

우선순위 2
- Lotfi et al. 2023, Song et al. 2022를 추가해 탈선 ML/비정형 데이터 파트 보강
- Zhang et al. 2018, Wang et al. 2016을 추가해 무단침입·보행자 severity 파트 보강

우선순위 3
- HRGC 분야에서 SHAP 또는 gradient boosting 적용 논문 세부 확인
- “ML 사용”과 “SHAP 사용”을 구분해 정리

우선순위 4
- 국내 법령상 철도사고/운행장애/사상자 보고 기준 정리
- 국내 severity outcome을 국제 FRA/ERA/RSSB 기준과 매핑

최종 판단
- 보고서는 리서치 브리핑 또는 연구계획 초안으로는 충분히 쓸 수 있습니다.
- 하지만 학술논문 문헌고찰로 사용하려면 누락 문헌 보강, severity 정의 분리, 빈도모형과 심각도모형 구분, 국내 문헌 재조사가 필요합니다.
- 가장 큰 보완점은 “국내 직접 관련 논문 10.7782/jksr.2018.21.6.604 추가”와 “사고유형 통합모델의 방법론적 위험 명시”입니다.
