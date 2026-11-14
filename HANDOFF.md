# FE Handoff

`WOOWANGTHON_TEAM_02_FE-main`을 BE(FastAPI)에 연결해 실행하고 수정하기 위한 문서입니다.

## 실행

**BE, FE, AI 폴더 안에는 아무것도 만들거나 수정하지 않습니다.** 세 폴더 바깥, 상위 폴더의 `run.bat` 하나가 BE 위에 FE를 겹쳐서 실행합니다.

```text
WOOWANGTHON/
├── run.bat                        # 더블클릭으로 실행 (실행 전용)
├── .env                           # 첫 실행 때 자동 생성
├── WOOWANGTHON_TEAM_02_BE-main/   # 읽기만 함
├── WOOWANGTHON_TEAM_02_AI-main/   # 사용 안 함 (BE가 호출하지 않음)
└── WOOWANGTHON_TEAM_02_FE-main/
    ├── templates/
    └── static/
```

Docker Desktop을 켜고 `run.bat`을 더블클릭합니다. 접속: `http://localhost:8000` (포트는 `.env`의 `APP_PORT`)

### 동작 방식

- BE의 `docker-compose.yml`에 FE의 `templates/`, `static/`을 읽기 전용으로 마운트하는 임시 설정(`%TEMP%`)을 겹쳐 `docker compose up --build -d`를 실행합니다.
- 컨테이너의 `/app/app/templates`, `/app/app/static`만 FE 파일로 대체됩니다. BE의 같은 폴더는 디스크에서 바뀌지 않습니다.
- `.env`는 BE의 `.env.example`을 읽어 `run.bat` 옆에 만들고 `JWT_SECRET`을 자동 생성합니다. `LLM_API_KEY`는 비어 있으므로 요약·챗봇을 쓰려면 직접 넣습니다.
- 마이그레이션은 BE 컨테이너가 시작할 때 자동으로 실행됩니다.

### 실행 외 작업 (`run.bat`에는 없음)

```bash
docker compose -p woowangthon down          # 중지 (데이터 유지)
docker compose -p woowangthon down -v       # 중지 + DB 삭제
docker exec woowangthon-app-1 python -m app.db.seed       # 학교/학과/게시판 시드 (첫 실행 후 1회)
docker exec woowangthon-app-1 python -m app.crawler.run   # 게시판 1회 수집
docker logs woowangthon-app-1               # 로그
```

회원가입에 필요한 학교·학과 데이터는 시드로 들어갑니다. 첫 실행 후 시드를 한 번 적용하세요.

### 주의

- `run.bat`, BE 폴더, FE 폴더가 같은 상위 폴더에 있어야 합니다.
- `.env`는 비밀값을 포함합니다. 상위 폴더를 git에 올린다면 `.env`를 제외하세요.
- BE의 `app/templates`, `app/static`에 FE 파일을 복사해 덮어쓰는 방식은 쓰지 않습니다.

## 헤더 인증 상태

통합본의 `/` 라우트는 `user`를 Jinja 컨텍스트로 전달하지 않습니다. 그래서 `base.html`은 로그인/비로그인 메뉴를 모두 `hidden`으로 두고 `auth.js`가 결정합니다.

1. `profile.html`은 `data-authenticated="true"`, `data-user-email="{{ user.email }}"`를 body에 서버에서 렌더링합니다.
2. 다른 페이지에서는 `auth.js`가 `GET /me`를 요청해 응답 body의 데이터 속성을 읽습니다.
3. `401` 또는 네트워크 오류면 비로그인 메뉴를 표시합니다.

- 비로그인 메뉴: 회원가입, 로그인
- 로그인 메뉴: 이메일, 내 정보, 로그아웃 (이메일은 데스크톱에서만 표시)

BE가 모든 `TemplateResponse`에 `user`를 전달하게 되면 body 속성을 조건부로 렌더링하고 `auth.js`의 `/me` 요청을 제거합니다. JSON API(`GET /api/me`)가 생기면 `auth.js`의 `resolveAuthState()` 요청만 교체합니다.

## 전체 공지 / 맞춤 공지 탭

탭의 API 주소는 `index.html`의 `data-endpoint`에 있습니다. 경로가 바뀌어도 `notices.js` 수정 없이 템플릿만 바꾸면 됩니다.

```html
<button data-view="all" data-endpoint="/api/notices?per_page=20">전체 공지</button>
<button data-view="recommended" data-endpoint="/api/recommendations">맞춤 공지</button>
```

응답 계약:

```json
{
  "items": [
    {
      "id": 1,
      "title": "공지 제목",
      "posted_at": "2026-01-01T09:00:00",
      "category_name": "학사",
      "preview": "본문 미리보기",
      "summary": "요약",
      "original_url": "https://...",
      "source_name": "게시판 이름",
      "reasons": ["내 학과 대상"]
    }
  ],
  "total": 1
}
```

- 개수는 `total`, 없으면 `items.length`를 씁니다.
- 본문은 `summary`, `preview` 순서로 씁니다.
- `reasons`가 있을 때만 추천 이유 태그를 표시합니다.
- API가 `401`이면 로그인 안내를 표시합니다.

## 스타일 수정 위치 (`static/css/app.css`)

| 영역 | 선택자 |
| --- | --- |
| 헤더 전체 | `.site-header`, `.header-actions` |
| 비로그인 메뉴 | `[data-auth-guest]`, `.login-link` |
| 로그인 메뉴 | `[data-auth-user]`, `.account-nav` |
| 사용자 이메일 | `.account-email` |
| 로그아웃 | `.logout-button` |
| 공지 탭 | `.feed-tabs`, `.feed-tab` |
| 추천 이유 | `.recommendation-reasons` |
| 로그인 필요 상태 | `.login-required` |

## 지켜야 할 원칙

- BE와 AI 폴더의 파일을 수정하지 않습니다. 필요한 변경은 FE에서 해결합니다.
- 홈 라우트에 `user`가 있다고 가정하지 않습니다.
- 사용자 이메일을 HTML이나 JavaScript에 하드코딩하지 않습니다.
- 로그아웃은 링크가 아니라 `POST /logout` form을 유지합니다.
- 맞춤 공지를 클라이언트에서 계산하지 않고, 추천 이유는 `/api/recommendations` 응답만 씁니다.
- API가 없거나 실패해도 임의 공지를 만들지 않습니다.
