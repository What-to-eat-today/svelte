# 스토어

모든 애플리케이션 상태가 애플리케이션의 컴포넌트 계층 구조로 속하지는 않는다.

더해서 관련이 없는 여러 컴포넌트 또는 일반 javascript 모듈에서 접근해야 하는 값이 있을 수 있다.

## 쓰기 가능 스토어

Svelte 는 스토에서 이 작업을 수행할 수 있다.

스토어는 스토어 값이 변경될 때 이를 알리는 `subscribe` 메소드가 있는 객체이다.

`App.svelte` 에서 `count` 가 스토어이며 `count.subscribe` 콜백에서 `count_value` 를 설정한다.

쓰기 가능한 스토어는 `subscribe` 이 외에도 `set` 과 `update` 를 할 수 있다.

스토어로 주입한 객체를 사용해 상태를 변경 할 수 있다.

```javascript
function increment() {
    count.update(n => n + 1);
}
```

```javascript
function reset() {
    count.set(0);
}
```

## 자동 구독

이전 예제에서 `unsubscribe` 함수는 호출하지 않는다.

이 때, 컴포넌트가 인스턴스화 되고 여러번 파괴되면 메모리 누수가 발생한다.

이를 해결하는 방법은 `onDestroy` 로 해소할 수 있다.

```javascript
// ...

let count_value;

const unsubscribe = count.subscribe(value => {
    count_value = value;
});

onDestroy(unsubscribe);
// ...
```

그러나 컴포넌트가 여러 개의 스토어를 구독(subscribe)하는 경우 문제가 있다.

Svelte 는 이를 위해서 구독하는 스토어에 `$` 를 붙여 참조할 수 있다.

```javascript
// ...
<h1>The count is {$count}</h1>
// ...
```

> 자동 구독(Auto-subscription)은 컴포넌트의 최상위 스코프에서 선언되어 가져온 스토어 변수에서만 작동한다.

태그 내에서 사용가능한 것이 아니라 이벤트 핸들러 또는 [반응성 선언](./2_reactivity.md#선언)과 같은 스크립트의 어느 곳에서나 사용가능하다.

> `$` 는 따라서 Svelte 의 접두사로 사용된 예약어로 자연스럽게 일반 변수는 `$` 로 시작할 수 없다.

## 읽기 가능 스토어

필요에 따라 쓰기 가능 스토어 외에도 읽기 가능 스토어를 만들 수 있다.

예를 들어 사용자 위치 기반 정보를 스토어에 담아 낼 수 있으며 이 정보는 `외부` 에서 해당 스토어를 변경 시킬 필요가 없다.

```javascript
export const time = readable(new Date(), function start(set) {
    const interval = setInterval(() => {
        set(new Date());
    }, 1000);

    // 중지 함수
    return function stop() {
        clearInterval(interval);
    };
});
```

1. 첫 번째 인수는 초기 값으로 아직 없는 경우 `null` 이거나 `undefined` 일 수도 있다.
2. 두 번째 인수는 설정된 콜백을 받고 중지 함수를 반환하는 시작함수이다.

시작 함수는 스토어가 첫 구독자를 획득할 때 호출된다.

중지 함수는 마지막 구독자가 구독을 취소할 때 호출된다.

## 파생 스토어

파생 스토어를 사용하여 하나 이상의 다른 스토어의 값을 사용하여 스토어를 작성할 수 있다.

이전 예제를 활용하여 페이지가 열린 시간을 계산하는 스토어를 작성할 수 있다.

```javascript
export const elapsed = derived(
    time,
    $time => Math.round(($time - start) / 1000)
);
```

여러 입력에서 스토어를 파생시켜 값을 반환하는 대신 명시적으로 값을 `set` 할 수 있다.

비동기 적으로 값을 파생시키는 것이 대표적인 케이스다.

자세한 내용은 [API 문서](https://svelte.dev/docs#derived) 를 참조하도록 한다.

## 커스텀 스토어

객체가 `subscribe` 메소드를 알맞게 구현하는 한, 이는 스토어이다.

그 외에도 모든 것이 진행된다.

따라서 도메인 별로 논리적 커스텀 스토어를 만드는 것은 매우 쉽다.

```javascript
function createCount() {
    const { subscribe, set, update } = writable(0);

    return {
    subscribe,
        increment: () => update(n => n + 1),
        decrement: () => update(n => n - 1),
        reset: () => set(0)
    };
}
```

## 스토어 바인딩

쓰기 가능 스토어는 내부 컴포넌트에 해당 값으로 바인딩 할 수 있다.

```javascript
<input bind:value={$name}>
```

입력 값을 변경하면 `name` 과 모든 의존 항목이 수정된다.

컴포넌트 내부에서 값을 저장하도록 직접 할당할 수도 있다.

```javascript
<button on:click="{() => $name += '!'}">
    Add exclamation mark!
</button>
```

`$name += '!'` 는 `name.set($name + '!')` 과 동일한 역할을 수행한다.
