# WOOWANGTHON_TEAM_02_FE

공지 알리미 웹 화면의 Jinja 템플릿, CSS, 브라우저 JavaScript입니다.

## 구성

- `templates/`: 메인, 로그인, 가입, 프로필 화면
- `static/css/`, `static/js/`: 스타일과 브라우저 동작

현재 화면은 FastAPI가 Jinja로 렌더링하고 `/static`에서 정적 파일을 제공합니다. 그래서 이 저장소는 화면 소스의 분리 저장소이며, 별도 개발 서버나 독립 배포용 SPA로 아직 전환되지는 않았습니다. 통합 실행은 BE 저장소의 Docker Compose를 사용하세요. 템플릿이나 정적 파일을 수정하면 실행 통합본인 BE의 `app/templates/`, `app/static/`에도 반영해야 합니다.

## 향후 독립 실행

BE가 제공하는 로그인·가입·공지 API를 JSON 기반으로 정리한 다음, 화면을 API 호출 방식으로 옮기고 독립 프론트엔드 빌드/배포를 추가해야 합니다. 현재 로그인 요청은 BE의 HTML form endpoint를 사용합니다.
