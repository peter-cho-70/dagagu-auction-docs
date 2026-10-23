# 대전 다가구 경매 파이프라인

대전 지방 다가구주택 경매 물건을 조사하는 과정을 자동화하는 **로컬 전용** 프로그램. 개인 투자 리서치용이며, 외부 서버 없이 Mac 로컬에서만 동작한다.

## 왜 만들었나

대법원 법원경매정보에서 물건을 추려서, 전문 경매사이트(보스옥션)에서 등기·감정평가서 등 상세 자료를 받아 권리분석을 하는 과정을 매번 수작업으로 반복하다가, 반복되는 부분(리스트업 → 자료 수집)을 프로그램으로 옮겼다.

## 구성 (3단계)

1. **법원 리스트업** — `scripts/scrape-daejeon.mjs`: 대전 다가구주택 전체(신건+유찰) 물건을 긁어와 네이버지도 링크를 붙인 JSON으로 저장
2. **로컬 웹앱** — `server/server.mjs` + `public/index.html`: 1단계 결과를 보면서 물건을 고르고, 지도로 위치를 확인하고, 가져올 경매사이트·자료를 선택
3. **자료 수집** — `scripts/fetch-bossauction.mjs`: 선택한 물건의 자료(감정평가서·매각물건명세서·현황조사서·문건송달내역·등기 2종)를 보스옥션에서 가져와 로컬에 저장

## 실행 방법

```bash
# 1단계만 단독 실행 (리스트 JSON 갱신)
ego-browser nodejs < scripts/scrape-daejeon.mjs

# 웹앱 실행 (2·3단계는 여기서 다 됨, "새로고침" 버튼으로 1단계도 다시 돌릴 수 있음)
node server/server.mjs
# → http://localhost:4600
```

## 기술 스택

- 브라우저 자동화: [ego lite](https://lite.ego.app/) (`ego-browser nodejs`) — 법원 사이트·보스옥션 모두 실제 브라우저로 조회
- 서버: Node.js 내장 `http` 모듈만 사용 (외부 의존성 없음, `npm install` 불필요)
- 데이터: JSON 파일 (로컬 우선 — 외부 DB·클라우드 없음)
- 프런트: 바닐라 HTML/CSS/JS (빌드 도구 없음)

## 개인정보 처리

받아온 자료(등기부·매각물건명세서 등)에는 소유자·임차인 실명과 주민등록번호 일부가 포함될 수 있어 `downloads/`는 `.gitignore`로 제외한다. 이 자료를 바탕으로 만드는 분석 리포트는 법원경매정보가 자체적으로 마스킹하는 수준에 맞춰 실명을 익명화해서만 작성한다.

## 문서

- [PRD — 설계 문서](https://peter-cho-70.github.io/dagagu-auction-docs/doc/viewer.html?file=spec/PRD.md)
- [개발 현황 (STATUS)](https://peter-cho-70.github.io/dagagu-auction-docs/doc/viewer.html?file=STATUS.md)
- [개발 기록 (DEVLOG)](https://peter-cho-70.github.io/dagagu-auction-docs/doc/viewer.html?file=DEVLOG.md)
