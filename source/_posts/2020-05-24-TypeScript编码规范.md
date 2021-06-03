---
title: TypeScript编码规范
author: 刘莹鑫
authorLink: https://github.com/lyx383982759
excerpt: 本文规定了开发团队在开发TypeScript项目时应遵循的编码规范
cover: 2020/05/24/TypeScript编码规范/cover.jpg
thumbnail: 2020/05/24/TypeScript编码规范/cover.jpg
categories:
  - - TypeScript
    - 编码规范
tags:
  - TypeScript
toc: true
date: 2020-05-24 18:11:11
---

# 1 命名及约定

## 1.1 类

使用 PascalCase 进行命名。

**_Bad_**

```typescript
class foo {}
```

**_Good_**

```typescript
class Foo {}
```

## 1.2 类成员（变量、方法）

使用 camelCase 进行命名。

**_Bad_**

```typescript
class Foo {
  Bar: number;
  Baz(): number {}
}
```

**_Good_**

```typescript
class Foo {
  bar: number;
  baz(): number {}
}
```

## 1.3 接口

使用 PascalCase 进行命名，不要在接口名前加“I”。

接口成员使用 camelCase 进行命名。

**_Bad_**

```typescript
interface IFoo {
  Bar: number;
  Baz(): number;
}
```

**_Good_**

```typescript
interface Foo {
  bar: number;
  baz(): number;
}
```

## 1.4 命名空间

使用 PascalCase 进行命名。

**_Bad_**

```typescript
namespace foo {}
```

**_Good_**

```typescript
namespace Foo {}
```

## 1.5 枚举

- 使用 PascalCase 进行命名。

  **_Bad_**

  ```typescript
  enum color {}
  ```

  **_Good_**

  ```typescript
  enum Color {}
  ```

- 枚举成员使用 PascalCase 进行命名。

  **_Bad_**

  ```typescript
  enum Color {
    red,
  }
  ```

  **_Good_**

  ```typescript
  enum Color {
    Red,
  }
  ```

## 1.6 文件名

- 使用破折号分隔描述性单词，比如：hero-list.ts。
- 使用点将描述性名称与类型分开，比如：user-info.page.ts。
- 尽量使用常规的几种类型名，包括.page,.service,.component,.pipe,.module,.directive,.controller 和.middleware。当然也可以自己创建其他类型，但不宜太多。
- 类名与文件名匹配，并遵循类命名规范。

| 类名                                | 文件名                  |
| :---------------------------------- | ----------------------- |
| export class AppComponent { }       | app.component.ts        |
| export class HeroListComponent { }  | hero-list.component.ts  |
| export class UserProfileService { } | user-profile.service.ts |

# 2 类型

- 需显式地为变量、数组和方法编写类型（类型推论能够推断出类型的不需要声明类型）。

  **_Bad_**

  ```typescript
  class Bar {
    bar(input) {
      let isZero;
      const foo: number = 5;
      if (input === 5) {
        isZero = false;
      }
      const resultObject = {
        fo: foo,
        isZeroRes: isZero,
      };
      return resultObject;
    }
  }
  ```

  **_Good_**

  ```typescript
  class Bar {
    bar(input: number): BarResult {
      let isZero: boolean;
      const foo = 5;
      if (input === 0) {
        isZero = true;
      }
      const resultObject = {
        fo: foo,
        isZeroRes: isZero,
      };
      return resultObject;
    }
  }

  interface BarResult {
    fo: number;
    isZeroRes: boolean;
  }
  ```

- 不要使用 Number、String、Boolean、Object 为变量、数组和方法设置类型。

  **_Bad_**

  ```typescript
  baz(foo: String): String {

  }
  ```

  **_Good_**

  ```typescript
  baz(foo: string): string {

  }
  ```

# 3 声明变量

- 如果变量在其生命周期可能发生改变，尽量使用 let。

- 如果一个值在程序生命周期内不会改变，尽量使用 const。

  **_Bad_**

  ```typescript
  var bar = "bar";
  var count;
  if (true) {
    console.log(bar);
    count += 1;
  }
  ```

  **_Good_**

  ```typescript
  const bar = "bar";
  let count: number;
  if (true) {
    console.log(bar);
    count += 1;
  }
  ```

# 4 对象

- 使用{}进行对象创建。

  **_Bad_**

  ```typescript
  const  item  =  new  Object（）;
  ```

  **_Good_**

  ```typescript
  const item = {};
  ```

- 在对象字面量里使用属性简写。

  **_Bad_**

  ```typescript
  const lukeSkywalker = "Luke Skywalker";
  const obj = {
    lukeSkywalker: lukeSkywalker,
  };
  ```

  **_Good_**

  ```typescript
  const lukeSkywalker = "Luke Skywalker";
  const obj = {
    lukeSkywalker,
  };
  ```

- 仅使用引号用于属于无效标识符的属性。

  **_Bad_**

  ```typescript
  const bad = {
    foo: 3,
    bar: 4,
    "data-blah": 5,
  };
  ```

  **_Good_**

  ```typescript
  const good = {
    foo: 3,
    bar: 4,
    "data-blah": 5,
  };
  ```

# 5 字符串

- 使用单引号声明字符串。

  **_Bad_**

  ```typescript
  const bar = "bar";
  ```

  **_Good_**

  ```typescript
  const bar = 'bar'；
  ```

# 6 解构

- 访问和使用对象的多个属性时，使用对象解构。

  **_Bad_**

  ```typescript
  const foo = user.firstName;
  const bar = user.lastName;
  ```

  **_Good_**

  ```typescript
  const { foo, bar } = user;
  ```

- 访问数组中的多个数据时，使用解构。

  **_Bad_**

  ```typescript
  const arr = [1, 2, 3, 4];
  const first = arr[0];
  const second = arr[1];
  ```

  **_Good_**

  ```typescript
  const arr = [1, 2, 3, 4];
  const [first, second] = arr;
  ```

# 7 空格

- 在定义类型前面加上空格。
- 赋值等号两边加上空格。
- 方法、类大括号前空格。
- 对象冒号后空格。

**_Bad_**

```typescript
class Foo {
  openDetail(item: string): void {
    let foo: string;
    foo = "";
    const foa = {
      name: "foo",
    };
    console.log(item);
  }
}
```

**_Good_**

```typescript
class Foo {
  openDetail(item: string): void {
    let foo: string;
    foo = "";
    const foa = {
      name: "foo",
    };
    console.log(item);
  }
}
```

# 8 缩进

使用两个空格缩进。

# 9 分号

语句结尾添加分号。

**_Bad_**

```typescript
const foo = "foo";
```

**_Good_**

```typescript
const foo = "foo";
```

# 10 数组

- 使用[]定义数组。

  **_Bad_**

  ```typescript
  let foos: Array<Foo>;
  ```

  **_Good_**

  ```typescript
  let foos: Foo[];
  ```

- 使用 push 添加数据

  **_Bad_**

  ```typescript
  const foos = [];
  foos[foos.length] = "abracadabra";
  ```

  **_Good_**

  ```typescript
  const foos = [];
  foos.push("abracadabra");
  ```
