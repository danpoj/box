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
      {name} {isPacked && '✔'}
    </li>
  )
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
        alert('You clicked on the toolbar!')
      }}
    >
      <button onClick={() => alert('Playing!')}>Play Movie</button>
      <button onClick={() => alert('Uploading!')}>Upload Image</button>
    </div>
  )
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
let componentHooks = []
let currentHookIndex = 0

// How useState works inside React (simplified).
function useState(initialState) {
  let pair = componentHooks[currentHookIndex]
  if (pair) {
    // This is not the first render,
    // so the state pair already exists.
    // Return it and prepare for next Hook call.
    currentHookIndex++
    return pair
  }

  // This is the first time we're rendering,
  // so create a state pair and store it.
  pair = [initialState, setState]

  function setState(nextState) {
    // When the user requests a state change,
    // put the new value into the pair.
    pair[0] = nextState
    updateDOM()
  }

  // Store the pair for future renders
  // and prepare for the next Hook call.
  componentHooks[currentHookIndex] = pair
  currentHookIndex++
  return pair
}
```

```js
const [number, setNumber] = useState(0)

setNumber(number + 5) // adds "replace with 5" to its queue
setNumber((n) => n + 1) // adds "n => n+1" function to its queue
setNumber(42) // adds "replace with 42" to its queue

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
import { useState } from 'react'

export default function App() {
  const [user, setUser] = useState({
    firstName: '',
    lastName: '',
    email: '',
  })

  function handleInput(e) {
    setUser({
      ...user,
      [e.target.name]: e.target.value,
    })
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
  )
}
```

### Immer로 쉽게 하기

- 아래 코드에서 `lastName`을 바꾸려는 경우를 어떻게 해야할까?

```js
const [person, setPerson] = useState({
  name: {
    firstName: '',
    lastName: '',
  },
  email: '',
  city: '',
  age: 0,
})
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
  })
}
```

- solution 2: `immer`
- The `draft` provided by Immer is a special type of object, called a [Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy), that “records” what you do with it.

```js
import { useImmer } from 'use-immer'

const [person, setPerson] = useImmer({
  name: {
    firstName: '',
    lastName: '',
  },
  email: '',
  city: '',
  age: 0,
})

function onLastNameChange(e) {
  setPerson((draft) => {
    draft.name.lastName = e.target.value
  })
}
```

- 배열에서 immer 사용하기
- [updating-arrays-in-state](https://react.dev/learn/updating-arrays-in-state)

```js
const initialList = [
  { id: 0, title: 'Big Bellies', seen: false },
  { id: 1, title: 'Lunar Landscape', seen: false },
  { id: 2, title: 'Terracotta Army', seen: true },
]

const [myList, updateMyList] = useImmer(initialList)
const [yourList, updateYourList] = useImmer(initialList)

function handleToggleMyList(id, nextSeen) {
  updateMyList((draft) => {
    const artwork = draft.find((a) => a.id === id)
    artwork.seen = nextSeen
  })
}

function handleToggleYourList(artworkId, nextSeen) {
  updateYourList((draft) => {
    const artwork = draft.find((a) => a.id === artworkId)
    artwork.seen = nextSeen
  })
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

## Reference

- [react.dev docs](https://react.dev)
