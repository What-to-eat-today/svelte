# 바인딩

일반적인 Svelte 의 데이터 흐름은 하향식이다.

상위 컴포넌트는 하위 컴포넌트에 프로퍼티를 설정할 수 있다.

## 텍스트

`bind:value` 디렉티브를 통해 input 에 바인딩할 수 있다.

```javascript
<input bind:value={name} />
```

## 숫자형

DOM 에서 데이터는 모두 문자열이다.

type 을 `number` 나 `range` 로 지정시에 숫자형으로 인식할 수 있다.

```javascript
<input type=number bind:value={a} min=0 max=10 />

<input type=range bind:value={a} min=0 max=10 />
```

## 체크박스

체크박스는 토글 가능한 상태를 가진다.

```javascript
<input type=checkbox bind:checked={yes}>
```

`yes` 에 체크박스의 선택 상태가 바인딩 된다.

## 그룹

일부 DOM 은 동일한 값과 관련된 입력이 여러개인 경우가 있다.

이 경우는 그룹 바인딩을 사용할 수 있다.

```javascript
<input type=radio bind:group={scoops} value={1}>

<input type=checkbox group={flavours} value={flavour}>
```

동일한 그룹에 바인딩 되어 여러개를 선택한 경우 체크박스의 경우 배열을 반환한다.

(라디오 버튼은 상호 배타적으로 1개를 유지한다.)

## 텍스트 에어리어

텍스트 input 과 동일하게 `bind:value` 디렉티브로 바인딩이 가능하다.

텍스트 에어리어는 부가적으로 축약하여 선언가능하다.

```javascript
<textarea bind:value></textarea>
```

단, 축약하여 사용한 경우 value 로 명을 매치해야한다.

## 셀렉트 바인드

select DOM 도 `bind:value` 디렉티브를 사용할 수 있다.

```javascript
<select bind:value={selected} on:change="{() => answer = ''}">
    {#each questions as question}
    <option value={question}>
    </option>
    {/each}
</select>
```

option 에서 사용되는 value 는 문자열이 아닌 객체이다.

선택된 초기 값을 설정하지 않은 경우 바인딩은 목록의 첫 번째 항목을 기본값으로 선택한다.

또한, 바인딩이 초기화 되기 이전까지는 select 는 정의되지 않으며 이 시점에서는 다른 곳에서 참조할 수 없다.

## 다중 셀렉트

select DOM 은 `multiple` 속성을 가질수 있는데, 이는 이전 그룹 체크 박스의 기능과 동일하다.

다음은 UI 는 다르지만 동일한 기능을 제공한다.

그룹 체크 박스 예제

```javascript
{#each menu as flavour}
    <label>
        <input type=checkbox bind:group={flavours} value={flavour}>
        {flavour}
    </label>
{/each}
```

다중 셀렉트 예제

```javascript
<select multiple bind:value={flavours}>
    {#each menu as flavour}
        <option value={flavour}>
            {flavour}
        </option>
    {/each}
</select>
```

## 컨텐츠 편집

HTML5 에서 제공하는 컨텐츠 편집 기능은 DOM 에서 사용자가 요소를 편집할 수 있는지 나타내는 열거형 특성이다. [MDN]<https://developer.mozilla.org/ko/docs/Web/HTML/Global_attributes/contenteditable>

사용 방법은 HTML5 와 규격사항인 `contenteditable="true"` 과 동일하다.

```javascript
<div
    contenteditable="true"
    bind:innerHTML={html}
></div>
```

bind 로 사용할 수 있는 속성은 `textContent` 와 `innerHtml` 이다.

## each

`{#each}` 구문 내부에서도 객체를 순회하면서 바인딩 할 수 있다.

```javascript
<input
    type=checkbox
    bind:checked={todo.done}
>

<input
    placeholder="What needs to be done?"
    bind:value={todo.text}
>
```

> 변경 불가능한 상태를 유지하려한다면, 이러한 바인딩을 피하고 대신 이벤트 핸들러를 사용해야한다.

## 미디어 엘리먼트

`<auido>` 와 `<video>` 엘리먼트는 바인딩 가능한 몇 가지 속성을 가지고 있다.

