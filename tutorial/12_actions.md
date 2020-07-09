# 액션

> 해당 튜토리얼은 내용보다 예제가 의도를 파악하는데 빠르다. [Svelte](https://svelte.dev/tutorial/animate)

액션은 엘리먼트 수준의 라이프 사이클 함수이다.

이는 다음과 같은 상황에서 유용하다.

- 타사의 라이브러리와 인터페이스
- 지연 로딩 이미지
- 툴팁
- 커스텀 이벤트 핸들러

## 표현식

이 앱에서는 주황색 박스를 '패닝이 가능한' 박스로 만들고자 한다.

`panstart`, `panmove` 및 `panend` 이벤트에 대한 이벤트 핸들러가 있지만 기본 DOM 이벤트는 아니다.

```javascript
import { pannable } from './pannable.js';

//...

<div class="box"
    use:pannable
    on:panstart={handlePanStart}
    on:panmove={handlePanMove}
    on:panend={handlePanEnd}
    style="transform:
        translate({$coords.x}px,{$coords.y}px)
        rotate({$coords.x * 0.2}deg)"
></div>
```

`pannable.js` 파일을 열면, 전환 함수와 마찬가지로 액션 함수는 노드와 일부 선택적 매개 변수를 수신하고 액션 객체를 반환한다.

해당 객체는 엘리먼트가 마운트 해제 될 때 호출되는 `destroy` 함수를 가질 수 있다.

우리는 사용자가 엘리먼트 위에서 마우스를 내려 놓으면 `panstart` 이벤트, 마우스를 끌 때 `panmove` 이벤트 (마우스가 얼마나 멀리 이동했는지를 나타내는 `dx` 및 `dy` 속성 포함) 및 마우스를 올리면 `panend` 이벤트를 시작하려고 한다.

## 파라미터 추가

전환 및 애니메이션과 마찬가지로 액션은 인수를 취할 수 있으며 액션 함수가 속한 엘리먼트와 함께 액션 함수가 호출된다.

여기서는 사용자가 지정된 기간 동안 버튼을 누르고 있을 때마다 동일한 이름으로 이벤트를 발생시키는 `longpress` 액션을 사용한다.

`longpress.js` 파일로 전환하면 `500ms` 로 하드 코딩 된 것을 볼 수 있다.

`duration` 을 두 번째 인자로 전달하고 해당 `duration` 을 `setTimeout` 호출에 전달하도록 함수를 변경할 수 있다.

```javascript
export function longpress(node, duration) {

    // ...

    const handleMousedown = () => {
        timer = setTimeout(() => {
            node.dispatchEvent(
                new CustomEvent('longpress')
            );
        }, duration);
    };

    // ...
}
```

> 액션에 여러 인자를 전달해야하는 경우 다음과 같이 사용중인 인자를 단일 객체로 결합한다. `use:longpress={{duration, spiciness}}`
