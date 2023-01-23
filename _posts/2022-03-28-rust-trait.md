---
title: "Rust - chapter 10-2 trait"
date: 2022-03-28
comments: true
read_time: false
toc: true
categories:
  - Rust
tags:
  - rust
  - trait
---

Rust 공식문서([링크](https://doc.rust-lang.org/book/ch10-02-traits.html)) 와 Rust 비공식 번역 문서 챕터 10-2 `trait`를 읽고 정리한 글이다.([링크](https://rinthel.github.io/rust-lang-book-ko/ch10-02-traits.html))  
원래 공식 문서는 영어인지라 비공식 번역 문서를 위주로 보고 있었는데 이번 번역글에서 없는 내용이 공식 문서에 있는 것을 확인하고 같이 넣었다. 역시 원문 문서를 보는 것을 습관 들여야 겠다. 🛟  
~~2주 넘게 안봤는데 음.. 나태해졌었다..⛈~~

## Trait

정확한 발음은 모르겠지만 트레이트 또는 트레잇 이라고 읽을 것 같다.  
트레잇은 공유하는 동작을 정의하는 것이다. 다른 프로그래밍 언어에서 인터페이스라고 불리는 것과 유사하지만 다른 점이 몇가지 있다.  
제너릭을 통해서 중복되는 코드를 줄일 수 있었던 것과 비슷하게 트레잇을 통해 다른 타입에 대해 같은 메소드를 호출할 수 있다면 이를 공유할 수 있다. 주관적으로 이러한 공유되는 동작을 한 곳에 선언할 수 있는 방법을 `트레잇`이라고 부르는 것 같다.

선언하는 방법은 아래와 같다. trait 키워드 이름을 사용하여 트레잇을 선언한다. 중괄호 안에서는 트레잇을 구현하는데 필요한 타입들을 표기하여 메소드 시그니처를 정의할 수 있다.  
이제 컴파일러는 컴파일 중 `Summarizable` 트레잇을 가지는 타입이 메소드 `summary` 를 동일한 시그니처로 정의하도록 강제할 것이다.

```rs
// summary/lib.rs

pub trait Summarizable {
  // 메소드 시그니처
  fn summary(&self) -> String;
}
```

### 특정 타입에 대한 Trait 구현

정의한 트레잇을 이제 사용해보자.

```rs
// summary/lib.rs

pub struct NewsArticle {
  pub headline: String,
  pub location: String,
  pub author: String,
  pub content: String,
}

impl Summarizable for NewsArticle {
  fn summary(&self) -> String {
    format!("{}, by {} ({})", self.headline, self.author, self.location)
  }
}

pub struct Tweet {
  pub username: String,
  pub content: String,
  pub reply: bool,
  pub retweet: bool,
}

impl Summarizable for Tweet {
  fn summary(&self) -> String {
    format!("{}: {}", self.username, self.content)
  }
}
```

```rs
// main.rs
use summary::NewsArticle;
use summary::Tweet;

fn main() {
  let tweet = Tweet {
    username: String::from("horse_ebooks"),
    content: String::from("of course, as you probably already know, people"),
    reply: false,
    retweet: false,
  };

  println!("1 new tweet: {}", tweet.summary());
}
```

위 두 예제중 위는 트레잇과 구조체가 정의된 모듈이고 아래는 메인 파일이다. 트레잇을 각 구조체 정의하는 방법은 예제와 같이 `impl ${트레잇 이름} for ${타입 이름}` 이다. 그 뒤 트레잇에서 정의한 메소드 시그니처를 내용을 작성한다.

> 💿 &nbsp;트레잇 구현 제약사항  
> 트레잇 또는 타입이 자신의 크레이트 내의 것일 경우만 구현할 수 있다.  
> 외부 트레잇을 외부 타입에 구현하는 것을 **불가능**하다.

### 기본(default) 트레잇 구현

기본적으로 동작할 구현하는 것이다. 타입에서 해당 기본 동작을 override 할 수 있다.

```rs
// summary/lib.rs

pub trait Summarizable {
  fn summary(&self) -> String {
    String::from("(Read more...)")
  }
}

impl Summarizable for NewsArticle {}
```

```rs
// main.rs

use summary::NewsArticle;

fn main() {
  let article = NewsArticle {
    headline: String::from("Penguins win the Stanley Cup Championship!"),
    location: String::from("Pittsburgh, PA, USA"),
    author: String::from("Iceburgh"),
    content: String::from("The Pittsburgh Penguins beat the Columbus Crew 1"),
  };

  println!("New article available! {}", article.summary());
}
```

위와 같이 기본 동작을 사용하고자 한다면 커스텀 동작을 구현할 필요가 없으므로 `impl ${트레잇 이름} for ${타입 이름}` 을 바로 닫아주면 된다.  
기본 구현은 해당 트레잇 내의 다른 메소드를 호출할 수 있는데 호출되는 메소드는 구본 구현을 가지고 있지 않아도 된다!  
당연한 이야기인 것 같기도 하지만 오버라이딩 된 구현으로부터 기본 구현은 호출할 수 없다.

```rs
// summary/lib.rs

pub trait Summarizable {
  fn author(&self) -> String;
  fn summary(&self) -> String {
    format!("(Read more from {}...)", self.author())
  }
}

impl Summarizable for Tweet {
  fn author(&self) -> String {
    format!("@{}", self.username)
  }
}
```

## 트레잇 바운드

트레잇은 매개변수로도 사용할 수 있다.

```rs
pub fn notify(item: &impl Summarizable) {
  println!("Breaking news! {}", item.summary());
}
```

하지만 두개 이상의 매개변수를 가지게 될 때 두 매개변수가 같은 유형을 가지도록 강제를 하거나 간결하게 표기하기 위해서 제너릭을 활용한 트레잇 바운드를 사용하면 된다.

```rs
pub fn notify<T: Summarizable>(item1: &T, item2: &T){}
```

### 여러 트레잇 지정

하나 이상의 트레잇을 지정할 수 있는데 `+` 구문을 사용하면 간단하다. 트레잇 바운드도 같은 동작을 한다.

```rs
pub fn notify(item: &(impl Summarizable + Display)) {}

// + 를 사용할 경우 아래와 같이 사용할 수 있다.
pub fn notify<T: Summarizabel + Display>(item: &T) {}
```

### where 절 사용하기

트레잇 바운드가 너무 많이 사용될 경우 가독성이 떨어지고 이러한 상황에서는 아래와 같이 `where` 절을 사용할 수 있다!

```rs
fn some_fn<T: Display + Clone, U: Clone + Debug>(t: &T, u: &U) -> i32 {}

// where 를 사용할 경우 아래와 같이 사용할 수 있다.
fn some_fn<T, U>(t: &T, u: &U) -> i32
  where T: Display Clone,
        U: Clone + Debug
{}
```

### Implement Trait 반환하기

정확히 뭐라고 지칭해야될지 몰라서 그냥 영어로 적었다. 아래 처럼 사용할 수 있다.

```rs
fn returns_summary() -> impl Summarizable {
  Tweet {
      username: String::from("horse_ebooks"),
      content: String::from(
        "of course, as you probably already know, people",
      ),
      reply: false,
      retweet: false,
  }
}

fn returns_summary2(switch: bool) -> impl Summarizable {
  if switch {
    NewsArticle {
      headline: String::from(
        "Penguins win the Stanley Cup Championship!",
      ),
      location: String::from("Pittsburgh, PA, USA"),
      author: String::from("Iceburgh"),
      content: String::from(
        "The Pittsburgh Penguins once again are the best \
        hockey team in the NHL.",
      ),
    }
  } else {
    Tweet {
      username: String::from("horse_ebooks"),
      content: String::from(
        "of course, as you probably already know, people",
      ),
      reply: false,
      retweet: false,
    }
  }
}
```

하지만 이와 같은 반환은 한 가지 타입만을 반환할 때 가능하고 위 예제에서 아래의 `returns_summary2` 와 같이 2개 이상의 타입이 조건에 따라 반환될 수 있는 경우에는 컴파일시 에러가 발생한다.  
이에 따르는 기능은 17장에서 언급한다고 하시니 일단 넘어가보자.

### 트레잇 바운드 조건 implement method 로 사용하기

이것도 정확히 명칭을 뭐라 할지 모르겠어서 영어로 적었다. 원문에서는 `Using Trait Bounds to Conditionally Implement Methods` 로 적혀 있다.  
아래의 예제를 보고 간단히 말하자면 조건으로 사용이 가능하다는 것으로 이해를 했다.

```rs
use std::fmt::Display;

struct Pair<T> {
  x: T,
  y: T
}

impl<T> Pair<T> {
  fn new(x: T, y: T) -> Self {
    Self { x, y}
  }
}

impl<T: Display + PartialOrd> Pair<T> {
  fn cmp_display(&self) {
    if self.x >= self.y {
      println!("x = {}", self.x);
    } else {
      println!("y -{}", self.y);
    }
  }
}
```

<hr/>

_개인이 참고하고자 작성한 글이며, 잘못된 정보가 있을 수 있습니다. 잘못된 정보는 메일로 보내주시면 감사하겠습니다._ 🙏
