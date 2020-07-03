# 전환

## 표현식

엘리먼트를 DOM 내부와 외부로 우아하게 전환(transition)하여보다 매력적인 UI 를 만들 수 있다.

Svelte 를 사용하면 `transition` 표현식을 통해 쉽게 수행 할 수 있다.

```javascript
<script>
    import { fade } from 'svelte/transition';
    let visible = true;
</script>

<p transition:fade>Fades in and out</p>
```

## 파라미터 추가

전환은 매개변수를 받아 처리할 수 있다.

```javascript
<script>
    import { fly } from 'svelte/transition';
    let visible = true;
</script>

<p transition:fly="{{ y: 200, duration: 2000 }}">
    Flies in and out
</p>
```

전환이 진행되는 동안 전환에 대한 상태를 전환하면 시작 또는 끝이 아닌 현재 지점에서 자연스럽게 전환된다.

## 인 아웃

`transition` 표현식 대신 `in` 또는 `out` 표현식이 있거나 둘 다있을 수 있다.

```javascript
<p in:fly="{{ y: 200, duration: 2000 }}" out:fade>
    Flies in, fades out
</p>
```

표현식을 분리하여 `in` 과 `out` 을 지정할 수 있다.

## 커스텀 CSS

`svelte/transition` 모듈에는 작은 빌트인 전환이 있다.

이를 자신만의 전환으로 수정하는 것은 매우 쉽다.

```javascript
function fade(node, {
    delay = 0,
    duration = 400
}) {
    const o = +getComputedStyle(node).opacity;

    return {
        delay,
        duration,
        css: t => `opacity: ${t * o}`
    };
}
```

`fade` 는 적용 되는 대상 노드와 2 개의 인수를 사용해 전환 속성을 포함한 전환 객체를 반환한다.

| 속성 | 설명 |
| --- | --- |
| `delay` | 전환 시작전 (밀리초) |
| `duration` | 전환 시간 (밀리초) |
| `easing` | `p => t` [트위닝](./9_motion.md#트위닝) 참조 |
| `css` | `(t, u) => css` 여기서 `u === 1 - t` |
| `tick` | `(t, u) => {...}` 로 노드에 추가적인 효과를 줄 수 있다. |

`t` 는 시작의 인트로와 끝의 아웃트로 시점에 0 이며 반대로 시작의 아웃트로와 끝의 인트로는 1 이다.

CSS 애니메이션이 가능한 경우 jank 를 방지하기 위해 주 스레드에서 실행되어 대부분의 경우 `tick` 속성이 아닌 `css` 속성을 반환해야한다.

Svelte 는 전환을 **시뮬레이션** 하고 CSS 애니메이션을 만든 다음 실행할수 있도록 한다.

페이드 전환은 다음과 같은 CSS 애니메이션을 생성한다.

```css
0% { opacity: 0 }
10% { opacity: 0.1 }
20% { opacity: 0.2 }
/* ... */
100% { opacity: 1 }
```

다양한 커스텀 CSS 를 사용할 수 있는 대신 부수효과도 주의해야 한다.

## 커스텀 JS

일반적인 전환은 CSS 를 통해 사용하지만 타자기 효과와 같이 JS 없이 얻을 수 없는 몇가지 효과가 있다.

```javascript
function typewriter(node, { speed = 50 }) {
    const valid = (
        node.childNodes.length === 1 &&
        node.childNodes[0].nodeType === Node.TEXT_NODE
    );

    if (!valid) {
        throw new Error(`This transition only works on elements with a single text node child`);
    }

    const text = node.textContent;
    const duration = text.length * speed;

    return {
        duration,
        tick: t => {
            const i = ~~(text.length * t);
            node.textContent = text.slice(0, i);
        }
    };
}
```

## 이벤트

전환의 시작과 종료를 감지하는 것은 유용하다.

Svelte 는 다른 DOM 이벤트와 동일하게 이벤트를 전달한다.

```javascript
<p
    transition:fly="{{ y: 200, duration: 2000 }}"
    on:introstart="{() => status = 'intro started'}"
    on:outrostart="{() => status = 'outro started'}"
    on:introend="{() => status = 'intro ended'}"
    on:outroend="{() => status = 'outro ended'}"
>
    Flies in and out
</p>
```

## 로컬

일반적으로 컨테이너 블록이 추가되거나 파괴되면 엘리먼트에서 전환이 시작된다.

다음 예제는 전체 목록의 `showItems` 을 전환하면 개별 목록 요소에 전환이 적용된다.

이를 대신하여 개별 항목을 추가 및 제거 할 때, 즉 사용자가 슬라이더를 드래그 할 때만 전환이 적용되도록 하고자 한다.

`local` 을 선언해 줌으로써 로컬 전환을 적용할 수 있다.

```javascript
{#if showItems}
    {#each items.slice(0, i) as item}
        <div transition:slide|local>
            {item}
        </div>
    {/each}
{/if}
```

다음과 같이 `local` 을 적용한다면 `item` 의 전환과 연관된 내용에만 전환이 적용된다.

없는 경우 `showItems` 의 값 변경에도 전환의 적용된다.

## 연기

Svelte 전환 엔진에서 특히 강력한 기능은 전환을 지연시켜 여러 엘리먼트를 조정할 수 있다는 것이다.

할 일 목록 쌍을 가져 와서 할 일을 전환하면 반대 목록으로 보낸다.

실제로는 다른 곳에서 사라지고 다시 나타나는 대신 객체가 그와 같이 동작하지 않고 일련의 중간 위치를 통해 이동한다.

모션을 사용하면 사용자가 앱에서 무슨 일이 일어나고 있는지 이해하는 데 도움이 될 수 있다.

`crossfade` 를 사용해 이 효과를 얻을 수 있다.

이 함수는 `send` 와 `receive` 라는 전환 쌍을 만든다.

엘리먼트가 '전송'되면 해당 엘리먼트가 '수신'되고 해당 엘리먼트를 상대방의 위치로 변환하고 페이드 아웃하는 전환을 생성한다.

엘리먼트가 '수신'되면 반대의 상황이 발생한다. 상대가 없으면 `fallback` 전환이 사용된다.

```javascript
<label
    class="done"
    in:receive="{{key: todo.id}}"
    out:send="{{key: todo.id}}"
>
```
