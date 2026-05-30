# 대기업집단 공익법인 결산 스캔

공정거래위원회 지정 92개 공시대상기업집단 소속 232개 공익법인의 결산·의결권·내부거래·세제를 한 페이지에서.

> 단일 파일 정적 대시보드. 데이터는 `data/*.json`에 분리. Chart.js + Tailwind (CDN). 백엔드 없음.

## 구조

```
chaebol-foundations/
├── index.html              # 본문 (data/ 폴더 JSON을 fetch로 로드)
├── case-hyundai.html       # Case Deep Dive 01 — 현대차 정몽구재단 (5개 의혹 카테고리)
├── data/                   # 데이터 — 갱신할 때 여기만 만지면 됨
│   ├── overview.json       # KPI · 3년 추세
│   ├── ratio.json          # 73개 그룹 사업비 비율 (하위 17 · 상위 10)
│   ├── delta.json          # 사업비 증감 10/10
│   ├── structure.json      # 자산·수입·비용 구성 + 분배비용 상위 등
│   ├── cases.json          # 9개 그룹 케이스 카드 (carrier로 deep_dive_url 포함)
│   ├── matrix.json         # 의결권 무력화 매트릭스 23행
│   ├── deep-cases.json     # 5개 개별 케이스 심층 (타임라인) + 0원 사례
│   ├── policy.json         # 세제 · 의결권 단계 · 시민단체 vs 재계
│   ├── sources.json        # 출처 링크 모음
│   └── case-hyundai.json   # Case 01 데이터 (의혹 5개·타임라인 18년·SPC 거래·이사진)
├── docs/                   # 추가 문서
│   ├── DATA.md             # 데이터 스키마
│   ├── DEPLOY.md           # GitHub Pages · EC2 배포 계획
│   └── NEXT.md             # 추가 취재 포인트 + 후속 작업
├── scripts/                # (예정) 데이터 수집·갱신 스크립트 자리
├── .gitignore
└── README.md
```

## 케이스 딥다이브

대시보드의 그룹 카드는 「수치와 정황」을 보여주지만, "왜 문제인가"의 인과 구조는 별도 케이스 페이지로 분리.

- **Case 01 · 현대차 정몽구재단** (`case-hyundai.html`) — 1조원 사회환원 약속 18년 추적, 2022.1 SPC 거래로 정의선 지분이 사익편취 임계점(20%)에 정확히 정렬된 정황까지
- **Case 02 · SM 필의료재단** (`case-sm.html`) — 김혜란(2023.9 별세) 상속 6개월 안에 우기원이 「보통주를 우선주로 전환 → 5%룰 한도까지 출연 → 출연 직전 이사진 사임」 동시 진행. 같은 시기 우오현·우지영 「업무상 횡령·배임」 고발
- **Case 03 · DL 통일과나눔** (`case-dl.html`) — 이준용(대림 명예회장)이 2016.10 외부 비영리법인 「통일과나눔」에 대림코퍼레이션 32.6%(평가액 2,868억) 무상 출연 → 2019.9 통일과나눔이 KCGI에 1,200억(장부가 1/4)에 매각. 「공익법인 통한 우회 매각」의 가장 노골적 사례
- **Case 04 · 삼성 4개 재단** (`case-samsung.html`) — 4개 재단 × 6개 계열사 의결권 매트릭스. 2023~2026 단계 축소 + 「삼성생명법」 + 삼성웰스토리 사법리스크가 「이재용 → 삼성물산 → 삼성생명 → 삼성전자」 지배구조를 단계적으로 압박하는 진행형 케이스
- **Case 05 · CJ 3단 우회 승계** (`case-cj.html`) — 다른 그룹과 달리 「재단」이 아닌 「개인회사」가 승계 도구. 씨앤아이레저산업(오너 일가 100%) → 타임와이즈인베스트먼트 → CJ올리브영(이선호 11.04%) 3단 경로. 이재현 회장 500억 탈세·700억 횡령으로 징역 2년 6개월 → 4개월 복역 후 2016 광복절 특사. 사위 정종환 CJ ENM 분기별 자사주 매입 (실적 발표 직후·분기보고서 직전 「규칙적 패턴」)
- **Case 06 · DB 두 재단** (`case-db.html`) — 한 그룹에 「공식 재단」(DB김준기문화재단)과 「그림자 재단」(동곡사회복지재단)이 명확히 역할 분담된 유일 케이스. 공식 재단은 2022.12.28(의결권 제한 시행 2일 전) DB 4%를 김준기에 89억(출연가의 30%)에 매각. 그림자 재단은 15년간 삼동흥산·빌텍 거쳐 220억 김준기 개인 대여·DB하이텍 지분 우회 취득 등 사익 전용. 2026.2 공정위가 김준기를 검찰 고발

