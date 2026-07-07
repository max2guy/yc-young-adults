# yc-young-adults — Codex Handoff (v0.4.0)

## 현재 상태
- 최신 커밋(push 예정): "PERMISSION_DENIED 수정 — 설문 전용 Firebase 프로젝트로 분리"
- 이전 커밋: `7ad5e0f` "2026 청년부 여름수련회 계획안 PDF 추가"
- 브랜치: `main`
- GitHub 저장소: https://github.com/max2guy/yc-young-adults (Public)
- GitHub Pages 배포됨: https://max2guy.github.io/yc-young-adults/

## 방금 수정한 내용
- **버그**: 설문 제출 시 `PERMISSION_DENIED: Permission denied` 오류로 응답이 저장되지 않음
- **원인**: 기존에 임시로 재사용하던 `ycprayer-7eac2` 프로젝트의 Realtime Database 규칙(`~/Projects/ycpraying/database.rules.json`)에 `homecoming2026` 경로 규칙이 없어 기본 거부됨. `curl`로 인증 없이 REST 호출해도 동일하게 거부되는 것으로 재현 확인.
- **해결**: `ycprayer-7eac2` 규칙을 건드리는 대신, 이미 존재하던 전용 Firebase 프로젝트 **`yc-homecoming-2026`**(웹앱은 등록돼 있었으나 DB 인스턴스는 없었음)에 Realtime Database 인스턴스(`yc-homecoming-2026-default-rtdb`, 리전 `us-central1`)를 새로 생성하고, `homecoming2026/responses` 경로에 `.read`/`.write: true` 규칙만 배포. `2026-homecoming-survey.html`의 `firebaseConfig`를 이 새 프로젝트 값으로 교체함 (129~137번째 줄).
- REST로 write/read/delete 왕복 테스트 완료, 다른 경로(`somethingElse`)는 기본 거부되는 것도 확인함 → 규칙이 필요한 범위에만 정확히 열려 있음.
- 이제 `ycprayer-7eac2`는 더 이상 이 설문과 무관 (기존 ycpraying 앱 전용으로 완전히 분리됨).

## 프로젝트 개요
- 순수 정적 HTML/CSS/JS (빌드 과정 없음, 프레임워크 없음)
- Firebase: Realtime Database만 사용, **전용 프로젝트 `yc-homecoming-2026`** (더 이상 `ycprayer-7eac2`와 공유하지 않음)
  - Console: https://console.firebase.google.com/project/yc-homecoming-2026/overview
  - 로컬 firebase 설정 디렉터리 없음 — 규칙 재배포가 필요하면 임시 디렉터리에서 `firebase init database --project yc-homecoming-2026` 후 `database.rules.json` 작성 → `firebase deploy --only database --project yc-homecoming-2026`
- 데이터 경로: `homecoming2026/responses` (변경 없음)
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
## 빌드 & 배포
- 빌드 명령 없음 (정적 파일)
- 배포 확인: `gh api repos/max2guy/yc-young-adults/pages/builds/latest` 로 최신 커밋 반영 여부 확인 가능 (단, API 응답이 실제 CDN 콘텐츠보다 갱신이 늦게 뜨는 경우가 있어 `curl`로 실제 페이지 내용을 직접 확인하는 편이 더 정확함)
- 배포 주소: `https://max2guy.github.io/yc-young-adults/`