```javascript
<video
    poster="https://sveltejs.github.io/assets/caminandes-llamigos.jpg"
    src="https://sveltejs.github.io/assets/caminandes-llamigos.mp4"
    on:mousemove={handleMousemove}
    on:mousedown={handleMousedown}
    bind:currentTime={time}
    bind:duration
    bind:paused
></video>
```

`bind:duration` 은 마찬가지로 `bind:duration={duration}` 를 축약하여 사용한 것이다.

이처럼 바인딩을 통해 사용자 지정 컨트롤을 만들 수 있다.

일반적으로 웹에서는 `timeupdate` 이벤트를 수신하여 `currentTime` 을 수신한다.

그러나 해당 이벤트는 자주 발생하지 않아 원하는 흐름대로 동작하지 않는다.

이를 해소하기 위해서 Svelte 는 `requestAnimationFrame` 을 사용하여 `currentTime` 을 수신한다. [MDN]<https://developer.mozilla.org/ko/docs/Web/API/Window/requestAnimationFrame>

미디어 엘리먼트는 6 개의 읽기 전용 속성이 있다.

| 속성 명 | 설명 |
| --- | --- |
| `duration` | 미디어 엘리먼트의 전체 시간을 초로 가져온다. |
| `buffer` | `{start, end}` 객체 배열 |
| `seekable` | ditto |
| `played` | ditto |
| `seeking` | boolean |
| `ended` | boolean |

그리고 4 가지의 양방향 속성이 있다.

| 속성 명 | 설명 |
| --- | --- |
| `currentTime` | 현재 비디오의 실행 시간을 가져온다. |
| `playbackRate` | 비디오의 실행 속도를 나타내며 기본값은 1 이다. |
| `paused` | 이 값은 자기 서술이 필요하다. |
| `volume` | 0 과 1 사이의 값을 받는다. |

`<video>` 엘리먼트에서는 추가로 `videoWidth` 와 `videoHeight` 읽기 속성이 있다.

## 넓이

모든 블록을 가지는 엘리먼트는 `clientWidth`, `clientHeight`, `offsetWidth` 그리고 `offsetHeight`, 4개의 속성을 바인딩할 수 있다.

```javascript
<div bind:clientWidth={w} bind:clientHeight={h}>
    <span style="font-size: {size}px">{text}</span>
</div>
```

`{w}` 와 `{h}` 는 읽기 전용으로 변경해도 아무런 일이 일어나지 않는다.

넓이와 관련된 속성은 오버헤드가 발생하므로 많은 수의 요소에서 이 기능을 사용하는 것을 지양해야한다.

또한 display:inline 요소에서는 해당 값을 측정할 수 없으며, 더하여 다른 요소를 포함 할 수 없는 `<canvas>` 도 마찬가지로 측정할 수 없다.

이 경우 래퍼 엘리먼트를 대신 측정하여 사용해야한다.

## This

This 바인딩은 모든 요소에 적용되며 렌더링 된 요소에 참조를 얻을 수 있다.

```javascript
<canvas
    bind:this={canvas}
    width={32}
    height={32}
></canvas>
```

`<canvas>` 의 경우 컴포넌트가 마운트 될 때 까지 정의되지 않으므로 `onMount` 라이프 사이클에 함수를 작성해야 한다.

## 컴포넌트

DOM 의 엘리먼트에 바인딩 가능한 것처럼 컴포넌트의 속성 들(`props`)에 바인딩할 수 있다.

```javascript
<Keypad bind:value={pin} on:submit={handleSubmit}/>
```

사용자가 키패드 컴포넌트를 사용하면 상위 컴포넌트의 `pin` 값이 즉시 갱신된다.

**컴포넌트 바인딩은 신중하게 사용해야한다.**

데이터 흐름이 너무 많은 경우, 특히 SSOT[WIKI]<https://ko.wikipedia.org/wiki/%EB%8B%A8%EC%9D%BC_%EC%A7%84%EC%8B%A4_%EA%B3%B5%EA%B8%89%EC%9B%90> 설계가 아닌 애플리케이션이라면 주변 데이터 흐름을 추적하기가 매우 어려워질 수 있다.
