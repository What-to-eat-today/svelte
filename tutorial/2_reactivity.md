# 반응성

## 과업

Svelte 의 핵심은 이벤트에 대한 응답과 같이 DOM 을 애플리케이션 상태와 동기화하기 위한 반응성 시스템이다.

```javascript
<button on:click={handlerClick}>
```

Svelte 는 DOM 을 업데이트 해야할 것을 코드로 이 과업(`Assignments`) 를 지휘(`Instruments`) 한다.

컴포넌트의 상태가 변경되면 Svelte 가 자동으로 DOM 을 변경한다.

## 선언

종종 파생된 다른 데이터도 변경을 해야 하는데, 이에 따라 반응성 선언이 있다.

```javascript
let counter = 0;
$: doubled = counter * 2;
```

물론 이를 대신해서 `{ counter * 2 }` 를 직접 사용할 수 있으나, 여러 번 참조해야 하거나 다른 값에 의존하는 값이 있는 경우 유용하다.

## 상태

반응 값을 선언하는 것외에도 임의의 선언문을 반응성으로 실행할 수 있다.

```javascript
$: if (count >= 10) {
    alert(`count is dangerously high!`);
    count = 9;
}
```

## 배열과 객체 수정

Svelte 의 반응성은 과업에 의해 트리거된다.

`Array.prototype.push()` 와 같은 배열 방법을 사용하면 업데이트 되지 않는다.

이를 해결하는 방법은 할당을 추가하는 것이다.

```javascript
function addNumber() {
    numbers = [...numbers, numbers.length + 1];
}
```
