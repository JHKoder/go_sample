### 소스코드 표현 
 - UTF-8 인코딩된 유니코드 텍스트
 - 컴파일러는 NUL 문자 를 허용하지 않을 수 있음

```go
newline        = /* 유니코드 코드 포인트 U+000A */ .
unicode_char   = /* 새 줄을 제외한 임의의 유니코드 코드 포인트 */ .
unicode_letter = /* "letter"로 분류된 유니코드 코드 포인트 */ .
unicode_digit  = /*"숫자, 십진 숫자"로 분류된 유니코드 코드 포인트. */ .
```

### 숫자 형식

---
```go 
uint8  (0 to 255)
uint16 (0 to 65535)
uint32 (0 to 4294967295)
uint64 (0 to 18446744073709551615)

int8   (-128 to 127)
int16  (-32768 to 32767)
int32  (-2147483648 to 2147483647)
int64  (-9223372036854775808 to 9223372036854775807)

float32     the set of all IEEE-754 32-bit floating-point numbers
float64     the set of all IEEE-754 64-bit floating-point numbers

complex64   the set of all complex numbers with float32 real and imaginary parts
complex128  the set of all complex numbers with float64 real and imaginary parts

byte        alias for uint8
rune        alias for int32
```
---

### 구현별 크기를 가진 미리 선언된 정수 형식 집합도 있습니다. 
```go
uint     either 32 or 64 bits
int      same size as uint
uintptr  an unsigned integer large enough to store the uninterpreted bits of a pointer value
```
### 배열
---
```go
// invalid array types
type (
	T1 [10]T1              // T1의 원소 종류는 T1입니다
	T2 [10]struct{ f T2 }  // T2에 T2가 구조체의 구성요소로 포함되어 있습니다
	T3 [10]T4              // T3는 T4 내의 구조물의 구성요소로서 T3를 포함합니다
	T4 struct{ f T3 }      // T4에 T4가 구조의 배열 T3의 구성 요소로 포함되어 있습니다
)
a[2:]  // same as a[2 : len(a)]
a[:3]  // same as a[0 : 3]
a[:]   // same as a[0 : len(a)]

// valid array types
type (
	T5 [10]*T5                //  T5에는 포인터의 구성 요소로 T5가 포함되어 있습니다
	T6 [10]func() T6          //  T6는 기능 유형의 구성 요소로 T6를 포함합니다
	T7 [10]struct{ f []T7 }   // T7에 구조물의 슬라이스 구성요소로 T7이 포함되어 있습니다
)
```

### 구조체 형식
구조체는 필드라고 하는 명명된 요소의 시퀀스이며, 각 요소에는 이름,유형,필드을 명시적 또는 암시적으로 지정 가능

---

```go
type P struct {
	T3 *T3
	T4 *T4
}

struct {
	T1       
	*T2      
	P.T3     
	*P.T4    
	x, y int 
}

```

### 함수 
---
```go
func()
func(x int) int
func(a, _ int, z float32) bool
func(a, b int, z float32) (bool)
func(prefix string, values ...int)
func(a, b int, z float64, opt ...interface{}) (success bool)
func(int, int, float64) (float64, *[]int)
func(n int) func(p *T)
```

### 인터페이스
---
```go

interface {
	Read([]byte) (int, error)
	Write([]byte) (int, error)
	Close() error
}
--------------
func (p T) Read(p []byte) (n int, err error)
func (p T) Write(p []byte) (n int, err error)
func (p T) Close() error
```

+ 빈 인터페이스의 형식 집합은 인터페이스가 아닌 모든 형식의 집합입니다.
+ 양식 용어의 형식 집합은 기본 형식이 인 모든 형식의 집합입니다. ~TT
+ 용어 합집합의 형식 집합은 용어 형식 집합의 합집합입니다. t1|t2|…|tn


## 변수 선언 

```go
var i int
var U, V, W float64
var k = 0
var x, y float32 = -1, -2
var (
	i       int
	u, v, s = 2.0, 3.0, "bar"
)
var re, im = complexSqrt(-1)
var _, found = entries[name]  // map lookup; only interested in "found"
```

