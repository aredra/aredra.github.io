---
title: "Rust - chapter 8-3(컬렉션-HashMap)"
date: 2022-03-01
comments: true
read_time: false
toc: true
categories:
  - Rust
tags:
  - rust
  - collection
  - hashmap
---

[공식 문서 - 번역글](https://rinthel.github.io/rust-lang-book-ko/ch08-03-hash-maps.html)의 8-3 챕터 정리

문서상 마지막으로 소개하는 일반적인 컬렉션은 `HashMap`이다. 마지막 장이기도 하고 현재 작성자가 졸린 상태라 날림 정리가 될 미래가 보인다. ☠️

## HashMap

`HashMap<K, V>` 타입으로 로 `K` 는 키, `V` 는 값이다. 이 두 값은 서로 매핑되어 저장되는데 이 매핑에는 해쉬 함수가 사용된다.  
해쉬맵은 특정한 키를 통해서 데이터를 찾을 때 유용하게 쓸 수 있으며, 해당 문서에서는 기본적인 부분만 설명하며, 자세한 내용은 표준 라이브러리를 볼 것을 강조하고 있다.

## HashMap 생성

새로운 해쉬맵을 여지껏 그랬듯 `new` 를 통해서 생성할 수 있고 `insert` 를 통해서 새로운 요소를 추가할 수 있다.

> 앞선 2개의 컬렉션은 `use` 로 가져올 필요가 없었지만 해쉬맵은 가져와야 함을 주의하자!

벡터와 마친가지로 해쉬맵도 데이터를 힙에 저장하는 방식을 가지며, 모든 키는 값은 타입이여야 하며, 값들도 같은 타입이여야 한다.

```rs
use std::collections::HashMap;

let mut scores = HashMap::new();

scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Yellow"), 50);
```

해쉬맵을 생성하는 또다른 방법이 있는데 튜플의 벡터에 대해 `collect` 메소드를 사용하는 방식이다. 예제를 보고 이런 방식도 있구나 머리 한 구석에 남겨놓자.  
`HashMap<_, _>` 과 같이 낯선 타입이 보이는데 이는 `collect` 가 많은 데이터 타입을 가질 수 있기에 어떠한 타입이 올지 모르는 상황에서 밑줄을 사용할 경우 벡터에 담긴 데이터의 타입에 기초하여 해쉬맵에 담길 타입을 추론할 수 있게 된다.

```rs
use std::collections::HashMap;

let teams  = vec![String::from("Blue"), String::from("Yellow")];
let initial_scores = vec![10, 50];

let scores: HashMap<_, _> = teams.iter().zip(initial_scores.iter()).collect();
```

### HashMap 소유권

해쉬맵의 키와 값에 넣은 데이터는 참조자를 넣지 않은 경우 해쉬맵이 소유권을 가져가게 된다.

```rs
use std::collections::HashMap;

let field_name = String::from("Favorite color");
let field_value = String::from("Blue");

let mut map = HashMap::new();
map.insert(field_name, field_value);
// field_name과 field_value은 이 지점부터 유효하지 않습니다.
```

### HashMap 값 접근

값에 대한 접근은 다른 언어들과 마찬가지로 `get` 을 통해 접근할 수 있다.  
반환값은 `Option<&V>` 이므로 없을 경우 `None` 이 반환되며, 순회도 가능하다.

```rs
use std::collections::HashMap;

let mut scores = HashMap::new();

scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Orange"), 430);

let team_name = String::from("Blue");
let score = scores.get(&team_name);

for (key, value) in socres {
  println!("{}: {}", key, value);
}
```

## HashMap 갱신하기

해쉬맵은 하나의 키에 한가지의 값을 가질 수 있다. 값에 대한 갱신에 대해 알아 보자.

1. 같은 키 값에 insert 할 경우 마지막 값이 남는다.
2. `entry API`를 통해 없는 경우에만 삽입하기
3. 기존값을 이용하여 갱신하기

```rs
// 1.
use std::collections::HashMap;

let mut scores = HashMap::new();

scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Blue"), 25);

println!("{:?}", scores);
// {"Blue": 25}

// 2.
scores.entry(String::from("Yellow")).or_insert(50);
scores.entry(String::from("Blue")).or_insert(50);

println!("{:?}", scores);
// {"Blue": 25, "Yellow": 50}

// 3.
let score = scores.entry(String::from("Blue")).or_insert(50);
*score += 10;
println!("{:?}", scores);
// {"Blue": 35, "Yellow": 50}
```

## 해쉬함수

문서 내용 그대로 복사한 내용이다.

> 💿 &nbsp;기본적으로, HashMap은 서비스 거부 공격(Denial of Service(DoS) attack)에 저항 기능을 제공할 수 있는 암호학적으로 보안되는 해쉬 함수를 사용합니다. 이는 사용 가능한 가장 빠른 해쉬 알고리즘은 아니지만, 성능을 떨어트리면서 더 나은 보안을 취하는 거래는 가치가 있습니다. 만일 여러분이 여러분의 코드를 프로파일하여 기본 해쉬 함수가 여러분의 목표에 관해서는 너무 느리다면, 다른 해쉬어(hasher) 를 특정하여 다른 함수로 바꿀 수 있습니다. 해쉬어는 BuildHasher 트레잇을 구현한 타입을 말합니다. 트레잇과 이를 어떻게 구현하는지에 대해서는 10장에서 다룰 것입니다. 여러분의 해쉬어를 바닥부터 새로 구현해야할 필요는 없습니다; crates.io에서는 많은 수의 범용적인 해쉬 알고리즘을 구현한 해쉬어를 제공하는 공유 라이브러리를 제공합니다.

<hr/>

_개인이 참고하고자 작성한 글이며, 잘못된 정보가 있을 수 있습니다. 잘못된 정보는 메일로 보내주시면 감사하겠습니다._ 🙏
