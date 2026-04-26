# CS 면접 퀴즈 앱 — Claude Code 빌드 가이드 v3

---

## 1. 프로젝트 개요

**목표**: iOS Safari PWA로 동작하는 CS 기술 면접 퀴즈 앱  
**문제 관리**: GitHub 저장소에 JSON 파일로 관리  
**배포**: GitHub Pages (무료, 자동 배포)  
**오프라인**: Service Worker로 캐싱  
**버전 관리**: Git으로 문제 추가/수정 이력 추적

---

## 2. 아키텍처

```
GitHub Repository (cs-quiz)
├── index.html          # 앱 전체 (HTML + CSS + JS)
├── manifest.json       # PWA 설정
├── sw.js               # Service Worker
└── data/
    ├── os.json         # 운영체제 문제
    ├── network.json    # 네트워크 문제
    ├── ds.json         # 자료구조 문제
    ├── db.json         # 데이터베이스 문제
    ├── java.json       # Java 문제
    ├── spring.json     # Spring 문제
    ├── design.json     # 디자인패턴 문제
    └── general.json    # 개발자 필수 지식 문제
```

**앱 동작 흐름**
1. GitHub Pages에서 앱 로드
2. `data/*.json` 파일들을 fetch
3. 문제 필터링/셔플 후 퀴즈 시작
4. 정답률은 localStorage에 저장

**문제 추가/수정 흐름**
1. 로컬에서 JSON 파일 편집
2. `git add`, `git commit`, `git push`
3. GitHub Pages 자동 배포 (1~2분 후 반영)

---

## 3. JSON 파일 구조

### 단일 파일 스키마 (`data/os.json`)
```json
{
  "topic": "os",
  "label": "운영체제",
  "questions": [
    {
      "id": "os-001",
      "difficulty": "basic",
      "type": "concept",
      "question": "데드락(Deadlock)이란 무엇인가?",
      "options": [
        "프로세스가 CPU를 독점하는 현상",
        "둘 이상의 프로세스가 서로 상대방의 자원을 기다리며 무한 대기하는 상태",
        "메모리가 부족하여 프로세스가 종료되는 현상",
        "프로세스 간 통신이 실패하는 현상"
      ],
      "answer": 1,
      "explanation": "데드락은 상호배제, 점유대기, 비선점, 환형대기 4가지 조건이 모두 충족될 때 발생합니다."
    }
  ]
}
```

### 필드 정의
| 필드 | 타입 | 값 |
|------|------|----|
| id | string | `[topic]-[번호]` 형식 (예: os-001) |
| difficulty | string | `basic` / `intermediate` / `advanced` |
| type | string | `concept` / `tradeoff` / `scenario` / `debug` / `ordering` |
| question | string | 문제 본문 |
| options | string[] | 4개 선택지 배열 |
| answer | number | 정답 인덱스 (0~3) |
| explanation | string | 해설 |

---

## 4. 앱에서 데이터 로드하는 코드

```javascript
const TOPICS = ['os', 'network', 'ds', 'db', 'java', 'spring', 'design', 'general'];
const BASE_URL = 'https://[깃헙유저명].github.io/cs-quiz/data';

async function loadAllQuestions() {
  const results = await Promise.all(
    TOPICS.map(t =>
      fetch(`${BASE_URL}/${t}.json`)
        .then(r => r.json())
        .catch(() => ({ questions: [] })) // 파일 없으면 빈 배열
    )
  );
  const all = results.flatMap(r => r.questions);
  localStorage.setItem('cached-questions', JSON.stringify(all)); // 오프라인 캐시
  return all;
}

// 오프라인 fallback
async function getQuestions() {
  try {
    return await loadAllQuestions();
  } catch {
    const cached = localStorage.getItem('cached-questions');
    return cached ? JSON.parse(cached) : [];
  }
}
```

---

## 5. GitHub Pages 배포 설정

### 5.1 저장소 초기 설정 (Claude Code에서 실행)
```bash
git init
git add .
git commit -m "init: CS quiz app"
git branch -M main
git remote add origin https://github.com/[유저명]/cs-quiz.git
git push -u origin main
```

### 5.2 GitHub Pages 활성화
1. GitHub 저장소 → Settings → Pages
2. Source: `Deploy from a branch`
3. Branch: `main` / `/ (root)`
4. Save → 1~2분 후 `https://[유저명].github.io/cs-quiz` 접속 가능

