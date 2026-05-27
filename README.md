# Sooltluck Party — The Pour Archive

> Tasted. Noted. Bound.

술트럭파티 시음 리포트를 회차(卷)별로 묶는 정적 아카이브.

## Structure

```
/                  → 랜딩 · 회차 인덱스
/about/            → 술트럭파티 소개
/3rd/              → Vol. 03 (2025-11-22 · 서울 강남)
/4th/              → Vol. 04 (2026-05-16 · 룸532 파티룸)
```

각 회차는 자체 폴더에 `index.html` + CSV + 사진을 모두 담는 자급자족 구조. 새 회차가 생기면 `/Nth/` 폴더를 새로 만들고 랜딩의 카드를 추가합니다.

## Run

빌드 단계 없는 인라인 HTML. 그대로 브라우저로 열거나:

```bash
python3 -m http.server 8000
```

외부 의존성은 CDN뿐 — Google Fonts (Fraunces, Outfit, Noto Serif/Sans KR) + Chart.js. 오프라인이면 차트/폰트가 깨집니다.

## Volumes

| Vol. | Date | Stamp | Report |
|---|---|---|---|
| № 00 | 2020.06.27 | Archived | — |
| № 01 | 2022.08.27 | Archived | — |
| № 02 | 2022.11.19 | Archived | — |
| № 03 | 2025.11.22 | Archived | `/3rd/` |
| № 04 | 2026.05.16 | Released | `/4th/` |

## Notes

자세한 디자인 토큰·서체·인터랙션 규칙은 [`CLAUDE.md`](./CLAUDE.md)에 정리되어 있습니다.
