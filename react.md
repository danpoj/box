## `export default () => {}`

- 함수 이름없이 내보내면 디버깅 힘드니까 하지말자. 물론 이렇게 쓰는사람은 없겠지만...

## JSX에서 여러 elements를 return 할 수 없는 이유

- JSX는 내부적으로 plain Javascript objects로 변환된다. 모든 태그를 감싸는 최상위 태그가 없다면 두 개 이상의 objects가 만들지기때문에 안된다.

```js
{
  type: 'a',

  props: {
    href: 'https://www.tmax.co.kr/',

    children: {
      type: 'img',
      props: {
        src: "https://imgs.xkcd.com/comics/random_number.png",
        alt: "RFC 1149.5 specifies 4 as the standard IEEE-vetted random number.",
      }
    },
  },
}
```

> JSX에서 element들의 attribute들은 camelCase다. `stroke-width` ❌ `strokeWidth` ✅
> `aria-*` `data-*` attributes는 예외로 dash를 사용한다.

## `return null | undefined | false`

```js
function Item({ name, isPacked }) {
  return (
    <li className='item'>
      {name} {isPacked && "✔"}
    </li>
  );
}
```

- `isPacked`가 true이면 `✔`
- `isPacked`가 false이면 `false`를 반환한다.

react는 `false` `null` `undefined`같은 걸 DOM의 빈 구멍으로 보고 랜더링하지않는다.

## Pure Components

- It minds its own business. It does not change any objects or variables that existed before it was called. (local mutation is ok.)
- Same inputs, same output. Given the same inputs, a pure function should always return the same result.
- side effect가 필요할때가 있다. `event handler`를 고려해보고, 최후의 수단으로 `useeffect`를 사용하자.
- You can improve performance by skipping rendering components whose inputs have not changed. This is safe because pure functions always return the same results, so they are safe to cache.
- memo를 통한 cache뿐만 아니라 여러 장점들 때문에 react에선 pure component를 토대로 동작한다.

## StrintMode

> React offers a “Strict Mode” in which it calls each component’s function twice during development. **By calling the component functions twice, Strict Mode helps find components that break pure component rules.**

## event propagation

- 자식 -> 부모로 event가 bubbling된다. `e.stopPropagation()` 막을 수 있다.

```js
export default function App() {
  return (
    <div
      className='Toolbar'
      onClick={() => {
        alert("You clicked on the toolbar!");
      }}
    >
      <button onClick={() => alert("Playing!")}>Play Movie</button>
      <button onClick={() => alert("Uploading!")}>Upload Image</button>
    </div>
  );
}
```

> ⚠️ All events propagate in React except onScroll, which only works on the JSX tag you attach it to.

### capture phase events

```js
<div
  onClickCapture={() => {
    /* this runs first */
  }}
>
  <button onClick={(e) => e.stopPropagation()} />
  <button onClick={(e) => e.stopPropagation()} />
</div>
```

1. It travels down, calling all onClickCapture handlers.
2. It runs the clicked element’s onClick handler.
3. It travels upwards, calling all onClick handlers.

## useState

- Don’t call Hooks inside loops, conditions, or nested functions
- Only Call Hooks from React Functions
- 컴포넌트의 한 단계 위 레이어에서 state를 관리한다고 생각하자.

