# GitHub 스타 급증(떡상) 레포지토리 일일 모니터링 방법 리서치

작성일: 2026-05-17
대상: 매일 GitHub에서 스타가 갑자기 많이 증가한 오픈소스 저장소를 자동 탐지하고 Discord/Slack 등으로 받아보는 파이프라인

## 0. 결론 요약

가장 실용적인 베스트 프랙티스는 다음 3가지입니다.

1. 빠른 구축/운영 안정성: OSS Insight Trending API + GitHub Actions cron + Discord/Slack Webhook
   - `https://api.ossinsight.io/v1/trends/repos/?period=past_24_hours`가 이미 최근 인기 저장소를 계산해 주므로, 매일 호출해서 상위 N개를 알림으로 보내면 됩니다.
   - 구현 난이도와 비용이 가장 낮고, PoC/운영용 MVP에 적합합니다.

2. 정확한 “스타 증가량” 기준: GH Archive BigQuery 또는 시간별 JSON.gz + WatchEvent 집계
   - GitHub에서 “star”는 공개 이벤트 데이터에서 `WatchEvent` + `payload.action == "started"`로 기록됩니다.
   - 일별/시간별 신규 스타 수를 직접 집계할 수 있어 “전일 대비 급증”, “최근 24시간 스타 수”, “언어별 급등” 같은 기준을 가장 정확하게 만들 수 있습니다.
   - 단, BigQuery 비용/설정 또는 데이터 다운로드/집계 인프라가 필요합니다.

3. 보조 신호/큐레이션: GitHub Trending RSS/Changelog Nightly/Star History 리더보드 병행
   - GitHub Trending은 공식 API가 없으므로 스크래핑 또는 비공식 RSS를 활용합니다.
   - Changelog Nightly, Star History weekly leaderboard 같은 큐레이션/랭킹 신호를 함께 넣으면 노이즈를 줄이고 사람이 읽기 좋은 결과를 만들 수 있습니다.

권장 운영안:
- 1단계: OSS Insight API로 매일 알림 MVP 구축
- 2단계: GH Archive 기반 자체 집계를 추가하여 “진짜 star velocity” 점수화
- 3단계: GitHub Trending/RSS/뉴스레터를 보조 신호로 합쳐서 최종 랭킹 생성

---

## 1. 방법별 비교 표

| 방법 | 얻을 수 있는 정보 | 장점 | 단점/주의점 | 난이도 | 비용 | 자동화 가능성 | 추천도 |
|---|---|---|---|---:|---:|---:|---:|
| GitHub Trending 스크래핑 | 일간/주간/월간 트렌딩 저장소, 언어별 트렌딩 | GitHub 공식 웹 UI 기준, 사람이 보는 트렌드와 가까움 | 공식 API 없음, HTML 변경에 취약, 정확한 랭킹 알고리즘 비공개 | 중 | 무료 | 높음, 단 스크래퍼 유지 필요 | ★★★★☆ |
| GitHub Trending 비공식 RSS | Trending 결과를 RSS로 구독 | 구현 매우 쉬움, RSS 리더/봇 연동 용이 | 비공식 서비스 의존, 세부 데이터 제한 | 하 | 무료 | 매우 높음 | ★★★☆☆ |
| GH Archive JSON.gz | 시간별 공개 GitHub 이벤트 원천 데이터 | WatchEvent를 직접 집계해 신규 스타 수 산출 가능, 가장 유연 | 데이터량 큼, 처리/저장/중복 관리 필요 | 중~상 | 무료~저비용 | 높음 | ★★★★★ |
| GH Archive BigQuery | 전체 GH Archive를 SQL로 분석 | 장기간/대규모 분석에 강함, SQL만으로 랭킹 가능 | Google Cloud/BigQuery 설정 필요, 쿼리 비용 가능 | 중 | 무료 티어~유료 | 높음 | ★★★★★ |
| OSS Insight API | 최근 인기 저장소, star history, 국가/조직별 stargazer 등 | API가 바로 있음, Trending 계산 완료, 구현 쉬움 | 베타/외부 서비스 의존, 자체 기준 커스터마이즈 한계 | 하 | 무료(현재 확인) | 매우 높음 | ★★★★★ |
| GitHub API v3 repo polling | `stargazers_count`, repo 메타데이터 | 안정적 공식 API, gh CLI와 잘 맞음 | 전체 GitHub 대상 급등 탐지에는 부적합. 후보군이 필요함 | 하~중 | 무료, rate limit 있음 | 높음 | ★★★★☆ |
| GitHub API v3 stargazers history | `Accept: application/vnd.github.star+json`로 `starred_at` | 특정 저장소의 스타 이력 확인 가능 | 대형 저장소는 페이지네이션이 매우 큼. 전역 급등 탐지에는 비효율 | 중 | 무료, rate limit 있음 | 중 | ★★★☆☆ |
| GitStar Ranking | 전체/언어별 스타 랭킹 | 전체 인기 저장소 파악 쉬움 | “급등”보다 누적 랭킹 중심, 공식 API 확인 안 됨 | 하~중 | 무료 | 중 | ★★☆☆☆ |
| Star History | 저장소별 스타 증가 시각화, weekly leaderboard | 시각화 좋음, 최근 급등 레포 보조 신호 가능 | 공식 API는 확인하지 못함(미확인). 자동화는 스크래핑 의존 가능 | 하~중 | 무료/상용 기능 여부 미확인 | 중 | ★★★☆☆ |
| 뉴스레터/큐레이션 | Changelog Nightly, GitDaily류, GitHub Explorer류 | 사람이 읽기 좋고 노이즈가 적음 | 데이터 원천/정량성 제한, 서비스 지속성 의존 | 하 | 무료~유료 | 중 | ★★★☆☆ |
| GitHub Actions cron + gh | 자체 스크립트 일일 실행 | 인프라 없이 GitHub 안에서 운영 가능 | GitHub API rate limit/시크릿/상태 저장 설계 필요 | 중 | 무료~저비용 | 매우 높음 | ★★★★★ |
| Discord/Slack Webhook | 매일 알림 | 구현 간단, 팀 공유 쉬움 | 메시지 길이/포맷 제한, 재시도/중복 방지 필요 | 하 | 무료~저비용 | 매우 높음 | ★★★★★ |