간단 변수 선언<Br>
``` ShortVarDecl = IdentifierList ":=" ExpressionList . ``` <br>

``` "var" IdentifierList "=" ExpressionList . ```
약자
```go
var d = math.Sin(0.5)  // d is float64
var i = 42             // i is int
var t, ok = x.(T)      // t is T, ok is bool
var n = nil            // illegal
```
---
### 형식 어설션 
인터페이스 타입의 표현식의 경우, 그러나 유형 매개 변수가 아니며 유형 , 기본 표현식 ```xT```

보다 정확하게는 인터페이스 유형이 아닌 경우 의 동적 유형은 유형과 동일합니다. 이 경우 의 (인터페이스) 유형을 구현해야 합니다. 그렇지 않으면 type 의 값을 저장할 수 없으므로 type 어설션이 유효하지 않습니다. 

Tx.(T)xTTxxTTx.(T)xT

```go
v, ok = x.(T)
v, ok := x.(T)
var v, ok = x.(T)
var v, ok interface{} = x.(T) // dynamic types of v and ok are T and bool
```

### 함수 선언
---
```go
func min[T ~int|~float64](x, y T) T {
	if x < y {
		return x
	}
	return y
}
```
### 함수 리터럴
```go
func(a, b int, z float64) bool { return a*b < int(z) }

f := func(x, y int) int { return x + y }
func(ch chan int) { ch <- ACK }(replyChan)
```

### 호출

```go 
func Split(s string, pos int) (string, string) {
	return s[0:pos], s[pos:]
}

func Join(s, t string) string {
	return s + t
}

if Join(Split(value, len(value)/2)) != value {
	log.Panic("test fails")
}
var p Point
p.Scale(3.5)
```

### 매개변수에 인수 전달

```go
func Greeting(prefix string, who ...string)
Greeting("nobody")
Greeting("hello:", "Joe", "Anna", "Eileen")
```
```go
s := []string{"James", "Jasmine"}
Greeting("goodbye:", s...)
```

### 인스턴스화 
유추할 수 있는 경우 형식 인수 목록은 함수가 다음과 같은 경우 완전히 생략될 수 있습니다.

일반 인수로 호출됩니다.
알려진 유형의 변수에 할당됨
다른 함수에 인수로 전달되거나
결과로 반환됩니다.
```go
func sum[T ~int | ~float64 | ~string](x... T) T { … }

x := sum                       // illegal: the type of x is unknown
intSum := sum[int]             // intSum has type func(x... int) int
a := intSum(2, 3)              // a has value 5 of type int
b := sum[float64](2.0, 3)      // b has value 5.0 of type float64
c := sum(b, -1)                // c has value 4.0 of type float64

type sumFunc func(x... string) string
var f sumFunc = sum            // same as var f sumFunc = sum[string]
f = sum                        // same as f = sum[string]
```

### 제네릭 (형식유추)
```go
func dedup[S ~[]E, E comparable](S) S { … }

type Slice []int
var s Slice
s = dedup(s) 
```
```go
    Slice ≡A S      (1)
	S     ≡C ~[]E   (2)
```
```go
    S ➞ Slice
	E ➞ int
```

### 변환 
*<- func
```go 
*Point(p)        // *(Point(p))와 동일
(*Point)(p)      // p를 *Point로 변환
<-chan int(c)    // <-(chan int(c))와 동일
(<-chan int)(c)  // c를 <-chan int로 변환
func()(x)        // 함수 시그니처 func() x
(func())(x)      // x를 func()로 변환
(func() int)(x)  // x를 func() int로 변환
func() int(x)    // x를 func() int로 변환 (모호하지 않음)

uint(iota)               // 타입이 uint인 iota 값
float32(2.718281828)     // 타입이 float32인 2.718281828
complex128(1)            // 타입이 complex128인 1.0 + 0.0i
float32(0.49999999)      // 타입이 float32인 0.5
float64(-1e-1000)        // 타입이 float64인 0.0
string('x')              // 타입이 string인 "x"
string(0x266c)           // 타입이 string인 "♬"
myString("foo" + "bar")  // 타입이 myString인 "foobar"
string([]byte{'a'})      // 상수가 아님: []byte{'a'}는 상수가 아님
(*int)(nil)              // 상수가 아님: nil은 상수가 아니며, *int는 boolean, numeric, 또는 string 타입이 아님
int(1.2)                 // 잘못된 사용: 1.2는 int로 표현될 수 없음
string(65.0)             // 잘못된 사용: 65.0은 정수 상수가 아님
```

