---
title: "Rust - chapter 10-1 generic"
date: 2022-03-11
comments: true
read_time: false
toc: true
categories:
  - Rust
tags:
  - rust
  - generic
---

Rust 문서 챕터 10-1 제너릭을 읽고 정리한 글이다.([링크](https://rinthel.github.io/rust-lang-book-ko/ch10-01-syntax.htmll))

사실 이 문서를 보기로 했었을 때 금방 끝날 줄 알았는데 실제 실무에 쓰지 않고 이곳 저곳 기웃거리다 보니 속도가 안 나고 있는데 느리더라도 끝까지 읽고 다시 한 번 볼 수 있도록 노력해야겠다. 😓

## Generic

제너릭 다른 프로그래밍 언어에도 대부분 있는 개념으로 인자의 타입을 받아서 해당 타입을 활용할 수 있도록 하는 타입이다.  
간단하게 일반적으로 함수는 특정 타입만을 인자로 받게끔 작성되었었다. 제너릭 타입을 쓸 경우에는 인자가 정수로 들어오면 인자를 정수로, 문자로 들어오면 문자를 받을 수 있게 해준다.  
간단한 예제를 살펴보자.

#### 제너릭을 사용하지 않은 경우

```rs
fn largest_i32(list: &[i32]) -> i32 {
  let mut largest = list[0];

  for &item in list.iter() {
    if item > largest {
      largest = item;
    }
  }

  largest
}

fn largest_char(list: &[char]) -> char {
  let mut largest = list[0];

  for &item in list.iter() {
    if item > largest {
      largest = item;
    }
  }

  largest
}

fn main() {
  let numbers = vec![34, 50, 25, 100, 65];

  let result = largest_i32(&numbers);
  println!("The largest number is {}", result);
  assert_eq!(result, 100);

  let chars = vec!['y', 'm', 'a', 'q'];

  let result = largest_char(&chars);
  println!("The largest char is {}", result);
  assert_eq!(result, 'y');
}
```

위의 경우 사실 같은 기능을 하고 있는데 타입이 다르기에 2개의 함수를 작성한 경우다. 이 얼마나 불편한 일인가!! 그래서 사용하는게 **제너릭**이다.

#### 제너릭 사용

```rs
fn largest<T>(list: &[T]) -> T {
  let mut largest = list[0];

  for &item in list.iter() {
    // 타입따라 아래의 비교가 성립여부가 다르다.
    if item > largest {
      largest = item;
    }
  }

  largest;
}

fn main() {
  let numbers = vec![34, 50, 25, 100, 65];
  let result = largest(&numbers);
  println!("The largest number is {}", result);

  let chars = vec!['y', 'm', 'a', 'q'];
  let result = largest(&chars);
  println!("The largest char is {}", result);
}
```

위 예제를 보면 제네릭 `<T>` 을 사용하여 중복된 기능을 하나의 함수로 통합한 것을 볼 수 있다. 일반적으로 `T` 를 사용하는데 Type 의 약자이며 다른 문자를 입력하여도 된다. 관습적으로 대문자 하나를 사용하여 표기한다.  
그리고 사실 위 코드는 컴파일 시 에러가 난다. 주석에 작성하였지만 타입에 따라 저렇게 비교할 수 있는 경우가 있고 없기 때문이다.

## 사용법 기초

사실 다른 프로그래밍 언어의 제너릭과 동일하다고 봐도 무방할 것 같다. 그래도 일단 간단한 사용법에 대해서 살펴보자.

### 구조체 내에서 제너릭 사용

```rs
struct Point<T> {
  x: T,
  y: T,
}

fn main() {
  let integer = Point { x: 5, y: 10 };
  let integer_float = Point { x: 5, y: 4.0 };
}
```

짐작하시겠지만 위의 경우는 당연히 에러가 발생한다. 타입을 하나(정수)만 받았는데 받은 2개의 타입이 다르기 때문이다.  
그렇다면 타입을 하나 더 추가하면 된다!

```rs
struct Point<T, K> {
  x: T,
  y: K,
}
```

추가적으로 사실 우리는 이미 이전에 `Option`, `Result` 열거형에서 제너릭을 사용해왔다.

```rs
enum Option<T> {
  Some(T),
  None,
}

enum Result<T, E> {
  Ok(T),
  Err(E),
}
```

### 메소드 정의 내에서 제너릭 사용

🚧 &nbsp; impl 키워드 뒤에 제너릭 타입을 정의해야지만 메소드 구현 중에 사용할 수 있음을 주의하자.

```rs
struct Point<T, K> {
  x: T,
  y: K,
}

impl<T, K> Point<T, K> {
  fn mixup<U, V>(self, other: Point<U, V>) -> Point<T, V> {
    Point {
      x: self.x,
      y: other.y,
    }
  }
}

fn main() {
  let p1 = Point { x: 5, y: 10.4 };
  let p2 = Point { x: "Hello", y: 'c' };

  let p3 = p1.mixup(p2);

  println!("p3.x = {}, p3.y = {}", p3.x, p3.y);
  // p3.x = 5, p3.y = c
}
```

## 제너릭을 이용한 코드의 성능

제너릭을 사용할 경우 런타임에 타입이 정해져서 런타임 비용이 발생하는지 궁금할 수 있다. 과연 어떨까?  
놀랍게도 **런타임 비용이 발생하지 않는다.** 구체적인 타입을 명시하여 작성한 메소드를 사용한 것과 차이가 없다는 말이다.  
이는 러스트가 컴파일 타임에 제너릭을 사용하는 코드에 대해 `단형성화(monomorphization)`를 사용하여 구체적인 타입으로 된 특정 함수의 코드를 생성하기 때문이다.

<hr/>

_개인이 참고하고자 작성한 글이며, 잘못된 정보가 있을 수 있습니다. 잘못된 정보는 메일로 보내주시면 감사하겠습니다._ 🙏
