# context: myEZtool 연수 도구

## 1. 무엇을 하고 있나

`myEZtool` (github.com/limeSl/myEZtool, GitHub Pages 정적 사이트)에 연수 기록 관리 도구를 추가했다.
교사 연수 이수 기록을 어느 기기에서든 보고 편집할 수 있게 하는 것이 목적이다.

데이터는 **GitHub Gist** (`연수기록.md`) 하나에 저장한다.
웹앱(브라우저)과 터미널 스킬(Claude Code) 모두 같은 Gist를 읽고 쓰기 때문에 양쪽이 항상 동기화된다.

---

## 2. 현재 상태 / 결정 사항

### 파일 구조
```
myEZtool/
  index.html          ← 메인 허브 (연수 링크 추가 완료)
  training/
    index.html        ← 연수 도구 메인 (완성)
  CLAUDE.md           ← 디자인 규칙 (이 프로젝트 작업 시 항상 준수)
```

### 디자인 규칙 (CLAUDE.md 기준, 반드시 지킬 것)
- 배경색 없음, 테두리 박스 없음, 꾸밈 없음
- `input`은 `border-bottom` 하나만
- 색상: `#333` / `#888` / `#aaa` / `#ccc` 만 사용
- 카드, 그라데이션, 그림자 금지

### 데이터 구조
- **Gist ID**: `458de82cffb5a743883d146a7c5451d5`
- **파일명**: `연수기록.md`
- **마크다운 테이블 컬럼**: 연수명 | 장소 | 일시 | 연수종류 | 이수시간 | 요약 | 소감 | 자료 | 수료증 | 나이스 등록 | 학점/호봉
- 일시 형식: `YYYY-MM-DD` 또는 `YYYY-MM-DD HH:MM`
- 이수시간 형식: `3h`, `2.5h`
- 수료증/나이스등록/학점호봉: `[ ]` 또는 `[x]`

### 웹앱 (training/index.html) 동작 방식
- GitHub Classic PAT(`ghp_...`, gist 스코프)를 localStorage에 저장 (`gh_pat`, `gh_gist`)
- 초기 연결 시 FILENAME이 있는 Gist를 자동 탐색, 없으면 새로 생성
- 탭 2개: **목록&통계** / **기록 추가**
- 목록 탭: 기록 클릭 → 인라인 편집 폼 전환 (모달 없음), 저장 시 Gist PATCH
- 수료증·나이스등록·학점호봉은 내부 데이터로는 유지하되 화면에 표시하지 않음
- Google Drive Picker 연동: Drive 파일 선택 → `[파일명](url)` 형식으로 자료 열에 저장
  - CLIENT_ID: `256572036104-45639a7v2cicg5pbfb0ip5qv8ml2ardr.apps.googleusercontent.com`
  - API Key: `AIzaSyCTkOc8moZNxm00caI6fqM7Lkx94Ar_t7A`
  - 연수자료 Drive 폴더 ID: `1tQuwT9zWUVJOKsmxviDiJHNWoo1emyke`

### 터미널 스킬 (Claude Code)
설정 파일: `C:\Users\wcdus\.claude\training-gist.json`
```json
{ "pat": "ghp_...", "gist_id": "458de82cffb5a743883d146a7c5451d5" }
```
스킬 4개 모두 이 파일을 읽어 Python3 urllib로 Gist API 호출:
- `/training-log` — 새 기록 추가 (Gist에 직접 씀)
- `/training-list` — 최신 20건 조회
- `/training-stats` — 이수시간 합계, 달성률, 종류별 통계
- `/training-import` — 나이스 이수현황 텍스트/배지/수료증 일괄 등록

### 주의사항
- GitHub **fine-grained PAT**(`github_pat_11...`)는 Gist API 미지원 → **Classic PAT**(`ghp_...`) 필수
- Python3 bash에서 한글 출력 시 `sys.stdout.reconfigure(encoding='utf-8')` 필수
- `buildMarkdown()` 함수로 전체 MD 재구성 후 Gist PATCH (행 단위 replace 아님)

---

## 3. 남은 작업

딱히 정해진 다음 작업 없음. 현재 완성된 기능:
- 목록 + 통계 표시 ✅
- 기록 추가 ✅
- 인라인 편집 ✅
- Drive 자료 연결 ✅
- Gist 동기화 (웹 ↔ 터미널) ✅

있으면 좋을 것들 (사용자가 요청한 적 없음, 임의로 추가하지 말 것):
- 기록 삭제 기능
- 나이스/수료증 체크 토글 (현재 편집 폼에서만 가능)
- 모바일 대응 (현재 max-width 480px 고정)
