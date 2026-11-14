# WOOWANGTHON_TEAM_02_FE

충북대학교 공지 알리미의 화면 소스입니다. FastAPI와 Jinja 템플릿을 사용하며, 현재는 독립 웹 서버가 아니라 BE 앱에서 렌더링됩니다.

## 구성

- `templates/`: 메인 공지, 로그인, 회원가입, 내 정보 화면
- `static/css/app.css`: 반응형 화면 스타일
- `static/js/auth.js`: 로그인 상태에 따른 헤더 표시
- `static/js/notices.js`: 전체 공지·맞춤 공지 조회, 검색, 추천 이유 표시

## 로컬 실행

Docker Desktop을 켠 뒤 BE 저장소 폴더에서 `docker compose up --build`를 실행하고 `http://localhost:8000`에 접속합니다. FE 파일을 수정한 경우 BE 통합 실행본의 `app/templates/`와 `app/static/`에도 같은 변경을 복사한 뒤 컨테이너를 다시 빌드해야 합니다.

## 백엔드 연결

- 페이지 및 폼: `/`, `/login`, `/register`, `/me`, `POST /login`, `POST /register`, `POST /logout`
- 전체 공지: `GET /api/notices?per_page=20`
- 맞춤 공지: `GET /api/recommendations`
- 정적 파일: `/static/css/app.css`, `/static/js/auth.js`, `/static/js/notices.js`

회원가입 폼은 학교·학과·학년·관심분야 필드를 사용합니다. 공지 API 응답은 `items` 배열을 제공하며 제목, 원문 링크, 요약 또는 미리보기, 출처, 추천 이유를 화면에 표시합니다.

현재 로그인·가입은 BE의 HTML 폼을 사용합니다. FE는 별도 빌드나 독립 배포용 SPA로 전환되지 않았습니다.