---

## 2. 조사 항목별 상세

### 2.1 GitHub Trending - 매일/매주 단위 트렌딩 레포, 스크래핑/API

확인 결과:
- 웹 페이지: `https://github.com/trending`
- 기간 파라미터:
  - `?since=daily`
  - `?since=weekly`
  - `?since=monthly`
- 언어별 URL 예:
  - `https://github.com/trending/python?since=daily`
  - `https://github.com/trending/typescript?since=weekly`

API 여부:
- 공식 REST/GraphQL API는 확인되지 않습니다.
- 실무에서는 HTML 스크래핑 또는 비공식 RSS/라이브러리를 사용합니다.

장점:
- GitHub 사용자들이 실제로 보는 “트렌딩” 기준과 일치합니다.
- 언어별 필터가 쉬워서 Python, TypeScript, Rust 등 관심 생태계만 모니터링하기 좋습니다.

단점:
- GitHub Trending의 랭킹 알고리즘은 공개되어 있지 않습니다.
- HTML 구조 변경에 취약합니다.
- “오늘 신규 스타 수”는 표시될 수 있지만, 정교한 전일 대비/증가율 계산에는 별도 저장이 필요합니다.

자동화 방식 예:
```bash
curl -L 'https://github.com/trending?since=daily' \
  -H 'User-Agent: Mozilla/5.0' > trending.html
```

Python에서는 BeautifulSoup 등으로 `article.Box-row` 구조를 파싱하는 방식이 일반적입니다. 단, GitHub HTML 구조는 바뀔 수 있으므로 테스트를 두는 것이 좋습니다.

---

### 2.2 GitHub Archive / GH Archive - WatchEvent, StarEvent 스트리밍 데이터

확인 결과:
- 웹: `https://www.gharchive.org/`
- 원천 데이터 예:
  - `https://data.gharchive.org/2026-05-16-0.json.gz`
- GH Archive는 GitHub 공개 이벤트를 시간 단위 `.json.gz`로 제공합니다.
- 데이터는 2011-02-12부터 제공되며, 2015-01-01 이후는 GitHub Events API 기반이라고 안내되어 있습니다.
- BigQuery 공개 데이터셋으로도 제공됩니다.

중요: StarEvent가 아니라 WatchEvent
- 현재 GitHub 공개 이벤트에서 “사용자가 저장소에 star를 눌렀다”는 이벤트는 일반적으로 `WatchEvent`이며, payload는 `{"action":"started"}` 형태입니다.
- “StarEvent”라는 이름으로 생각하기 쉽지만, GH Archive/Event API에서는 WatchEvent를 집계해야 합니다.