다음 후보: LG 상속분쟁 / KCC 서전문화재단 / 롯데·한화 등

새 케이스를 추가하려면:
1. `data/case-{group}.json` 데이터 작성 (5개 의혹·타임라인·핵심 숫자·이사진·출처)
2. `case-{group}.html`은 `case-hyundai.html` 복제 후 데이터 경로만 교체
3. `data/cases.json`의 해당 그룹 카드에 `"deep_dive_url": "case-{group}.html"` 추가
4. `index.html` 상단 배너에 링크 추가

## 로컬에서 보기

`file://` 프로토콜로 직접 열면 fetch가 막혀 데이터를 못 불러옵니다. 둘 중 하나로 띄우세요.

### Python (가장 간단)

```bash
cd chaebol-foundations
python3 -m http.server 8080
# http://localhost:8080 에서 확인
```

### Node

```bash
npx serve chaebol-foundations
# http://localhost:3000 에서 확인
```

## GitHub Pages 배포

레포 Settings → Pages → Source = `Deploy from a branch` → Branch = `main` / Folder = `/ (root)` → Save.

수 분 내에 `https://USERNAME.github.io/chaebol-foundations/` URL 생성.

## EC2 이전 계획

`docs/DEPLOY.md` 참고. 단일 정적 사이트라 Nginx로 그대로 서빙. 향후 데이터 수집 cron + 데이터 API가 붙으면 Node 백엔드 추가.

## 갱신 흐름

데이터만 바꾸면 됩니다. HTML은 손대지 않음.

1. CEO스코어 다음 발표(연 1회 추정)가 나오면 `data/ratio.json` · `data/delta.json` 갱신
2. 공정위 매년 5월 발표 후 `data/overview.json` 모집단 수 갱신
3. 국세청 차기 연차보고서 발간 시 `data/structure.json` 갱신
4. 새 케이스가 나오면 `data/cases.json` · `data/deep-cases.json` · `data/matrix.json` 추가

각 JSON 파일 상단에 `source` 필드로 출처 명시. 새 출처가 들어가면 `data/sources.json`도 같이 업데이트.

## 데이터 출처

- 공정거래위원회 — 연 1~2회 「공시대상기업집단 비영리법인 운영현황」, 「주식소유현황」, 「내부거래 현황」
- 국세청 — 「공익법인 결산서류 공시시스템」 + 「공익법인 연차보고서」(연 1회)
- 기업데이터연구소 CEO스코어 — 연 1회 「공시대상기업집단 특수관계 공익법인 사업수행비용 조사」
- 프레스나인 — 「공익법인 의결권」 시리즈 (2023.5)

전체 링크는 `data/sources.json` 또는 페이지 하단 「출처」 섹션 참고.

## 라이선스

이 리포지토리의 코드(HTML/CSS/JS)는 MIT. 데이터는 각 1차 출처(공정위·국세청·CEO스코어 등)의 저작권을 따르며, 본 정리물은 출처 명시 + 비상업 인용 범위에서 사용.

## 변경 이력

| 날짜 | 버전 | 변경 |
|---|---|---|
| 2026-05-30 | v2 | 초기 스캐폴드 — 9개 그룹 케이스 + 의결권 매트릭스 + 5개 심층 케이스 + 제도·세제 + 시민단체·재계 두 입장 |
