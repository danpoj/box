## [React Synthetic Event](https://react.dev/reference/react-dom/components/common#react-event-object)

- Your event handlers will receive a React event object. It is also sometimes known as a _“synthetic event”._
- It conforms to the same standard as the underlying DOM events, but fixes some **browser inconsistencies.**
- Some React events do not map directly to the browser’s native events.

## Coordinate

- `clientX / clientY`: 브라우저 창 기준 좌표
- `x / y`: 브라우저 창 기준 좌표 **alias for clientX clientY** (react event - `event.nativeEvent.x`)
- `pageX / pageY`: 브라우저 전체 페이지 기준 좌표
- `offsetX / offsetY`: 이벤트 발생한 DOM element 기준 좌표 (react event - `event.nativeEvent.offsetX`)
- `screenX / screenY`: 사용자 모니터 기준 좌표
- `movementX / movementY`: 마지막 `mousemove event`와 현재 좌표의 차이

## Mouse

### `click`

- `mousedown` `mouseup` event 이후에 실행된다. (상위 element의 event라도 마찬가지)
- mouse pressed -> mouse released -> fired
- mouse를 누르고 때는 순간에 mouse 포인터가 이벤트가 있는 element에 있어야한다.
  > ⚠️ `click`이벤트가 있는 element에서 mouse를 누른 상태로 드래그하여 element 밖에서 마우스를 때는 경우 이벤트는 발생하지않습니다.

### `mousedown`

- mouse pressed될 때 fired

### `mouseup`

- mouse released될 때 fired
- mouse relaesed될 때의 pointer 위치의 element의 event fired

### `mouseover mouseenter`

- mouse를 hover할 때 fired
- `mouseover` → event bubbling ✅
- `mouseenter` → event bubbling ❌

### `mouseout mouseleave`

- mouse를 exited할 때 fired
- `mouseout` → event bubbling ✅
- `mouseleave` → event bubbling ❌

### `mousemove`

- mouse move
- event bubbling ✅