샘플 이벤트:
```json
{
  "type": "WatchEvent",
  "created_at": "2026-05-16T00:00:00Z",
  "repo": {"name": "EvoLinkAI/awesome-gpt-image-2-API-and-Prompts"},
  "payload": {"action": "started"}
}
```

직접 다운로드 집계 예:
```bash
wget https://data.gharchive.org/2026-05-16-{0..23}.json.gz
zcat 2026-05-16-*.json.gz \
  | jq -r 'select(.type=="WatchEvent" and .payload.action=="started") | .repo.name' \
  | sort | uniq -c | sort -nr | head -50
```

BigQuery 예시 SQL:
```sql
SELECT
  repo.name AS repo_name,
  COUNT(*) AS stars_24h
FROM `githubarchive.day.20260516`
WHERE type = 'WatchEvent'
  AND JSON_VALUE(payload, '$.action') = 'started'
GROUP BY repo_name
ORDER BY stars_24h DESC
LIMIT 100;
```

장점:
- “지난 24시간 star 증가 수”를 직접 계산할 수 있습니다.
- GitHub Trending보다 기준이 명확하고 재현 가능합니다.
- 장기 추세, 전일 대비, 이동평균 대비 z-score 등 고급 이상치 탐지가 가능합니다.

단점:
- 데이터량이 큽니다. 하루 24개 gzip 파일을 처리해야 합니다.
- 이벤트 데이터는 공개 이벤트 기반이므로 private repo는 당연히 제외됩니다.
- BigQuery 사용 시 비용 관리가 필요합니다.

추천 점수식 예:
```text
score = stars_24h * log10(total_stars + 10) + growth_ratio_bonus + freshness_bonus
```
또는:
```text
surge_score = (stars_today - avg_stars_7d) / max(stddev_stars_7d, 1)
```

---

### 2.3 OSS Insight - PingCAP 오픈소스 인사이트, star history API

확인 결과:
- 웹: `https://ossinsight.io/`
- API 문서: `https://ossinsight.io/docs/api`
- Trending 페이지: `https://ossinsight.io/trending`
- GitHub 저장소: `https://github.com/pingcap/ossinsight`

주요 API:
```text
GET https://api.ossinsight.io/v1/trends/repos/
GET https://api.ossinsight.io/v1/trends/repos/?period=past_24_hours
GET https://api.ossinsight.io/v1/trends/repos/?period=past_week
GET https://api.ossinsight.io/v1/trends/repos/?period=past_month
GET https://api.ossinsight.io/v1/trends/repos/?period=past_3_months
```

응답 컬럼 예:
- `repo_id`
- `repo_name`
- `primary_language`
- `description`
- `stars`
- `forks`
- `pull_requests`
- `pushes`
- `total_score`
- `contributor_logins`
- `collection_names`

Star history API 예:
```text
GET https://api.ossinsight.io/v1/repos/pingcap/tidb/stargazers/history
```

응답 예:
```json
{
  "columns": ["date", "stargazers"],
  "rows": [
    {"date": "2015-09-01", "stargazers": "2468"}
  ]
}
```

장점:
- 트렌딩/스타 이력 API가 바로 있어 구현이 매우 빠릅니다.
- 단순 스타 수뿐 아니라 forks, PR, pushes 등 커뮤니티 활동까지 반영된 `total_score`가 있습니다.
- 매일 알림용 MVP로 가장 적합합니다.

단점:
- API가 beta 성격이며, 서비스 정책/엔드포인트 변경 가능성이 있습니다.
- OSS Insight의 스코어링 기준에 의존합니다.
- 완전히 독립적인 자체 기준을 만들려면 GH Archive 집계가 필요합니다.

권장 사용:
- 매일 오전 9시에 `past_24_hours` 호출
- 상위 20개 중 관심 언어/키워드 필터링
- GitHub API로 repo 상세(`stargazers_count`, pushed_at, license 등) 보강
- Discord/Slack으로 요약 발송

---

### 2.4 GitHub API v3 - stargazers_count, stargazers 이력

확인 결과:
- Repo 상세 API:
```text
GET https://api.github.com/repos/{owner}/{repo}
```
- 응답에 `stargazers_count`가 포함됩니다.

특정 저장소의 stargazer 목록:
```text
GET https://api.github.com/repos/{owner}/{repo}/stargazers
```

