# Sooltluck Party — The Pour Archive

술트럭파티 시음 리포트를 회차(卷)별로 묶는 정적 아카이브 사이트. 빌드 단계 없는 인라인 HTML.

## 파일 구조

```
.
├── index.html              # 랜딩 — "The Pour Archive" 회차 인덱스
├── README.md
├── _headers                # Cloudflare Pages 캐시·보안 헤더
├── img_bottles_bar.png     # 랜딩 wordmark 배경
├── about/                  # 술트럭파티 소개
│   ├── index.html
│   └── img_*.jpg / png
├── 0th/ ~ 2nd/             # 0~2회차 — 메타데이터만 있는 간이 페이지 (Archived)
│   └── index.html
├── 3rd/                    # Vol. 03 (2025-11-22, 서울 강남) — Archived
│   ├── index.html
│   ├── index-relayout.html # 대안 레이아웃 백업 (배포 미사용)
│   ├── img_*.jpeg / .jpg
│   └── sooltluck-2025.csv, sooltluck-all-bottle-2025.csv
├── 4th/                    # Vol. 04 (2026-05-16, 룸532 파티룸) — Released · latest
│   ├── index.html
│   ├── img_*.jpeg
│   └── sooltluck-4th.csv, sooltluck-4th-all-bottle.csv
└── CLAUDE.md
```

각 회차는 자체 폴더에 자료를 모두 담는 자급자족 구조. 새 회차가 추가되면 `/Nth/` 폴더를 새로 만들고 랜딩의 카드 + stamp 상태를 갱신한다.

## 실행 / 배포

로컬: 빌드 단계 없음. `index.html` 을 브라우저로 열거나

```bash
python3 -m http.server 8000
```

배포: **Cloudflare Pages** — `https://sooltluck-party.pages.dev`. `main` push 시 자동 빌드(빌드 명령 없음, output 디렉토리 `/`). `_headers` 파일이 캐시(이미지 1년 immutable, 나머지 1시간) + 보안 헤더(X-Frame-Options / X-Content-Type-Options / Referrer-Policy)를 적용한다. **Cloudflare Web Analytics** 스니펫이 모든 페이지 `</head>` 직전에 삽입돼 있다 (사이트별 토큰이라 노출 안전).

원격 push 인증: 이 repo는 **conquerex** 계정 소유. `~/.ssh/config` 에 `Host github.com-conquerex` alias가 설정돼 있고, `origin` remote URL이 그 alias를 가리킨다. 다른 repo(barley-sfn)와 SSH 키가 분리돼 있다.

외부 의존성은 CDN만 — Google Fonts + 3·4회차의 Chart.js + Cloudflare beacon. 오프라인이면 차트/폰트/Analytics가 깨진다.

## 디자인 시스템

### 컨셉 — "The Pour Archive"
편집·서가(書架) 미감. 회차를 한 권의 책처럼 다루고, 큰 세리프 디스플레이 + 파치먼트 톤 + 그레인 오버레이로 의례적인 무드를 만든다. 슬로건 — **Tasted. Noted. Bound.**

### 토큰 (모든 페이지 공통)
```
--bg:           #111111
--ink:          #F3F4F6
--ink-muted:    #9CA3AF
--paper:        #E8DFC9    (파치먼트 — 편집체 텍스트)
--paper-muted:  #8B826B
--primary-blue: #60A5FA    (최근 권 stamp)
--accent-orange:#FBBF24    (호버 / em 하이라이트 / 차트 상승)
--accent-purple:#A78BFA    (예정 stamp / 차트 하락)
--accent-teal:  #2DD4BF    (4회차 차트 — 증류주)
--rule:         rgba(232, 223, 201, 0.18)
```

### 서체
- **Fraunces** (가변 세리프, ital + opsz) — 디스플레이·편집 모먼트
- **Outfit** — UI 텍스트, 라벨, 캡션
- **Noto Serif KR / Noto Sans KR** — 한국어 본문

### Stamp 시스템 (랜딩 카드)
행사 시점으로 세 가지 구분. `.volume--released` 안의 `.stamp` modifier:

