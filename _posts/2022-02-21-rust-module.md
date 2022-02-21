---
title: "Rust - chapter 7(모듈)"
date: 2022-02-21
comments: true
read_time: false
toc: true
categories:
  - Rust
tags:
  - rust
  - module
---

[공식 문서 - 번역글](https://rinthel.github.io/rust-lang-book-ko/ch07-00-modules.html)의 7번째 챕터 정리  
`'Hello world!'` 같은 간단한 동작은 한 파일로 작성할 수 있지만 코드 규모가 커질 경우 재사용성과 조직화를 위해서 기준대로 코드를 분리하게 되고 러스트는 분리된 코드들을 사용할 수 있는 모듈 시스템을 가지고 있다.

## mod

모듈 예제를 작성하기 위해서 라이브러리 크레이트를 먼저 만들면 평상시 생기는 `main.rs` 가 아닌 `lib.rs` 파일이 생성되는 것을 볼 수 있다.  
이러한 라이브러리 크레이틑 실행할 것이 없으므로 컴파일을 할 때 `cargo build` 를 사용하자.

```sh
cargo new hello-world --lib
```

먼저 모듈을 정의할 때는 `mod` 키워드를 사용하고 `::` 을 사용하여 호출할 수 있다. 현재 `network` 안에 `client` 이 있는데 이런 부분들을 파일로 분리할 수 있다.

```rs
mod network {
    fn connect() {
        println!("Network Connecting...");
    }

    mod client {
        fn connect() {
          println!("Client Connecting...");
        }
    }
}


network::connect();
network::client::connect();
```

모듈들을 분리할 경우 파일시스템을 이용하여 폴더와 파일명으로 모듈을 분리할 수 있다.  
같은 레벨에 있는 모듈의 경우 `client.rs` 와 같이 해당하는 모듈의 이름으로 파일을 작성하면 되며, 해당 모듈 안에 있는 모듈을 다시 분리하고자 한다면 `network` 와 같이 디렉토리를 만들고 `network` 의 내용은 `mod.rs` 로 `network` 모듈 안에 있는 `server` 모듈을 다시 파일로 만들어 옮겨주면 된다.

```
src
├── client.rs
├── lib.rs
└── network
    ├── mod.rs
    └── server.rs
```

## pub

`extern crate 모듈명;` 을 통해 만들어진 라이브러리 크레이트를 가져올 수 있다. 아래와 같은 코드에서는 `private` 모듈이라는 에러가 발생하게 될 텐데 모듈을 `pub` 을 넣어주지 않을 경우 `private` 하여 외부에서 사용할 수 없기 때문이다.  
외부에서 사용하고자 하는 모듈과 함수는 `pub` 키워드를 사용해주어야 한다.

```rs
extern crate communicator;

fn main() {
    communicator::client::connect();
}

// src/lib.rs
pub mod client;
pub mod network;
```

### use

`use` 를 통해서도 간결하게 가져와서 사용할 수 있다.

```rs
pub mod a {
    pub mod series {
        pub mod of {
            pub fn nested_modules() {}
        }
    }
}
enum TrafficLight {
    Red,
    Yellow,
    Green,
}

// 전체 가져오기
// use TrafficLight::*;
use TrafficLight::{Red, Yellow};
use a::series::of;

fn main() {
    of::nested_modules();
    let red = Red;
    let yellow = Yellow;
    let green = TrafficLight::Green;
}
```

## super

`super` 키워드를 통해서 부모 모듈로 거슬러 올라 갈 수 있다.

```rs
#[cfg(test)]
mod tests {
    use super::client;

    #[test]
    fn it_works() {
        client::connect();
    }
}
```

<hr/>

_개인이 참고하고자 작성한 글이며, 잘못된 정보가 있을 수 있습니다. 잘못된 정보는 메일로 보내주시면 감사하겠습니다._ 🙏
