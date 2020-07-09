# 애니메이션

> 해당 튜토리얼은 내용보다 예제가 의도를 파악하는데 빠르다. [Svelte](https://svelte.dev/tutorial/actions)

## 표현식

이전 챕터에서 엘리먼트가 TODO 목록에서 다른 TODO 목록으로 이동할 때 지연된 전환을 사용하여 모션을 만들었다.

애니메이션 표현식을 사용하여 엘리먼트에 자연스러운 움직임을 적용할 수 있다.

```javascript
<label
    in:receive="{{key: todo.id}}"
    out:send="{{key: todo.id}}"
    animate:flip="{{duration: 200}}"
>
```

> `duration` 은 `d => milliseconds` 함수일 수도 있다. 여기서 `d` 는 엘리먼트가 이동해야하는 픽셀 수이다.

모든 전환과 애니메이션은 JS 가 아닌 CSS 로 적용되므로 주 스레드를 차단하지 않는다.