### 5.3 이후 문제 추가할 때
```bash
# JSON 파일 편집 후
git add data/os.json
git commit -m "add: 운영체제 시나리오 문제 10개 추가"
git push
# 끝. 1~2분 후 자동 반영
```

---

## 6. 앱 기능 명세

### 6.1 화면 구성
1. **홈**: 주제 선택 + 난이도/유형 필터 + 문제 수 설정 + 시작 + 통계
2. **퀴즈**: 진행 바 + 유형 배지 + 문제 + 4지선다 + 즉시 피드백 + 해설
3. **결과**: 점수 + 유형별 정답률 + 틀린 문제 복습

### 6.2 문제 유형 배지
- 🔵 concept — 개념 확인
- 🟠 tradeoff — 트레이드오프
- 🟣 scenario — 시나리오
- 🔴 debug — 디버깅
- 🟡 ordering — 순서/흐름

---

## 7. 문제 생성 프롬프트

결과물을 그대로 JSON 파일에 붙여넣으면 됩니다.

---

### 프롬프트 A — 개념 확인형 (concept)
> 정의, 특성, 구성요소를 묻는 가장 기본적인 유형

```
[주제]에 대한 CS 면접 개념 확인 문제를 20개 JSON으로 만들어줘.

조건:
- type: "concept"
- basic 10개, intermediate 10개
- 오답 3개는 실제로 헷갈릴 만한 것으로 (명백히 틀린 선택지 금지)
- 문제 형식 예시: "A의 특성으로 옳지 않은 것은?", "A와 B의 차이로 올바른 것은?"
- 해설은 핵심만 2~3문장
- id는 "[topic키]-c001" 형식으로 순번

출력: 아래 스키마의 questions 배열만 출력 (json 코드블록으로)
{
  "id": "os-c001",
  "difficulty": "basic",
  "type": "concept",
  "question": "...",
  "options": ["...", "...", "...", "..."],
  "answer": 0,
  "explanation": "..."
}

주제: 운영체제 (topic키: os)
다룰 내용: 프로세스/스레드, 스케줄링, 메모리 관리, 가상메모리, 데드락, 동기화, 뮤텍스/세마포어
```

---

### 프롬프트 B — 트레이드오프형 (tradeoff)
> "언제 A를 쓰고 언제 B를 써야 하는가"를 묻는 유형

```
[주제]에 대한 CS 면접 트레이드오프 문제를 15개 JSON으로 만들어줘.

조건:
- type: "tradeoff"
- intermediate 10개, advanced 5개
- 선택지가 "상황 A에서는 X가 낫다"처럼 맥락을 포함할 것
- 문제 형식 예시: "다음 중 TCP보다 UDP가 더 적합한 상황은?", "인덱스를 사용하면 안 되는 경우는?"
- 해설에서 왜 그 선택이 맞는지 + 나머지가 왜 틀린지 간략히 포함
- id는 "[topic키]-t001" 형식

출력: questions 배열만 (json 코드블록)

주제: 데이터베이스 (topic키: db)
다룰 내용: 인덱스, 트랜잭션, 정규화, JOIN, 캐싱, 격리수준
```

---

### 프롬프트 C — 시나리오형 (scenario)
> 실제 상황을 주고 원인이나 해결책을 묻는 유형

```
[주제]에 대한 CS 면접 시나리오 문제를 15개 JSON으로 만들어줘.

조건:
- type: "scenario"
- intermediate 8개, advanced 7개
- 문제는 반드시 구체적인 상황 묘사로 시작
  예: "트래픽이 갑자기 10배 증가했을 때...", "API 응답이 간헐적으로 느려질 때..."
- 선택지는 모두 그럴듯하게, 정답과 오답의 차이가 미묘할 것
- 해설에서 오답이 왜 틀렸는지도 간략히 포함
- id는 "[topic키]-s001" 형식

출력: questions 배열만 (json 코드블록)

주제: 네트워크 (topic키: network)
다룰 내용: TCP/UDP, HTTP, DNS, 로드밸런싱, 캐싱, 보안
```

---

### 프롬프트 D — 디버깅/오류 찾기형 (debug)
> 잘못된 설명이나 설계에서 문제를 찾는 유형

