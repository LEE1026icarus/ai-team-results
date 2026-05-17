# Research Worker
현재 상황:
- 요청하신 GitHub 스타 급증 레포지토리 자동 모니터링 리서치를 완료했습니다.
- 마크다운 리포트를 작성했고, save-result 호출까지 완료했습니다.

완료된 일:
- 조사 항목 1~9 전체 반영
  - GitHub Trending
  - GH Archive / WatchEvent
  - OSS Insight API
  - GitHub API v3
  - GitStar Ranking
  - Star History
  - RSS/뉴스레터
  - GitHub Actions cron + gh
  - Discord/Slack Webhook 파이프라인
- 각 방법별 장단점/난이도/비용/자동화 가능성 비교 표 작성
- 최종 추천 베스트 프랙티스 3가지 정리
- 결과 파일 저장 및 Hermes Bridge save-result 등록 완료

저장 위치:
- 요청 경로:
  /home/ubuntu/ai_team/hermes_bridge/results/20260517-7e992c_github-star-monitoring-research.md

- save-result 산출물 경로:
  /home/ubuntu/ai_team/hermes_bridge/results/20260517-7e992c/20260517-7e992c_github-star-monitoring-research.md

검증:
- 두 파일 모두 존재 확인 완료
- 파일 크기: 22,746 bytes
- job 상태: completed
- artifact 등록 완료

최종 추천 요약:
1. 즉시 운영 MVP:
   OSS Insight API + GitHub Actions cron + Discord/Slack webhook

2. 정확한 급등 탐지:
   GH Archive WatchEvent 집계로 stars_24h / surge_score 자체 계산

3. 브리핑 품질 강화:
   GitHub Trending RSS + Changelog Nightly + Star History 리더보드를 보조 신호로 통합


# Reviewer 피드백
이카루스님, 조사 결과 파일을 직접 열어 검토했고, 핵심 외부 엔드포인트도 간단히 확인했습니다.

검증한 사항:
- 두 저장 경로 모두 존재합니다.
- 파일 크기: 각각 22,746 bytes
- 두 파일 내용은 동일합니다.
- OSS Insight `past_24_hours` API는 현재 200 응답을 반환합니다.
- GH Archive `2026-05-16-0.json.gz`도 200 응답이며, 실제 `WatchEvent` + `payload.action == "started"` 샘플을 확인했습니다.
- GitHub Trending, Star History, GitStar Ranking, GitHubTrendingRSS, Changelog Nightly 페이지도 접근 가능했습니다.

종합 평가:
- 전체적으로 실무 MVP 설계까지 이어질 수 있는 잘 정리된 리서치입니다.
- 특히 “StarEvent가 아니라 WatchEvent를 집계해야 한다”는 핵심 포인트를 정확히 짚은 점이 좋습니다.
- 다만 “급등 탐지”를 실제 운영 지표로 만들기 위한 정의, 데이터 품질, 실패 모드, 비용/제한사항 분석이 아직 부족합니다.

최종 품질 점수: 4.2 / 5


1. 잘 된 점

1) 조사 범위가 넓고 균형적입니다.
- GitHub Trending
- GH Archive
- OSS Insight
- GitHub API
- GitStar Ranking
- Star History
- RSS/뉴스레터
- GitHub Actions cron
- Discord/Slack webhook

요청 항목은 대부분 빠짐없이 반영되어 있습니다.

2) 최종 추천 구조가 실용적입니다.
추천안인

- MVP: OSS Insight API + GitHub Actions cron + Discord/Slack webhook
- 정밀 탐지: GH Archive WatchEvent 집계
- 브리핑 강화: Trending RSS + Changelog Nightly + Star History

이 조합은 구현 속도, 운영 비용, 정확도의 균형이 좋습니다.

3) GH Archive 사용법이 핵심을 잘 짚었습니다.
GitHub star 이벤트가 `StarEvent`가 아니라 `WatchEvent`로 기록된다는 점은 실무에서 자주 헷갈리는 부분인데, 정확히 설명되어 있습니다.

4) GitHub API의 한계를 잘 설명했습니다.
GitHub API만으로 “전체 GitHub에서 급등하는 레포”를 찾기는 어렵고, 후보군이 필요하다는 지적은 타당합니다.

5) 구현 예시가 포함되어 있어 바로 PoC로 연결 가능합니다.
GitHub Actions YAML, curl, jq, webhook 예제가 있어서 단순 조사 보고서에 그치지 않고 실행 계획으로 이어질 수 있습니다.


2. 주요 보완점 / 논리적 허점

1) “스타 급증”의 정의가 아직 불명확합니다.

