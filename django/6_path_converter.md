
# URL Dispatcher

특정 URL 패턴을 view에서 처리하기 위해, 이를 mapping하는 list 

> 프로젝트/settings.py에서 최상위 URLConf 모듈을 지정하고, 최초의 urlpatterns로부터 include를 통해 TREE 구조로 확장

HTTP 요청이 들어올 때마다 등록된 urlpatterns 상의 mapping list를 처음부터 순차적으로 훓으며 URL 매칭을 시도함

> 매칭이 되는 URL Rule이 다수 존재하더라도 처음 Rule 만을 사용

> 매칭되는 URL Rule이 없을 경우 404 Page Not found 응답 발생

## 기본 제공되는 Path Converters

1. IntConverter -> r"[0-9]+"

> 정수가 1회 이상 반복

2. StringConverter ->  r"[^/]+"

> /를 제외한 문자열이 1회 이상 반복

3. UUIDConverter -> r"[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}"

> 16진수 범위에 있는 문자가 "8회-4회-4회-4회-12회", 총 36 글자가 있어야 mapping

4. SlugConverter (StringConverter 상속) -> r"[-a-zA-Z0-9_]+"

5. PathConverter (StringConverter 상속) -> r".+"

> 모든 문자열에 대해서 1회 이상 반복

## 정규 표현식

문자열의 패턴, 규칙, Rule을 정의하는 방법으로 문자열 검색이나 치환작업을 간편하게 처리할 수 있음

> 대부분의 프로그래밍 언어에서 지원

- 1 글자에 대한 패턴 + 연속된 출연 횟수 지정

- 대괄호 내에 글자에 대한 후보 글자들을 나열

* 다양한 정규 표현식 패턴 예시

```
1. 1자리 숫자 -> [0123456789] or [0-9] or r"[\d]" or r"\d"

2. 2자리 숫자 -> [0123456789][0123456789] or [0-9][0-9] or r"\d\d"

3. 3자리 숫자 -> r"\d\d\d" or r"\d{3}"

4. 2자리~4자리 숫자 -> r"\d{2,4}"

5. 휴대폰 번호 -> r"010[1-9]\d{7}"

6. 알파벳 소문자 1글자 -> [abcdegghijklmnopqrstuvwxyz] or [a-z]

7. 알파벳 대문자 1글자 -> [ABCDEFGHIJKLMNOPQRSTUVWXYZ] or [A-Z]
```

* 반복횟수 지정 문법

```
1회 반복 : r"\d" or r"d{1}"

n회 반복 : r"d{n}"

n회~m회 반복 : r"\d{n,m}"

0회 or 1회 반복 : r"\d?"

0회 이상 반복 : r"\d*"

1회 이상 반복 : r"\d+"
```