```
[주제]에 대한 CS 면접 디버깅/오류 찾기 문제를 10개 JSON으로 만들어줘.

조건:
- type: "debug"
- intermediate 5개, advanced 5개
- 문제 형식: "다음 설명 중 틀린 것은?", "다음 설계/코드의 문제점은?"
- 코드가 나올 경우 의사코드(pseudocode)로 (특정 언어 문법 최소화)
- 오답 선택지도 실제 발생할 수 있는 문제처럼 작성
- id는 "[topic키]-d001" 형식

출력: questions 배열만 (json 코드블록)

주제: Java (topic키: java)
다룰 내용: JVM, GC, 동시성, 메모리 누수, equals/hashCode, 예외 처리
```

---

### 프롬프트 E — 순서/흐름형 (ordering)
> 과정이나 순서를 올바르게 나열하는 유형

```
[주제]에 대한 CS 면접 순서/흐름 문제를 10개 JSON으로 만들어줘.

조건:
- type: "ordering"
- basic 3개, intermediate 4개, advanced 3개
- 선택지는 단계들의 순서를 다르게 나열 (① ② ③ ④ 형태)
- 순서가 실제로 헷갈리는 부분에 집중
- 문제 형식 예시: "TCP 3-way handshake의 올바른 순서는?", "Spring Bean 생성 순서로 올바른 것은?"
- id는 "[topic키]-o001" 형식

출력: questions 배열만 (json 코드블록)

주제: Spring (topic키: spring)
다룰 내용: Bean 생성 순서, MVC 요청 흐름, AOP 동작 순서, 트랜잭션 처리 순서
```

---

### 주제별 추천 조합

| 주제 (topic키) | concept | tradeoff | scenario | debug | ordering | 합계 |
|---------------|---------|----------|----------|-------|----------|------|
| os | 42 | 18 | 10 | 10 | 6 | 86 |
| network | 38 | 16 | 15 | 5 | 6 | 80 |
| ds | 36 | 14 | 10 | 10 | 5 | 75 |
| db | 51 | 19 | 15 | 10 | 5 | 100 |
| java | 36 | 11 | 11 | 15 | 5 | 78 |
| spring | 30 | 18 | 15 | 10 | 12 | 85 |
| design | 22 | 16 | 11 | 6 | 5 | 60 |
| general | 29 | 19 | 10 | 5 | 6 | 69 |
| **합계** | **284** | **131** | **97** | **71** | **50** | **633** |

---

## 8. JSON 파일 조립 방법

프롬프트 A~E로 생성한 questions 배열을 하나의 파일로 합치면 됩니다.

```json
{
  "topic": "os",
  "label": "운영체제",
  "questions": [
    /* 프롬프트 A 결과 (concept 20개) */
    { "id": "os-c001", ... },
    { "id": "os-c002", ... },

    /* 프롬프트 B 결과 (tradeoff 15개) */
    { "id": "os-t001", ... },

    /* 프롬프트 C 결과 (scenario 10개) */
    { "id": "os-s001", ... },

    /* 프롬프트 D 결과 (debug 10개) */
    { "id": "os-d001", ... },

    /* 프롬프트 E 결과 (ordering 5개) */
    { "id": "os-o001", ... }
  ]
}
```

---

## 9. Git 커밋 컨벤션 (나중에 히스토리 보기 편하도록)

```bash
# 문제 추가
git commit -m "add(os): 데드락 시나리오 문제 5개"

# 문제 수정
git commit -m "fix(db): 트랜잭션 격리수준 해설 보완"

# 앱 기능 변경
git commit -m "feat: 오답 노트 화면 추가"

# 버그 수정
git commit -m "fix: iOS Safari 레이아웃 깨짐 수정"
```

---

## 10. Claude Code 실행 순서

```
1. Claude Code에 이 문서 붙여넣고:
   "이 가이드대로 CS 퀴즈 PWA 앱 만들어줘.
    BASE_URL은 placeholder로 두고, data/ 폴더에 각 topic별 빈 JSON 파일도 만들어줘"

2. GitHub 저장소 생성 후 push
   → Settings → Pages → main 브랜치로 배포 활성화

3. BASE_URL을 실제 GitHub Pages URL로 교체 후 재push

4. 문제 생성 프롬프트 A~E를 주제별로 실행
   → 결과를 data/*.json에 붙여넣고 commit + push

5. Safari에서 GitHub Pages URL 접속
   → 홈 화면에 추가
```

---

## 11. 2차 기능 목록

- [ ] 오답 노트 (틀린 문제만 다시 풀기)
- [ ] 문제 유형별 정답률 분석
- [ ] 타이머 모드
- [ ] 북마크
- [ ] 주제별 JSON 파일 자동 병합 스크립트