현재 보고서는 여러 방법을 비교하지만, 최종적으로 어떤 레포를 “급등”으로 볼지 기준이 명확하지 않습니다.

예를 들어 다음 기준들이 서로 다릅니다.

- 최근 24시간 스타 수가 많은 레포
- 전일 대비 스타 증가량이 큰 레포
- 7일 평균 대비 z-score가 높은 레포
- 누적 스타 수 대비 증가율이 높은 레포
- 신생 레포 중 빠르게 성장한 레포
- 이미 유명한 레포가 릴리즈 후 다시 주목받은 경우

현재 점수식 예시는 있지만, 운영 기준으로는 부족합니다.

보완 제안:
보고서에 다음과 같은 명시적 정의를 추가하는 것이 좋습니다.

예시:

```text
급등 후보 조건:
- stars_24h >= 50
- stars_24h / max(avg_stars_7d, 1) >= 3
- repo_created_at <= 2 years 또는 별도 신생 레포 랭킹
- archived == false
- fork == false
- pushed_at <= 90 days
```

그리고 랭킹은 2개로 나누는 것이 좋습니다.

- Absolute Surge: 최근 24시간 스타 수 절대값 기준
- Relative Surge: 기존 평균 대비 이상 증가 기준


2) GH Archive의 한계가 더 명확히 필요합니다.

보고서에서는 GH Archive가 가장 정확하다고 표현하지만, 엄밀히 말하면 GH Archive WatchEvent는 “신규 star 이벤트 수”에 가깝고, GitHub repo의 순증가 star 수와는 다를 수 있습니다.

중요한 차이:
- WatchEvent started: 사용자가 star를 누른 이벤트
- GitHub `stargazers_count` delta: 누적 star 수의 순변화
- unstar 이벤트는 일반적으로 같은 방식으로 잡기 어렵거나 공개 이벤트에서 충분히 다루기 어렵습니다.

따라서 GH Archive는 “gross new stars”에는 강하지만, “net star delta”는 GitHub API snapshot과 함께 확인해야 합니다.

보완 문장 예시:

```text
GH Archive WatchEvent는 신규 star 이벤트 수를 측정하는 데 적합하지만,
unstar로 인한 감소분을 직접 반영하지는 않는다.
따라서 순증가량이 필요한 경우 GitHub API의 stargazers_count 스냅샷과 병행해야 한다.
```


3) OSS Insight API 의존성 리스크가 과소평가되어 있습니다.

OSS Insight는 MVP로 매우 좋지만, 외부 서비스 의존성이 큽니다.

추가로 확인해야 할 사항:
- rate limit 여부
- API 안정성 / SLA 없음
- 응답 스키마 변경 가능성
- `stars` 컬럼이 정확히 어떤 기간의 star인지 명확한 정의
- `total_score` 산식
- 장애 시 fallback 전략

현재 보고서에는 “beta 성격”이라고만 되어 있는데, 운영 설계 관점에서는 fallback이 필요합니다.

보완 제안:
MVP에도 fallback을 넣는 것이 좋습니다.

```text
Primary: OSS Insight trends API
Fallback 1: GitHub Trending RSS
Fallback 2: 전날 후보군 GitHub API polling
Fallback 3: GH Archive 전날 데이터 집계
```


4) GitHub Actions cron 운영 리스크가 빠져 있습니다.

GitHub Actions cron은 편하지만 운영상 몇 가지 주의점이 있습니다.

누락된 리스크:
- scheduled workflow는 정각에 몰리면 지연될 수 있음
- public repo에서 Actions 사용량/정책 확인 필요
- repository inactivity 시 scheduled workflow가 비활성화될 수 있음
- 매일 commit 방식은 branch protection, token permission, commit noise 문제가 있음
- 중복 실행 방지를 위한 concurrency 설정 필요
- 실패 알림 필요

보완 예시:

```yaml
concurrency:
  group: github-star-surge-monitor
  cancel-in-progress: true

permissions:
  contents: write
```

그리고 상태 저장은 commit보다 다음 중 하나를 검토하는 것이 좋습니다.

- GitHub Actions artifact
- GitHub cache
- gist
- S3/R2
- SQLite 파일 commit
- Supabase/Postgres
- BigQuery table


5) 점수식이 유명 레포 편향을 만들 수 있습니다.

보고서의 예시 점수식:

```text
score = stars_24h * log10(total_stars + 10) + growth_ratio_bonus + freshness_bonus
```

이 방식은 이미 유명한 레포에 유리합니다. “떡상하는 신생 레포”를 찾고 싶다면 반대로 누적 스타가 낮은 레포도 보정해야 합니다.

보완 제안:
랭킹을 분리하는 것이 좋습니다.

