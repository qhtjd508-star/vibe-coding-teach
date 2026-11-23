# 바이브코딩 교원 연수 사이트

교원 대상 바이브코딩 연수에서 쓰는 단일 페이지 정적 사이트.

## 언어

- 사용자에게는 항상 **한국어**로 답한다.

## 작업 규칙

- 코드를 수정한 뒤 **커밋 전에 변경 내용을 사용자에게 보여 주고 검토**를 받는다 (`git diff` 요약, 무엇을 왜 바꿨는지).
- **커밋과 push는 사용자가 명시적으로 요청할 때만** 한다. 수정이 끝났다고 임의로 커밋/push하지 않는다.
- push한 뒤에는 GitHub Pages 배포가 끝났는지 확인한다 (아래 "배포 확인" 참고).

## 사이트 구조

빌드 도구·프레임워크·`package.json` 없음. 파일을 그대로 서빙한다.

```
index.html   # 사이트 전체 (HTML + CSS + JS가 한 파일에)
fonts/       # Paperlogy 웹폰트 (Regular / SemiBold / ExtraBold, woff2)
img/         # vibe-map.png, apps-script-menu.png
README.md
```

`index.html` 구성:

- `<style>`: 상단에서 `fonts/`의 Paperlogy를 `@font-face`로 불러오고 모든 스타일 정의.
- 입장 화면 `#gate`: 비밀번호를 맞히면 본문 `#app`이 보인다.
- 본문: 왼쪽 내비게이션 + 6개 단계 섹션 (`<section class="view">`)
  1. `#map` 바이브코딩 지도
  2. `#game` 간단한 게임 만들기
  3. `#quiz` 학습 퀴즈 앱 만들기 (예시 프롬프트는 공개 비밀번호로 잠금)
  4. `#apps-script` 구글 앱스크립트로 웹앱 배포
  5. `#api-key` API 키 발급 (Google AI Studio)
  6. `#ai` AI 기능 붙이기 (Gemini API 호출 Apps Script 예제)
- 하단 `<script>`: 섹션 이동, 비밀번호 확인, 예시 코드 문자열(`QUIZ_PROMPTS`, `AI_CODE` 등).

## 비밀번호

`index.html`의 `<script>` 맨 위에 있다.

| 변수 | 값 | 용도 | 기록 저장 위치 |
|---|---|---|---|
| `ENTER_PW` | `1234` | 사이트 입장 | `sessionStorage`의 `vc-enter` (탭 단위) |
| `REVEAL_PW` | `0000` | 퀴즈 예시 프롬프트 공개 | `localStorage`의 `vc-reveal` (계속 유지) |

- 입장 기록은 탭 단위라서 **새 탭/새 창을 열 때마다 비밀번호를 다시 묻는다**. 같은 탭에서 새로고침할 때는 묻지 않는다.
- 페이지가 열릴 때 예전 버전이 `localStorage`에 남긴 `vc-enter`를 지운다.
- 비밀번호는 클라이언트 코드에 그대로 있고 저장소도 공개라서 진짜 보안 장치가 아니다. 수업 진행용 잠금일 뿐이다.
- `index.html`의 Padlet 링크 주소에 `1113`이 들어 있는데, 예전 비밀번호와 상관없는 링크이므로 건드리지 않는다.

## 배포 (GitHub Pages)

- 저장소: `qhtjd508-star/vibe-coding-teach`
- 배포 소스: `main` 브랜치 루트(`/`), 빌드 방식 legacy (Actions 워크플로 파일 없음)
- 주소: https://qhtjd508-star.github.io/vibe-coding-teach/
- `main`에 push하면 1~2분 안에 자동으로 다시 배포된다. 따로 빌드할 것은 없다.

### 배포 확인

```sh
# 1) push가 됐는지: "up to date with 'origin/main'" 확인
git status

# 2) Pages 빌드: status가 built이고 commit이 방금 push한 커밋과 같으면 성공
gh api repos/qhtjd508-star/vibe-coding-teach/pages/builds/latest --jq '.status, .commit'

# 3) 실제 사이트에 반영됐는지
curl -s https://qhtjd508-star.github.io/vibe-coding-teach/ | grep -n 'ENTER_PW ='
```

브라우저에서 확인할 때는 시크릿 창을 쓰고, 예전 화면이 보이면 강력 새로고침(Cmd+Shift+R).

### 스크립트 문법 검사

```sh
awk '/^<script>$/{f=1;next} /^<\/script>$/{f=0} f' index.html > /tmp/s.js && node --check /tmp/s.js
```

## 변경 이력

- 2026-11-23 `5bdb844`: 입장 비밀번호 `1113` → `1234`. 입장 기록을 `localStorage`에서 `sessionStorage`로 옮겨 새 탭마다 다시 묻게 함. 예전 `localStorage` 기록 정리.
