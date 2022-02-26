---
title: "Rust - chapter 8-2(컬렉션-String)"
date: 2022-02-27
comments: true
read_time: false
toc: true
categories:
  - Rust
tags:
  - rust
  - collection
  - string
---

[공식 문서 - 번역글](https://rinthel.github.io/rust-lang-book-ko/ch08-02-strings.html)의 8-2 챕터 정리

앞선 `vetor` 에 이어 `rust`의 `string` 에 대해 알아볼 시간이다. 해당 글에서는 많은 뉴비들이 string 에서 헤매고 그 어려움에 대해서 설명하고 있는데 직접 겪어보자.

## String

많은 언어에 존재하는 `String` 이 `rust` 에서는 어떠한 의미인지 알아보자. 러스트에서 제공되는 문자열 타입은 `str` 하나 뿐이며 `String` 타입은 표준 라이브러리를 통해서 제공되고 있고 두 타입 모두 많이 사용되는 타입이며 `UTF-8` 로 인코딩 되어있다.  
이 두 타입 외에 표준 라이브러리와 라이브러리에서는 다양한 스트링 타입을 제공하고 있으나 해당 글에서는 언급 대상이 아님을 밝히고 있다.

## String 생성

앞선 Vec 에서 사용되었던 많은 연산들이 `String` 에도 같게 사용될 수 있다.  
또한 아래의 "rust string" 에서 `String` 을 생성 하고 있는데 `Display` 트레잇으로 구현된 어떤 타입이든 `to_string` 메소드를 통해서 생성할 수 있다.  
~~아직 트레잇에 대해서 명확한 개념을 잡지 못했다....~~ 💦  
또한 String::from 함수를 사용할 수도 있다.

```rs
// 생성
let mut s1 = String::new();

let text = "rust string";
let s2 = text.to_string();
let s3 = "rust string".to_string();
let s3 = String::from("rust string");
```

## String 갱신

String 은 크기가 변경될 수 있으며 다양한 방법을 통해서 갱신할 수 있다.

`push_str` 는 스트링 슬라이스 `str` 를 파라미터로 가지며 파라미터에 대한 소유권을 가지고 않는다. `push` 메소드는 한 글자만을 추가한다.

String 끼리의 조합의 경우는 `+` 를 사용 할 수 있다. 아래의 코드에서 연산 후 s1 을 더 이상 사용할 수 없게 되는 이유와 &s2 를 사용하는 이유는 + 연산자가 add 메소드를 사용하는데 이 메소드의 형태가 다음과 같기 때문이다.

```rs
fn add(self, s: &str) -> String {
```

`self` 에 `s1` 이 `s` 에 `&s2` 가 대입이 되고 이 동작은 두 스트링을 복사하여 새로운 스트링으로 만드는 것이 아니라 s1의 소유권을 가져와 s2의 복사본을 추가하여 결과와 소유권을 반환하는 형태이다.

\+ 연산의 경우 다수의 스트링을 조합하기에는 어려움이 있으나 이에 대해 `format!` 매크로를 사용할 수 있다.

```rs
let mut s = String::from("hello");
s.push_str(", world");  // "hello, world"
s.push('!');  // "hello, world!"

let s1 = String::from("tic");
let s2 = String::from("tac");
let s3 = s1 + &s2;  // "tictac" 더 이상 s1 을 사용할 수 없다!!

let t1 = String::from("tic");
let t2 = String::from("tac");
let t3 = String::from("toe");
let text = format!("{}-{}-{}", t1, t2, t3);  // "tic-tac-toe"
```

## String 인덱싱

다른 언어는 스트링의 인덱스를 통해 특정 문자에 접근하는 경우가 많았으나 러스트에서는 그럴 경우 에러가 발생하게 된다. 💣

```rs
let s1 = String::from("hello");
let h = s1[0];

// error: the trait bound `std::string::String: std::ops::Index<_>` is not
// satisfied [--explain E0277]
//   |>
//   |>     let h = s1[0];
//   |>             ^^^^^
// note: the type `std::string::String` cannot be indexed by `_`
```

~~'아니 다른 언어에서는 잘만 되는데 왜 러스트에서는 안되냐!'~~ 라고 할 수 있지만 안 되는 이유는 다음과 같다.

`String` 은 `Vec<u8>` 로 래핑되어 있고 `UTF-8` 로 인코딩 값들이 저장되어 있기 때문이다. 인코딩 시 2바이트를 사용하는 문자라면 **실제 문자 길이는 8자여도 String 의 길이는 16** 이 될 것이다. 이렇게 실제 위치가 일치 하지 않으며 각 인덱스에는 문자가 아닌 유니코드의 스칼라 값이 들어 있기 때문에 원하는 값이 나오지 않는다. 아래는 원문에 작성된 마지막 이유이다.

> 📔 러스트가 `String`을 인덱스로 접근하여 문자를 얻지 못하도록 하는 마지막 이유는 인덱스 연산이 언제나 상수 시간(`O(1)`)에 실행될 것으로 기대받기 때문입니다. 그러나 `String`을 가지고 그러한 성능을 보장하는 것은 불가능한데, 그 이유는 러스트가 스트링 내에 얼마나 많은 유효 문자가 있는지 알아내기 위해 내용물의 시작 지점부터 인덱스로 지정된 곳까지 훑어야 하기 때문입니다.

### String 슬라이싱

위와 같이 문자가 가지는 바이트 크기가 다를 수 있기 때문에 사용에 주의를 해야되며 스트링 자르기 위해서는 [] 와 범위를 명시하는 것 좋다.

> ⚠️ 2바이트를 차지하는 문자에 대해서 [0..1] 범위를 사용한다면 프로그램을 죽게 만들 수 있다!

```rs
let text = "hello, world!";

let s = &hello[0..4];
```

## String 내에서 반복적으로 실행되는 메소드

개별적인 유니코드 스칼라 값에 대한 연산이 필요할 경우는 `chars` 를 가공되지 않은 각각의 바이트가 필요할 경우는 `bytes` 를 사용하면 된다.

```rs
for c in "hello".chars() {
    println!("{}", c);
}

for b in "hello".bytes() {
    println!("{}", b);
}
```

<hr/>

_개인이 참고하고자 작성한 글이며, 잘못된 정보가 있을 수 있습니다. 잘못된 정보는 메일로 보내주시면 감사하겠습니다._ 🙏

```

```