| 상태 | Modifier | 라벨 | 색 / 시머 |
|---|---|---|---|
| 지나간 행사 | `.stamp` (기본) | **Archived** | paper / 시머 OFF |
| 가장 최근 행사 | `.stamp--latest` | **Released** | primary-blue / 파란 시머 |
| 예정 행사 | `.stamp--coming-soon` | (예: Coming Soon) | accent-purple / 보라 시머 |

`.volume--sealed` 클래스도 정의돼 있지만 현재 사용 카드 없음.

### 분위기 디테일
- `body::before` — 라디얼 그라데이션 색 워시
- `body::after` — SVG `feTurbulence` 노이즈를 `mix-blend-mode: overlay` 로 깔아 필름 그레인
- 카드 호버 — `cubic-bezier(.2,.7,.2,1)` 이지로 들여쓰기/숫자 색 시프트
- 랜딩 wordmark — `img_bottles_bar.png` 가 wordmark 섹션 자체 배경으로 깔리고 상단 어둠 → 하단 짙은 페이드 그라데이션으로 텍스트 가독성 확보

## 페이지별

### 랜딩 `/index.html`
- Masthead → Wordmark (배경 이미지 + "Tasted. Noted. Bound." + lede) → Section rule → Volumes ledger → Colophon.
- Volumes 카드: `220px / 1fr / 220px` 그리드 (번호 / 본문·메타 / 상태).
- 반응형: `@max 1100px` (wordmark·meta 1열) / `@max 900px` (전부 1열).
- 로드 시 `.reveal` 스태거 (`r1`~`r7`, 0.05s 간격).
- 0·1·2·3회차 = Archived stamp, 4회차 = Released latest.

### 소개 `/about/index.html`
술트럭파티의 의례·매너·참가비를 4부(I. The Gathering / II. The Ritual / III. House Notes / IV. Correspondence)로 묶은 정적 페이지. 연락처는 `conquerex@gmail.com` 만 공개.

### 0~2회차 `/0th/` `/1st/` `/2nd/index.html`
행사 메타데이터만 있는 간이 페이지. 시음 리포트는 없는 권. 랜딩 카드에서 Archived stamp.

### 3회차 `/3rd/index.html` — Vol. 03 (2025-11-22 · 서울 강남)
17명 평가자 · 33종(평가 1~29, 이벤트 30~33) · 평균 3.18.

- **데이터 원본**: `3rd/sooltluck-2025.csv` 가 점수 원본이지만, **실제 페이지가 읽는 것은 `3rd/index.html` 내부의 `csvData` 템플릿 리터럴** (line ~853). CSV 만 수정하면 페이지에 반영되지 않으므로 두 곳 동기화 필수.
- **CSV 구조**: 열 0 빈칸 / 1 전통주 마커(`O`) / 2 주종 / 3 주류 이름(콤마 시 따옴표) / 4 시음번호 / 5~21 평가자 17명 점수 / 22 평균. 상단 2~3행에 평가자별 나이대/성별.
- **시음 주류**: 총 33종, 평가/차트는 1~29번만. 9번/18번은 같은 술(Louis Perdrier Brut Excellence) 의도치 않게 두 번 시음.
- **주요 인터랙션**: `parseCSV` 수동 파서(line 918~) / 랭킹·주종별 Chart.js / `updateHero` 스크롤 패럴랙스 / `updateGallery` 짝홀 좌우 슬라이드 / `IntersectionObserver` 호스트 텍스트 페이드인.
- **모바일 분기 (line ~1326, 커밋 `e4cf1b8`)**: 모바일(≤900px) 에서는 호스트 텍스트의 `visible` 클래스를 제거하지 않는다. 함부로 단순화 금지.
- **하드코딩**: 평가자 수 17명이 `slice(5, 22)` 로 하드코딩(line 895/900/940).
- **주종 분류**: 차트 색상은 문자열 매칭(`type.includes('위스키')`). 새 주종이 분류 외면 "기타"(teal).
- **`index-relayout.html`**: 대안 레이아웃 백업. 배포에 영향 없음 — 만지지 않는다.

### 4회차 `/4th/index.html` — Vol. 04 "Wide Pour" (2026-05-16 · 룸532 파티룸)
20명 평가자 · 12종 시음(+ 이벤트 2종) · 평균 3.52. 3회차 패턴을 답습하되 데이터 형태가 다름:

