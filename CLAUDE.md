# Sooltluck Party — The Pour Archive

술트럭파티 시음 리포트를 회차(卷)별로 묶는 정적 아카이브 사이트. 빌드 단계 없는 인라인 HTML.

## 파일 구조

```
.
├── index.html              # 랜딩 — "The Pour Archive" 회차 인덱스
├── 3rd/                    # 3회차 (2025-11-22, 서울 강남) — Released
│   ├── index.html
│   ├── img_*.jpeg / img_*.jpg
│   └── sooltluck-2025.csv, sooltluck-all-bottle-2025.csv
├── 4th/                    # 4회차 — Sealed 플레이스홀더
│   └── index.html
└── CLAUDE.md
```

각 회차는 자체 폴더에 자료를 모두 담는 자급자족 구조. 새 회차가 추가되면 `/Nth/` 폴더를 새로 만든다.

## 실행

빌드/서버 불필요. `index.html` 을 브라우저로 열거나:

```bash
python3 -m http.server 8000
```

외부 의존성은 CDN만 — Google Fonts (Fraunces, Outfit, Noto Serif KR, Noto Sans KR) + 3회차에서 Chart.js (`cdn.jsdelivr.net`). 오프라인이면 차트/폰트가 깨진다.

## 디자인 시스템

### 컨셉 — "The Pour Archive"
편집·서가(書架) 미감. 회차를 한 권(卷)의 책처럼 다루고, 큰 세리프 디스플레이 + 파치먼트 톤 + 그레인 오버레이 + 왁스 씰 모티프로 의례적인 무드를 만든다.

- **활성 회차 (Released)**: 호버 시 들여쓰기 + 숫자 색 전이(파치먼트 → 앰버) + 좌측 4px 액센트 바.
- **봉인 회차 (Sealed)**: 회색조 숫자 + 자색 시머 애니메이션 + 왁스 씰 시각화. 클릭 시 `/Nth/` placeholder 페이지로.

### 토큰 (모든 페이지 공통)
```
--bg:           #111111      (배경)
--ink:          #F3F4F6      (기본 흰 텍스트)
--ink-muted:    #9CA3AF      (보조 회색)
--paper:        #E8DFC9      (파치먼트 크림 — 편집체 텍스트)
--paper-muted:  #8B826B      (어두운 파치먼트)
--primary-blue: #60A5FA      (3회차에서 계승 — Released stamp)
--accent-orange:#FBBF24      (호버/하이라이트)
--accent-purple:#A78BFA      (Sealed 상태)
```

### 서체
- **Fraunces** (가변 세리프, ital + opsz) — 디스플레이/편집 모먼트
- **Outfit** — UI 텍스트, 라벨, 캡션
- **Noto Serif KR / Noto Sans KR** — 한국어 본문, 한글 편집체 디테일

### 분위기 디테일
- `body::before` — 라디얼 그라데이션 색 워시 (앰버/블루/퍼플)
- `body::after` — SVG `feTurbulence` 노이즈를 `mix-blend-mode: overlay` 로 깔아 필름 그레인 질감
- 카드 호버 — `cubic-bezier(.2,.7,.2,1)` 이지로 들여쓰기/숫자 시프트
- 4회차 페이지 — SVG `<textPath>` 로 원형 회전 텍스트가 들어간 왁스 씰, `pulse` keyframe 으로 상태 표시

## 페이지별

### 랜딩 `/index.html`
- Masthead → Wordmark ("Tasted. Noted. Bound.") → Section rule → Volumes ledger → Colophon.
- Volumes 카드: `220px / 1fr / 220px` 그리드 (번호 / 본문·메타 / 상태).
- 반응형 분기: `@media (max-width: 1100px)` (lede 1열로) / `@media (max-width: 900px)` (전부 1열, 메타 2x2).
- 로드 시 `.reveal` 스태거 (`r1`~`r7`, 0.05s 간격).

### 3회차 `/3rd/index.html`
기존 리포트. 외부에서 보면 자급자족 정적 1페이지. 데이터/인터랙션 디테일:

- **데이터 원본**: `3rd/sooltluck-2025.csv` 가 점수 원본이지만, **실제 페이지가 읽는 것은 `3rd/index.html` 내부의 `csvData` 템플릿 리터럴** (line ~853). CSV 만 수정하면 페이지에 반영되지 않으므로 두 곳 동기화 필수.
- **CSV 구조**: 열 0 빈칸 / 1 전통주 마커(`O`) / 2 주종 / 3 주류 이름(콤마 시 따옴표) / 4 시음번호 / 5~21 평가자 17명 점수 / 22 평균. 상단 2~3행에 평가자별 나이대/성별.
- **시음 주류**: 총 33종이지만 평가/차트는 **1~29번만** 사용. 30~33은 이벤트용. 9번/18번은 같은 술(Louis Perdrier Brut Excellence)을 의도치 않게 두 번 시음한 케이스.
- **주요 인터랙션**: `parseCSV` 수동 파서(line 918~) / 랭킹·주종별 Chart.js / `updateHero` 스크롤 패럴랙스 / `updateGallery` 짝홀 좌우 슬라이드 / `IntersectionObserver` 호스트 텍스트 페이드인.
- **모바일 분기 (line ~1326, 커밋 `e4cf1b8`)**: 모바일(≤900px) 에서는 호스트 텍스트의 `visible` 클래스를 제거하지 않는다. 스크롤 시 텍스트가 사라지던 버그를 막기 위한 의도된 분기. 함부로 단순화 금지.
- **하드코딩 주의**: 평가자 수 17명이 `slice(5, 22)` 로 하드코딩(line 895/900/940). 인원이 바뀌면 슬라이스 범위와 합계 행 위치 동시 수정.
- **주종 분류**: 차트 색상은 문자열 매칭(`type.includes('위스키')`). 새 주종은 기존 분류에 포함 안 되면 "기타"(teal) 로 떨어짐.

### 4회차 `/4th/index.html`
"Sealed" 플레이스홀더. 콘텐츠가 준비되면 3회차 패턴을 답습하여 채워 넣는다. SVG 왁스 씰과 `pulse` 애니메이션을 사용한다.

## 새 회차 추가하기

1. `/Nth/` 폴더 생성. 3회차를 자료째로 복제 후 콘텐츠/CSV 교체.
2. CSV 와 `index.html` 내부 `csvData` 모두 갱신.
3. 랜딩 `/index.html` 의 `<nav class="volumes">` 에 새 `<a class="volume volume--released">` 카드를 추가하고, 봉인된 회차는 `volume--sealed` 로 표시.
4. Masthead 의 `N° 04` 도 다음 권 숫자로 갱신.

## 작업 시 주의

- 모든 사용자 노출 텍스트는 한국어 + 영문 편집체 혼용. 영어 카피는 짧고 운율 있는 편집 헤드라인(예: "Tasted. Noted. Bound.") 톤을 유지.
- 그레인/그라데이션 워시는 `position: fixed` 라 스크롤과 무관하게 일관된 분위기를 만든다. 제거 시 헐벗어 보임.
- 코멘트/문서 신규 작성은 사용자가 요청할 때만.
