## ts

- ts를 통해 js의 **자유도를 잃는 대신**, 코드의 에러들을 미연에 방지할 수 있어 **코드의 안정성**을 얻을 수 있다
- typescript가 명시적 타입 없이도 타입을 적절하게 추론했다면 그대로 냅두어야한다
- 타입은 최대한 좁게 만들어야한다
- 런타임이 돼야 발견할 수 있는 에러를 런타임 이전에 알려준다 👍

## 시작하기

```
$ npm init -y
$ npm i typescript (tsc 설치: typescript compiler)
$ npx tsc --init (tsconfig.json 생성)
```

## tsconfig.json 설정

```js
// js + ts 같이 쓸 수 있게 해준다
// Allow JavaScript files to be a part of your program.
"allowJs": true

// 무조건 strict는 true로 설정해야한다
// false로 설정하면 typescript를 쓰는 이유가 없어진다
// Enable all strict type-checking options.
"strict": true

// ts코드를 js의 es2016버전으로 컴파일 해준다
// Set the JavaScript language version for emitted JavaScript and include compatible library declarations.
"target": "es2016"

// 모듈 시스템을 설정해준다
// Specify what module code is generated.
"module": "CommonJS"

// window에선 import 해올 때 대소문자 구분을 안해도 불러와지고
// linux, mac에선 대소문자를 구분해줘야 불러와진다
// 이런 차이에 의한 에러를 방지하기위해 window에서도 대소문자를 구분해야 불러올 수 있게 강제해준다
// Ensure that casing is correct in imports.
"forceConsistentCasingInFileNames": true

// library checking을 건너뛰는 설정
// 패키지를 설치하면 패키지마다 타입을 설정해둔 .d.ts 라는 파일이 있는데
// 수많은 .d.ts 파일을 검사하면 느려지기때문에 skip해두는게 좋다
// Skip type checking all .d.ts files.
"skipLibCheck": true

// 빈 배열같은거 any[] -> never[]로 바꿔준다
// Enable error reporting for expressions and declarations with an implied 'any' type.
"noImplicitAny": false
```

## 명령어

```js
let foo: string = 'danpoj'
foo = 1997
```

```
// 타입 잘못 작성한거 있는지 check
$ npx tsc --noEmit

// 실행 결과
first.ts:2:1 - error TS2322: Type 'number' is not assignable to type 'string'.
2 foo = 1997
  ~~~
Found 1 error in first.ts:2

// ts -> js로 컴파일
// ts파일에 type에러가 있어도 컴파일은 됨
// tsc는 타입체크 <-> 컴파일이 별개로 동작하기 때문이다
$ npx tsc (ts파일 컴파일 후 js파일 생성)
```

## 명칭

- `|` : union
- `&` : intersection
- `type User = ...` : type alias
- `const add: (a: number, b: number) => number` : call signature
- `하나의 함수에 여러개의 call signature`: overloading (**매개변수의 개수, 타입** 또는 **리턴값**이 다르면서 함수 이름이 같은 것)
- `polymorphism`

## 네이밍 규칙

1️⃣

```
interface IUesr
type TUser
enum EUser
```

2️⃣

```
interface Uesr
type User
enum User
```

둘 다 상관없다

## 문법

const로 만들어 진 원시 값 5는 바뀔 일이 없어 typescript는 자동으로 foo를 숫자 5 타입으로 추론한다

```js
const foo = 5

// const foo: 5 👍
```

`number` 타입으로 지정해주는 아래 코드는 숫자 `5` -> `number` 로 타입 범위를 오히려 넓히기 때문에 typescript가 타입을 적절하게 추론했다면 따로 명시적 타입을 추가하지 말아야한다

```js
const foo: number = 5

// const foo: number ❌
```

<br/><hr/><br/>

`!`는 `null | undefined`가 아님을 나타낸다  
`null | undefined`가 아님이 확실하다해도 쓰지 않는걸 권장한다  
`id='root'`를 `id='root-container'` 처럼 실수로 바꾸는 경우가 있기 때문이다

```js
// const head: Element | null
const root = document.querySelector('#root')

// const head: Element
const root = document.querySelector('#root')!
```

아래와 같이 쓰면 안전하게 쓸 수 있다

```js
// const head: Element | null 👍
const root = document.querySelector('#root')
if (root) {
  root.innerHTML = 'hello world!'
}
```

<br/><hr/><br/>

> `?` optional : `age` property가 있어도 되고 없어도 된다

