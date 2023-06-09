## 일급 객체

- 자바스크립트의 함수는 값처럼 변수에 할당할 수도 있고 프로퍼티 값이 될 수도 있으며 배열의 요소가 될 수도 있다. 이처럼 값의 성질을 갖는 객체를 **일급 객체**라 한다. **자바스크립트의 함수는 일급 객체다.**. 함수가 일급 객체라는 것은, 함수를 값처럼 자유롭게 사용할 수 있다는 것이다.

## 함수 선언문, 함수 표현식, 화살표 함수, 호이스팅

- 함수 선언문으로 정의한 함수는 함수 선언문 이전에 호출할 수 있다. 이를 호이스팅이라 하며, 호이스팅이란 코드가 한 줄씩 실행되는 런타임 이전에 자바스크립트 엔진에 의해 먼저 실행되는 것을 뜻한다. 즉, 함수 선언문은 런타임 이전에 자바스크립트 엔진에 의해 미리 실행되어 함수 객체를 가지고있는 식별자를 생성하게된다.

- 함수 표현식은 런타임에 평가되어 함수 객체가 된다. 함수 표현식으로 정의된 함수는 함수 호이스팅이 아닌, 변수 호이스팅이 발생한다. 즉, 함수 할당문 이전에 함수 표현식의 식별자를 참조하게 되면 `undefined`이고, 런타임에서 함수 할당문에 도달해야 함수 객체가 식별자에 할당되어 호출 가능하다.

- ES6에서 추가된 화살표 함수는 함수 선언문과 함수 표현식을 완전히 대체하기 위해 디자인된 거슨 아니다. 생성자 함수로 사용할 수 없으며, this의 바인딩 방식이 다르고, prototype 프로퍼티가 없으며, arguments를 생성하지 않는다.

## 함수의 매개변수

- 매개변수가 많아지면 함수의 사용법을 어렵게 만들고 실수를 발생시킬 가능성을 높인다. 매개변수는 최대한 적게 만드는 것이 권장되며, 많은 매개변수가 필요하다면 아래 예시와 같이 `하나의 객체 매개변수`를 받는것이 좋다. 객체의 key값을 통해 어떤 인수를 받는지 명확히 알 수 있고, 매게변수의 순서를 신경쓰지 않아도 되어 코드의 가독성은 높아지고 실수를 줄일 수 있는 효과가 있다.

- 함수의 매개변수를 통해 다른 함수의 내부로 전달되는 함수를 **콜백 함수**라고 하며, 매개 변수를 통해 함수의 외부에서 콜백 함수를 전달받은 함수를 **고차 함수**라고 한다.

```js
const result = postUser({
  method: "POST",
  url: "/user",
  data: { nmae: "danpoj", age: 27 },
  cache: false,
});
```

> ⚠️ 매개변수로 받은 객체는 pass by reference이기 때문에 함수 내부에서 외부의 객체 값을 변경하는 side effect가 발생할 수 있다. 비순수 함수가 되는 것이다. 객체를 deep copy 하여 새로운 객체 복사본을 만들어낸 후 복사본을 함수의 인수로 전달하는 해결법이 있다.

## javascript runtime

- 비동기 처리란 특정 코드의 실행이 끝날 때까지 코드의 실행을 멈추지 않고 다른 코드를 먼저 실행해주는 것이다. (non blocking)
- javascript engine은 싱글 스레드이다. 즉, 하나의 call stack을 가지고있다. 근데 어떻게 비동기적으로 동작할수있을까?
- javascript engine은 memory heap과 call stack으로 구성된다. 그리고 엔진 밖에서 자바스크립트 실행에 관여하는 요소인 web API와 task Queue가 존재한다.
- DOM event, Ajax, setTimeout등의 비동기 작업들은 cll stack에서 순차적으로 실행되지않고 background로 작업을 넘기게된다.
- background에서 비동기 작업이 완료되면 task Queue(microtask Queue + 매크로task Queue)에 순차적으로 핸들러가 쌓이게 되고, 자바스크립트 런타임 내내 동작하는 이벤트 루프에 의해 call stack에 작업이 없는 시점에 task queue에 쌓인 작업들을 하나씩 call stack에 올려 실행시켜준다.

