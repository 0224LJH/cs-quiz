# CS Quiz 학습 자료 정리 작업 지시서

이 문서는 `base` 폴더의 원문 정리 자료와 `data` 폴더의 퀴즈 데이터를 앱 내부 학습 자료로 전환하기 위한 공통 작업 지시서입니다.

여러 터미널에서 동시에 작업할 수 있도록, 각 작업자는 **하나의 파트만 맡고**, 자신의 파트 산출물만 수정합니다.

## 목표

앱의 학습 자료 화면은 다음 흐름으로 읽히게 만든다.

1. 홈 화면
2. 파트 선택: `운영체제`, `네트워크`, `자료구조`, `데이터베이스`, `Java`, `Spring`, `디자인패턴`, `개발자 필수`
3. 파트 내부 소분류 선택
4. 소분류 안의 글 읽기
5. 글에서 관련 퀴즈로 이동

정리 결과물은 모바일에서 읽기 편한 Markdown이어야 한다. 원문 전체를 그대로 붙여 넣는 것이 아니라, 앱 안에서 읽을 수 있게 구조화하고 다듬는 것이 핵심이다.

## 실행 프롬프트

작업자는 다음 프롬프트만 받아도 이 문서를 읽고 바로 실행할 수 있어야 한다.

```text
LEARNING_CONTENT_WORKFLOW.md 읽고 실행해줘.
```

파트를 명시받지 않은 경우에는 아래의 `파트 선점 규칙`에 따라 아직 선점되지 않은 파트 하나를 맡는다.
파트를 명시받은 경우에는 해당 파트만 작업하고, 다른 파트는 자동으로 선택하지 않는다.

## 작업 범위

작업자는 콘텐츠 정리만 수행한다.

- 수정 가능: `content/notes/{topic}.md`
- 생성 가능: `content/claims/{topic}.lock`
- 읽기 전용 참고: `base/*.md`, `data/*.json`, `content/manifest.json`, `index.html`, `README.md`
- 수정 금지: `base/*.md`, `data/*.json`, `content/manifest.json`, `index.html`, `README.md`, 다른 파트의 `content/notes/*.md`

앱 구현, 스타일 수정, 라우팅 구현은 이 작업의 범위가 아니다. 콘텐츠 정리가 끝난 뒤 별도 단계에서 앱이 `content/notes/*.md`를 읽도록 구현한다.

## 시작 준비

작업 시작 전 필요한 폴더가 없으면 만든다.

```powershell
New-Item -ItemType Directory -Force content/claims, content/notes
```

한글 파일명이나 공백이 있는 파일을 읽을 때는 PowerShell에서 `-LiteralPath`와 `-Encoding UTF8`을 사용한다.

```powershell
Get-Content -Encoding UTF8 -LiteralPath "base/자바, 스프링, JPA 관련.md"
Get-Content -Encoding UTF8 -LiteralPath "base/필수 상식.md"
```

실제 콘텐츠 파일 편집은 작업 도구의 파일 편집 기능을 사용한다. 단순히 긴 문자열을 터미널로 흘려보내 파일을 덮어쓰지 않는다.

## 파트 선점 규칙

여러 터미널에서 병렬 작업할 때 충돌을 막기 위해 작업 시작 시 파트를 먼저 선점한다.

1. 아래 `파트별 작업표`의 순서대로 확인한다.
2. `content/claims/{topic}.lock` 파일이 없고 `content/notes/{topic}.md`가 없거나 미완성이면 그 파트를 선택한다.
3. 선택한 파트의 lock 파일을 생성한다.
4. lock 생성에 실패하거나 이미 존재하면 다음 파트를 고른다.
5. 선점한 뒤에는 해당 파트의 출력 파일만 수정한다.

lock 파일 내용 예시:

```md
# claim: db

- worker: 터미널 또는 작업자 식별자
- started_at: YYYY-MM-DD HH:mm
- output: content/notes/db.md
```

모든 파트가 이미 선점되어 있으면 작업하지 말고 현재 상태만 보고한다.

선점 후 작업을 완료하면 출력 Markdown의 front matter에서 `status: complete`로 바꾼다. 중간 저장 상태라면 `status: draft`를 유지한다.

## 파트별 작업표

| topic | 앱 표시명 | 출력 파일 | 주요 원문 | 퀴즈 데이터 |
|---|---|---|---|---|
| `os` | 운영체제 | `content/notes/os.md` | `base/운영체제.md`, `base/운영체제_핵심기초개념.md` | `data/os.json` |
| `network` | 네트워크 | `content/notes/network.md` | `base/네트워크_면접대비_정리본.md` | `data/network.json` |
| `ds` | 자료구조 | `content/notes/ds.md` | `base/자료구조.md`, `base/정렬 알고리즘.md` | `data/ds.json` |
| `db` | 데이터베이스 | `content/notes/db.md` | `base/데이터베이스.md` | `data/db.json` |
| `java` | Java | `content/notes/java.md` | `base/자바, 스프링, JPA 관련.md`의 `# 자바` 영역 | `data/java.json` |
| `spring` | Spring | `content/notes/spring.md` | `base/자바, 스프링, JPA 관련.md`의 `# 스프링`, `# JPA` 영역 | `data/spring.json` |
| `design` | 디자인패턴 | `content/notes/design.md` | `base/필수 상식.md`의 디자인 패턴 영역, 필요 시 SOLID 관련 영역 | `data/design.json` |
| `general` | 개발자 필수 | `content/notes/general.md` | `base/필수 상식.md`, `base/암호화 방법 방식.md` | `data/general.json` |

