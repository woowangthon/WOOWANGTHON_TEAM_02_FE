# WOOWANGTHON_TEAM_02_FE

FastAPI가 Jinja로 렌더링하는 공지 알리미(알려줌) 프론트엔드입니다. 실행·연결·수정 방법은 [HANDOFF.md](HANDOFF.md)를 참고하세요.

## 빠른 실행

Docker Desktop을 켜고 상위 폴더의 `run.bat`을 더블클릭합니다. 자세한 내용은 HANDOFF.md를 참고하세요.

BE와 AI 폴더는 수정하지 않고, FE의 `templates/`와 `static/`을 컨테이너에 마운트해서 적용합니다.

## 구조

```text
templates/   base.html, index.html, login.html, profile.html, register.html
static/
  css/app.css
  js/auth.js, notices.js
HANDOFF.md   실행 외 작업, 인증/탭 연동, 스타일 수정 위치, 원칙
```

## 백엔드 계약

- 페이지: `/`, `/login`, `/register`, `/me`
- 폼: `POST /login`, `POST /register`, `POST /logout`
- 공지: `GET /api/notices?per_page=20&scope=school|department`
- 맞춤 공지: `GET /api/recommendations`
- 정적 파일: `/static/css/app.css`, `/static/js/auth.js`, `/static/js/notices.js`

Jinja 컨텍스트는 `registered`, `error`, `email`, `schools`, `departments`, `tags`, 선택된 ID, `user`를 사용합니다.
