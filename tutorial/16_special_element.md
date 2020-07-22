# 특별한 엘리먼트

[dom]: ./5_event
[api_ref]: https://svelte.dev/docs

Svelte 는 다양한 내장 엘리먼트를 제공한다.

## svelte:self

컴포넌트가 재귀적으로 포함되도록 한다.

폴더가 다른 폴더를 포함 할 수 있는 폴더 트리보기와 같은 항목에 유용하다.

`Folder.svelte` 에서 할 수 있다.

```javascript
{#if file.type === 'folder'}
    <Folder {...file}/>
{:else}
    <File {...file}/>
{/if}
```

하지만 파일을 가지고 올 수 없기 때문에 불가능하다.

대신 `<svelte:self>` 를 사용한다.

```javascript
{#if file.type === 'folder'}
    <svelte:self {...file}/>
{:else}
    <File {...file}/>
{/if}
```

## svelte:component

컴포넌트는 `<svelte:component>` 를 사용하여 카테고리(범주)를 모두 변경할 수 있다.

```javascript
{#if selected.color === 'red'}
    <RedThing/>
{:else if selected.color === 'green'}
    <GreenThing/>
{:else if selected.color === 'blue'}
    <BlueThing/>
{/if}
```

와 같은 `if` 코드를 하나의 동적 컴포넌트로 사용가능하다.

```javascript
<svelte:component this={selected.component}/>
```

이 `this` 는 모든 컴포넌트 생성자 또는 허위 값(falsy)일 수도 있다. 허위 값이면 어떤 구성 요소도 렌더링되지 않는다.

## svelte:window

DOM 엘리먼트에 이벤트 리스너를 [추가][dom]할 수 있는 것처럼 `<svelte:window>` 를 사용하여 `window` 리스너에 이벤트 리스너를 추가할 수 있다.

```javascript
<svelte:window on:keydown={handleKeydown}/>
```

> DOM 엘리먼트와 마찬가지로 `prevaneDefault` 와 같은 이벤트 수정자를 추가할 수 있다.

## svelte:window 바인딩

scrollY 와 같은 `window` 의 특정 프로퍼티에 바인딩 할 수 도 있다.

```javascript
<svelte:window bind:scrollY={y}/>
```

바인딩 가능한 프로퍼티는 다음과 같다

- `indderWidth`
- `innerHeight`
- `outerWidth`
- `scrollX`
- `scrollY`
- `online` (`window.navigator.online` 의 별칭이다.)

`scrollX` 와 `scrollY` 를 제외한 모든 것은 읽기 전용이다.

## svelte:body

`<svelte:window>` 와 마찬가지로 `<svelte:body>` 엘리먼트를 사용하면 `document.body` 에서 발생하는 이벤트를 수신할 수 있다.

이는 창에서 실행되지 않는 `mouseenter` 와 `mouseleave` 이벤트에 유용하다.

```javascript
<svelte:body
    on:mouseenter={handleMouseenter}
    on:mouseleave={handleMouseleave}
/>
```

## svelte:head

`<svelte:head>` 엘리먼트를 사용하면 문서의 `<head>` 안에 엘리먼트를 삽입할 수 있다.

```javascript
<svelte:head>
    <link rel="stylesheet" href="tutorial/dark-theme.css">
</svelte:head>
```

> 서버 사이드 렌더링(SSR) 모드에서는 `<svelte:head>` 의 내용은 나머지 HTML 과 별도로 반환된다.

## svelte:options

마지막으로 `<svelte:options>` 은 컴파일러 옵션을 지정할 수 있다.

`immutable` 옵션을 예로, 이 앱에서 `<Todo>` 컴포넌트는 새로운 데이터를 받을 때마다 깜박인다.

항목 중 하나를 클릭하면 업데이트 된 `todos` 배열을 작성하여 `doen` 상태를 토글한다.

이로 인해 다른 `<Todo>` 항목이 DOM 을 변경하지 않아도 깜박인다.

`<Todo>` 컴포넌트에 불변 데이터를 기대하도록함으로써 이를 최적화 할 수 있다.

이것은 `todo` 프로퍼티를 절대로 변경하지 않을 것을 약속하지만, 상황이 바뀔 때마다 새로운 `todo` 객체를 생성할 것을 의미한다.

```javascript
<svelte:options immutable={true}/>

// ...
<script>
// ...
```

원하는 경우 `<svelte:options immutable />` 로 사용할 수 있다.

이제 할 일을 클릭하여 전환하면 업데이트 된 컴포넌트만 깜박인다.

`<svelte:options>` 로 설정할 수있는 옵션은 다음과 같다.

- `immutable = {true}`: 변경 가능한 데이터를 사용하지 않으므로 컴파일러는 간단한 참조 등식 검사를 수행하여 값이 변경되었는지 확인할 수 있다.
- `immutable = {false}`: 기본값. Svelte 는 변경 가능한 객체가 변경되었는지 여부에 대해 더 보수적이다.
- `accessors = {true}`: 컴포넌트의 프로퍼티에 `getter` 와 `setter` 를 추가한다.
- `accessors = {false}`: 기본값.
- `namespace = "..."`: 컴포넌트가 사용될 네임 스페이스, 가장 일반적으로 `"svg"`
- `tag = "..."`: 이 컴포넌트를 사용자 정의 엘리먼트로 컴파일 할 때 사용할 이름을 지정한다.

좀 더 자세한 내용은 [API 문서][api_ref]를 참조한다.
