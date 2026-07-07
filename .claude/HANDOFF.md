# yc-young-adults — Codex Handoff (v0.3.0)

## 현재 상태
- 최신 커밋: `6600e88` "관리자 페이지에 결과 내보내기 기능 추가 (향후 결과지 작성용)"
- 브랜치: `main`
- GitHub 저장소: https://github.com/max2guy/yc-young-adults (Public)
- GitHub Pages 배포됨: https://max2guy.github.io/yc-young-adults/

## 방금 수정한 내용
- 설문 문항 수정: Q6 다음에 "좋았던 프로그램은 무엇인가요?"(경매/레크레이션/기도손/사진 전시/기타) 문항 신설 → 이후 문항 id 전부 +1 리넘버링(q7~q14)
- Q8(구 Q7) 선택지 수정: "결단찬송" → "특송", "친교" → "친교(레크레이션, 경매 포함)"
- 관리자(`#admin`) 페이지에 **"결과 내보내기"** 버튼 추가: 전체 문항 통계 + 주관식 응답을 텍스트로 정리해 클립보드에 복사 (`buildReportText()` / `exportReportText()` 함수, `2026-homecoming-survey.html` 내)

## 프로젝트 개요
- 순수 정적 HTML/CSS/JS (빌드 과정 없음, 프레임워크 없음)
- Firebase: Realtime Database만 사용 (`ycprayer-7eac2` 프로젝트 기존 것을 임시로 재사용 중 — 사용자가 별도 Firebase 프로젝트를 원하면 `firebaseConfig` 교체 필요)
- 데이터 경로: `homecoming2026/responses`
- 인증 없음, 관리자 페이지는 PIN 하드코딩(`ADMIN_PIN = "6291"`) 방식 — 보안 수준 낮음, 민감 데이터 저장 금지
- 배포: `gh repo create` + `git push` 로 생성, GitHub Pages는 `gh api repos/.../pages` REST 호출로 활성화함 (수동 웹 설정 아님)

## 주요 파일
- `index.html` — 청년부 자료실 메인(허브) 페이지. `RESOURCES` 배열 수정으로 자료 추가/삭제
- `2026-homecoming-survey.html` — 홈커밍데이 설문(Q1~Q14) + 관리자 통계 대시보드 + 결과 내보내기 기능 (단일 파일)

## 다음으로 할 수 있는 작업
- **문항 추가 수정 예정** — 사용자가 "나중에 문항 수정이 있을 예정"이라고 언급함. `Q` 배열(대략 146번째 줄 부근) 수정 시, id는 위치 기반이 아니라 임의 문자열이라 순서만 맞으면 렌더링/집계 로직 안 건드려도 됨. 단, 이미 응답이 쌓인 뒤엔 id를 바꾸면 과거 응답과 매핑이 어긋나므로 주의.
- **결과지(보고서) 작성 — 응답이 쌓이면 진행**: 절차는 다음과 같음
  1. `https://max2guy.github.io/yc-young-adults/2026-homecoming-survey.html#admin` 접속, PIN `6291` 입력
  2. "결과 내보내기" 버튼 클릭 → 클립보드에 텍스트 요약 복사됨
  3. 이 텍스트를 Claude에게 붙여넣고 "지난번 체육대회 결과지처럼 작성해줘"라고 요청
  4. 참고 전례: `~/Downloads/체육대회_결과보고서_2026.docx`, `~/Downloads/체육대회_통합결과보고서_2026.docx`, `~/Downloads/체육대회_피드백_결과보고서.docx` — 이 사례들처럼 최종 결과물은 **docx 문서**로 작성하는 것이 관례
  5. (참고) `~/Downloads/sports_day_feedback_analyzer.html`은 브라우저에서 Anthropic API를 직접 호출하는 방식인데, 이는 Claude.ai 아티팩트 샌드박스 안에서만 동작하는 패턴이라 일반 GitHub Pages에는 이식 불가 (API 키 노출 위험) — 그래서 이번엔 "텍스트 내보내기 → Claude에게 직접 전달" 방식으로 대체함
- Firebase 프로젝트를 전용으로 새로 팔 경우 `2026-homecoming-survey.html`의 `firebaseConfig` 교체

## 빌드 & 배포
- 빌드 명령 없음 (정적 파일)
- 배포 확인: `gh api repos/max2guy/yc-young-adults/pages/builds/latest` 로 최신 커밋 반영 여부 확인 가능 (단, API 응답이 실제 CDN 콘텐츠보다 갱신이 늦게 뜨는 경우가 있어 `curl`로 실제 페이지 내용을 직접 확인하는 편이 더 정확함)
- 배포 주소: `https://max2guy.github.io/yc-young-adults/`
