---
title: "Rust - chapter 9 error handling"
date: 2022-03-08
comments: true
read_time: false
toc: true
categories:
  - Rust
tags:
  - rust
  - error
---

Rust 문서 챕터 9 에러처리를 읽고 정리한 글이다.([링크](https://rinthel.github.io/rust-lang-book-ko/ch09-00-error-handling.html))

## 에러 처리

러스트에서는 에러를 두가지 범주로 묶는다고 한다. `복구 가능한 에러`, `복구 불가능한 에러`  
다수의 프로그래밍 언어는 예외처리 기능을 가지고 있지만 러스트는 없다고 한다. 대신 복구 가능한 에러를 위한 `Result<T, E>` 와 복구 불가능한 에러에 대응할 수 있는 `panic!` 매크로가 있다.

## 복구 불가능한 에러

복구 불가능한 에러의 경우는 `panic!` 매크로를 사용하여 실패 메시지를 출력하고 스택을 되감아 청소한 뒤 프로그램을 종료할 수 있다.

> 💿 &nbsp; **panic! 매크로 스택 되감기 설정**  
> 기본적으로 이 매크로는 실행되면 프로그램이 되감기를 시작하는데 이는 스택을 거꾸로 훓어가면서 데이터를 제거하는 동작이다. 하지만 이 작업은 비용이 크므로 데이터 없이 프로그램을 종료할 수 있는 방법으로 Cargo.toml 파일에 `[profile]` 섹션에 `panic = 'abort'` 를 설정이 있다.
>
> ```toml
> [profile.release]
> panic = 'abort'
> ```

복구 불가능한 에러로는 벡터에 존재하지 않는 인덱스를 호출하는 것 같이 있다.  
panic! 매크로는 간단하게 사용할 수 있는데 아래와 같다.  
또한 각 구현체에 구현이 되어 있기에 따로 작성하지 않더라도 대부분의 오류 케이스에 러스트는 panic! 매크로를 호출 할 것이다.

```rust
fn main() {
    panic!("crash and burn");
}
```

발생하는 오류를 자세하 확인하고 싶다면 실행할 때 `RUST_BACKTRACE` 환경변수를 넣어주도록 하자.

```sh
RUST_BACKTRACE=1 cargo run
```

## 복구 가능한 에러

`Result<T, E>` 타입을 사용하는 경우이다. T 는 제너릭, E 는 에러 타입이다.  
간단한 예제를 살펴보자

```rs
use std::fs::File;

fn main() {
  let f: u32 = File::open("hello.txt");
}
```

위 예제는 오류를 발생한다. `File::open()` 함수는 반환 타입이 `Resut<T, E>` 이기 때문이다. `Result<T, E>` 타입을 처리하는 경우는 많다.

```rs
use std::fs::File;
use std::io::ErrorKind;

fn main() {
  let f = File::open("hello.txt");
  let f = match f {
    Ok(file) => file,
    Err(ref error) if error.kind() == ErrorKind::NotFound => {
      match File::create("hello.txt") {
        Ok(fc) => fc,
        Err(e) => {
          // 1
          panic!("Tried to create file but there was a problem: {:?}", e);
        }
      }
    },
    Err(error) => {
      // 2
      panic!("Problem opening the file: {:?}", error);
    }
  };
}
```

위에서는 `match` 를 통해 파일이 없을 경우는 파일을 생성하고 생성애도 실패할 경우 패닉 매크로를 호출하고 다른 에러라면 2번 매크로가 동작하게 된다. 작성한 순서대로 확인을 한다는 점과 참조자를 얻기 위해서 `&` 이 아닌 `ref` 를 사용한 점을 유의하자. (이는 18장에서 설명한다고 한다. ~~9장인데...~~)

위 형태를 mat`ch 를 대신하여 아래와 같이도 작성할 수 있으니 참조하자.

```rs
use std::fs::File;
use std::io::ErrorKind;

fn main() {
  let f = File::open("hello.txt").unwrap_or_else(|error| {
    if error.kind() == ErrorKind::NotFound {
      File::create("hello.txt").unwrap_or_else(|error| {
        panic!("Problem creating the file: {:?}", error);
      })
    } else {
      panic!("Problem opening the file: {:?}", error);
    }
  });
}
```

### panic! 단축기능 - unwrap, expect

매번 저렇게 패턴을 작성하는 것은 매우 비효율적이다. 그래서 간단히 작성 할 수 있는 방법이 있다.

```rs
use std::fs::File;

fn main() {
  let f = File::open("hello.txt").unwrap();
  let f = File::open("hello.txt").expect("Failed to open hello.txt");
}
```

모두 파일을 여는데 실패한다면 패닉 매크로를 호출하게 된다. 문서에는 `expect` 를 사용하는 것을 권장하는데 이는 에러에 대해 예제처럼 명확하게 작성할 수 있기 때문이다. `upwarp` 메소드는 에러 메시지를 사용자가 작성할 수 없다.

### 에러 전파하기, 단축기능 - ?

보통 에러가 발생할 수 있는 함수일 경우 함수 내에서 에러를 처리하기보다는 호출하는 쪽에 코드를 반환하여 호출하는 곳에서 어떻게 처리할지 결정하도록 한다. 이를 에러 전파하기(Propagating Errors) 라 한다.

```rs
use std::io;
use std::io::Re
use std::fs::File;

fn read_username_from_file() -> Result<String, io::Error> {
  let f = File::open("hello.txt");

  let mut f = match f {
    Ok(file) => file,
    Err(e) => return Err(e),
  };

  let mut s = String::new();

  match f.read_to_string(&mut s) {
    Ok(_) => Ok(s),
    Err(e) => Err(e),
  }
}
```

먼저 파일을 열고 파일이 없을 경우 먼저 에러를 반환하고 파일에서 문자열을 읽고 여기서도 실패하면 에러를 반환하는 코드이다. 이 코드를 `?` 를 사용하여 단축하면 아래와 같이 된다.

```rs
use std::io;
use std::io::Re
use std::fs::File;

fn read_username_from_file() -> Result<String, io::Error> {
  let mut s = String::new();
  File::open("hello.txt")?.read_to_string(s)?;

  Ok(s);
}
```

## panic! 사용 시점

언제 `panic!` 을 사용하고 `Result` 를 반환할지 결정하는 것도 어려운 일이 될 것으로 보인다. 문서에서는 아래의 경우가 아니라면 기본적으로 Result를 반환하는 것을 권장하고 있다.

- 예제, 프로토타입 코드, 테스트 코드
- 사용자가 컴파일러보다 많은 정보를 가지고 확신할 수 있는 경우
- 타인이 내 코드를 호출하거나 하는 등 코드에 대한 제어권이 없을 경우
- 커스텀 타입을 생성하고 해당 타입의 유효성을 위해서 사용하는 경우
<hr/>

_개인이 참고하고자 작성한 글이며, 잘못된 정보가 있을 수 있습니다. 잘못된 정보는 메일로 보내주시면 감사하겠습니다._ 🙏