- **원본 CSV 방향이 다름**: `sooltluck-4th.csv` 는 **rater-major** (행=평가자, 열=술). 페이지 내부 `csvData` 는 3회차 파서 재사용 위해 **bottle-major로 transpose** 된 상태로 들어 있다 (line ~1349). CSV 와 csvData 모두 갱신 필요.
- **평가자 이름 마스킹**: 페이지에 노출되는 이름은 성+`**` 형태 (예: `민**`). 호버 툴팁에도 마스킹된 이름만. 원본 CSV에는 실명 보존. 마스킹 컨벤션을 깨지 말 것.
- **하드코딩**: 평가자 20명이 `slice(5, 25)` (line 1371~1374, 1393).
- **카테고리 매핑** — 4회차에서 새로 도입한 발효주/증류주 분류:
  - `fermentedKinds = ['막걸리', '청약주', '과실주', '맥주', '와인']`
  - `distilledKinds = ['브랜디', '보드카', '위스키', '백주']`
  - 차트 라벨: 전통주 / 위스키 / 발효주 / 증류주 / 전체.
  - 3회차와 비교 계산 시 약주·황주·사케·미드는 발효주, 리큐르·소주·일본소주·증류주는 증류주로 잡았다.
- **호스트 섹션 없음**: 3회차의 호스트 인물 사진 자리는 "X. Closing Notes" 로 대체. `.host-text` 패턴과 모바일 `.visible` 분기는 보존.
- **Plate 구성**: Plate I (보틀 단일) → Plate II (현장 5장 — 첫 spread 3장 + 두 번째 spread `.plate-spread.is-pair` modifier 1.4:1 비율 2장) → Closing 단체사진(`.closing-portrait`, 자연 비율).
- **술 이름 표기**: 한글 우선, 영문/한자는 괄호 안 (예: `포쉐트 (Fourchette)`, `명녹액 (明绿液)`, `소테른 (Sauternes)`).
- **Findings-side**: 7개 행 모두 3회차 대비 비교 수치 + Δ% (상승=accent-orange, 하락=accent-purple).
- **footer 표기**: `Hosted · Jongkook · Jiwon · Eunsang`. "Curated" 대신 "Hosted" 컨벤션.

## 새 회차 추가하기

1. `/Nth/` 폴더 생성. 4회차를 자료째로 복제 후 콘텐츠·CSV 교체.
2. CSV 와 `index.html` 내부 `csvData` 모두 갱신 (rater-major CSV 라면 bottle-major로 transpose).
3. 평가자 수에 따라 slice 범위 (`slice(5, 5+N)`) 동시 수정.
4. 술 이름 표기 컨벤션 확인 (한글 우선, 영문/한자 괄호).
5. 평가자 이름 마스킹 적용 (성+`**`).
6. 랜딩 `/index.html` 의 `<nav class="volumes">` 에 새 카드 추가.
7. **이전 최신 권의 stamp를 `.stamp--latest` 에서 기본 `.stamp` (Archived)로 강등**, 새 권에 `.stamp--latest` (Released) 부여.
8. 새 페이지 `</head>` 직전에 Cloudflare Web Analytics 스니펫이 들어가는지 확인 (복제 시 같이 따라옴).

## 작업 시 주의

- 모든 사용자 노출 텍스트는 한국어 + 영문 편집체 혼용. 영어 카피는 짧고 운율 있는 편집 헤드라인 톤 ("Tasted. Noted. Bound." 류).
- 술 이름은 한글 우선 + 영문/한자 괄호 표기 컨벤션 유지.
- 평가자 실명은 페이지에 노출 금지 (성+`**` 마스킹). 호스트 이름은 footer 의 `Hosted ·` 라인에만 영문 표기.
- 그레인/그라데이션 워시는 `position: fixed` 라 스크롤과 무관하게 일관된 분위기를 만든다. 제거 시 헐벗어 보임.
- 코멘트/문서 신규 작성은 사용자가 요청할 때만.
- 푸시는 conquerex 계정 SSH alias 로만 — git config 건드리지 않는다.
