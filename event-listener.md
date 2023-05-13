## Event

### Coordinate

- `clientX clientY`: 브라우저 창 기준 좌표
- `x y`: 브라우저 창 기준 좌표 **alias for clientX clientY** (react event - `event.nativeEvent.x`)
- `pageX pageY`: 브라우저 전체 페이지 기준 좌표
- `offsetX, offsetY`: 이벤트 발생한 DOM element 기준 좌표 (react event - `event.nativeEvent.offsetX`)
- `screenX screenY`: 사용자 모니터 기준 좌표
- `movementX movementY`: 마지막 `mousemove event`와 현재 좌표의 차이
