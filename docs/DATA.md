# 데이터 스키마

`data/` 폴더의 각 JSON 파일 구조. 갱신할 때 이 스키마를 깨지 않으면 index.html 손댈 일이 없음.

## overview.json

```jsonc
{
  "version": "v2",
  "generated": "2026-05-30",         // 갱신 일자 (사이트 헤더에 표시)
  "period": "2022~2024 회계연도",
  "sources_primary": ["공정거래위원회", "국세청", "CEO스코어"],
  "sources_secondary": ["프레스나인", "..."],
  "kpi": [
    {
      "label": "...",                 // 작은 설명
      "value": "232",                 // 큰 숫자
      "unit": "개",
      "tone": "accent"                // (선택) accent | danger | success | warn
    }
  ],
  "trend_groups": {
    "labels": ["2022", "2023", "2024", "2025"],
    "공시대상기업집단": [76, 78, 88, 92],
    "소속 공익법인": [null, 215, null, 232]
  },
  "trend_188": { /* 동일 형식 */ }
}
```

## ratio.json

```jsonc
{
  "source": "CEO스코어 2025.9 (...)",
  "universe": "73개 그룹 188개 공익법인",
  "average": 72.1,
  "low": [ { "group": "KCC", "ratio": 1.4 }, ... ],   // 50% 미만 그룹
  "high": [ { "group": "아모레퍼시픽", "ratio": 211.3 }, ... ],
  "unknown_count": 46,
  "unknown_band": "50~99% (...)",
  "kcc_detail": { /* 차트 하단 주석용 KCC 케이스 세부 */ }
}
```

## delta.json

```jsonc
{
  "source": "CEO스코어 (2023 → 2024 그룹 단위 합산)",
  "unit": "억원",
  "down": [ { "group": "HD현대", "change": -1961 }, ... ],
  "up":   [ { "group": "현대자동차", "change": 220 }, ... ],
  "summary": { /* 부가 합계 */ }
}
```

## structure.json

```jsonc
{
  "source": "국세청 ...",
  "universe": "72개 대기업 231개 공익법인",
  "asset":  { "총액_조원": 31.9, "구성": [ {"label":"...", "value": 33}, ... ] },
  "income": { "총액_조원": 9.4,  "구성": [...], "1법인당_기부금_억원": 46 },
  "expense_function": { "구성": [ {"label":"인력비용", "value":39}, ... ] },
  "expense_type":     { "구성": [...] },
  "분배비용_상위": [ {"그룹":"두산", "분배비용_억원":1878}, ... ],
  "그룹별_자산_상위":   [...],
  "그룹별_공익법인수_상위": [...],
  "유형분포": [...],
  "보유주식_100_특수관계_사례": [...]
}
```

## cases.json

9개 그룹 카드. tag_class는 색상 매핑 → `danger | warn | accent | success | mute`.

```jsonc
{
  "cards": [
    {
      "group": "삼성",
      "title": "삼성문화재단·...",
      "tag": "의결권 단계 축소",
      "tag_class": "accent",
      "stats": [
        { "label": "...", "value": "...", "tone": "danger" }
      ],
      "note": "..."
    }
  ],
  "extra_gc_nokvit": "GC녹십자 추가 설명"
}
```

## matrix.json

의결권 무력화 매트릭스 행.

```jsonc
{
  "rows": [
    {
      "그룹": "삼성",
      "재단": "삼성문화재단",
      "보유계열사": "삼성생명",
      "지분율": "4.68%",
      "상태": "무력화",
      "상태_class": "danger",  // danger | warn | success | mute
      "사유": "2023~ 한도 30% 초과"
    }
  ]
}
```

## deep-cases.json

5개 심층 케이스 + 0원 사례 2개.

```jsonc
{
  "cases": [
    {
      "no": "01", "group": "현대차",
      "title": "...",
      "tag": "...", "tag_class": "danger",
      "timeline": [
        { "year": "2007", "text": "...", "dim": false }   // dim: 보조 사실
      ],
      "summary_quote": "...",
      "summary_source": "프레스나인 2023.5.23",
      "extra": "..."  // (선택) 추가 단락
    }
  ],
  "zero_cases": [
    {
      "group": "SK", "재단": "행복전통마을",
      "stats": [ { "label":"...", "value":"...", "tone":"danger" } ],
      "note": "..."
    }
  ]
}
```

## policy.json

세제·의결권 단계·시민단체·재계.

```jsonc
{
  "tax_limit": {
    "title": "상속·증여세 면제 한도",
    "rows": [ { "구분":"...", "한도":"5%", "tone":"accent" } ],
    "note": "..."
  },
  "voting_limit": {
    "title": "의결권 단계 축소 ...",
    "stages": [ { "year":"2023", "limit":30, "tone":"" } ],
    "note": "..."
  },
  "context_cards": [ { "title":"...", "text":"..." } ],
  "civic_view":    { "title":"...", "paragraphs": ["..."] },
  "industry_view": { "title":"...", "paragraphs": ["..."] }
}
```

## sources.json

페이지 하단 「출처」 섹션.

```jsonc
{
  "groups": [
    {
      "label": "공정위",
      "items": [
        { "title": "2023.12.18 ...", "url": "https://..." }
      ]
    }
  ]
}
```

## 색상 토큰 (참고)

- `tone: "danger"` → 빨강 (`#ef4444`)
- `tone: "warn"` → 주황 (`#fb923c`)
- `tone: "accent"` → 노랑 (`#fbbf24`)
- `tone: "success"` → 초록 (`#34d399`)
- `tone: "mute"` → 회색

## 추가 규칙

- 새 그룹 케이스를 cases.json에 추가하면 matrix.json에 의결권 행도 함께 추가
- 사업비 비율 새 발표가 나오면 ratio.json + delta.json 동시 갱신
- 모든 평가/해석 문장은 출처 명시 (CLAUDE.md 「데스크 교정 규칙」 따라)