```text
main_score = stars_24h

relative_score = stars_24h / max(avg_stars_7d, 1)

novelty_score = stars_24h / log10(total_stars + 10)

surge_score = zscore_7d + log1p(stars_24h) + freshness_bonus
```

또는 최종 브리핑을 다음 3개 섹션으로 나누면 더 유용합니다.

- 오늘 가장 많이 오른 레포
- 평소 대비 비정상적으로 오른 레포
- 신생/저스타 레포 중 급성장한 레포


6) 데이터 품질 필터가 부족합니다.

실제 운영하면 노이즈가 많습니다.

추가해야 할 필터:
- fork 제외
- archived repo 제외
- mirror repo 제외
- template/demo/spam성 repo 제외
- description 없는 repo 감점
- 최근 push가 오래된 repo 감점
- license 없는 repo 표시
- organization allowlist/blocklist
- topic/language 필터
- 성인/도박/크랙/불법 키워드 필터
- autogenerated repo 또는 awesome-list 계열 별도 분류

특히 GitHub Trending이나 GH Archive 상위권에는 “awesome list”, prompt 모음, 바이럴성 모음집이 자주 섞일 수 있습니다. 목적에 따라 이들을 포함할지 별도 분류할지 정해야 합니다.


7) 비용/데이터량 추정이 부족합니다.

GH Archive 방식은 “데이터량 큼”이라고 되어 있지만 운영 판단에는 대략적인 수치가 필요합니다.

추가하면 좋은 항목:
- 하루 GH Archive gzip 총량 대략 범위
- 압축 해제 후 처리량
- GitHub Actions에서 24개 파일 처리 가능한지
- BigQuery 1회 쿼리 예상 스캔량
- 월간 비용 추정
- 로컬 SQLite 저장 시 월간 DB 크기

현재 리포트는 기술적으로 맞지만, “실제 운영 비용이 얼마나 될지” 판단하기에는 부족합니다.


8) “브리핑 품질 강화” 쪽 소스 검증이 약합니다.

Changelog Nightly, GitHubTrendingRSS, Star History leaderboard를 보조 신호로 제안한 것은 좋지만, 자동화 관점에서는 다음 확인이 더 필요합니다.

- RSS가 실제로 제공되는지
- RSS 항목에 repo URL이 안정적으로 포함되는지
- robots.txt / 이용약관상 스크래핑 문제가 없는지
- HTML 구조가 안정적인지
- rate limit 또는 Cloudflare 차단 가능성
- 장애 시 해당 소스를 무시하고 계속 진행할 수 있는지

즉, 보조 소스는 “큐레이션 품질”은 좋지만 “운영 안정성”은 낮을 수 있습니다. 이 구분을 더 명확히 해야 합니다.


9) 알림 메시지 설계가 아직 단순합니다.

보고서에는 Discord/Slack webhook 예시가 있지만, 실제로 매일 쓰려면 메시지 설계가 중요합니다.

추가 권장 필드:
- “왜 떴는지” 추정 사유
- 어제 대비 순위 변화
- 7일 평균 대비 배수
- 신규 진입 여부
- repo age
- 최근 release 여부
- 주요 topics
- README 요약
- 설치/사용 난이도
- 라이선스 위험 표시
- AI/DevTool/Security 등 카테고리 라벨

예시:

```text
1. owner/repo
   +342 stars / 24h, 7d avg 대비 5.8x
   누적 2.1k stars, Python, MIT
   신규 진입, created 2026-04-12, last push 3h ago
   이유 후보: HN 언급, v0.2 release, "local AI agent" 키워드
```


3. 추가 조사하면 좋은 항목

1) GitHub GraphQL API 활용 가능성
- REST보다 여러 repo 메타데이터를 batch로 가져오기 좋을 수 있습니다.
- rate limit 계산 방식이 다르므로 대량 후보 보강에 유리할 수 있습니다.

2) GitHub Search API 보조 후보군
예:
- 최근 생성된 repo 중 stars 높은 순
- 특정 topic별 최근 인기 repo
- pushed 최근 7일 이내 + stars 조건

단, Search API는 1,000개 결과 제한과 rate limit이 있으므로 메인보다는 보조 후보군에 적합합니다.

3) Hacker News / Reddit / Product Hunt / X 연동
스타 급등의 원인을 파악하려면 GitHub 내부 신호만으로는 부족합니다.

추가 소스:
- Hacker News Algolia API
- Reddit API 또는 RSS
- Product Hunt
- Lobsters
- dev.to
- X/Twitter 검색
- Changelog / TLDR / GitHub newsletter

4) ecosyste.ms / Libraries.io / deps.dev
오픈소스 생태계 메타데이터 보강에 유용할 수 있습니다.