공유 원문을 읽는 것은 괜찮지만, 출력 파일은 반드시 파트별로 분리한다.

## 출력 Markdown 구조

앱 파서는 다음 규칙을 기준으로 목차를 만들 예정이므로, 헤딩 레벨을 반드시 지킨다.

- `#`: 파트 제목. 파일당 1개만 사용한다.
- `##`: 파트 안의 소분류.
- `###`: 소분류 안의 개별 글.
- `####`: 글 내부의 작은 섹션. 필요할 때만 사용한다.

기본 템플릿:

```md
---
topic: db
label: 데이터베이스
quiz_topic: db
source_files:
  - base/데이터베이스.md
  - data/db.json
status: draft
---

# 데이터베이스

이 문서는 CS 면접 준비를 위해 데이터베이스 핵심 개념을 모바일에서 읽기 좋게 정리한 학습 자료입니다.

## 데이터베이스 기본 개념
<!-- category: db-basics -->

### 데이터베이스를 사용하는 이유
<!-- article: db-why-database -->

#### 한눈에 보기
- 파일 시스템 기반 관리의 한계는 데이터 종속성, 중복성, 무결성 문제다.
- 데이터베이스는 데이터를 통합 관리해 일관성과 보안성을 높인다.

#### 핵심 정리

짧은 문단으로 핵심을 설명한다. 한 문단은 모바일 기준 2-4줄을 넘기지 않도록 한다.

#### 면접 답변

> 데이터베이스는 데이터를 구조화해 중복을 줄이고, 무결성, 보안성, 일관성을 보장하기 위해 사용합니다.

#### 헷갈리는 포인트
- 데이터 독립성은 물리적 독립성과 논리적 독립성으로 나뉜다.
- 무결성은 데이터가 유효하고 일관된 상태를 유지하는 성질이다.

#### 관련 퀴즈
- `db-c001`
- `db-c002`
```

## 글 작성 규칙

모바일 화면에서 읽는 학습 자료라는 점을 우선한다.

- 한 문단은 짧게 쓴다.
- 한 글(`###`)은 하나의 질문이나 개념에 답하도록 만든다.
- 긴 원문은 여러 글로 나눈다.
- 표는 4열 이하일 때만 사용한다. 넓은 비교는 목록으로 바꾼다.
- 코드나 명령어는 fenced code block을 사용한다.
- 한국어 용어를 먼저 쓰고, 필요한 경우 영어를 괄호로 병기한다.
- 원문을 그대로 복사하지 말고, 면접 준비용 설명으로 다시 정리한다.
- `data/*.json`의 퀴즈는 본문 내용 검증과 `관련 퀴즈` 연결에 활용한다.
- 내용이 애매하거나 원문에 충돌이 있으면 임의로 확정하지 말고 `> 확인 필요:` 형태로 표시한다.

좋은 글의 길이 기준:

- 쉬운 개념: 500-900자
- 비교/트레이드오프: 800-1400자
- 복잡한 주제: 여러 개의 `###` 글로 분리

## 글 내부 추천 구성

모든 글이 똑같을 필요는 없지만, 가능하면 아래 순서를 따른다.

```md
### 글 제목
<!-- article: topic-slug -->

#### 한눈에 보기
- 핵심 2-4개

#### 핵심 정리

개념을 설명한다.

#### 면접 답변

> 30-60초 안에 말할 수 있는 답변 형태로 정리한다.

#### 헷갈리는 포인트
- 비슷한 개념과의 차이
- 자주 틀리는 조건

#### 관련 퀴즈
- `topic-c001`
```

주제에 따라 `동작 과정`, `장단점`, `선택 기준`, `예시`, `시간복잡도`, `실무 포인트` 같은 섹션을 추가해도 된다.

## article id 규칙

각 `###` 글 바로 아래에는 article 주석을 둔다.

```md
<!-- article: db-index-btree -->
```

규칙:

- `{topic}-{영문-slug}` 형식
- 소문자, 숫자, 하이픈만 사용
- 같은 파일 안에서 중복 금지
- 한국어 제목을 그대로 slug로 쓰지 않는다

예시:

- `db-transaction-acid`
- `os-process-thread`
- `network-tcp-udp`
- `ds-hash-collision`
- `java-gc`
- `spring-ioc-di`
- `design-singleton`
- `general-rest-api`

## 파트별 소분류 가이드

아래 소분류는 출발점이다. 원문 구조가 더 자연스러우면 조정해도 되지만, 앱에서 보기 좋게 너무 잘게 쪼개지지 않도록 한다.

### `os`

추천 `##`:

- 운영체제 기초
- 프로세스와 스레드
- 스케줄링
- 동기화와 교착상태
- IPC와 시스템 콜
- 메모리 관리
- 가상 메모리
- 캐시와 지역성
- 면접 질문 정리

### `network`

추천 `##`:

- 네트워크 계층
- HTTP와 HTTPS
- TCP와 UDP
- 연결 설정과 종료
- IP, MAC, ARP
- 쿠키, 세션, JWT
- REST API와 CORS
- 웹 통신 실무 포인트
- 면접 질문 정리

### `ds`

추천 `##`:

- 배열과 연결 리스트
- 스택과 큐
- 트리
- 힙
- 해시 테이블
- Map과 Set
- 그래프
- 정렬 알고리즘
- 시간복잡도와 선택 기준
- 면접 질문 정리

### `db`

추천 `##`:

- 데이터베이스 기본 개념
- 인덱스
- 정규화
- 트랜잭션
- 교착상태
- 조인
- 스토리지 엔진
- Statement와 PreparedStatement
- NoSQL
- 면접 질문 정리

### `java`

추천 `##`:

- Java 기본
- 객체지향
- 컬렉션
- 예외 처리
- JVM과 메모리
- GC
- 동시성
- Java 8+ 핵심 기능
- 면접 질문 정리

### `spring`

추천 `##`:

- Spring 기본
- IoC와 DI
- Bean과 ApplicationContext
- AOP
- Spring MVC
- 트랜잭션
- JPA와 ORM
- 영속성 컨텍스트
- 연관관계와 로딩 전략
- JPA 실무 이슈
- 면접 질문 정리

### `design`

추천 `##`:

- 디자인패턴 개요
- 생성 패턴
- 구조 패턴
- 행동 패턴
- Singleton
- Factory
- Strategy
- Observer
- Proxy
- Iterator
- SOLID와 설계 원칙
- 면접 질문 정리

### `general`

추천 `##`:

- 좋은 코드와 추상화
- OOP와 함수형 프로그래밍
- 라이브러리와 프레임워크
- RESTful API와 API 개념
- TDD와 테스트
- MVC, MVP, MVVM
- 컴파일러, 인터프리터, JIT
- CI/CD
- Docker와 컨테이너
- HTTP 버전
- Git과 GitHub
- 암호화와 인증
- 면접 질문 정리

## 관련 퀴즈 연결 규칙

각 파트의 `data/{topic}.json`을 읽고 글과 관련 있는 문제 id를 `관련 퀴즈`에 적는다.

연결 기준:

- 글의 핵심 개념을 직접 묻는 문제
- 같은 용어가 등장하는 문제
- 같은 선택 기준이나 트레이드오프를 묻는 문제

너무 많이 연결하지 않는다. 글 하나당 1-5개 정도가 적당하다.

관련 문제가 없으면 다음처럼 쓴다.

```md
#### 관련 퀴즈
- 아직 직접 연결할 퀴즈 없음
```

## 품질 기준

완료된 파트는 다음 조건을 만족해야 한다.

- front matter가 있다.
- `#` 제목은 파일당 1개다.
- `##` 소분류가 5개 이상 있다.
- 각 `##` 아래에 `###` 글이 1개 이상 있다.
- 모든 `###` 글에는 `<!-- article: ... -->` 주석이 있다.
- 각 글은 모바일에서 읽기 쉬운 짧은 문단으로 구성되어 있다.
- `관련 퀴즈` 섹션이 가능한 한 포함되어 있다.
- 원문 파일과 퀴즈 데이터의 핵심 주제를 빠뜨리지 않았다.
- 작업자는 자신의 출력 파일과 lock 파일 외에는 수정하지 않았다.

## 검증 명령

작업 종료 전 아래 명령으로 구조를 확인한다.

```powershell
rg -n "^#{1,4} " content/notes/{topic}.md
```

변경 범위를 확인한다.

```powershell
git status --short
git diff --stat
```

예상 변경 범위는 다음과 비슷해야 한다.

```text
?? content/claims/{topic}.lock
?? content/notes/{topic}.md
```

다른 파트 파일이나 앱 파일이 변경되어 있으면, 자신이 만든 변경인지 확인하고 범위 밖 변경은 만들지 않는다.

## 완료 보고 형식

작업을 마치면 짧게 보고한다.

```md
완료 파트: db
생성 파일: content/notes/db.md
소분류 수: 10
글 수: 38
관련 퀴즈 연결: 74개
검증: rg 헤딩 확인 완료, git diff 범위 확인 완료
남은 확인 필요: 없음
```

## 금지 사항

- 여러 파트를 한 번에 정리하지 않는다.
- 원문 `base/*.md`를 수정하지 않는다.
- 퀴즈 `data/*.json`을 수정하지 않는다.
- 앱 구현 파일 `index.html`을 수정하지 않는다.
- 다른 작업자가 선점한 파트의 출력 파일을 수정하지 않는다.
- 출처 없는 새 사실을 대량으로 추가하지 않는다.
- 긴 원문을 그대로 복사해 앱용 글이라고 처리하지 않는다.