상수가 아닌 값은 다음과 같은 경우에  xT 형식으로 변환될 수 있습니다.

- x는 T에 할당될 수 있습니다.
- 구조체 태그를 무시하면(아래 참조), x의 타입과 T는 타입 매개 변수는 아니지만 동일한 기본 형을 갖습니다.
- 구조체 태그를 무시하면(아래 참조), x의 타입과 T는 이름이 지정되지 않은 포인터 유형이며, 포인터의 기본 형은 타입 매개 변수는 아니지만 동일한 기본 형을 갖습니다.
- x의 타입과 T는 모두 정수 또는 부동 소수점 타입입니다.
- x의 타입과 T는 모두 복소수 타입입니다.
- x는 정수 또는 바이트 또는 룬 슬라이스이고, T는 문자열 타입입니다.
- x는 문자열이고, T는 바이트 또는 룬 슬라이스입니다.
- x는 슬라이스이고, T는 배열 [Go 1.20] 또는 배열에 대한 포인터 [Go 1.17]이며, 슬라이스와 배열의 요소 유형이 동일합니다.
- 또한, T 또는 x의 타입 V가 타입 매개 변수이면, x는 다음 조건 중 하나가 적용되는 경우에도 T로 변환될 수 있습니다:
  - V와 T가 모두 타입 매개 변수이고, V의 타입 집합에 있는 각 타입의 값이 T의 타입 집합에 있는 각 타입으로 변환될 수 있습니다.
  - V만이 타입 매개 변수이고, V의 타입 집합에 있는 각 타입의 값이 T로 변환될 수 있습니다.
  - T만이 타입 매개 변수이고, x가 T의 타입 집합에 있는 각 타입으로 변환될 수 있습니다.
- 구조체 유형을 변환할 때 구조체 태그는 변환의 목적을 위해 구조체 유형의 식별성을 비교할 때 무시됩니다.

### 명령문 종료
- return , goto 


### for 
```go
for a < b {
	a *= 2
}
for i := 0; i < 10; i++ {
	f(i)
}
for _, p := range prints {
	p()
} //"prints" 슬라이스의 각 요소에 대해 함수를 호출하고, 해당 함수는 반환 값을 무시
var ch chan Work = producer()
for w := range ch {
	doWork(w)
}
// 워커 패턴 
```
continue // 이건  goto 문느낌이라서 봉인 

### 런타임 패닉 처리
종료 시퀀스를 패닉
```go
func panic(interface{})
func recover() interface{}

panic(42)
panic("unreachable")
panic(Error("cannot parse"))

func protect(g func()) {
	defer func() {
		log.Println("done")  // println은 패닉이 발생해도 정상적으로 실행됩니다
		if x := recover(); x != nil {
			log.Printf("run time panic: %v", x)
		}
	}()
	log.Println("start")
	g()
}
```

### 패키지

```go
Import declaration          임포트 선언 

import   "lib/math"         math.Sin
import m "lib/math"         m.Sin
import . "lib/math"         Sin
```


### 그외 설명 
언어적 특성
```adoc
- 유형유추는 대부분 *예상대로 작동* 하도록 설계 
- 유형 통합은 매칭모드에 의해 제어되며 *정확*하거나 _느슨_해야함
- 
- 
```

### 버전별 차이 

#### 1.9 
```
별칭 선언
1.18
제네릭 추가 
1.20
slice 및 array 요소가 있는 경우 slice를 배열로 변환할 수 있습니다.
1.21
형식 유추
1.22range for 반복 상한선은 정수값 
```