```js
type User = {
  name: string
  age?: number // age property is optional
}

const danpoj: User = { // 👍
  name: 'danpoj',
  age: 26,
}

const kanaba: User = { // 👍
  name: 'kanaba',
}
```

```
type User = {
    name: string;
    age?: number | undefined;
}
```

```js
type User = {
  name: string
  age?: number
}

const danpoj: User = {
  name: 'danpoj',
  age: 26,
}

// ERROR: 'user.age' is possibly 'undefined'.
if (danpoj.age > 20) {
  console.log('adult')
}

// 👍
if (danpoj.age && danpoj.age > 20) {
  console.log('adult')
}
```

<br/><hr/><br/>

값의 수정을 막고싶으면 `readonly`를 추가하면된다

```js
type User = {
  readonly name: string
  readonly age?: number
}

const danpoj: User = {
  name: 'danpoj',
  age: 26,
}

// ERROR : Cannot assign to 'name' because it is a read-only property.
danpoj.name = 'kanaba'
```

```js
const ages: readonly number[] = [10, 26, 55, 4]

// ERROR : Property 'push' does not exist on type 'readonly number[]'.
ages.push(5)
```

<br/><hr/><br/>

`tuple` 타입을 지정해줘도 `push`는 막지 못한다

```js
const user: [string, number] = ['danpoj', 26]

// error
user[2] = 'welcome!'

// no error
tuple.push('welcome!')
```

<br/><hr/><br/>

당연한 얘기지만, js로 컴파일 후, ts 문법들은 모두 코드에서 사라진다

```js
// 컴파일 후 사라짐
const enum EDirection {
  Up,
  Down,
  Left,
  Right,
}

// 컴파일 후 as const 부분만 사라짐
const IDirection = {
  Up: 0,
  Down: 1,
  Left: 2,
  Right: 3,
} as const
```

<br/><hr/><br/>

`&`는 타입을 만들 때 **상속** 받는 식으로 사용가능하다

```js
type User = { name: string, age: number }
type Admin = User & { isRoot: true } // Admin은 User를 상속받았다고 이해하자

const danpojInfo: Admin = {
  name: 'danpoj',
  age: 26,
  isRoot: true,
}
```

위의 상속 개념을 아래처럼 `interface`로 똑같이 구현 가능하다

- `type`로 object의 틀을 만들어 줄 수도 있고 `type alias -> type Color = 'red' | 'blue' | 'red'`를 만들수도있다
- `interface`는 object의 틀을 만들어주는 역할을 한다

```js
interface User {
  name: string
  age: number
}

interface Admin extends User {
  isRoot: true
}

const danpojInfo: Admin = {
  name: 'danpoj',
  age: 26,
  isRoot: true,
}
```

<br/><hr/><br/>

`interface`는 중복 선언을 하면 안의 내용들을 합쳐준다  
이러한 확장성 때문에 보통 객체를 정의할 땐 `type`대신 `interface`로 타입을 지정한다  
(라이브러리 사용자가 직접 `interface` 수정을 쉽게 할 수 있다)

```js
interface Person {
  talk: () => void;
}

interface Person {
  sleep: () => void;
}

interface Person {
  sit: () => void;
}

const person: Person = {
    talk() {...}
    sleep() {...}
    sit() {...}
}
```

<br/><hr/><br/>

**잉여 속성 검사**

```js
interface User {
  name: string;
}

// ERROR - age는 User interface에 없어서 잉여 속성 검사에 의해 에러 발생
const uesr1: User = { name: 'danpoj', age: 26 }

// no ERROR - 따로 빼주면 잉여 속성 검사 X
const user = { name: 'danpoj', age: 26 }
const user2: User = user
```

<br/><hr/><br/>

> (ts의 장점 ⭐️) 런타임이 돼야 발견할 수 있는 에러를 런타임 이전에 알려준다

```js
const user = { name: 'danpoj' }

user.hello()
```

- js에선 위의 코드가 런타임 이전에는 에러가 나는지 알 수 없다
- ts를 사용하면 아래와 같이 런타임 이전에 에러를 잡을 수 있다
- 런타임이 돼서야 에러를 발견할 수 있다 === 런타임 에러를 보게되는건 실제 유저가 된다... 😅

```
Property 'hello' does not exist on type '{ name: string; age: number; }'.ts(2339)
```

<br/><hr/><br/>

`any` type 지정은 ts의 타입 보호장치를 빠져나오는 행위이므로 최대한 `any`가 없게 만들어야한다!  
`any`를 어쩔 수 없이 사용해야 하는 상황에서만 써야하고 최대한 지양해야한다

<br/><hr/><br/>

