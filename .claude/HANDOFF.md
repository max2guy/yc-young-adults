# yc-young-adults — Codex Handoff (v0.1.0)

## 현재 상태
- 최신 커밋: `e75fd98` "청년부 자료실 허브 페이지 및 2026 홈커밍데이 설문조사 추가"
- 브랜치: `main`
- 아직 GitHub 원격 저장소에 push되지 않은 로컬 전용 상태 (Pages 미배포)

## 방금 수정한 내용
- 청년부 전용 GitHub Pages 자료실(허브) 신규 생성
- `index.html`: 자료 목록 허브 페이지. `RESOURCES` 배열(스크립트 내)에 항목을 추가하면 카드가 자동 생성되는 구조
- `2026-homecoming-survey.html`: 2026 홈커밍데이 설문조사 (Firebase Realtime Database에 응답 저장, `#admin` 해시로 관리자 통계 페이지 진입, PIN `6291`)

## 프로젝트 개요
- 순수 정적 HTML/CSS/JS (빌드 과정 없음, 프레임워크 없음)
- Firebase: Realtime Database만 사용 (`ycprayer-7eac2` 프로젝트 기존 것을 임시로 재사용 중 — 사용자가 별도 Firebase 프로젝트를 원하면 `firebaseConfig` 교체 필요)
- 데이터 경로: `homecoming2026/responses`
- 인증 없음, 관리자 페이지는 PIN 하드코딩(`ADMIN_PIN = "6291"`) 방식 — 보안 수준 낮음, 민감 데이터 저장 금지

## 주요 파일
- `index.html` — 청년부 자료실 메인(허브) 페이지. `RESOURCES` 배열 수정으로 자료 추가/삭제
- `2026-homecoming-survey.html` — 홈커밍데이 설문 + 관리자 통계 대시보드 (단일 파일)

## 다음으로 할 수 있는 작업
- GitHub에 `yc-young-adults` 저장소 생성 후 `git remote add origin ...` + push, Settings → Pages에서 main/root 활성화
- Firebase 프로젝트를 전용으로 새로 팔 경우 `2026-homecoming-survey.html`의 `firebaseConfig`, `.firebaserc` 성격의 설정 교체
- 향후 자료(회의자료, 계획서 등) 추가 시 `index.html`의 `RESOURCES` 배열에 항목 추가 + 해당 HTML 파일 저장소에 커밋

## 빌드 & 배포
- 빌드 명령 없음 (정적 파일)
- 배포: GitHub 저장소 생성 → 파일 업로드/push → Settings → Pages → Branch: main, Folder: / (root) → Save
- 배포 후 주소: `https://<github-id>.github.io/yc-young-adults/`