`starred_at` 타임스탬프를 받으려면 Accept 헤더가 중요합니다.
```bash
curl -H 'Accept: application/vnd.github.star+json' \
  https://api.github.com/repos/torvalds/linux/stargazers?per_page=100
```

일반 Accept 헤더에서는 사용자 목록만 나오지만, `application/vnd.github.star+json`를 쓰면 다음처럼 `starred_at`이 포함됩니다.
```json
{
  "starred_at": "2011-09-04T22:48:12Z",
  "user": {"login": "..."}
}
```

Rate limit:
- 비인증 요청: 기본 60 requests/hour
- 인증 요청: 일반적으로 5,000 requests/hour
- Search API는 별도 제한과 1,000개 결과 접근 제한이 있습니다.

장점:
- 공식 API라 안정적입니다.
- 후보 저장소의 현재 누적 스타 수, 설명, 언어, 라이선스, 최근 push 시각 등을 보강하기 좋습니다.
- 자체 DB에 어제 `stargazers_count`를 저장해 두면 오늘 값과 차분하여 증가량을 계산할 수 있습니다.

단점:
- “GitHub 전체에서 갑자기 뜬 레포”를 찾으려면 후보군이 필요합니다.
- `stargazers` 이력 API는 대형 저장소에서 페이지가 매우 많아 비효율적입니다.
- Search API만으로 일일 스타 증가량을 직접 구하기 어렵습니다.

권장 역할:
- 후보 발굴: OSS Insight/GH Archive/GitHub Trending
- 후보 검증/보강: GitHub API v3

---

### 2.5 GitStar Ranking - 언어별/전체 랭킹

확인 결과:
- 웹: `https://gitstar-ranking.com/`
- 전체 저장소 랭킹: `https://gitstar-ranking.com/repositories`
- 언어별/전체 누적 스타 랭킹을 제공합니다.

장점:
- “가장 스타가 많은 레포”를 빠르게 확인하기 좋습니다.
- 언어별/조직별 랭킹 탐색에 유용합니다.

단점:
- 목적이 “급등 탐지”라기보다 누적 랭킹입니다.
- 공식 API는 확인하지 못했습니다(미확인).
- 신규 급등 레포는 누적 스타가 낮으면 상위 랭킹에 나타나지 않을 수 있습니다.

권장 사용:
- 급등 탐지의 메인 소스로는 비추천
- 유명 저장소 대비 신생 저장소의 상대적 위치 확인용 보조 지표로 활용

---

### 2.6 Star History - 스타 변동 시각화, API 여부

확인 결과:
- 웹: `https://www.star-history.com/`
- GitHub 저장소: `https://github.com/star-history/star-history`
- 저장소별 스타 히스토리 그래프를 제공합니다.
- 사이트에서 Weekly/All-time leaderboard 형태의 최근 증가 저장소를 보여줍니다.

API 여부:
- 공식 public API는 확인하지 못했습니다(미확인).
- 웹 페이지/프론트엔드 내부 API를 사용할 가능성은 있으나, 안정적 운영용으로는 권장하지 않습니다.
- 자동화가 필요하면 OSS Insight의 stargazers history API 또는 GitHub API `starred_at`/GH Archive를 쓰는 편이 더 낫습니다.

장점:
- 시각화가 훌륭합니다.
- 특정 후보 저장소를 사람에게 보여줄 때 유용합니다.
- Weekly leaderboard는 급등 레포 보조 신호로 쓸 수 있습니다.

단점:
- 공식 API 미확인.
- 대규모 자동 분석보다는 시각화/검증용에 적합합니다.

---

### 2.7 GitHub Trending RSS feeds / 뉴스레터

#### GitHubTrendingRSS

확인 결과:
- 저장소: `https://github.com/mshibanami/GitHubTrendingRSS`
- 홈페이지: `https://mshibanami.github.io/GitHubTrendingRSS/`
- 설명: GitHub Trending 비공식 RSS feed generator

장점:
- RSS 리더, Slack RSS 앱, Discord RSS bot, 자체 cron 파서와 쉽게 연결됩니다.
- 직접 HTML 스크래퍼를 유지하지 않아도 됩니다.

단점:
- 비공식 프로젝트 의존.
- 원본 GitHub Trending 구조가 바뀌면 같이 깨질 수 있습니다.

#### Changelog Nightly

확인 결과:
- 저장소: `https://github.com/thechangelog/nightly`
- 홈페이지: `https://changelog.com/nightly`
- 설명: “hottest repos on GitHub before they blow up”를 표방하는 뉴스레터/프로젝트입니다.

