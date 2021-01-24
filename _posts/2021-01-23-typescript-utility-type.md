---
title: "TypeScript utility types"
date: 2021-01-23
comments: true
read_time: false
categories:
  - TypeScript
tags:
  - utility types
---

TypeScript는 일반적인 유형변환을 용이하게 하는 기본적인 유틸리티 유형들을 제공하고 있다.  
자세한 내용은 [타입스크립트 공식 유틸리티 타입 안내](https://www.typescriptlang.org/docs/handbook/utility-types.html) 를 참고하면 도움이 될 것 같으며, 기본적인 타입 몇가지를 소개한다.

## Partial 타입

정의된 타입의 각 속성들을 전부 optional하게 바꿔주는 타입니다.

```ts
/**
 * Make all properties in T optional
 */
type Partial<T> = {
  [P in keyof T]?: T[P];
};

interface Product {
  id: number;
  name: string;
  price: number;
  brand: string;
  stock: number;
}

type partialProduct = Partial<Product>;
```

## Pick 타입

정의된 타입에서 일부 속성들만 선택하여 타입을 정의합니다.

```ts
/**
 * From T, pick a set of properties whose keys are in the union K
 */
type Pick<T, K extends keyof T> = {
  [P in K]: T[P];
};

interface Product {
  id: number;
  name: string;
  price: number;
  brand: string;
  stock: number;
}

type pickProduct = Pick<Product, "id" | "name">;
```

## Omit 타입

정의된 타입에서 일부 속성을 제거하여 타입을 정의합니다.

```ts
/**
 * Construct a type with the properties of T except for those in type K.
 */
type Omit<T, K extends keyof any> = Pick<T, Exclude<keyof T, K>>;

interface Product {
  id: number;
  name: string;
  price: number;
  brand: string;
  stock: number;
}

type omitProduct = Omit<Product, "id" | "name">;
```
