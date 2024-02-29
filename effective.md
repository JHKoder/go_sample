# 효과적인 GO

C++ ,Java를 go로 간단하게 변환하여 만족스러운 결과를 얻을 수 없음 

속성의 이해하는것이 중요

관용구, 확립된 규칙을 아는것도 중요 

### 서식
---
표준 패키지의 모든 Go 코드는 gofmt로 형식이 지정되었습니다. 
```go
//gofmt
type T struct {
    name    string // name of the object
    value   int    // its value
}
```
- 들여쓰기: 
    - **탭을 사용** 
    - 필요한 경우에만 스페이스를 사용하십시오.
- 라인 길이:
    - 행 길이 제한이 없음
    - 길이에 제한이 없어서 코드가 가독성을 높일 수 있도록 행을 여러 줄로 나누는 것이 권장됩니다.
- 괄호
    - 제어 구조(if, for, switch)는 문법에서 괄호를 사용하지 않습니다. 
    - 연산자 우선순위 계층 구조가 더 짧고 명확하므로, 다른 언어와는 달리 x<<8 + y<<16와 같은 표현은 공백이 의미하는 대로 해석됩니다.
    - 필수 중괄호는 간단한 문장을 작성하도록 권장합니다

### 해설 
- Go는 C 스타일 블록 주석을 제공
### 이름 
- 패키지 외부에 있는 이름의 가시성은 해당 첫 번째 문자는 대문자입니다. 
### getter 
- 게터와 세터에 대한 자동 지원을 제공하지 않습니다. 
- sample 
    - ```go
        obj.User()
        obj.SetUser(1)
      ```
### 인터페이스 이름 
- 메서드 이름과 -er 접미사 또는 유사한 수정 사항 에이전트 명사를 구성
### 믹스캡(MixedCaps)
- 다중 단어로 이루어진 이름을 작성할 때 언더스코어(_) 대신 각 단어의 첫 글자는 대문자
### 세미클론 
- 세미콜론이 필요하지 않습니다. 
- 줄 바꿈 앞의 마지막 토큰이 식별자인 경우
- 세미콜론은 닫는 중괄호 바로 앞에서 생략할 수도 있습니다. 
    - ```go 
          go func() { for { dst <- <-src } }()
      ```
## 제어 구조
C의 제어 구조와 관련이 있지만 다름

- for
    - C 언어에서의 do나 while 루프가 없고, 대신에 약간 일반화된 형태의 for 루프가 사용됩니다.
    - for 루프는 초기화 구문, 조건식, 후속 작업이 있는 구조를 갖습니다.
- switch 
    - C 언어의 switch에 비해 더 유연한 구조를 가집니다.
    - if와 switch 구문은 선택적인 초기화 구문을 허용하며, 이는 for와 유사한 형태를 가집니다.
- break와 continue
    - break와 continue 문은 어떤 것을 끝낼지 또는 계속 진행할지를 식별하기 위해 선택적인 레이블을 허용합니다.
- 새로운 제어 구조
    - type switch와 multiway communications multiplexer인 select와 같은 새로운 제어 구조가 있습니다.
- 문법 차이
    - 괄호가 없고 항상 중괄호로 감싸진 형태를 가집니다.

### if
---
if 문을 사용할 때, 특히 에러 처리(error handling)와 관련하여 else를 필요 없이 사용하는 경우에 대한 관례

```go
f, err := os.Open(name)
if err != nil {
    return err
}
codeUsing(f)
```
위의 코드에서는 파일을 열 때 에러가 발생하면 즉시 함수를 종료하도록 되어 있습니다. 이 경우 else 문을 사용하지 않아도 되며, 코드는 에러 케이스를 바로 처리하고 나머지 코드는 에러가 없을 때만 실행됩니다.

이러한 스타일은 에러 처리 코드가 더 간결해지고 가독성이 향상되도록 도와줍니다. 에러 처리는 함수의 제일 첫 부분에서 먼저 이루어지며, 성공적인 흐름은 아래로 진행되기 때문에 else가 필요하지 않습니다.

다음 예제에서도 비슷한 패턴이 사용되고 있습니다:
```go
f, err := os.Open(name)
if err != nil {
    return err
}
d, err := f.Stat()
if err != nil {
    f.Close()
    return err
}
codeUsing(f, d)

```
여기서도 에러가 발생하면 즉시 함수를 종료하며, else를 사용하지 않아도 코드가 명확하게 구조화되어 있습니다.

### 선언 및 재항당 
- 여담: 이전 섹션의 마지막 예제에서는 짧은 선언 양식의 작동 방식에 대한 세부 정보를 보여 줍니다.
```go
f, err := os.Open(name)
d, err := f.Stat()
```

### for 
```go
// Like a C for
for init; condition; post { }

// Like a C while
for condition { } 

// Like a C for(;;)
for { }

// i := 0; i < 10; i++ 
// key, value := range oldMap
// key := range m
// _, value := range array
// pos, char := range "日本\x80語" 
// i, j := 0, len(a)-1; i < j; i, j = i+1, j-1

```

### switch
```go
switch {
    case '0' <= c && c <= '9':
        return c - '0'
    case 'a' <= c && c <= 'f':
        return c - 'a' + 10
    case 'A' <= c && c <= 'F':
        return c - 'A' + 10
    case ' ', '?', '&', '=', '#', '+', '%':
        return true
    }
```