장점:
- 단순 숫자 랭킹보다 사람이 읽기 좋은 큐레이션입니다.
- 이미 “뜨기 전” 저장소 발굴이라는 목적에 맞춰져 있습니다.

단점:
- 정량 기준과 원천 데이터가 내부 구현에 의존합니다.
- 알림 자동화는 RSS/email 파싱 또는 제공 채널에 따라 달라집니다.

#### GitDaily / GitHub Explorer 등

- 검색 결과상 GitHub 트렌딩/개발자 뉴스레터류 서비스가 여럿 존재합니다.
- 다만 현재 조사에서 “GitDaily”, “GitHub Explorer”의 공식적이고 안정적인 API/RSS 제공 여부는 명확히 확인하지 못했습니다(미확인).
- 실무에서는 특정 서비스에 강하게 의존하기보다, RSS가 있는 소스만 보조 입력으로 붙이는 것을 권장합니다.

---

### 2.8 GitHub Actions cron + GitHub CLI(gh) 자체 모니터링 스크립트

가장 간단한 자체 운영 구조:

```yaml
name: Daily GitHub Star Surge Monitor

on:
  schedule:
    - cron: '0 0 * * *'  # UTC 00:00, KST 09:00
  workflow_dispatch:

jobs:
  monitor:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Install deps
        run: pip install requests
      - name: Run monitor
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          DISCORD_WEBHOOK_URL: ${{ secrets.DISCORD_WEBHOOK_URL }}
          SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
        run: python scripts/github_star_surge_monitor.py
```

스크립트 설계:
1. OSS Insight `past_24_hours` 호출
2. 상위 N개 repo 추출
3. 관심 언어/키워드/제외 org 필터링
4. GitHub API로 상세 메타데이터 보강
5. 전날 결과 JSON과 비교해서 신규 진입/순위 상승/스타 증가량 계산
6. Markdown 메시지 생성
7. Discord/Slack webhook 전송
8. `data/YYYY-MM-DD.json` 저장 및 commit하거나 artifact/cache로 보관

`gh` CLI 활용 예:
```bash
gh api repos/{owner}/{repo} --jq '{name: .full_name, stars: .stargazers_count, language: .language, pushed_at: .pushed_at}'
```

장점:
- 별도 서버 없이 운영 가능합니다.
- GitHub Secrets로 webhook/token 관리가 쉽습니다.
- 결과를 repo에 누적 저장하면 추세 DB처럼 사용할 수 있습니다.

주의점:
- GitHub Actions 기본 `GITHUB_TOKEN`은 GitHub API 호출에 사용할 수 있으나, Search API 등 rate limit/권한에 주의해야 합니다.
- 매일 commit 방식으로 상태 저장 시 커밋이 너무 많이 쌓일 수 있으므로 `data/`만 갱신하거나 artifact/cache/외부 DB를 고려합니다.
- webhook 실패 재시도와 중복 발송 방지 키가 필요합니다.

---

### 2.9 Discord/Slack 웹훅 연동으로 매일 알림 파이프라인

Discord webhook 예:
```python
import requests

content = """# 오늘의 GitHub 스타 급등 레포
1. tinyhumansai/openhuman — +294 stars, Rust
2. ...
"""

requests.post(DISCORD_WEBHOOK_URL, json={"content": content[:1900]})
```

Slack webhook 예:
```python
import requests

payload = {
    "text": "오늘의 GitHub 스타 급등 레포",
    "blocks": [
        {"type": "section", "text": {"type": "mrkdwn", "text": "*1. owner/repo* — +294 stars"}}
    ]
}
requests.post(SLACK_WEBHOOK_URL, json=payload)
```

권장 메시지 필드:
- 순위
- 저장소명 + 링크
- 최근 24시간 스타 수 또는 OSS Insight stars
- 누적 스타 수
- 언어
- 설명
- 최근 push 시각
- 라이선스
- 급등 이유 후보: README 키워드, 릴리즈 여부, Hacker News/트위터 언급은 별도 확장

중복 방지:
- `repo_name + date`를 키로 저장
- 이미 보낸 repo는 “재등장”으로 표시하거나 일정 기간 숨김

---

## 3. 추천 아키텍처

### A안: 최소 운영 MVP

```text
GitHub Actions cron
  -> OSS Insight trends API(past_24_hours)
  -> GitHub API repo details
  -> Markdown summary
  -> Discord/Slack webhook
```

