## 개념 정리

- [react](https://github.com/danpoj/danpoj-docs/blob/main/react.md)
- [javascript](https://github.com/danpoj/danpoj-docs/blob/main/js.md)
- [typescript](https://github.com/danpoj/danpoj-docs/blob/main/ts.md)
- [http](https://github.com/danpoj/danpoj-docs/blob/main/http.md)
- [algorithm](https://github.com/danpoj/danpoj-docs/blob/main/algorithm.md)
- [database](https://github.com/danpoj/danpoj-docs/blob/main/db.md)
- [npm](https://github.com/danpoj/danpoj-docs/blob/main/npm.md)

### virtual DOM

- SPA는 DOM 조작이 자주 발생한다.
- DOM 구조가 바뀔 떄 마다 화면을 그려주게 되면 연산이 너무 많아진다.
- 따라서 React는 수많은 렌더링된 결과를 Virtual DOM에 먼저 적용하고 그 최종 결과를 한번에 실제 DOM에 그려준다.
- 그렇다면 Virtual DOM이 퍼포먼스에 큰 영향을 줄까?
- 아니다. 실제로 본인의 앱 구조에 맞게 최적화 작업을 **제대로** 했을 경우가 제일 빠를 것이다.
- 하지만 그 작업이 복잡하고 힘들기 때문에 Virtual DOM을 통해 최적화 작업을 어느정도 자동으로 해줘 생산성을 폭발적으로 높여준다.
- React의 Virtual DOM을 사용한다고 성능이 훨씬 좋은것이 **절대** 아니다. React안에서도 최적화 작업을 잘 해줘야한다!

### 렌더링 최적화 (useMemo, useCallback, memo, 리렌더링조건)

- useMemo: 리턴값을 캐싱(memoization)해두어 리렌더가 일어나도 재연산이 일어나지않는다. 오직 최적화만을 위해서만 사용해야한다.
- useCallback: 함수를 캐싱한다.

- 인터랙션에 의해 전체 페이지를 변경하는 그런 경우에는 당연히 useMemo가 그다지 필요없을 것이다.
- 그림을 그리는 것과 같이 렌더링이 많이 일어나는 경우 최적화에 큰 영향을 준다.
- react Profiler로 어떤 컴포넌트가 memoization의 장점을 취할 수 있을 지 확인해보자.

```js
console.time('filter array')
const visibleTodos = filterTodos(todos, tab)
console.timeEnd('filter array')

// 1ms 를 기준으로 useMemo를 사용할지 정하는 걸 권장한다.
// development 환경에선 정확한 결과를 얻기힘들 수 있다. (ex. <StrictMode>)
// production으로 배포한 후, 성능이 다소 좋지 않은 디바이스로 테스트 해보자.
```

```js
function ProductPage({ productId, referrer, theme }) {
  const handleSubmit = (orderDetails) => {
    post('/product/' + productId + '/buy', {
      referrer,
      orderDetails,
    })
  }

  return (
    <div className={theme}>
      <ShippingForm onSubmit={handleSubmit} />
    </div>
  )
}

// 1. theme 값이 바뀌어 ProductPage가 리렌더되면, 자식인 ShippingForm도 리렌더링이 발생한다.
// 2. 만약 ShippingForm 컴포넌트를 그리는데 오랜 시간이 걸리는 컴포넌트라면?
// 3. ShippingForm 컴포넌트는 theme값과 관계가 없으므로 리렌더링이 필요없다.
// 4. ShippingForm 컴포넌트를 memo로 감싼다. (memo는 props가 바뀌지 않으면 리렌더링을 발생시키지않는다.)
// 5. handleSubmit 함수는 렌더링이 될때마다 새로운 함수를 생성하므로 useCallback으로 캐싱해두자. 그럼 끝~
```

### 실행 컨텍스트

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

### this가 결정되는 방법

- this는 함수가 **호출** 되는 시점에 결정된다! (중요)
- this가 가리키는 값을 알고싶으면 함수의 호출부를 봐보자.
- 화살표 함수의 경우 외부의 this를 물려받는다.
- `call apply bind` `생성자함수의 this` `객체 의 메소드`등에 의해 this의 값은 호출시점에 달라진다.

### 클로저

- 외부 함수의 변수에 접근할 수 있는 내부 함수
- 정의를 보면 자바스크립트의 모든? 함수는 클로저라고 말할 수 있을 것 같다.
- 함수는 선언될 떄 렉시컬 환경이 구성된다. (상위 스코프들의 대한 정보, 상위 스코프에서 참조할 수 있는 변수들...등이 정해진다.)
- 아래의 클로저 예시를 봐보자.

```js
function outerFunc() {
  var x = 10
  var innerFunc = function () {
    console.log(x)
  }
  return innerFunc
}

var inner = outerFunc()
inner() // 10
```

1. outerFunc는 실행 컨텍스로 콜스택에 올라가게되고 innerFunc를 반환하며 outerFunc의 실행 컨텍스트는 콜스택에서 pop되어 사라진다.
2. 그런데 내부 함수인 innerFunc를 실행하자 outerFunc 스코프의 지역변수 x값이 찍히는 걸 볼 수 있다.
3. 이처럼 함수는 선언된 시점에 렉시컬 환경을 기억하고있어 외부함수가 소멸한 후에도 내부함수는 선언시 저장된 스코프체인으로 외부 함수의 정보를 가지고있기 때문에 `도달가능한 상태`가 되며 외부 스코프의 변수 x를 참조 가능하게 된 것이다.

- 사용 예시
- 클로저 상의 변수는 직접적인 참조 및 제어가 불가능하므로 변수 값 변경의 위험을 덜어준다. (private하게 값을 다룰떄?)
- 따라서 side effect를 억제해줘 프로그램 안전성이 올라간다.

### 이벤트 루프 (event bubbling, capturing, delegation)

### promise, async / await

### async await 예외처리의 까다로운 점

### 자바스크립트 원시값은 뭐가있나요

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

### display: none을 브라우저가 어떻게 처리하는지

### 프로젝트관련 질문 (기술 사용 이유)

### 상태관리 라이브러리

### 팀프로젝트 힘들었던거, 갈등 해결, 원하는 동료

### Date객체의 현재 시간은 실제 시간과 차이가 있을 수 있다?

### 면접 질문들!

https://zero-base.co.kr/event/media_insight_contents_FE_frontend_tech_Interview?gclid=CjwKCAjw3POhBhBQEiwAqTCuBrkdrbP1IKyimKUkQkBwtY5q_oVkqGg2quSPIddswt2LMxZIt053DRoCGiMQAvD_BwE

### 쿠키 세션 토큰

https://hongong.hanbit.co.kr/%EC%99%84%EB%B2%BD-%EC%A0%95%EB%A6%AC-%EC%BF%A0%ED%82%A4-%EC%84%B8%EC%85%98-%ED%86%A0%ED%81%B0-%EC%BA%90%EC%8B%9C-%EA%B7%B8%EB%A6%AC%EA%B3%A0-cdn/