`foo` 변수에는 API를 통해 받아온 데이터가 담겨있다고 가정해보자  
만약 API 데이터들의 type을 모르는 경우에는 `unknown`을 써주면된다

```js
let foo: unknown

// ERROR: 'foo' is of type 'unknown'.
let bar = foo + 3

// ERROR: 'foo' is of type 'unknown'.
let baz = foo.toLowerCase()
```

```js
let foo: unknown

// unknown인 경우, 아래와 같이 타입 확인 작업해주면 된다
if (typeof foo === 'number') {
  let bar = foo + 3
}

if (typeof foo === 'string') {
  let baz = foo.toLowerCase()
}
```

<br/><hr/><br/>

`void`는 function의 return이 없을 경우에 사용한다  
return값이 `void`인 function에서 `return '123'` 같이 사용하면 에러 발생

그 이외에 `void`가 사용되는 두가지 경우

- 매개변수가 `void`를 return하는 함수인 경우
- method가 `void`를 return하는 함수인 경우

위 두 경우에서의 `void`는 return값을 사용하지 않겠다 라는 뜻으로 사용 돼 `return '123'`등과 같이 써도 에러가 발생하진 않는다

**정리**

- function의 `void`는 _리턴값이 없다는 뜻_
- 매개변수의 `void`는 _리턴값을 쓰지 않는다는 뜻_
- method의 `void`는 _리턴값을 쓰지 않는다는 뜻_

<br/><hr/><br/>

### generic - polymorphism

아래와 같이 비슷한 여러개의 call signatures가 필요한 경우, 모든 경우의 타입을 만들지말고 `generic`을 사용하면된다

```js
type Print = {
  (arr: number[]): void
  (arr: string[]): void
  // (arr: boolean[]): void
  // ...
}

const _print: Print = (arr) => {
  for (let item of arr) {
    console.log(item)
  }
}

const arr1 = [10, 20, 30, 40]
const arr2 = ['a', 'b', 'c', 'd', 'e']
const arr3 = [true, true, false, false, false]

_print(arr1)
_print(arr2)

// boolean[] 매개변수를 받는 call signature는 없음
// No overload matches this call.
_print(arr3)

```

### generic 예시 1

```js
// generic
const _print = <T>(arr: T[]) => {
  for (let item of arr) {
    console.log(item)
  }
}

const arr1 = [10, 20, 30, 40]
const arr2 = ['a', 'b', 'c', 'd', 'e']
const arr3 = [true, true, false, false, false]
const arr4 = ['a', 1, true]

// const _print: <number>(arr: number[]) => void
_print(arr1)

// const _print: <string>(arr: string[]) => void
_print(arr2)

// const _print: <boolean>(arr: boolean[]) => void
_print(arr3)

// const _print: <string | number | boolean>(arr: (string | number | boolean)[]) => void
_print(arr4)
```

### generic 예시 2

```js
type Player<T> = {
  name: string
  info: T
}

const player: Player<{ age: number }> = {
  name: 'danpoj',
  info: {
    age: 26,
  },
}
```

<br/><hr/><br/>

### typescript OOP 예시

#### TS

```js
// 추상 클래스 (인스턴스생성 불가능, 상속만 시켜줄 수 있음)
// private, protected, public으로 접근 범위를 제한한다
abstract class User {
  constructor(protected name: string, protected age: number) {}

  // 추상 메소드 (call signature만 정해주고, 상속받은 클래스에서 함수를 구현해주면 된다)
  abstract whoAreYou(): void
}

class CurrentUser extends User {
  whoAreYou() {
    console.log(`I'm ${this.name}, ${this.age}years old.`)
  }
}

const user = new CurrentUser('danpoj', 26)

user.whoAreYou()

```

`$ npx tsc` 하여 js로 컴파일 하면

#### JS

```js
'use strict'
class User {
  constructor(name, age) {
    this.name = name
    this.age = age
  }
}
class CurrentUser extends User {
  whoAreYou() {
    console.log(`I'm ${this.name}, ${this.age}years old.`)
  }
}
const user = new CurrentUser('danpoj', 26)
user.whoAreYou()
```

## references

[typescript docs: handbook](https://www.typescriptlang.org/docs/handbook/intro.html)

[typescript docs: what's new](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-4-9.html)

[typescript로 블록체인 만들기](https://nomadcoders.co/typescript-for-beginners/lobby)

[타입스크립트 올인원: Part1](https://www.inflearn.com/course/%ED%83%80%EC%9E%85%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EC%98%AC%EC%9D%B8%EC%9B%90-1)
