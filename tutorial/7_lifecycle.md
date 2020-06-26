# 라이프 사이클

모든 컴포넌트는 컴포넌트가 생성될 때 시작되고 소멸될 때 종료되는 라이프 사이클(수명주기)를 가진다.

이 라이프 사이클 동안 필요한 시점에 코드를 실행할 수 있는 기능이 있다.

가장 자주 사용하는 것은 컴포넌트가 DOM 으로 렌더링 된 후 실행되는 `onMount` 이다.

이전 바인딩에서 언급한 `<canvas>` 엘리먼트가 대표적이다.

## onMount

```javascript
<script>
    import { onMount } from 'svelte';

    let photos = [];

    onMount(async () => {
        const res = await fetch(`https://jsonplaceholder.typicode.com/photos?_limit=20`);
        photos = await res.json();
    });
</script>
```

서버 사이드 렌더링(SSR)으로 fetch 를 `onMount` 에 위치하는 것이 바람직하다.

`onDestroy` 를 제외하고 SSR 중에 라이프 사이클 함수가 실행되지 않으므로 컴포넌트가 DOM 에 마운트되고나서 로드되어야 하는 데이터를 가져오는 것을 피할 수 있다.

콜백이 컴포넌트 인스턴스에 바인드 되도록 컴포넌트가 초기화되는 동안 라이프 사이클 함수를 호출해야한다.

`onMount` 콜백이 함수를 반환하는 경우 컴포넌트가 소멸 될 때 해당 함수가 호출된다.

## onDestroy

컴포넌트를 소멸시키는 방법으로는 `onDestroy` 가 있다.

```javascript
<script>
    import { onDestroy } from 'svelte';

    let seconds = 0;
    const interval = setInterval(() => seconds += 1, 1000);

    onDestroy(() => clearInterval(interval));
</script>
```

컴포넌트를 초기화하는 동안 라이프 사이클 함수를 호출하는 것이 중요하지만 어디에서 호출하는지는 중요하지 않다.

즉, 라이프 사이클 또한 컴포넌트로 분리하여 사용할 수 있다.

```javascript
<script>
    import { onInterval } from './utils.js';

    let seconds = 0;
    onInterval(() => seconds += 1, 1000);
</script>
```

## beforeUpdate 와 afterUpdate

`beforeUpdate` 는 DOM 이 업데이트되기 직전에 발생하도록 작업을 예약한다.

`afterUpdate` 는 DOM 이 업데이트되고 난후에 발생하도록 작업을 예약한다.

이 라이프 사이클 함수는 순수하게 상태 중심의 방식으로 관리하기 어려운 작업을 수행하는 데 유용하다.

예제에서 문제는 채팅을 칠 때마다 계속 스크롤해야하기 때문에 사용하기 불편하다.

```javascript
let div;
let autoscroll;

beforeUpdate(() => {
    autoscroll = div && (div.offsetHeight + div.scrollTop) > (div.scrollHeight - 20);
});

afterUpdate(() => {
    if (autoscroll) div.scrollTo(0, div.scrollHeight);
});
```

컴포넌트가 마운트되기 전에 `beforeUpdate` 가 먼저 실행되므로 프로퍼티를 읽기 전에 div 를 먼저 확인해야 한다.

## tick

`tick` 은 다른 라이프사이클 함수와 다르게 언제든지 호출할 수 있다.

보류중인 상태 변경이 DOM 에 적용 되자마자 (또는 보류중인 상태 변경없는 경우 즉시) resolve 상태의 promise 를 반환한다.

Svelte 는 컴포넌트 상태를 업데이트할 때 DOM 이 즉시 반영되지 않는다.

다른 컴포넌트를 포함하여 적용해야할 다른 변경사항이 있는지 확인하기 위해 다음 마이크로 태스크까지 기다린다.

이렇게 처리하면 브라우저가 효과적으로 일괄 처리할 수 있다.