[react hooks not magic just arrays](https://medium.com/@ryardley/react-hooks-not-magic-just-arrays-cd4f1857236e)

```js
let componentHooks = [];
let currentHookIndex = 0;

// How useState works inside React (simplified).
function useState(initialState) {
  let pair = componentHooks[currentHookIndex];
  if (pair) {
    // This is not the first render,
    // so the state pair already exists.
    // Return it and prepare for next Hook call.
    currentHookIndex++;
    return pair;
  }

  // This is the first time we're rendering,
  // so create a state pair and store it.
  pair = [initialState, setState];

  function setState(nextState) {
    // When the user requests a state change,
    // put the new value into the pair.
    pair[0] = nextState;
    updateDOM();
  }

  // Store the pair for future renders
  // and prepare for the next Hook call.
  componentHooks[currentHookIndex] = pair;
  currentHookIndex++;
  return pair;
}
```

```js
const [number, setNumber] = useState(0);

setNumber(number + 5); // adds "replace with 5" to its queue
setNumber((n) => n + 1); // adds "n => n+1" function to its queue
setNumber(42); // adds "replace with 42" to its queue

// result: 42
```

## React Lifecycle

1. triggering a render
2. rendering the component **“Rendering” is React calling your components.** (각각의 render cycle은 snapshot이다.)
3. committing to the DOM

> state변경 -> trigger발생 -> render queue에 추가 -> rerender(여러 state가 변경되는 경우에도 한번에 처리 - `batching`) -> 이전 DOM트리와 비교해 바뀐 부분만 browser DOM에 commit한다

- React waits until all code in the event handlers has run before processing your state updates. (prevent triggering too many re-renders.) This behavior, also known as **batching,** makes your React app run much faster.

## useState object

- useState에 primitive values 뿐만아니라 object를 넣어도 된다.
- object를 값으로 사용하는 경우에도 immutable하게 값을 다뤄야한다. (object또한 read-only하게 set으로 새로운 값을 대체해줘야한다는 뜻!)
- 또한 새로운 값으로 대체해줘야한다.

```js
import { useState } from "react";

export default function App() {
  const [user, setUser] = useState({
    firstName: "",
    lastName: "",
    email: "",
  });

  function handleInput(e) {
    setUser({
      ...user,
      [e.target.name]: e.target.value,
    });
  }

  return (
    <>
      <label>
        First name:
        <input name='firstName' value={user.firstName} onChange={handleInput} />
      </label>
      <label>
        Last name:
        <input name='lastName' value={user.lastName} onChange={handleInput} />
      </label>
      <label>
        Email:
        <input name='email' value={user.email} onChange={handleInput} />
      </label>
    </>
  );
}
```

### Immer로 쉽게 하기

- 아래 코드에서 `lastName`을 바꾸려는 경우를 어떻게 해야할까?

```js
const [person, setPerson] = useState({
  name: {
    firstName: "",
    lastName: "",
  },
  email: "",
  city: "",
  age: 0,
});
```

- solution 1: `spread`

```js
function onLastNameChange(e) {
  setPerson({
    ...person,
    name: {
      ...person.name,
      lastName: e.target.value,
    },
  });
}
```

- solution 2: `immer`
- The `draft` provided by Immer is a special type of object, called a [Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy), that “records” what you do with it.

```js
import { useImmer } from "use-immer";

const [person, setPerson] = useImmer({
  name: {
    firstName: "",
    lastName: "",
  },
  email: "",
  city: "",
  age: 0,
});

function onLastNameChange(e) {
  setPerson((draft) => {
    draft.name.lastName = e.target.value;
  });
}
```

- 배열에서 immer 사용하기
- [updating-arrays-in-state](https://react.dev/learn/updating-arrays-in-state)

```js
const initialList = [
  { id: 0, title: "Big Bellies", seen: false },
  { id: 1, title: "Lunar Landscape", seen: false },
  { id: 2, title: "Terracotta Army", seen: true },
];

const [myList, updateMyList] = useImmer(initialList);
const [yourList, updateYourList] = useImmer(initialList);

function handleToggleMyList(id, nextSeen) {
  updateMyList((draft) => {
    const artwork = draft.find((a) => a.id === id);
    artwork.seen = nextSeen;
  });
}

function handleToggleYourList(artworkId, nextSeen) {
  updateYourList((draft) => {
    const artwork = draft.find((a) => a.id === artworkId);
    artwork.seen = nextSeen;
  });
}
```

## State Structure principles

- **Group related state.** If you always update two or more state variables at the same time, consider merging them into a single state variable.
- **Avoid contradictions in state.** When the state is structured in a way that several pieces of state may contradict and “disagree” with each other, you leave room for mistakes. Try to avoid this.
- **Avoid redundant state.** If you can calculate some information from the component’s props or its existing state variables during rendering, you should not put that information into that component’s state.
- **Avoid duplication in state.** When the same data is duplicated between multiple state variables, or within nested objects, it is difficult to keep them in sync. Reduce duplication when you can.
- **Avoid deeply nested state.** Deeply hierarchical state is not very convenient to update. When possible, prefer to structure state in a flat way.

## Preserving and Resetting State

- 같은 구조, 같은 위치 DOM상의 UI tree이면 컴포넌트의 state는 유지된다.
- state를 리셋시키고 싶다면 컴포넌트에 key attribute를 넣어주면된다.

> DOM에서 컴포넌트가 제거된 후에도 그 상태를 유지하고싶으면?

1. css를 통해 hide 시키는 방법 (간단한 UI에서는 괜찮지만, hidden trees들이 많아지면 성능에 영향을 줄 수 있다.)
2. state를 부모 컴포넌트로 끌어올려 저장해두기.
3. localStorage 이용하기. (페이지를 실수로 나간 경우에도 state를 기억할 수 있다.)

## [comparing usestate and usereducer](https://react.dev/learn/extracting-state-logic-into-a-reducer#comparing-usestate-and-usereducer)

- **Code size**: Generally, with useState you have to write less code upfront. With useReducer, you have to write both a reducer function and dispatch actions. However, useReducer can help cut down on the code if many event handlers modify state in a similar way.
- **Readability**: useState is very easy to read when the state updates are simple. When they get more complex, they can bloat your component’s code and make it difficult to scan. In this case, useReducer lets you cleanly separate the how of update logic from the what happened of event handlers.
- **Debugging**: When you have a bug with useState, it can be difficult to tell where the state was set incorrectly, and why. With useReducer, you can add a console log into your reducer to see every state update, and why it happened (due to which action). If each action is correct, you’ll know that the mistake is in the reducer logic itself. However, you have to step through more code than with useState.
- **Testing**: A reducer is a pure function that doesn’t depend on your component. This means that you can export and test it separately in isolation. While generally it’s best to test components in a more realistic environment, for complex state update logic it can be useful to assert that your reducer returns a particular state for a particular initial state and action.
- **Personal preference**: Some people like reducers, others don’t. That’s okay. It’s a matter of preference. You can always convert between useState and useReducer back and forth: they are equivalent!

> reducers must be pure and immutable. reducer또한 `Immer`를 통해 쉽게 관리 가능하다.

## fetch / axios / react-query

|            | fetch | axios | react query |
| ---------- | ----- | ----- | ----------- |
| syntax     | 💩    | 👍    | 👍          |
| loading    | 💩    | 💩    | 👍          |
| success    | 💩    | 💩    | 👍          |
| error      | 💩    | 💩    | 👍          |
| stale data | 💩    | 💩    | 👍          |
| caching    | 💩    | 💩    | 👍          |

- axios: body를 `JSON`으로 보내 줄 필요가 없고, `response.json()`으로 한번 더 받아올 필요가 없는 등 `fetch`에 비해 간단한 syntax를 제공한다.

### react query

- fetching library for fetch and maintain state easier.
- useState와 contextAPI만으로 대부분의 client state를 다룰 수 있다.
- 하지만 구조가 복잡해지면 위의 native API들로는 state를 다루는데 한계가 발생할 수 있어, redux, mobx, recoil, jotai같은 상태관리 라이브러리들이 등장했다. 이러한 라이브러리들은 client state를 다루는 데엔 좋지만, `async or server state`를 다루기 어렵다.

### 그래서 react query를 왜 쓰는데?

> it makes fetching, caching, synchronizing and updating server state in your web applications a breeze. [tanstack query overview](https://tanstack.com/query/v4/docs/react/overview)

1. 데이터의 변경이 있는 경우에만 리렌더링을 유발한다.
2. 이미 받아온 데이터면 다른 페이지라도 그 상태를 확인하고 재호출을 방지해준다. 즉, 캐싱 처리에 관한 편한 인터페이스를 제공한다. (deduping multiple requests for the same data into a single request.)
3. 데이터를 다시 불러올때, 응답이 오기 전까지 이전 stale data로 화면을 표시해준다.
4. 요약하자면 client state와 server state를 최대한 일치시켜주고 fetch logic을 추상화하여 사용하기 쉬운 인터페이스를 제공해준다. (knowing when data is _out of data_ and updating _out of data_ in the background.)

## `<script>` CDN

> 실무에서는 webpack 같은 툴로 관리하기 때문에 `<script>` CDN을 직접 쓰진 않는다

```html
<!-- development -->
<script
  crossorigin
  src="https://unpkg.com/react@18/umd/react.development.js"
></script>
<script
  crossorigin
  src="https://unpkg.com/react-dom@18/umd/react-dom.development.js"
></script>
```

위의 코드는 개발용으로 적합하며 배포용 버전에는 적합하지 않다  
React의 용량 및 성능 최적화된 배포용 버전은 아래와 같이 제공되고 있다

```html
<!-- production -->
<script
  src="https://unpkg.com/react@18/umd/react.production.min.js"
  crossorigin
></script>
<script
  src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"
  crossorigin
></script>
```

## 컴포넌트

> 컴포넌트란? 데이터와 화면을 하나로 묶은 덩어리  
> 데이터가 바뀌면 화면도 따라 바뀐다

1. **함수 컴포넌트** • 요새는 대부분의 경우 함수로 작성
2. **클래스 컴포넌트** • 옛날 코드 || `ErrorBoundary`

## JSX

React에서 렌더링을 위해선 아래와 같이 해야한다

```js
return React.createElement(
  "button",
  { onClick: () => setCount(count + 1) },
  "plus"
);
```

하지만 복잡하고 읽기 힘들기 때문에 React에선 JSX를 사용한다
위와 동일한 코드이다

```js
return <button onClick={() => setCount(count + 1)}>plus</button>;
```

위와 같이 JSX를 사용하면 브라우저에서 JSX문법을 해석하지 못해 에러가 발생한다
이를 해결하기위해 JSX는 항상 Babel과 같이 사용해야한다

## React **V17**, **V18**

```js
// 17버전 index.js
ReactDOM.render(<App />, document.querySelector("#root"));

// 18버전 index.js
ReactDOM.createRoot(document.querySelector("#root")).render(<App />);
```

## controlled / uncontrolled form input

```js
// controlled input
<input  value={value} onChange={onChange} type='text'  />

// uncontrolled input
<input ref={inputRef} type='text' />
```

`form` `submit` 할 때만 `input` 값이 필요한 경우 **uncontrolled input**  
대부분의 경우는 **controlled input**을 쓴다  
**uncontrolled input** 에 기본 값을 주고싶은 경우 `defaultValue`를 써야한다  
`value`를 쓰게되면 controlled로 간주된다

[goshacmd controlled vs uncontrolled input](https://goshacmd.com/controlled-vs-uncontrolled-inputs-react/) 참고

## 컴포넌트 리랜더링 조건

1. state가 바뀌었을 때
2. props가 바뀌었을 때
3. 부모 컴포넌트가 리랜더링 될 때

> 부모 컴포넌트의 리랜더링에 의해 자식 컴포넌트가 불필요한 리랜더링이 발생한다면 자식 컴포넌트를 `memo`로 감싸주자  
> `memo`로 감싸진 자식 컴포넌트는 1,2 번에 의해서만 리랜더링 되고 3번은 막아준다

> react dev tools의 컴포넌트 창을 보면, `memo`로 감싸진 자식 컴포넌트의 이름이 **Anonymous**로 표시된다
> `memo`를 사용할 땐 아래 처럼 `displayName`을 같이 설정해주자

```js
Test.displayName = "Test";
```

**참고** class 컴포넌트에선 `memo` 대신 `pureComponent`, `shouldComponentUpdate`

## props 값 변경

부모 컴포넌트로부터 받은 props를 자식 컴포넌트에서 수정하면 안되지만,  
필요한 경우 props를 `useState`의 초기값으로 설정하여 `setState`를 통해 바꿔줘야한다

## virtual DOM

- SPA는 DOM 조작이 자주 발생한다.
- DOM 구조가 바뀔 떄 마다 화면을 그려주게 되면 연산이 너무 많아진다.
- 따라서 React는 수많은 렌더링된 결과를 Virtual DOM에 먼저 적용하고 그 최종 결과를 한번에 실제 DOM에 그려준다.
- 그렇다면 Virtual DOM이 퍼포먼스에 큰 영향을 줄까?
- 아니다. 실제로 본인의 앱 구조에 맞게 최적화 작업을 **제대로** 했을 경우가 제일 빠를 것이다.
- 하지만 그 작업이 복잡하고 힘들기 때문에 Virtual DOM을 통해 최적화 작업을 어느정도 자동으로 해줘 생산성을 폭발적으로 높여준다.
- React의 Virtual DOM을 사용한다고 성능이 훨씬 좋은것이 **절대** 아니다. React안에서도 최적화 작업을 잘 해줘야한다!

## 렌더링 최적화 (useMemo, useCallback, memo, 리렌더링조건)

- useMemo: 리턴값을 캐싱(memoization)해두어 리렌더가 일어나도 재연산이 일어나지않는다. 오직 최적화만을 위해서만 사용해야한다.
- useCallback: 함수를 캐싱한다.

- 인터랙션에 의해 전체 페이지를 변경하는 그런 경우에는 당연히 useMemo가 그다지 필요없을 것이다.
- 그림을 그리는 것과 같이 렌더링이 많이 일어나는 경우 최적화에 큰 영향을 준다.
- react Profiler로 어떤 컴포넌트가 memoization의 장점을 취할 수 있을 지 확인해보자.

```js
console.time("filter array");
const visibleTodos = filterTodos(todos, tab);
console.timeEnd("filter array");

// 1ms 를 기준으로 useMemo를 사용할지 정하는 걸 권장한다.
// development 환경에선 정확한 결과를 얻기힘들 수 있다. (ex. <StrictMode>)
// production으로 배포한 후, 성능이 다소 좋지 않은 디바이스로 테스트 해보자.
```

```js
function ProductPage({ productId, referrer, theme }) {
  const handleSubmit = (orderDetails) => {
    post("/product/" + productId + "/buy", {
      referrer,
      orderDetails,
    });
  };

  return (
    <div className={theme}>
      <ShippingForm onSubmit={handleSubmit} />
    </div>
  );
}

// 1. theme 값이 바뀌어 ProductPage가 리렌더되면, 자식인 ShippingForm도 리렌더링이 발생한다.
// 2. 만약 ShippingForm 컴포넌트를 그리는데 오랜 시간이 걸리는 컴포넌트라면?
// 3. ShippingForm 컴포넌트는 theme값과 관계가 없으므로 리렌더링이 필요없다.
// 4. ShippingForm 컴포넌트를 memo로 감싼다. (memo는 props가 바뀌지 않으면 리렌더링을 발생시키지않는다.)
// 5. handleSubmit 함수는 렌더링이 될때마다 새로운 함수를 생성하므로 useCallback으로 캐싱해두자.
```

## Reference

- [react.dev docs](https://react.dev)
