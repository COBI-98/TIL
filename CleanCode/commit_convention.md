# 커밋 컨벤션

## 커밋 메시지 구조
기본 적인 커밋 메시지 구조는 **제목,본문,꼬리말** 세가지 파트로 나누고, 각 파트는 빈줄을 두어 구분한다.

```
<type>(<scope>): <subject>
<BLANK LINE>
<body>
<BLANK LINE>
<footer>
```
<br>

## 커밋 타입
- feat : 새로운 기능 추가
- fix : 버그 수정
- docs : 문서 수정
- style : 코드 포맷팅, 세미콜론 누락, 코드 변경이 없는 경우
- refactor : 코드 리펙토링
- test : 테스트 코드, 리펙토링 테스트 코드 추가
- chore : 빌드 업무 수정, 패키지 매니저 수정

<br>


## scope
커밋 변경 위치를 지정하는 것
ex: $location, $browser, $compile, $rootScope, ...

<br>

## subject
- 변경 사항에 대한 간결한 설명
- 현재 시제의 명령어 사용
    - (ex: changed X, changes X, change O)
- 첫 글자 대문자 금지
- 끝에 (.) 금지

## Body
- 한 줄 당 72자 내로 작성
- 양에 구애받지 않고 최대한 상세히 작성한다.
- 어떻게 변경했는지 보다 무엇을 변경했는지 또는 왜 변경했는지를 설명

<br>

## footer
꼬릿말은 다음의 규칙을 지킨다.

꼬리말은 optional이고 이슈 트래커 ID를 작성한다.
꼬리말은 "유형: #이슈 번호" 형식으로 사용한다.
여러 개의 이슈 번호를 적을 때는 쉼표(,)로 구분한다.
이슈 트래커 유형은 다음 중 하나를 사용한다.
- Fixes: 이슈 수정중 (아직 해결되지 않은 경우)
- Resolves: 이슈를 해결했을 때 사용
- Ref: 참고할 이슈가 있을 때 사용
- Related to: 해당 커밋에 관련된 이슈번호 (아직 해결되지 않은 경우)
    - ex) Fixes: #45 Related to: #34, #23


##  Commit 예시
```
feat: "회원 가입 기능 구현"

SMS, 이메일 중복확인 API 개발

Resolves: #123
Ref: #456
Related to: #48, #45
```

```
fix($compile): IE9를 위한 몇 가지 유닛 테스트

이전 버전의 IE는 대문자로 HTML을 직렬화하지만, IE9는 그렇지 않습니다.
대소문자 구분 없이 일치하도록 기대하는 것이 좋을 것입니다.
하지만 Jasmine은 예외 기대에 정규식 사용을 허용하지 않습니다.

#392를 해결했습니다.
foo.bar API가 깨졌으며, 대신 foo.baz를 사용해야 합니다.
```

```
style($location): 누락된 세미콜론 몇 개 추가
```

```
docs(guide): Google 문서에서 수정된 문서 업데이트

들여쓰기
batchLogbatchLog -> batchLog
주기적인 확인 시작
누락된 중괄호
```

<br>

참고자료

https://velog.io/@shin6403/Git-git-%EC%BB%A4%EB%B0%8B-%EC%BB%A8%EB%B2%A4%EC%85%98-%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0

https://github.com/yeon-06/memo/blob/main/clean-code/%EC%BB%A4%EB%B0%8B%EB%A9%94%EC%8B%9C%EC%A7%80.md
