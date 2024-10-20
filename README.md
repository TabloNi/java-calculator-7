# 문제 해결을 위한 초기 생각 정리

문제를 처음 읽었을 때는 쉽다고 생각했지만, 생각할수록 예외 사항이 많다는 판단이 들었다. 그래서 문제를 작게 만들어 관련 코드를 구현하고, 이후 확장 기능을 제작하여 로직을 추가하고자 했다.

## 문제 나누기
1. 커스텀 구분자 기능을 초기에는 제외하고 코드를 구현한다.
2. 예외 사항을 먼저 구현하여 문자열의 예외 사항을 처리한다.
3. 올바른 문자열을 조건에 맞게 처리하는 로직을 구현한다.
4. 커스텀 구분자 기능이 추가될때의 예외/변경 추가한다.
5. 커스텀 구분자 기능을 추가한다.

## 기본 로직 (올바른 문자열)

1. 문자열을 입력 받는다.
2. 해당 값을 `split` 기능을 통해 구분자 기준으로 나눈다.
3. 나뉜 문자열은 `int` 형태로 변환되어, sum에 더한다.
4. sum을 반환한다.

## 생각해볼 예외 사항

1. 입력이 없을 경우 (바로 엔터)
   - 길이가 0이면 0을 반환한다.

2. 음수가 있을 경우
   - 예를 들어, `... 13,-12:14 ...` 같은 경우.
   - 음수가 있을 경우, 구분자와 - 기호가 붙어 있게 된다.
   - 즉, 2개 이상의 문자열이 연속으로 있으면 -를 판단할 수 있다.
        - 단순히 - 기호를 검색하기에는 구분자가 -일 경우가 있다.

3. `,`와 `:` 외의 구분자가 있을 수 있다.
   - 문자열 순환을 하며 `,`와 `:` 외의 문자열을 반환하면 오류를 발생시킨다.

4. 맨 처음과 끝이 구분자일 경우
   - 맨 앞글자와 뒷글자가 숫자인지 확인하고 아니면 오류를 발생시킨다.
   - 음수가 맨 처음 들어오는 것도 방지할 수 있다.

5. `int` 범위(약 21억) 이상의 개념도 처리해준다.
   - `BigInteger` 개념을 활용한다.

6. 구분자가 여러 개 연속으로 붙어있을 수 있다.
   - 예를 들어, `... 1:2::4,5 ...`라고 할 경우, 구분자 안에 숫자가 없기에 오류로 처리한다.
   - 이것도 2개 이상의 문자열이 연속으로 붙어 있으면 오류를 발생시킨다.

7. 추후 커스텀 구분자를 위해서, 코드를 커스텀 구분자가 추가될 경우 이것도 함께 반영되도록 짠다.
   - 커스텀 구분자 배열을 만들어, 이 배열의 조건을 기준으로 검사한다. (이론상 커스텀 구분자를 더 추가 가능하다.)

## 커스텀 구분자 고려

문자열의 0, 1, 3, 4 번지가 각각 `/`, `/`, `\`, `n`인지 확인해보고, 맞으면 `extended` 모드를 `true`로 바꿔준다.

- `extended` 모드가 `true`일 경우 해줘야 할 일.

1. 문자열의 2번지의 문자열을 구분자 배열에 추가해준다.
   - 이때, 2번지의 문자열이 숫자일 경우 `exception`을 발생시킨다.

2. 위 기본 조건 사항의 변경사항을 반영한다.

## 생각해 본 예외 사항

1. 커스텀 조건 사이에 문자열이 2개 이상일 경우
   - 애초에 자리수에 맞는 조건 검사가 안 되어 `extended` 모드가 안 켜질 것이고, 기존 조건의 문자 연속 2개 불가 조건에 의하여 안 될 것이다.

2. `-` 기호가 구분자로 들어올 경우
   - 마이너스 숫자와 겹칠 수 있다. 이때, 마이너스 숫자가 나오게 될 경우는 `... --14-7-8 ...`이거나, `... ,-98,72 ...`와 같은 경우일 것이다.
   - 따라서, 이 경우에도 문자가 2개 이상 있을 경우 `exception`을 발생시키는 것으로 해결할 수 있다.

## 기존 조건의 변경 사항

1. 기존 2번 조건에서, 5번지 이후의 영역을 검사 하게 설정한다.
2. 기존 3번 조건에서, 구분자 배열을 기반으로 검사하고, 5번지 이후의 영역을 검사하게 한다.
3. 기존 4번 조건에서, 맨 처음 부분의 시작 검사 주소를 5번지로 설정한다.
4. 기존 5번 조건에서, `int` 범위 조건을 추가한다.

## 추가 고려 사항

- 그냥 `//*\n`이나 딸랑 `3` 같은 숫자만 올 경우?
  - 왼쪽은 결국 숫자가 없기에 에러를 띄우고, 오른쪽은 `3`만 띄우는 게 프로그램의 취지상 맞다.