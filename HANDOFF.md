# FE 인수인계

## 역할과 구조

- 이 저장소는 웹 화면, 스타일, 브라우저 동작을 관리합니다.
- 화면은 Jinja 템플릿과 정적 CSS/JavaScript로 구성되며 별도 SPA는 없습니다.
- 화면 소스는 `templates/`, `static/`입니다. 통합 실행용 BE 저장소에도 `app/templates/`, `app/static/` 복사본이 있으므로 화면을 바꾸면 BE 복사본도 동기화해야 합니다.
- 사용자는 BE 앱 `http://localhost:8000`에 접속합니다. 브라우저가 AI 서비스에 직접 요청하지 않습니다.

## 현재 화면과 API 연결

- 홈: 날짜가 표시되는 헤더 영역, 공지 검색, 전체·학교·학과·맞춤·저장 공지 탭, 도우미 영역
- 인증: 로그인/회원가입 중앙 모달. 인증과 계정 데이터 처리는 BE가 담당합니다.
- 공지 피드: 탭의 `data-endpoint`가 API 주소를 결정합니다. 공지 데이터에 없는 내용을 화면에서 임의로 만들지 않습니다.
- 저장 공지: 카드별 저장/해제와 저장 목록을 BE API에 연결합니다. 로그인하지 않은 사용자는 저장 시 로그인 안내를 받습니다.
- 공지 도우미: BE의 `/api/assistant/chat`, 요약 요청은 `/api/assistant/notices/{id}/summary`를 호출합니다. BE가 접근 권한을 검사하고 AI 서비스 결과를 중계합니다.
- 확장프로그램 가져오기 화면: BE가 제공하는 확장프로그램 import API와 연결됩니다.

## 수정할 때

1. 화면 원본은 FE의 `templates/`, `static/`에서 수정합니다.
2. 대응되는 파일을 BE의 `app/templates/`, `app/static/`에도 동기화합니다. 예: `FE/templates/index.html` → `BE/app/templates/index.html`.
3. 화면/API 응답 계약을 바꿀 때는 BE README 및 API와 함께 확인합니다.
4. 비밀번호, JWT, `AI_SERVICE_TOKEN`, 모델 제공자 키를 FE 코드나 HTML에 넣지 않습니다.
5. 로그아웃은 `POST /logout` form을 유지하고, 로그인 여부를 화면에서 추측하거나 하드코딩하지 않습니다.

## 실행과 게시

- 로컬 통합 앱은 BE 프로젝트의 Docker Compose로 실행하며 웹 주소는 `http://localhost:8000`입니다.
- FE 자체 저장소를 푸시할 때는 이 저장소의 `main`을 사용합니다. BE 복사본 동기화는 BE 저장소 변경에도 포함해야 통합 앱에 반영됩니다.
- 최신 반영된 FE 커밋은 `31fb9bc`이며 GitHub `main`에 푸시되어 있습니다.
- 마지막 AI 서비스 분리 작업에서는 FE 코드를 수정하지 않았습니다. 자동 테스트는 별도로 실행하지 않았습니다.