적합한 경우:
- 바로 매일 받아보고 싶을 때
- 별도 서버/DB를 두고 싶지 않을 때
- 정교한 통계보다 “오늘 뜨는 레포” 큐레이션이 중요할 때

장점:
- 1~2시간 내 구현 가능
- 무료에 가깝게 운영 가능
- 유지보수 부담 낮음

단점:
- OSS Insight 기준에 의존
- 세밀한 급등 기준 커스터마이즈는 제한

---

### B안: 정밀 탐지형

```text
GitHub Actions 또는 Cloud Run cron
  -> GH Archive 전날 24개 json.gz 다운로드
  -> WatchEvent started 집계
  -> 최근 7/14/30일 baseline과 비교
  -> surge_score 계산
  -> GitHub API로 repo metadata 보강
  -> SQLite/Postgres/BigQuery에 저장
  -> Discord/Slack webhook
```

적합한 경우:
- “스타가 갑자기 증가한” 것을 정량적으로 정의하고 싶을 때
- 특정 언어/AI/개발도구/보안 등 도메인별 급등 탐지가 필요할 때
- 장기적으로 데이터 자산을 만들고 싶을 때

장점:
- 가장 정확하고 커스터마이즈 가능
- z-score, 전일 대비, 이동평균 대비, 신규 repo 보정 등 가능

단점:
- 구현/운영 난이도 상승
- 데이터 처리량과 비용 관리 필요

---

### C안: 큐레이션 혼합형

```text
OSS Insight API
+ GitHub Trending RSS
+ Changelog Nightly
+ Star History weekly leaderboard
+ optional GH Archive score
  -> 후보 통합/중복 제거
  -> 점수화
  -> 일일 브리핑 발송
```

적합한 경우:
- 사람이 읽는 “트렌드 브리핑” 품질이 중요할 때
- 단순 star count 외에 큐레이션 신호를 넣고 싶을 때

장점:
- 노이즈 감소
- 이미 검증된 큐레이션 소스를 활용

단점:
- 소스별 포맷/안정성 관리 필요
- 일부 소스는 공식 API가 없어 스크래핑/RSS 의존

---

## 4. 구현 우선순위 제안

1. Day 1: OSS Insight 기반 MVP
   - `past_24_hours` API 호출
   - 상위 20개 Markdown 생성
   - Discord/Slack webhook 발송

2. Day 2: GitHub API 보강
   - 누적 `stargazers_count`
   - `created_at`, `pushed_at`, `license`, `topics`, `homepage`
   - 관심 언어 필터

3. Day 3: 상태 저장/중복 방지
   - `data/latest.json`
   - 전일 대비 신규 진입/순위 상승 표시

4. 이후: GH Archive 정밀 집계 추가
   - WatchEvent 일별 집계
   - 7일 평균 대비 급등 점수
   - 언어별/토픽별 별도 랭킹

---

## 5. 참고 소스

- GitHub Trending: https://github.com/trending
- GH Archive: https://www.gharchive.org/
- GH Archive data: https://data.gharchive.org/
- GitHub REST API docs - starring: https://docs.github.com/en/rest/activity/starring
- GitHub REST API rate limits: https://docs.github.com/en/rest/using-the-rest-api/rate-limits-for-the-rest-api
- GitHub REST API search: https://docs.github.com/en/rest/search/search
- OSS Insight: https://ossinsight.io/
- OSS Insight API docs: https://ossinsight.io/docs/api
- OSS Insight Trending: https://ossinsight.io/trending
- OSS Insight GitHub repo: https://github.com/pingcap/ossinsight
- GitStar Ranking: https://gitstar-ranking.com/
- Star History: https://www.star-history.com/
- Star History GitHub repo: https://github.com/star-history/star-history
- GitHubTrendingRSS: https://github.com/mshibanami/GitHubTrendingRSS
- Changelog Nightly: https://github.com/thechangelog/nightly
- Changelog Nightly site: https://changelog.com/nightly

---

## 최종 추천

최종적으로는 다음 조합을 추천합니다.

1. 즉시 구축: OSS Insight API + GitHub Actions cron + Discord/Slack webhook
2. 정확도 강화: GH Archive WatchEvent 집계로 자체 `stars_24h`/`surge_score` 계산
3. 브리핑 품질 강화: GitHub Trending RSS와 Changelog Nightly를 보조 신호로 통합

이 조합은 구현 속도, 비용, 정확도, 자동화 가능성의 균형이 가장 좋습니다.
