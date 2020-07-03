# 모션

값을 설정하고 DOM 에 반영하는 것을 알아보았다.

더 멋진 DOM 업데이트는 모션을 부여하는 것이다.

## 트위닝

Svelte 는 애니메이션을 사용하여 변경 사항을 전달하는 매끄러운 UI 를 구현할 수 있는 도구가 포함되어있다.

다음은 트위닝(tweened)을 사용한 예제이다.

```javascript
<script>
    import { tweened } from 'svelte/motion';
    import { cubicOut } from 'svelte/easing';

    const progress = tweened(0, {
        duration: 400,
        easing: cubicOut
    });
</script>
```

> `svelte/easing` 모듈에는 **Penner easing equations** 를 가지고 있으며, `p` 와 `t` 는 모두 0 과 1 사이의 값인 고유 한 `p => t` 함수를 제공 할 수 있다.

`p => t` 는 시간에 따른 파라미터 변화율을 지정한다. 예) easing : easeInBounce

자세한 옵션은 **Penner easing equations** 을 참조하는 것이 바람직하다.

트위닝의 전체 옵션은 다음과 같다.

| 옵션 | 설명 |
| --- | --- |
| `delay` | 트위닝이 시작되기전 밀리초 |
| `duration` | 트위닝의 지속시간(밀리초) 또는 `(from, to) => milliseconds` 함수를 사용하여 값을 크게 변경하기 위해 더 긴 트윈을 지정할 수 있다. |
| `easing` | `p => t` |
| `interpolate` | `(from, to) => t => value`. 기본적으로 Svelte 는 숫자, 날짜 및 같은 모양의 배열과 객체 사이를 보간할 수 있다. (보간:두 점사이를 연결하는 방법) |

이 옵션을 `progress.set` 과 `progress.update` 에 두 번째 인수로 전달할 수도 있다.

이 경우 기본 값을 무시하며 `set` 과 `update` 메소드는 트윈이 완료되면 `Promise` 를 반환한다.

## 스프링

스프링 기능은 자주 변경되는 값에 대한 트위닝 모션보다 좋은 대안이다.

```javascript
<script>
    import { spring } from 'svelte/motion';

    let coords = spring({ x: 50, y: 50 }, {
        stiffness: 0.1,
        damping: 0.25
    });

    let size = spring(10);
</script>
```
