---
title: "Shell sript 특수문자"
date: 2021-02-22
comments: true
read_time: false
toc: false
categories:
  - Linux
tags:
  - shell
  - script
---

shell script에서 사용되는 특수문자에 대한 간단 정리

|   표기   |                명칭                |                              의미                               |
| :------: | :--------------------------------: | :-------------------------------------------------------------: |
|   ` `    |       공백문자(White Space)        |                탭, 줄바꿈, 캐리지리턴 등을 지칭                 |
|   `$`    |        확장문자(Expansion)         |          파라미터 확장, 명령 대체, 산술 확장 등이 있음          |
|   `""`   |      큰 따옴표(Double Quotes)      | 텍스트가 공백 등으로 인해 여러 단어나 인수로 분리되지 않도록 함 |
|   `''`   |     작은 따옴표(Single Quotes)     |                  모든 문자가 일반 문자로 해석                   |
|   `\`    |          역슬래쉬(Escape)          |    큰따옴표 내에 다음의 글자가 특수문자로 인식되는 것을 막음    |
|   `#`    |           주석(Comment)            |                      해당 줄의 끝까지 주석                      |
|   `[]`   |             조건(Test)             |                    조건의 true, false를 평가                    |
|   `!`    |            부정(Negate)            |          조건의 결과를 반전시커거나 종료 상태를 무효화          |
| `>`, `<` |      방향재지정(Redirection)       |                   명령의 출력과 입력을 재지정                   |
|  &#124;  |            파이프(Pipe)            |          초기 명령의 출력을 2차 명령의 입력으로 재지정          |
|   `;`    |    명령분리자(Command Sperator)    |                   같은 행에 있는 명령을 구분                    |
|   `{}`   |     인라인 그룹(Inline Group)      |                내부 명령을 하나의 명령처럼 취급                 |
|   `()`   |    서브쉘 그룹(Subshell Group)     |              내부 명령이 서브쉘에서 실행되는 경우               |
|  `(())`  | 산술 표현식(Arithmetic Expression) |          산술 표현식에 사용되나 테스트에서도 사용 가능          |