- [event loop visualizer website](http://latentflip.com/loupe/?code=JC5vbignYnV0dG9uJywgJ2NsaWNrJywgZnVuY3Rpb24gb25DbGljaygpIHsKICAgIHNldFRpbWVvdXQoZnVuY3Rpb24gdGltZXIoKSB7CiAgICAgICAgY29uc29sZS5sb2coJ1lvdSBjbGlja2VkIHRoZSBidXR0b24hJyk7ICAgIAogICAgfSwgMjAwMCk7Cn0pOwoKY29uc29sZS5sb2coIkhpISIpOwoKc2V0VGltZW91dChmdW5jdGlvbiB0aW1lb3V0KCkgewogICAgY29uc29sZS5sb2coIkNsaWNrIHRoZSBidXR0b24hIik7Cn0sIDUwMDApOwoKY29uc29sZS5sb2coIldlbGNvbWUgdG8gbG91cGUuIik7!!!PGJ1dHRvbj5DbGljayBtZSE8L2J1dHRvbj4%3D)

## 클로저 / 스코프

- 외부 변수를 기억하고 이 외부 변수에 접근할 수 있는 함수
- 스코프: 유효범위
- 렉시컬 스코프: 함수를 어디서 선언했는지에 따라 결정된다. 함수를 선언한 시점에 상위 스코프가 결정된다.

```js
function makeCounter() {
  let count = 0;

  // 함수 선언 시점에 상위 스코프들이 결정된다.
  // 함수에는 [[ Envirnment ]] 라 불리는 숨김 프로퍼티가 있는데, 여기에 함수가 만들어진 곳의 렉시컬 환경에 대한 참조가 저장된다.
  return function () {
    return count++;
  };
}

let counter = makeCounter();

// counter 함수는 외부 함수 렉시컬 환경에 대한 정보를 가지고있으므로 외부 함수의 변수인 count 값은 도달 가능한 값이된다. 따라서 count는 garbage collecting의 대상이 되지 않는다. 메모리에 계속 유지된다.
console.log(counter()); // 0
console.log(counter()); // 1
console.log(counter()); // 2
```

## promise

- 결과값을 나중에 원하는 시점에 꺼내쓸 수 있는 유연함이 있다.
- 코드를 분리 할 수 있다는 장점.
- ⚠️ callback은 결과값을 바로 받아야 한다는 단점이 있다. 유연하지않음.

```js
let promise = new Promise((resolve, reject) => {
  // excutor (실행 함수)
  // 프로미스가 만들어지면 실행함수가 즉각적으로 실행된다.

  setTimeout(() => {
    resolve("resolved.");
  }, 1000);
});

// .then의 첫번째 인자는 resolve됐을 때, 두번째 인자는 reject됐을 때 실행된다.
// .then의 두번째 인자 대신 .catch를 주로 사용한다.
// .then의 두번째 인자에서 받은 reject는 첫번째 인자에서 받은 resolve에서의 에러를 잡지못한다.
promise.then(console.log, console.log);
```

- 처음 프로미스가 생성될때는 `state: pending / result: undefined`
- resolve(res)된 경우에는 `state: fulfilled / result: res`
- rejecte(error)된 경우에는 `state: rejected  reult: error`
- state와 result값은 내부 프로퍼티 이므로 직접 접근이 불가능하다. `.then.catch.finally`를 통해 접근 가능하다.
- promise 체이닝 각각은 promise를 반환한다.

```js
// 에러 핸들링
// 에러가 발생하면 제어 흐름이 가장 가까운 에러 핸들러로 넘어간다.
new Promise((resolve, reject) => {
  throw new Error("에러 발생!");
})
  .catch(function (error) {
    console.log("에러가 잘 처리되었습니다. 정상적으로 실행이 이어집니다.");
  })
  .then(() => console.log("다음 핸들러가 실행됩니다."));

// 에러가 잘 처리되었습니다. 정상적으로 실행이 이어집니다.
// 다음 핸들러가 실행됩니다.
```

> 처리되지 못한 에러가 발생하지않게 에러 핸들링을 잘하자!
> 브라우저 환경에선 처리하지 못한 에러를 `unhandledrejection`이벤트에서 처리 가능하다.

- `Promise.all`: promise들을 배열 형태로 받아 모든 promise가 reslove돼야 결과를 반환한다. 하나라도 reject된 경우 모든 promise는 무시된다.
- `Promise.allSettled`: promise들을 배열 형태로 받아 resolve된 경우 `{status: fullfilled, value: result}` reject된 경우 `{status: rejected, reason: error}`가 반환되며, 응답에 성공한 promise와 실패한 promise를 다르게 처리 가능하다.
- `Promise.race`: 가장 먼저 처리된 promise의 결과(resolve or reject)만 반환한다.
- async로 감싸진 함수는 Promise를 반환한다.
- await를 남용하지말자. 여러 데이터를 서버에서 받아오는 경우, 데이터 간의 연관 관계가 없다면 `Promise.all`로 묶어서 동시에 처리하자.

[js info - promise](https://ko.javascript.info/promise-error-handling)

## this

- ✅ **this는 함수가 호출될 때 결정된다.**
- ✅ **화살표 함수는 부모 함수의 this를 물려받는다.**
- `call apply bind`로 this를 바꿀 수 있다.

```js
const obj = {
  name: "danpoj",
  sayName() {
    console.log(this.name); // 함수를 어떻게 호출했는지 보자. obj.sayName()으로 호출했으므로 this는 obj를 가리킨다.
    function inner() {
      console.log(this.name); // 이것또한 함수를 호출했을때를 보자. inner()로 호출했으므로 this는 window를 가리킨다.
    }
    inner();
  },
};

obj.sayName();
// danpoj
// undefined (inner에서의 window.name)
```

```js
const obj = {
  name: "danpoj",
  sayName() {
    console.log(this.name); // 함수를 어떻게 호출했는지 보자. obj.sayName()으로 호출했으므로 this는 obj를 가리킨다.
    const inner = () => {
      console.log(this.name); // 함수 정의부만 봐서는 뭐를 호출할지 모른다.
      // 화살표함수는 부모 함수의 this를 물려받는다.
      // 부모 함수는 sayName이고 sayName의 this는 뭘까?
      // this가 가리키는것을 알고싶으면 함수의 호출 부분을 봐야한다!!
      // obj.sayName()으로 sayName 함수를 호출했으므로 this는 obj이다.
      // inner 화살표함수는 sayName의 this를 물려받으므로 inner의 this는 똑같이 obj가 되는 것이다.
    };
    inner();
  },
};

obj.sayName();
// danpoj
// danpoj
```

## this가 결정되는 방법

- this는 함수가 **호출** 되는 시점에 결정된다! (중요)
- this가 가리키는 값을 알고싶으면 함수의 호출부를 봐보자.
- 화살표 함수의 경우 외부의 this를 물려받는다.
- `call apply bind` `생성자함수의 this` `객체 의 메소드`등에 의해 this의 값은 호출시점에 달라진다.

## 실행 컨텍스트

- 실행할 코드에 제공할 환경 정보들을 모아놓은 객체

1. 호이스팅 발생
2. 식별자, 상위 스코프들을 가르키는 외부 참조
3. this값을 설정

- 실행 컨텍스트가 언제 생기는가?

1. 전역공간은 자동으로 컨텍스트 구성
2. 함수 실행
3. eval()함수 실행
4. block 만든경우 (ES6+)

- 자바스크립트의 함수는 **일급객체**다. 일급객체란 뭔가?

1. 변수에 저장할 수 있고,
2. 매개변수로 넘길수 있고,
3. 리턴값으로 내보내줄수도있는 것
4. 즉, 함수를 값처럼 다룰 수 있으므로 자바스크립트의 함수는 **일급객체**이다.

## 클로저

- 외부 함수의 변수에 접근할 수 있는 내부 함수
- 정의를 보면 자바스크립트의 모든? 함수는 클로저라고 말할 수 있을 것 같다.
- 함수는 선언될 떄 렉시컬 환경이 구성된다. (상위 스코프들의 대한 정보, 상위 스코프에서 참조할 수 있는 변수들...등이 정해진다.)
- 아래의 클로저 예시를 봐보자.

```js
function outerFunc() {
  var x = 10;
  var innerFunc = function () {
    console.log(x);
  };
  return innerFunc;
}

var inner = outerFunc();
inner(); // 10
```

1. outerFunc는 실행 컨텍스로 콜스택에 올라가게되고 innerFunc를 반환하며 outerFunc의 실행 컨텍스트는 콜스택에서 pop되어 사라진다.
2. 그런데 내부 함수인 innerFunc를 실행하자 outerFunc 스코프의 지역변수 x값이 찍히는 걸 볼 수 있다.
3. 이처럼 함수는 선언된 시점에 렉시컬 환경을 기억하고있어 외부함수가 소멸한 후에도 내부함수는 선언시 저장된 스코프체인으로 외부 함수의 정보를 가지고있기 때문에 `도달가능한 상태`가 되며 외부 스코프의 변수 x를 참조 가능하게 된 것이다.

- 사용 예시
- 클로저 상의 변수는 직접적인 참조 및 제어가 불가능하므로 변수 값 변경의 위험을 덜어준다. (private하게 값을 다룰떄?)
- 따라서 side effect를 억제해줘 프로그램 안전성이 올라간다.

### 브라우저 렌더링 과정 (repainting, reflow)

- google.com 입력
- 리소스를 가져오기 위해선 google.com 도메인과 맵핑된 ip 주소를 찾아야한다.
- DNS에 ip주소가 뭔지 물어본다. (DNS는 도메인과 ip주소를 맵핑해주는 서버가 있다.)
- ip 주소를 찾았으면 리소스들을 request한다.
- 성공적으로 response를 받게되면 렌더링 엔진이 html과 css를 파싱하여 DOM, CSSOM 트리로 변환하고 이를 렌더 트리로 합쳐준 후 화면을 그려준다. (당연히 내부적으론 더 복잡한 과정이 있다.)
- js는 렌더링 엔진이 아닌 자바스크립트 엔진이 처리한다.
- script 태그를 만나게 되면 파싱 과정을 멈추고 자바스크립트 엔진이 작업을 시작한다.
- 자바스크립트 엔진의 역할이 끝나면 다시 파싱과정을 거친다.

> 이렇듯, 렌더링 엔진과 자바스크립트 엔진은 **동기적**으로 일을 처리하기떄문에, <script> 자바스크립트 코드는 <body>의 최하단에 놓는 것이 좋다. (화면을 빠르게 먼저 그려줄 수 있고, 생성되지 않은 DOM에 접근할 가능성을 없애준다.)

## 나중에 정리할 것

- 옵저버 패턴
- 이벤트 루프 (event bubbling, capturing, delegation)
- promise, async / await
- async await 예외처리의 까다로운 점
- 자바스크립트 원시값은 뭐가있나요
- display: none을 브라우저가 어떻게 처리하는지
- 프로젝트관련 질문 (기술 사용 이유)
- 상태관리 라이브러리
- 팀프로젝트 힘들었던거, 갈등 해결, 원하는 동료
- Date객체의 현재 시간은 실제 시간과 차이가 있을 수 있다?
- [면접 질문](https://zero-base.co.kr/event/media_insight_contents_FE_frontend_tech_Interview?gclid=CjwKCAjw3POhBhBQEiwAqTCuBrkdrbP1IKyimKUkQkBwtY5q_oVkqGg2quSPIddswt2LMxZIt053DRoCGiMQAvD_BwE)
- [쿠키 세션 토큰](https://hongong.hanbit.co.kr/%EC%99%84%EB%B2%BD-%EC%A0%95%EB%A6%AC-%EC%BF%A0%ED%82%A4-%EC%84%B8%EC%85%98-%ED%86%A0%ED%81%B0-%EC%BA%90%EC%8B%9C-%EA%B7%B8%EB%A6%AC%EA%B3%A0-cdn/)

## Reference

- 모던 자바스크립트 deep dive
