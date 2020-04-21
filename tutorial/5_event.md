# 이벤트

## DOM 이벤트

```javascript
<div on:mousemove={handleMousemove}>
    The mouse position is {m.x} x {m.y}
</div>
```

`on:${eventListener}` 지시자를 통해서 DOM 이벤트에 바인딩 할 수 있다.

## 인라인 핸들러

```javascript
<div on:mousemove={handleMousemove}>
    The mouse position is {m.x} x {m.y}
</div>
```

일부 프레임워크에서는 성능상의 이유로 반복문 내부에서 인라인 핸들러를 비권장한다.

그러나 Svelte 는 적용되지 않으며 컴파일러는 항상 올바른 형식을 선택한다.

## 이벤트 수정자

이벤트 핸들러에는 동작을 변경하는 수정자가 올 수 있다.

```javascript
<script>
    function handleClick() {
        alert('no more alerts')
    }
</script>

<button on:click|once={handleClick}>
    Click me
</button>
```

| modifier | description |
| ---  | --- |
| preventDefault | `e.preventDefault()` 를 호출한다. 클라이언트 사이드를 핸들링하는 것에 유용하다. |
| stopPropagation | `e.stopPropagation()` 를 호출한다. 이벤트가 다음 요소에 전파되지 못하게한다. |
| passive | 터치, 휠 이벤트에서 스크롤 성능을 향상시킨다. Svelte 는 안전한 위치에서 자동으로 추가한다. |
| capture | 버블링 단계 대신에 캡처 단계에서 핸들링을 시작한다. [MDN](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Building_blocks/Events#Event_bubbling_and_capture) |
| once | 한번 실행 후에 핸들러를 제거한다. |
| self | `event.target` 이 요소 자체인 경우에만 핸들러를 트리거한다. |

또한 수정자는 체이닝으로 같이 사용할 수 있다.

`on:click|once|capture={...}.`

## 컴포넌트 이벤트

컴포넌트는 이벤트를 전달할 수 있다.

```javascript
<script>
    import { createEventDispatcher } from 'svelte';

    const dispatch = createEventDispatcher();

    function sayHello() {
        dispatch('message', {
            text: 'Hello!'
        });
    }
</script>
```

컴포넌트가 인스턴스화 되기 전에 `createEventDispatcher()` 를 호출해야한다.

## 이벤트 포워딩

DOM 이벤트와는 다르게 컴포넌트 이벤트는 버블링되지 않는다.

따라서 중첩된 구조에서는 다른 요소가 이벤트를 청취하려면 이벤트를 전달해야한다.

```javascript
<script>
    import Inner from './Inner.svelte';
    import { createEventDispatcher } from 'svelte';

    const dispatch = createEventDispatcher();

    function forward(event) {
        dispatch('message', event.detail);
    }
</script>

<Inner on:message={forward}/>
```

전달을 위해서 매번 많은 코드를 줄이기 위해서 간단한 문법을 제공한다.

```javascript
<script>
    import Inner from './Inner.svelte';
</script>

<Inner on:message/>
```

## DOM 이벤트 포워딩

DOM 이벤트도 마찬가지로 전달할 수 있다.

```javascript
<button on:click>
    Click me
</button>
```

선언되어있는 `on:click` 지시자에 이벤트를 포워딩하면 된다.