확인할 항목:
- 패키지 매니저 등록 여부
- npm/PyPI/crates.io 다운로드 수
- dependency graph
- 라이선스
- maintainer activity

5) 장기 저장 스키마
보고서에 간단한 상태 저장 언급은 있지만, 데이터 모델이 없습니다.

추천 테이블:

```text
repos
- repo_id
- full_name
- owner
- name
- language
- description
- created_at
- pushed_at
- license
- topics
- is_fork
- is_archived

daily_repo_metrics
- date
- repo_id
- stars_24h
- stargazers_count
- forks_count
- open_issues_count
- watchers_count
- surge_score
- source_flags

alerts
- date
- repo_id
- rank
- message_hash
- sent_to
- sent_at
```


4. 보고서에 바로 추가하면 좋은 보완 섹션

다음 섹션들을 추가하면 리포트 완성도가 크게 올라갑니다.

1) “급등”의 운영 정의

```text
본 시스템에서는 급등을 다음 세 가지로 분류한다.

A. Absolute Trending:
최근 24시간 신규 star 수가 많은 저장소

B. Relative Surge:
최근 24시간 신규 star 수가 최근 7일 평균 대비 비정상적으로 높은 저장소

C. Emerging Repo:
누적 star는 낮지만 repo 생성 후 짧은 기간 내 빠르게 성장하는 저장소
```

2) 데이터 소스별 정확도 구분

```text
OSS Insight: 빠른 후보 발굴용
GH Archive: 신규 star 이벤트 직접 집계용
GitHub API: 현재 repo 상태 및 순증가 검증용
Trending/RSS: 사람이 보는 큐레이션 보조 신호
```

3) 실패 대응 전략

```text
OSS Insight 실패 -> Trending RSS 사용
GH Archive 특정 시간 파일 누락 -> 다음 실행에서 재시도
GitHub API rate limit -> 후보 수 축소 및 ETag/캐시 사용
Webhook 실패 -> retry queue 저장
```

4) 운영 체크리스트

```text
- webhook secret 등록
- GitHub token 권한 확인
- cron timezone 확인
- 중복 실행 방지
- 전송 실패 재시도
- 상태 저장 위치 결정
- rate limit 로깅
- 알림 메시지 2,000자 제한 처리
```


5. 최종 추천 수정안

현재 최종 추천은 적절하지만, 다음처럼 조금 더 정교하게 바꾸는 것이 좋습니다.

수정 전:
1. 즉시 운영 MVP:
   OSS Insight API + GitHub Actions cron + Discord/Slack webhook

2. 정확한 급등 탐지:
   GH Archive WatchEvent 집계로 stars_24h / surge_score 자체 계산

3. 브리핑 품질 강화:
   GitHub Trending RSS + Changelog Nightly + Star History 리더보드를 보조 신호로 통합

수정 후:
1. 즉시 운영 MVP:
   OSS Insight trends API를 primary source로 사용하고, GitHub API로 repo metadata를 보강한 뒤 Discord/Slack webhook으로 발송한다.

2. 정밀 탐지:
   GH Archive WatchEvent로 gross new stars를 집계하고, GitHub API stargazers_count snapshot으로 net delta를 보정한다. 급등 점수는 absolute, relative, emerging 세 가지 랭킹으로 분리한다.

3. 브리핑 품질 강화:
   GitHub Trending RSS, Changelog Nightly, Star History, HN/Reddit/Product Hunt 언급량을 보조 신호로 통합하되, 비공식 소스는 장애 시 무시 가능한 optional source로 둔다.


6. 품질 평가

항목별 평가:

- 조사 범위: 4.7 / 5
- 기술 정확성: 4.3 / 5
- 실무 구현 가능성: 4.4 / 5
- 운영 리스크 분석: 3.6 / 5
- 비용/제한사항 분석: 3.4 / 5
- 최종 추천의 타당성: 4.5 / 5
- 바로 실행 가능한 정도: 4.0 / 5

최종 점수:
4.2 / 5

판정:
좋은 리서치입니다. MVP 구현에 바로 사용할 수 있는 수준입니다. 다만 “정확한 급등 탐지 시스템”으로 발전시키려면 급등 정의, GH Archive와 GitHub API의 역할 분리, 비용 추정, 실패 대응, 데이터 품질 필터를 보강해야 합니다.

우선 보완 우선순위는 다음 3개입니다.

1. “급등” 기준을 absolute / relative / emerging으로 명확히 분리
2. GH Archive WatchEvent는 gross new stars이고, GitHub API snapshot은 net delta 검증용이라는 점 추가
3. GitHub Actions 운영 리스크, rate limit, fallback, 중복 방지 설계 추가
